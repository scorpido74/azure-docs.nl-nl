---
title: Niet-compatibele resources herstellen
description: Met deze handleiding u resources herstellen die niet voldoen aan het beleid in Azure Policy.
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 71af5c81e0dce4d5c0a0461534f634db36bd66a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471384"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Niet-compatibele resources herstellen met Azure-beleid

Resources die niet voldoen aan een **deployIfNotExists** of **een wijzigingsbeleid** kunnen via **Remediation**in een conforme status worden geplaatst. Herstel wordt uitgevoerd door Azure Policy te instrueren om het effect **deployIfNotExists** of de **tagbewerkingen** van het toegewezen beleid op uw bestaande resources uit te voeren, ongeacht of die toewijzing is voor een beheergroep, een abonnement, een resourcegroep of een afzonderlijke resource. In dit artikel worden de stappen weergegeven die nodig zijn om de herstelprocedure met Azure Policy te begrijpen en te realiseren.

## <a name="how-remediation-security-works"></a>Hoe herstelbeveiliging werkt

Wanneer azure-beleid de sjabloon uitvoert in de beleidsdefinitie **deployIfNotExists,** wordt dit uitgevoerd met behulp van een [beheerde identiteit](../../../active-directory/managed-identities-azure-resources/overview.md).
Azure Policy maakt een beheerde identiteit voor elke toewijzing, maar moet details bevatten over welke rollen de beheerde identiteit moet worden toegekend. Als de beheerde identiteit rollen mist, wordt deze fout weergegeven tijdens de toewijzing van het beleid of een initiatief. Wanneer u de portal gebruikt, verleent Azure Policy automatisch de beheerde identiteit van de vermelde rollen zodra de toewijzing wordt gestart. De _locatie_ van de beheerde identiteit heeft geen invloed op de werking van de identiteit met Azure Policy.

![Beheerde identiteit - ontbrekende rol](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Als een resource die is gewijzigd door **deployIfNotExists** of **wijzigen** buiten het bereik van de beleidstoewijzing valt of als de sjabloon toegang heeft tot eigenschappen op resources buiten het bereik van de beleidstoewijzing, moet de beheerde identiteit van de toewijzing [handmatig toegang](#manually-configure-the-managed-identity) krijgen of wordt de herstelimplementatie mislukt.

## <a name="configure-policy-definition"></a>Beleidsdefinitie configureren

De eerste stap is het definiëren van de rollen die **IfNotExists implementeren** en **behoeften wijzigen** in de beleidsdefinitie om de inhoud van de opgenomen sjabloon succesvol te implementeren. Voeg onder de eigenschap **details** een eigenschap **roleDefinitionIds** toe. Deze eigenschap is een reeks tekenreeksen die overeenkomen met rollen in uw omgeving. Zie voor een volledig voorbeeld het [voorbeeld deployIfNotExists](../concepts/effects.md#deployifnotexists-example) of de [voorbeelden wijzigen](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

De eigenschap **roleDefinitionIds** gebruikt de volledige resource-id en neemt niet de korte **roleName** van de rol. Gebruik de volgende code om de id voor de rol 'Inzender' in uw omgeving te krijgen:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Handmatig de beheerde identiteit configureren

Bij het maken van een toewijzing met behulp van de portal genereert Azure Policy zowel de beheerde identiteit als verleent het de rollen die zijn gedefinieerd in **roleDefinitionIds**. In de volgende voorwaarden moeten stappen om de beheerde identiteit te maken en machtigingen toe te wijzen handmatig worden uitgevoerd:

- Tijdens het gebruik van de SDK (zoals Azure PowerShell)
- Wanneer een resource buiten het toewijzingsbereik wordt gewijzigd door de sjabloon
- Wanneer een resource buiten het toewijzingsbereik wordt gelezen door de sjabloon

> [!NOTE]
> Azure PowerShell en .NET zijn de enige SDK's die deze mogelijkheid momenteel ondersteunen.

### <a name="create-managed-identity-with-powershell"></a>Beheerde identiteit maken met PowerShell

Als u een beheerde identiteit wilt maken tijdens de toewijzing van het beleid, moet **Locatie** worden gedefinieerd en **AssignIdentity** gebruikt. In het volgende voorbeeld wordt de definitie van het ingebouwde beleid **Deploy SQL DB-transparante gegevensversleuteling,** wordt de doelgroep ingesteld en wordt de toewijzing gemaakt.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

De `$assignment` variabele bevat nu de hoofd-ID van de beheerde identiteit, samen met de standaardwaarden die worden geretourneerd bij het maken van een beleidstoewijzing. Het kan worden `$assignment.Identity.PrincipalId`benaderd via .

### <a name="grant-defined-roles-with-powershell"></a>Gedefinieerde rollen verlenen met PowerShell

De nieuwe beheerde identiteit moet replicatie voltooien via Azure Active Directory voordat de benodigde rollen kunnen worden toegewezen. Zodra replicatie is voltooid, wordt in het `$policyDef` volgende voorbeeld de beleidsdefinitie voor de **roleDefinitionIds** geïrispereerd en wordt [nieuw-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) gebruikt om de nieuwe beheerde identiteit de rollen toe te kennen.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Gedefinieerde rollen toekennen via portal

Er zijn twee manieren om de beheerde identiteit van een opdracht de gedefinieerde rollen toe te kennen met behulp van de portal, door **Toegangsbeheer (IAM) te** gebruiken of door de beleids- of initiatieftoewijzing te bewerken en op **Opslaan**te klikken.

Voer de volgende stappen uit om een rol toe te voegen aan de beheerde identiteit van de opdracht:

1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

1. Selecteer **Toewijzingen** in het linkerdeelvenster van de Azure Policy-pagina.

1. Zoek de toewijzing met een beheerde identiteit en klik op de naam.

1. Zoek de eigenschap **Toewijzings-id** op de bewerkingspagina. De toewijzings-ID zal zoiets zijn als:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   De naam van de beheerde identiteit is het laatste `2802056bfc094dfb95d4d7a5` gedeelte van de toewijzingsbron-id, dat in dit voorbeeld staat. Kopieer dit gedeelte van de toewijzingsbron-id.

1. Navigeer naar de bron of de bovenliggende container resources (resourcegroep, abonnement, beheergroep) die de handmatige roldefinitie moet toevoegen.

1. Klik op de koppeling **Toegangsbesturingselement (IAM)** op de pagina Resources en klik op **+ Roltoewijzing toevoegen** boven aan de pagina toegangsbeheer.

1. Selecteer de juiste rol die overeenkomt met een **roleDefinitionIds** in de beleidsdefinitie.
   Laat **Assign toegang** toewijzen om in te stellen op de standaardinstelling 'Azure AD-gebruiker, -groep of -toepassing'. Plak of typ in het vak **Selecteren** het gedeelte van de toewijzingsbron-id dat zich eerder bevindt. Zodra de zoekopdracht is voltooid, klikt u op het object met dezelfde naam om ID te selecteren en klikt u op **Opslaan**.

## <a name="create-a-remediation-task"></a>Een hersteltaak maken

### <a name="create-a-remediation-task-through-portal"></a>Een hersteltaak maken via portal

Tijdens de evaluatie bepaalt de beleidstoewijzing met **deployIfNotExists** of **het wijzigen van** effecten of het wijzigen van effecten of er niet-conforme resources zijn. Wanneer niet-conforme bronnen worden gevonden, worden de details weergegeven op de **pagina Herstel.** Samen met de lijst met beleidsregels met niet-conforme resources is de optie om een **hersteltaak**te activeren. Met deze optie wordt een implementatie gemaakt van de sjabloon **deployIfNotExists** of de **bewerkingen wijzigen.**

Voer de volgende stappen uit om een **hersteltaak te**maken:

1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

   ![Zoeken naar beleid in alle services](../media/remediate-resources/search-policy.png)

1. Selecteer **Herstel aan** de linkerkant van de azure-beleidspagina.

   ![Remediation selecteren op de pagina Beleid](../media/remediate-resources/select-remediation.png)

1. Alle **deployIfNotExists** en **wijzigen** beleidstoewijzingen met niet-conforme resources zijn opgenomen in de tabel Beleid om het tabblad en de gegevenstabel **te herstellen.** Klik op een beleid met resources die niet voldoen. De pagina **Nieuwe hersteltaak** wordt geopend.

   > [!NOTE]
   > Een alternatieve manier om de **taakpagina voor herstel** te openen, is door het beleid te zoeken en op het beleid te klikken op de **pagina Naleving** en vervolgens op de knop **Hersteltaak maken.**

1. Filter op de pagina **Nieuwe hersteltaak** de resources die u wilt herstellen met behulp van de **scope-ellipsen** om onderliggende bronnen te kiezen van waaruit het beleid is toegewezen (inclusief naar de afzonderlijke resourceobjecten). Gebruik bovendien de vervolgkeuzelijst **Locaties** om de resources verder te filteren. Alleen de middelen in de tabel worden gesaneerd.

   ![Saneren - selecteer welke resources moeten worden verholpen](../media/remediate-resources/select-resources.png)

1. Begin de hersteltaak zodra de resources zijn gefilterd door op **Herstellen te**klikken . De pagina Beleidsnaleving wordt geopend op het tabblad **Hersteltaken** om de status van de voortgang van de taken weer te geven. Implementaties die zijn gemaakt door de hersteltaak, beginnen meteen.

   ![Herstel - voortgang van hersteltaken](../media/remediate-resources/task-progress.png)

1. Klik op de **hersteltaak** op de pagina beleidsnaleving om meer informatie te krijgen over de voortgang. De filtering die voor de taak wordt gebruikt, wordt weergegeven samen met een lijst met de resources die worden gesaneerd.

1. Klik vanaf de **taakpagina voor herstel** met de rechtermuisknop op een resource om de implementatie van de hersteltaak of de resource weer te geven. Klik aan het einde van de rij op **Gerelateerde gebeurtenissen** om details te zien, zoals een foutbericht.

   ![Contextmenu voor herstel - resourcetaak](../media/remediate-resources/resource-task-context-menu.png)

Resources die via een **hersteltaak** zijn geïmplementeerd, worden toegevoegd aan het tabblad **Geïmplementeerde resources** op de pagina beleidsnaleving.

### <a name="create-a-remediation-task-through-azure-cli"></a>Een hersteltaak maken via Azure CLI

Als u een **hersteltaak** wilt maken `az policy remediation` met Azure CLI, gebruikt u de opdrachten. Vervang `{subscriptionId}` door uw `{myAssignmentId}` abonnements-ID en door uw **deployIfNotExists** of **wijzig** de beleidstoewijzings-id.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Zie voor andere herstelopdrachten en voorbeelden de opdrachten [voor het az-beleid voor herstel.](/cli/azure/policy/remediation)

### <a name="create-a-remediation-task-through-azure-powershell"></a>Een hersteltaak maken via Azure PowerShell

Als u een **hersteltaak** wilt maken `Start-AzPolicyRemediation` met Azure PowerShell, gebruikt u de opdrachten. Vervang `{subscriptionId}` door uw `{myAssignmentId}` abonnements-ID en door uw **deployIfNotExists** of **wijzig** de beleidstoewijzings-id.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Zie voor andere herstelcmdlets en voorbeelden de module [Az.PolicyInsights.](/powershell/module/az.policyinsights/#policy_insights)

## <a name="next-steps"></a>Volgende stappen

- Voorbeelden bekijken bij [Azure Policy-voorbeelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).
- Begrijpen hoe [u programmatisch beleid maken.](programmatically-create.md)
- Meer informatie over het [verzamelen van nalevingsgegevens](get-compliance-data.md).
- Bekijk wat een beheergroep is met [Uw resources organiseren met Azure-beheergroepen.](../../management-groups/overview.md)
