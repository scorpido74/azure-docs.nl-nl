---
title: Voor beelden van sleutel kluizen zonder vNet-eind punten
description: Met deze voorbeeldbeleidsdefinitie wordt een controle uitgevoerd op Key Vault-kluizen om exemplaren te detecteren die geen service-eindpunten voor een virtueel netwerk hebben.
ms.date: 01/26/2019
ms.topic: sample
ms.openlocfilehash: 078a781ca96df421d52179f5e8d25650def8a2f3
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076210"
---
# <a name="sample---key-vault-vaults-with-no-virtual-network-endpoints"></a>Voorbeeld: Key Vault-kluizen zonder eindpunten voor een virtueel netwerk

Dit voor beeld van het [integreren van Key Vault met Azure Policy](../../../key-vault/azure-policy.md) controles voor sleutel kluizen die geen eind punten van virtuele netwerken hebben. Gebruik het om uw beveiligingsvereisten af te dwingen. Zie [service-eindpunten voor een virtueel netwerk in Key Vault](../../../key-vault/key-vault-overview-vnet-service-endpoints.md) voor meer informatie

U kunt dit voorbeeldbeleid implementeren met behulp van:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure CLI](#azure-cli)
- [REST-API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Voorbeeldbeleid

### <a name="policy-definition"></a>Beleidsdefinitie

De volledige samengestelde JSON beleidsdefinitie, die wordt gebruikt door de REST-API, 'Deploy to Azure'-knoppen en handmatig in de portal.

[!code-json[full](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.json "KeyVault vnet rules")]

> [!NOTE]
> Als u een beleid handmatig in de portal maakt, gebruikt u de gedeelten **properties.parameters** en **properties.policyRule** van de bovenstaande code. Voeg de twee secties samen met accolades `{}` om er geldige JSON van te maken.

### <a name="policy-rules"></a>Beleidsregels

De JSON definieert de regels van het beleid, zoals gebruikt door Azure CLI en Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Beleidsparameters

In dit voorbeeld van beleidsdefinitie zijn geen parameters gedefinieerd.

## <a name="azure-portal"></a>Azure Portal

[![het voor beeld van het beleid implementeren naar azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)
[![het voor beeld van het beleid implementeren naar Azure gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Implementeren met Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name "audit-keyvault-vnet-rules" -DisplayName "Audit if Key Vault has no virtual network rules" -description "Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' -Mode Indexed

# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'audit-keyvault-vnet-rules-assignment' -DisplayName 'Audit Key Vault Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition
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

De scripts voor implementeren en verwijderen gebruiken de volgende opdrachten. Elke opdracht in onderstaande tabel is een link naar opdracht-specifieke documentatie:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | Hiermee maakt u een nieuwe Azure Policy-definitie. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | Hiermee vraagt u één resourcegroep op. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | Hiermee maakt u een nieuwe Azure Policy-toewijzing. In dit voorbeeld bieden we een definitie aan, maar er kan ook een initiatief nodig zijn. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | Hiermee verwijdert u een bestaande Azure Policy-toewijzing. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | Hiermee verwijdert u een bestaande Azure Policy-definitie. |

## <a name="azure-cli"></a>Azure-CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Implementeren met Azure CLI

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'audit-keyvault-vnet-rules' --display-name 'Audit if Key Vault has no virtual network rules' --description 'Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' --mode Indexed)

# Set the scope to a resource group; may also be a subscription or management group
scope=$(az group show --name 'YourResourceGroup')

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'audit-keyvault-vnet-rules-assignment' --display-name 'Audit Key Vault Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r`)
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

## <a name="rest-api"></a>REST API

Er zijn verschillende hulpprogramma's die u kunt gebruiken om te communiceren met de REST-API van Resource Manager, zoals [ARMClient](https://github.com/projectkudu/ARMClient) of PowerShell.

### <a name="deploy-with-rest-api"></a>Implementeren met REST-API

- Maak de beleidsdefinitie (abonnementsbereik). Gebruik de [beleidsdefinitie](#policy-definition) geschreven in JSON voor de body van de aanvraag.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

- Maak de beleidstoewijzing (scope van resourcegroep).

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

  Gebruik het volgende JSON-voorbeeld voor de body van de aanvraag:

  ```json
  {
      "properties": {
          "displayName": "Audit Key Vault Assignment",
          "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules"
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Verwijderen met REST-API

- Beleidstoewijzing verwijderen

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

- Beleidsdefinitie verwijderen

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
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
- Meer informatie over het [integreren van Azure Key Vault met Azure Policy](../../../key-vault/azure-policy.md).
- [Structuur van Azure Policy-definities](../concepts/definition-structure.md) bekijken