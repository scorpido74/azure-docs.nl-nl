---
title: Programmatisch beleid maken
description: In dit artikel u programmatisch beleid maken en beheren voor Azure Policy met Azure CLI, Azure PowerShell en REST API.
ms.date: 01/31/2019
ms.topic: how-to
ms.openlocfilehash: 08ed43a464d1dd7de8220428dbc1c61ce9fc3ad6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264542"
---
# <a name="programmatically-create-policies"></a>Programmatisch beleid maken

In dit artikel u programmatisch beleid maken en beheren. Azure Policy-definities dwingen verschillende regels en effecten af voor uw resources. Handhaving zorgt ervoor dat resources voldoen aan uw bedrijfsnormen en servicelevelovereenkomsten.

Zie [Nalevingsgegevens verkrijgen](get-compliance-data.md)voor informatie over naleving.

## <a name="prerequisites"></a>Vereisten

Controleer voordat u begint of aan de volgende voorwaarden is voldaan:

1. Installeer de [ARMClient](https://github.com/projectkudu/ARMClient) als u dit nog niet hebt gedaan. Dit is een hulpprogramma waarmee HTTP-aanvragen worden verzonden naar Azure Resource Manager-API’s.

1. Werk uw Azure PowerShell-module bij naar de nieuwste versie. Zie [Azure PowerShell-module installeren](/powershell/azure/install-az-ps) voor gedetailleerde informatie. Zie [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)voor meer informatie over de nieuwste versie.

1. Registreer de Azure Policy Insights-bronprovider met Azure PowerShell om te valideren dat uw abonnement werkt met de resourceprovider. Als u een resourceprovider wilt registreren, moet u toestemming hebben om de actiebewerking voor het register voor de resourceprovider uit te voeren. Deze bewerking is opgenomen in de rollen Inzender en Eigenaar. Voer de volgende opdracht uit om de resourceprovider te registreren:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Zie [Resourceproviders en -typen](../../../azure-resource-manager/management/resource-providers-and-types.md) voor meer informatie over het registreren en weergeven van resourceproviders.

1. Als u dit nog niet hebt gedaan, installeert u Azure CLI. U de nieuwste versie downloaden bij [Installatie Azure CLI op Windows.](/cli/azure/install-azure-cli-windows)

## <a name="create-and-assign-a-policy-definition"></a>Een beleidsdefinitie maken en toewijzen

De eerste stap naar een betere zichtbaarheid van uw resources is het maken en toewijzen van beleid over uw resources. De volgende stap is om te leren hoe u programmatisch een beleid maken en toewijzen. Het voorbeeldbeleid controleert opslagaccounts die openstaan voor alle openbare netwerken met PowerShell-, Azure CLI- en HTTP-aanvragen.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Een beleidsdefinitie maken en toewijzen met PowerShell

1. Gebruik het volgende JSON-fragment om een JSON-bestand te maken met de naam AuditStorageAccounts.json.

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

   Zie [Azure Policy Definition Structure](../concepts/definition-structure.md)voor meer informatie over het maken van een beleidsdefinitie.

1. Voer de volgende opdracht uit om een beleidsdefinitie te maken met het bestand AuditStorageAccounts.json.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   Met de opdracht wordt een beleidsdefinitie gemaakt met de naam _Audit Storage Accounts Open to Public Networks_.
   Zie [Nieuwe-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition)voor meer informatie over andere parameters die u gebruiken.

   Wanneer u zonder `New-AzPolicyDefinition` locatieparameters wordt aangeroepen, wordt de beleidsdefinitie standaard opgeslagen in het geselecteerde abonnement van de sessiecontext. Als u de definitie op een andere locatie wilt opslaan, gebruikt u de volgende parameters:

   - **AbonnementId** - Opslaan in een ander abonnement. Vereist een _GUID-waarde._
   - **ManagementGroupName** - Opslaan in een beheergroep. Vereist een _tekenreekswaarde._

1. Nadat u uw beleidsdefinitie hebt gemaakt, u een beleidstoewijzing maken door de volgende opdrachten uit te voeren:

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Vervang _ContosoRG_ door de naam van uw beoogde resourcegroep.

   De **Scope** parameter `New-AzPolicyAssignment` Scope voor werken met beheergroep, abonnement, resourcegroep of één resource. De parameter maakt gebruik van een volledig `Get-AzResourceGroup` resourcepad, waarbij de eigenschap **ResourceId** op retourneert. Het patroon voor **Scope** voor elke container is als volgt. Respectievelijk `{rName}` `{rgName}`vervang `{subId}`, `{mgName}` , en uw resourcenaam, naam van de resourcegroep, abonnements-id en de naam van de beheergroep.
   `{rType}`wordt vervangen door het **resourcetype** van `Microsoft.Compute/virtualMachines` de resource, bijvoorbeeld voor een virtuele machine.

   - Resource -`/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Resourcegroep -`/subscriptions/{subId}/resourceGroups/{rgName}`
   - Abonnement -`/subscriptions/{subId}/`
   - Managementgroep -`/providers/Microsoft.Management/managementGroups/{mgName}`

Zie [Az.Resources](/powershell/module/az.resources/#policies)voor meer informatie over het beheren van resourcebeleid met de Azure Resource Manager PowerShell-module.

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Een beleidsdefinitie maken en toewijzen met ARMClient

Gebruik de volgende procedure om een beleidsdefinitie te maken.

1. Kopieer het volgende JSON-fragment om een JSON-bestand te maken. Je belt het bestand in de volgende stap.

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

   Vervang de voorgaande {subscriptionId} door de id van uw abonnement of {managementGroupId} door de id van uw [beheergroep](../../management-groups/overview.md).

   Zie [Azure Policy Definities – Definities voor Azure-beleid maken of bijwerken](/rest/api/resources/policydefinitions/createorupdate) en [beleidsdefinities – Maken of bijwerken bij de beheergroep voor](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) meer informatie over de structuur van de query.

Gebruik de volgende procedure om een beleidstoewijzing te maken en de beleidsdefinitie toe te wijzen op het niveau van de resourcegroep.

1. Kopieer het volgende JSON-fragment om een JSON-beleidstoewijzingsbestand te maken. Vervang voorbeeldinformatie &lt; &gt; in symbolen door uw eigen waarden.

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

1. Maak de beleidstoewijzing met de volgende aanroepen:

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
   ```

   Vervang voorbeeldinformatie &lt; &gt; in symbolen door uw eigen waarden.

   Zie [Azure REST API Resources](/rest/api/resources/)voor meer informatie over het voeren van HTTP-aanroepen naar de REST API.

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Een beleidsdefinitie maken en toewijzen met Azure CLI

Als u een beleidsdefinitie wilt maken, gebruikt u de volgende procedure:

1. Kopieer het volgende JSON-fragment om een JSON-beleidstoewijzingsbestand te maken.

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

   Zie [Azure Policy Definition Structure](../concepts/definition-structure.md)voor meer informatie over het maken van een beleidsdefinitie.

1. Voer de volgende opdracht uit om een beleidsdefinitie te maken:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   Met de opdracht wordt een beleidsdefinitie gemaakt met de naam _Audit Storage Accounts Open to Public Networks_.
   Zie [az-beleidsdefinitie maken](/cli/azure/policy/definition#az-policy-definition-create)voor meer informatie over andere parameters die u gebruiken.

   Wanneer u zonder `az policy definition creation` locatieparameters wordt aangeroepen, wordt de beleidsdefinitie standaard opgeslagen in het geselecteerde abonnement van de sessiecontext. Als u de definitie op een andere locatie wilt opslaan, gebruikt u de volgende parameters:

   - **--abonnement** - Opslaan in een ander abonnement. Vereist een _GUID-waarde_ voor de abonnements-ID of een _tekenreekswaarde_ voor de abonnementsnaam.
   - **--management-group** - Sparen voor een managementgroep. Vereist een _tekenreekswaarde._

1. Gebruik de volgende opdracht om een beleidstoewijzing te maken. Vervang voorbeeldinformatie &lt; &gt; in symbolen door uw eigen waarden.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   De parameter **-scope** voor `az policy assignment create` werken met beheergroep, abonnement, resourcegroep of één resource. De parameter gebruikt een volledig resourcepad. Het patroon voor **--scope** voor elke container is als volgt. Respectievelijk `{rName}` `{rgName}`vervang `{subId}`, `{mgName}` , en uw resourcenaam, naam van de resourcegroep, abonnements-id en de naam van de beheergroep. `{rType}`wordt vervangen door het **resourcetype** van `Microsoft.Compute/virtualMachines` de resource, bijvoorbeeld voor een virtuele machine.

   - Resource -`/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Resourcegroep -`/subscriptions/{subID}/resourceGroups/{rgName}`
   - Abonnement -`/subscriptions/{subID}`
   - Managementgroep -`/providers/Microsoft.Management/managementGroups/{mgName}`

U de Azure Policy Definition ID opgebruiken met PowerShell met de volgende opdracht:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

De beleidsdefinitie-id voor de beleidsdefinitie die u hebt gemaakt, moet op het volgende voorbeeld lijken:

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Zie [Azure CLI Resource Policies](/cli/azure/policy?view=azure-cli-latest)voor meer informatie over hoe u resourcebeleid beheren met Azure CLI.

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende artikelen voor meer informatie over de opdrachten en query's in dit artikel.

- [Azure REST API-bronnen](/rest/api/resources/)
- [Azure PowerShell-modules](/powershell/module/az.resources/#policies)
- [Azure CLI-beleidsopdrachten](/cli/azure/policy?view=azure-cli-latest)
- [Verwijzing naar REST API-referentie azure Policy Insights-bronprovider](/rest/api/policy-insights)
- [Uw resources organiseren met Azure-beheergroepen](../../management-groups/overview.md).