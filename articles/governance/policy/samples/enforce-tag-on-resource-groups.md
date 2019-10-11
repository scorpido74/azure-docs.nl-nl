---
title: 'Voor beeld: code en waarde voor resource groepen afdwingen'
description: Deze voorbeeldbeleidsdefinitie vereist een tag en waarde in een brongroep.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 01/31/2019
ms.author: dacoulte
ms.openlocfilehash: 5f4af5ee88ad491e7864e82afc337801e47f2204
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255772"
---
# <a name="sample---enforce-tag-and-its-value-on-resource-groups"></a>Voorbeeld: tag en de waarde ervan afdwingen in brongroepen

Dit beleid vereist een tag en waarde in een brongroep. U geeft de vereiste naam en waarde voor de tag op.

U kunt dit voorbeeldbeleid implementeren met behulp van:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure CLI](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Voorbeeldbeleid

### <a name="policy-definition"></a>Beleidsdefinitie

De volledige samengestelde JSON beleidsdefinitie, die wordt gebruikt door de REST-API, 'Deploy to Azure'-knoppen en handmatig in de portal.

[!code-json[main](../../../../policy-templates/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.json "Enforce tag and its value on resource groups")]

> [!NOTE]
> Als u een beleid handmatig in de portal maakt, gebruikt u de gedeelten **properties.parameters** en **properties.policyRule** van de bovenstaande code. Voeg de twee secties samen met accolades `{}` om er geldige JSON van te maken.

### <a name="policy-rules"></a>Beleidsregels

De JSON definieert de regels van het beleid, zoals gebruikt door Azure CLI en Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Beleidsparameters

De JSON definieert de beleidsparameters, zoals gebruikt door Azure CLI en Azure PowerShell.

[!code-json[parameters](../../../../policy-templates/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.parameters.json "Policy parameters (JSON)")]

|Naam |Type |Veld |Beschrijving |
|---|---|---|---|
|tagName |Tekenreeks |tags |Naam van de tag, bijvoorbeeld costCenter|
|tagValue |Tekenreeks |tags |Waarde van de tag, bijvoorbeeld headquarter|

Bij het maken van een toewijzing via PowerShell of Azure CLI kunnen de parameterwaarden worden doorgegeven als JSON in een tekenreeks of via een bestand met `-PolicyParameter` (PowerShell) of `--params` (Azure CLI).
PowerShell ondersteunt ook `-PolicyParameterObject`, waarvoor de cmdlet een hashtabel met naam/waardeparen moet ontvangen waarin **Name** de parameternaam is en **Value** is de enkelvoudige waarde of matrix met waarden die tijdens toewijzing wordt doorgegeven.

In deze voorbeeldparameter zijn een _tagName_ van **costCenter** en _tagValue_ van **headquarter** gedefinieerd.

```json
{
    "tagName": {
        "value": "costCenter"
    },
    "tagValue": {
        "value": "headquarter"
    }
}
```

## <a name="azure-portal"></a>Azure Portal

[![Deploy het voor beeld van het beleid naar azure](../media/deploy/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FResourceGroup%2Fenforce-resourceGroup-tags%2Fazurepolicy.json)
[![Deploy het voor beeld van het beleid naar Azure gov](../media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FResourceGroup%2Fenforce-resourceGroup-tags%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Implementeren met Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'enforce-resourceGroup-tags' -DisplayName 'Enforce tag and its value on resource groups' -description 'Enforces a required tag and its value on resource groups.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyParam = '{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'enforce-resourceGroup-tags-assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyParam
```

### <a name="remove-with-azure-powershell"></a>Verwijderen met Azure PowerShell

Voer de volgende opdrachten uit om de vorige toewijzing en definitie te verwijderen:

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Toelichting van Azure PowerShell

De scripts voor implementeren en verwijderen gebruiken de volgende opdrachten. Elke opdracht in de volgende tabel is een koppeling naar opdrachtspecifieke documentatie:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | Hiermee maakt u een nieuwe Azure Policy-definitie. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | Hiermee vraagt u één resourcegroep op. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | Hiermee maakt u een nieuwe Azure Policy-toewijzing. In dit voorbeeld bieden we een definitie aan, maar er kan ook een initiatief nodig zijn. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | Hiermee verwijdert u een bestaande Azure Policy-toewijzing. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | Hiermee verwijdert u een bestaande Azure Policy-definitie. |

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Implementeren met Azure CLI

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'enforce-resourceGroup-tags' --display-name 'Enforce tag and its value on resource groups' --description 'Enforces a required tag and its value on resource groups.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a resource, subscription, or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyParam='{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
assignment=$(
az policy assignment create --name 'enforce-resourceGroup-tags-assignment' --display-name 'Enforce tag and its value on resource groups'  --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
```

### <a name="remove-with-azure-cli"></a>Verwijderen met Azure CLI

Voer de volgende opdrachten uit om de vorige toewijzing en definitie te verwijderen:

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Toelichting van Azure CLI

| Opdracht | Opmerkingen |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Hiermee maakt u een nieuwe Azure Policy-definitie. |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Hiermee vraagt u één resourcegroep op. |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Hiermee maakt u een nieuwe Azure Policy-toewijzing. In dit voorbeeld bieden we een definitie aan, maar er kan ook een initiatief nodig zijn. |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Hiermee verwijdert u een bestaande Azure Policy-toewijzing. |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Hiermee verwijdert u een bestaande Azure Policy-definitie. |

Er zijn verschillende hulpprogramma's die u kunt gebruiken om te communiceren met de REST-API van Resource Manager, zoals [ARMClient](https://github.com/projectkudu/ARMClient) of PowerShell. Een voorbeeld van het aanroepen van een REST-API vanuit PowerShell vindt u in de sectie **Aliassen** van [Structuur van Azure-beleidsdefinities](../concepts/definition-structure.md#aliases).

## <a name="rest-api"></a>REST-API

### <a name="deploy-with-rest-api"></a>Implementeren met REST API

- Maak de beleidsdefinitie (abonnementsbereik). Gebruik de [beleidsdefinitie](#policy-definition) geschreven in JSON voor de body van de aanvraag.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/enforce-resourceGroup-tags?api-version=2016-12-01
  ```

- Maak de beleidstoewijzing (scope van resourcegroep).

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/enforce-resourceGroup-tags-assignment?api-version=2017-06-01-preview
  ```

  Gebruik het volgende JSON-voorbeeld voor de body van de aanvraag:

```json
  {
      "properties": {
          "displayName": "Enforce tag and its value Assignment",
          "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/enforce-resourceGroup-tags",
          "parameters": {
              "tagName": {
                  "value": "costCenter"
              },
              "tagValue": {
                  "value": "headquarter"
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Verwijderen met REST-API

- Beleidstoewijzing verwijderen

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/enforce-resourceGroup-tags-assignment?api-version=2017-06-01-preview
  ```

- Beleidsdefinitie verwijderen

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/enforce-resourceGroup-tags?api-version=2016-12-01
  ```

### <a name="rest-api-explanation"></a>Toelichting van REST-API

| Service | Groep | Bewerking | Opmerkingen |
|---|---|---|---|
| Resourcebeheer | Definities voor beleid | [Maken](/rest/api/resources/policydefinitions/createorupdate) | Hiermee maakt u een nieuwe Azure Policy-definitie voor een abonnement. Alternatief: [Maken in beheergroep](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Resourcebeheer | Beleidstoewijzingen | [Maken](/rest/api/resources/policyassignments/create) | Hiermee maakt u een nieuwe Azure Policy-toewijzing. In dit voorbeeld bieden we een definitie aan, maar er kan ook een initiatief nodig zijn. |
| Resourcebeheer | Beleidstoewijzingen | [Verwijderen](/rest/api/resources/policyassignments/delete) | Hiermee verwijdert u een bestaande Azure Policy-toewijzing. |
| Resourcebeheer | Definities voor beleid | [Verwijderen](/rest/api/resources/policydefinitions/delete) | Hiermee verwijdert u een bestaande Azure Policy-definitie. Alternatief: [Verwijderen in beheergroep](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Volgende stappen

- Aanvullende [voorbeelden van Azure Policy](index.md) bekijken
- [Structuur van Azure Policy-definities](../concepts/definition-structure.md) bekijken