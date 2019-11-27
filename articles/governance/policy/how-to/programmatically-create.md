---
title: Programmatisch beleid maken
description: Dit artikel helpt u bij het programmatisch maken en beheren van beleids regels voor Azure Policy met Azure CLI, Azure PowerShell en REST API.
ms.date: 01/31/2019
ms.topic: conceptual
ms.openlocfilehash: 98af714e5aaf8e103b81e77c9960589fa0ee6b77
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463535"
---
# <a name="programmatically-create-policies"></a>Programmatisch beleid maken

Dit artikel helpt u bij het programmatisch beleid maken en beheren. Met Azure Policy definities worden verschillende regels en effecten voor uw resources afgedwongen. Afdwingen zorgt ervoor dat resources voldoen aan uw bedrijfsnormen en serviceovereenkomsten blijven.

Zie [nalevings gegevens ophalen](get-compliance-data.md)voor meer informatie over naleving.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, zorg ervoor dat de volgende vereisten wordt voldaan:

1. Installeer de [ARMClient](https://github.com/projectkudu/ARMClient) als u dit nog niet hebt gedaan. Dit is een hulpprogramma waarmee HTTP-aanvragen worden verzonden naar Azure Resource Manager-API’s.

1. Werk de Azure PowerShell-module bij naar de nieuwste versie. Zie [Azure PowerShell-module installeren](/powershell/azure/install-az-ps) voor gedetailleerde informatie. Zie [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)voor meer informatie over de meest recente versie.

1. Registreer de Azure Policy Insights-resource provider met behulp van Azure PowerShell om te controleren of uw abonnement werkt met de resource provider. Als u wilt een resourceprovider registreren, moet u gemachtigd zijn om uit te voeren van de registreeractie voor de resourceprovider. Deze bewerking is opgenomen in de rollen Inzender en Eigenaar. Voer de volgende opdracht uit om de resourceprovider te registreren:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Zie [Resourceproviders en -typen](../../../azure-resource-manager/resource-manager-supported-services.md) voor meer informatie over het registreren en weergeven van resourceproviders.

1. Als u niet hebt gedaan, installeert u Azure CLI. U kunt de nieuwste versie downloaden bij de [installatie van Azure cli in Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Maken en een beleidsdefinitie toewijzen

De eerste stap voor beter inzicht in uw resources is het maken en toewijzen van beleid voor uw resources. De volgende stap is om te leren hoe u programmatisch maken en toewijzen van een beleid. De voorbeeldbeleid controleert storage-accounts die zijn geopend met alle openbare netwerken met behulp van PowerShell, Azure CLI en HTTP-aanvragen.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Maken en toewijzen van een beleidsdefinitie met PowerShell

1. Gebruik de volgende JSON-codefragment om te maken van een JSON-bestand met de naam AuditStorageAccounts.json.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Zie [Azure Policy Definition structure](../concepts/definition-structure.md)(Engelstalig) voor meer informatie over het ontwerpen van een beleids definitie.

1. Voer de volgende opdracht om te maken van een beleidsdefinitie met behulp van het bestand AuditStorageAccounts.json.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   Met de opdracht wordt een beleids definitie gemaakt met de naam _audit Storage accounts open open bare netwerken_.
   Zie [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition)voor meer informatie over andere para meters die u kunt gebruiken.

   Als de para meters zonder locatie worden aangeroepen, wordt `New-AzPolicyDefinition` standaard ingesteld op het opslaan van de beleids definitie in het geselecteerde abonnement van de sessie context. Als u wilt de definitie van de opslaan naar een andere locatie, gebruik de volgende parameters:

   - **SubscriptionId** : opslaan in een ander abonnement. Vereist een _GUID_ -waarde.
   - **ManagementGroupName** : Sla het bestand op in een beheer groep. Vereist een _teken reeks_ waarde.

1. Nadat u de beleidsdefinitie van uw maakt, kunt u een beleidstoewijzing maken door het uitvoeren van de volgende opdrachten:

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Vervang _ContosoRG_ door de naam van de gewenste resource groep.

   De **bereik** parameter op `New-AzPolicyAssignment` werkt met de beheer groep, het abonnement, de resource groep of een enkele resource. De para meter gebruikt een volledig bronpad, die de eigenschap **ResourceID** op `Get-AzResourceGroup` retourneert. Het patroon voor de **Scope** voor elke container is als volgt. Vervang `{rName}`, `{rgName}`, `{subId}`en `{mgName}` door de resource naam, de naam van de resource groep, de abonnements-ID en de naam van de beheer groep.
   `{rType}` wordt vervangen door het **resource type** van de resource, zoals `Microsoft.Compute/virtualMachines` voor een virtuele machine.

   - Resource-`/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Resource groep-`/subscriptions/{subId}/resourceGroups/{rgName}`
   - Abonnement-`/subscriptions/{subId}/`
   - Beheer groep-`/providers/Microsoft.Management/managementGroups/{mgName}`

Zie [AZ. resources](/powershell/module/az.resources/#policies)(Engelstalig) voor meer informatie over het beheren van bron beleid met behulp van de Azure Resource Manager Power shell-module.

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Maken en toewijzen van een beleidsdefinitie ARMClient

Gebruik de volgende procedure om de beleidsdefinitie van een te maken.

1. Kopieer de volgende JSON-fragment voor het maken van een JSON-bestand. Noem het bestand in de volgende stap.

   ```json
   "properties": {
       "displayName": "Audit Storage Accounts Open to Public Networks",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
       "parameters": {},
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Storage/storageAccounts"
                   },
                   {
                       "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                       "equals": "Allow"
                   }
               ]
           },
           "then": {
               "effect": "audit"
           }
       }
   }
   ```

1. Maak de beleidsdefinitie met een van de volgende aanroepen:

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
   ```

   Vervang de voor gaande {subscriptionId} met de ID van uw abonnement of {managementGroupId} door de ID van uw [beheer groep](../../management-groups/overview.md).

   Zie voor meer informatie over de structuur van de query [Azure Policy definities – maken of bijwerken](/rest/api/resources/policydefinitions/createorupdate) en [beleids definities: maken of bijwerken in beheer groep](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

Gebruik de volgende procedure een beleidstoewijzing maken en toewijzen van de beleidsdefinitie op het niveau van de resource.

1. Kopieer de volgende JSON-fragment voor het maken van een JSON-bestand voor het toewijzen van beleid. Vervang voorbeeld informatie in &lt;&gt; symbolen door uw eigen waarden.

   ```json
   {
       "properties": {
           "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
           "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
           "parameters": {},
           "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
           "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
       }
   }
   ```

1. Maak de toewijzing van beleid met behulp van de volgende oproep verzenden:

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
   ```

   Vervang voorbeeld informatie in &lt;&gt; symbolen door uw eigen waarden.

   Zie [Azure rest API resources](/rest/api/resources/)(Engelstalig) voor meer informatie over het maken van http-aanroepen naar de rest API.

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Maken en toewijzen van een beleidsdefinitie met Azure CLI

Gebruik de volgende procedure voor het maken van een beleidsdefinitie:

1. Kopieer de volgende JSON-fragment voor het maken van een JSON-bestand voor het toewijzen van beleid.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Zie [Azure Policy Definition structure](../concepts/definition-structure.md)(Engelstalig) voor meer informatie over het ontwerpen van een beleids definitie.

1. Voer de volgende opdracht om een beleidsdefinitie maken:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   Met de opdracht wordt een beleids definitie gemaakt met de naam _audit Storage accounts open open bare netwerken_.
   Zie [AZ Policy Definition Create](/cli/azure/policy/definition#az-policy-definition-create)(Engelstalig) voor meer informatie over andere para meters die u kunt gebruiken.

   Als de para meters zonder locatie worden aangeroepen, wordt `az policy definition creation` standaard ingesteld op het opslaan van de beleids definitie in het geselecteerde abonnement van de sessie context. Als u wilt de definitie van de opslaan naar een andere locatie, gebruik de volgende parameters:

   - **--abonnement** : Sla het bestand op in een ander abonnement. Vereist een _GUID_ -waarde voor de abonnements-id of een _teken reeks_ waarde voor de naam van het abonnement.
   - **--beheer-groep** -opslaan in een beheer groep. Vereist een _teken reeks_ waarde.

1. Gebruik de volgende opdracht om een beleidstoewijzing te maken. Vervang voorbeeld informatie in &lt;&gt; symbolen door uw eigen waarden.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   De para meter **--Scope** op `az policy assignment create` werkt met de beheer groep, het abonnement, de resource groep of een enkele resource. De para meter gebruikt een volledig bronpad. Het patroon voor de **Scope** voor elke container is als volgt. Vervang `{rName}`, `{rgName}`, `{subId}`en `{mgName}` door de resource naam, de naam van de resource groep, de abonnements-ID en de naam van de beheer groep. `{rType}` wordt vervangen door het **resource type** van de resource, zoals `Microsoft.Compute/virtualMachines` voor een virtuele machine.

   - Resource-`/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Resource groep-`/subscriptions/{subID}/resourceGroups/{rgName}`
   - Abonnement-`/subscriptions/{subID}`
   - Beheer groep-`/providers/Microsoft.Management/managementGroups/{mgName}`

U kunt de Azure Policy definitie-ID ophalen met behulp van Power shell met de volgende opdracht:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

De beleidstoewijzings-ID voor de beleidsdefinitie die u hebt gemaakt, moet lijken op het volgende voorbeeld:

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Zie [Azure cli-resource beleid](/cli/azure/policy?view=azure-cli-latest)voor meer informatie over hoe u resource beleid kunt beheren met Azure cli.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over de opdrachten en query's in dit artikel.

- [Azure REST API-resources](/rest/api/resources/)
- [Azure PowerShell modules](/powershell/module/az.resources/#policies)
- [Azure CLI-beleids opdrachten](/cli/azure/policy?view=azure-cli-latest)
- [Naslag informatie over REST API van Azure Policy Insights-resource provider](/rest/api/policy-insights)
- [Uw resources organiseren met Azure-beheergroepen](../../management-groups/overview.md).