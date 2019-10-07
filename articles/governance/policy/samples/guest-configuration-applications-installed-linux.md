---
title: "Voor beeld: controleren of toepassingen niet in Linux-Vm's zijn geïnstalleerd"
description: In dit voor beeld van een gast configuratie-initiatief en definities wordt gecontroleerd of de opgegeven toepassingen niet in virtuele Linux-machines zijn geïnstalleerd.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 05/02/2019
ms.author: dacoulte
ms.openlocfilehash: ef2ab4bebf2247b08cdc80ed74bbe17a67c5baae
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71977041"
---
# <a name="sample---audit-if-specified-applications-arent-installed-inside-linux-vms"></a>Voor beeld: controleren of opgegeven toepassingen niet zijn geïnstalleerd in Linux Vm's

Deze beleids gast configuratie-initiatief maakt een controle gebeurtenis wanneer de opgegeven toepassingen niet in virtuele Linux-machines zijn geïnstalleerd. De ID van dit ingebouwde initiatief is `/providers/Microsoft.Authorization/policySetDefinitions/c937dcb4-4398-4b39-8d63-4a6be432252e`.

> [!IMPORTANT]
> Alle gast configuratie initiatieven bestaan uit **controle** -en **deployIfNotExists** -beleids definities. Het toewijzen van slechts een van de beleids definities leidt ertoe dat gast configuratie niet goed werkt.

U kunt dit voor beeld toewijzen met behulp van:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="components-of-the-initiative"></a>Onderdelen van het initiatief

Dit initiatief voor [gast configuratie](../concepts/guest-configuration.md) bestaat uit het volgende beleid:

- [controleren](#audit-definition) : controleren of toepassingen niet in Linux-vm's zijn geïnstalleerd
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/fee5cb2b-9d9b-410e-afe3-2902d90d0004`
- [deployIfNotExists](#deployIfNotExists-definition) -VM-extensie implementeren om te controleren wanneer toepassingen niet in Linux-vm's zijn geïnstalleerd
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/4d1c04de-2172-403f-901b-90608c35c721`

### <a name="initiative-definition"></a>Initiatiefdefinitie

Het initiatief wordt gemaakt door de **audit** -en **deployIfNotExists** definities samen en de [para meters](#initiative-parameters)van het initiatief toe te voegen. Dit is de JSON van de definitie.

[!code-json[initiative-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/azurepolicyset.json "Initiative definition (JSON)")]

### <a name="initiative-parameters"></a>Initiatiefparameters

|Name |Type |Description |
|---|---|---|
|applicationName |Tekenreeks |Toepassings namen. Voor beeld: python, Power shell of een door komma's gescheiden lijst zoals python, Power shell. Gebruik \* voor joker tekens, zoals ' Power @ no__t-1 '. |

Bij het maken van een toewijzing via PowerShell of Azure CLI kunnen de parameterwaarden worden doorgegeven als JSON in een tekenreeks of via een bestand met `-PolicyParameter` (PowerShell) of `--params` (Azure CLI).
PowerShell ondersteunt ook `-PolicyParameterObject`, waarvoor de cmdlet een hashtabel met naam/waardeparen moet ontvangen waarin **Name** de parameternaam is en **Value** is de enkelvoudige waarde of matrix met waarden die tijdens toewijzing wordt doorgegeven.

In deze voorbeeld parameter wordt de installatie van de toepassingen _python_ en _Power shell_ gecontroleerd.

```json
{
    "applicationName": {
        "value": "python,powershell"
    }
}
```

Alleen de **deployIfNotExists** -beleids definitie maakt gebruik van de para meters van het initiatief.

### <a name="audit-definition"></a>audit definitie

De JSON waarin de regels van de definitie van het **controle** beleid worden gedefinieerd.

[!code-json[audit-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json "audit policy rules (JSON)")]

### <a name="deployifnotexists-definition"></a>deployIfNotExists definitie

De JSON waarin de regels van de **deployIfNotExists** -beleids definitie worden gedefinieerd.

[!code-json[deployIfNotExists-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json "deployIfNotExists policy rules (JSON)")]

De **deployIfNotExists** -beleids definitie definieert de Azure-installatie kopieën waarvoor het beleid is gevalideerd:

|Uitgever |Aanbieding |SKU |
|-|-|-|
|OpenLogic |CentOS @ no__t-0 |Alles behalve 6 @ no__t-0 |
|RedHat |RHEL |Alles behalve 6 @ no__t-0 |
|RedHat |Osa | Alle |
|credativ |Debian | Alle behalve 7 @ no__t-0 |
|SUSE |SLES @ NO__T-0 |Alle behalve 11 @ no__t-0 |
|Canonical| UbuntuServer |Alle behalve 12 @ no__t-0 |
|microsoft-dsvm |linux-data-science-vm-ubuntu |Alle |
|microsoft-dsvm |azureml |Alle |
|cloudera |cloudera-centos-os |Alles behalve 6 @ no__t-0 |
|cloudera |cloudera-altus-CentOS-OS |Alle |
|microsoft-ads |linux\* |Alle |
|microsoft-aks |Alle |Alle |
|AzureDatabricks |Alle |Alle |
|qubole-inc |Alle |Alle |
|datastax |Alle |Alle |
|Couch base |Alle |Alle |
|scalegrid |Alle |Alle |
|Points |Alle |Alle |
|paloaltonetworks |Alle |Alle |

In het gedeelte **implementatie** van de regel wordt de para meter _installedApplication_ door gegeven aan de gast configuratie agent op de virtuele machine. Met deze configuratie kan de agent de validaties uitvoeren en de naleving van het **controle** beleid weer rapporteren.

## <a name="azure-portal"></a>Azure Portal

Nadat de **controle** -en **deployIfNotExists** definities zijn gemaakt in de portal, is het raadzaam ze te groeperen in een [initiatief](../concepts/definition-structure.md#initiatives) voor toewijzing.

### <a name="create-copy-of-audit-definition"></a>Kopie maken van audit definitie

[![Deploy het voor beeld van het beleid naar azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)
[![Deploy het voor beeld van het beleid naar Azure gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)

Als u met deze knoppen via de portal implementeert, wordt er een kopie van de definitie van het **controle** beleid gemaakt.
Zonder de gekoppelde **deployIfNotExists** -beleids definitie werkt de gast configuratie niet goed.

### <a name="create-copy-of-deployifnotexists-definition"></a>Een kopie maken van de deployIfNotExists-definitie

[![Deploy het voor beeld van het beleid naar azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)
[![Deploy het voor beeld van het beleid naar Azure gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)

Als u met deze knoppen via de portal implementeert, wordt er een kopie gemaakt van de **deployIfNotExists** -beleids definitie. Zonder de definitie van het gekoppelde **controle** beleid werkt de gast configuratie niet goed.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Implementeren met Azure PowerShell

#### <a name="copy-and-assign-the-initiative"></a>Het initiatief kopiëren en toewijzen

Met deze stappen maakt u een kopie van het initiatief dat het ingebouwde beleid voor zowel **audit** als **deployIfNotExists** bevat, en wijst u het initiatief toe aan een resource groep.

```azurepowershell-interactive
# Create the policy initiative (Subscription scope)
$initDef = New-AzPolicySetDefinition -Name 'guestconfig-installed-application-linux' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This initiative will both deploy the policy requirements and audit that the specified application is installed inside Linux virtual machines.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.definitions.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the initiative parameter (JSON format)
$initParam = '{ "applicationName": { "value": "python,powershell" } }'

# Create the initiative assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicySetDefinition $initDef -PolicyParameter $initParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Voer de volgende opdrachten uit om de vorige toewijzing en definitie te verwijderen:

```azurepowershell-interactive
# Remove the initiative assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the initiative definition
Remove-AzPolicySetDefinition -Id $initDef
```

#### <a name="copy-and-assign-the-audit-definition"></a>De audit definitie kopiëren en toewijzen

Met deze stappen maakt u een kopie van de **audit** definitie en wijst u deze toe aan een resource groep. Deze definitie werkt niet correct zonder dat de gekoppelde **deployIfNotExists** -definitie ook wordt toegewezen.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-audit' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This policy audits that the specified application is installed inside Linux virtual machines. This policy should only be used along with its corresponding deploy policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-audit-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicyDefinition $definition
```

Voer de volgende opdrachten uit om de vorige toewijzing en definitie te verwijderen:

```azurepowershell-interactive
# Remove the policy definition
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

#### <a name="copy-and-assign-the-deployifnotexists-definition"></a>De deployIfNotExists-definitie kopiëren en toewijzen

Met deze stappen maakt u een kopie van de **deployIfNotExists** -definitie en wijst u deze toe aan een resource groep.
Deze definitie werkt niet correct zonder dat de definitie van de gekoppelde **controle** ook wordt toegewezen.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-deployIfNotExists' -DisplayName 'GuestConfig - Deploy VM extension to audit that an application is installed inside Linux VMs' -description 'Include this rule to deploy the VM extension for Microsoft Guest Configuration, the VM extension for Microsoft Azure Managed Service Identity, and the content required to audit that an application is installed inside Linux virtual machines. This policy should only be used along with its corresponding audit policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the definition parameter (JSON format)
$policyParam  = '{ "applicationName": { "value": "python,powershell" } }'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-deployIfNotExists-assignment' -DisplayName 'GuestConfig - Deploy VM extension to audit that Python and PowerShell are installed inside Linux VMs' -Scope $scope.ResourceID -PolicyDefinition $definition -PolicyParameter $policyParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Voer de volgende opdrachten uit om de vorige toewijzing en definitie te verwijderen:

```azurepowershell-interactive
# Remove the policy assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

### <a name="azure-powershell-explanation"></a>Toelichting van Azure PowerShell

De scripts voor implementeren en verwijderen gebruiken de volgende opdrachten. Elke opdracht in de volgende tabel is een koppeling naar opdrachtspecifieke documentatie:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzPolicySetDefinition](/powershell/module/az.resources/New-AzPolicySetDefinition) | Hiermee maakt u een Azure Policy initiatief. |
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-AzPolicyDefinition) | Hiermee maakt u een Azure Policy definitie. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup) | Hiermee vraagt u één resourcegroep op. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-AzPolicyAssignment) | Hiermee maakt u een nieuwe Azure Policy toewijzing voor een initiatief of definitie. |
| [New-AzRoleAssignment](/powershell/module/az.resources/New-AzRoleAssignment) | Geeft een bestaande roltoewijzing aan de specifieke principal. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-AzPolicyAssignment) | Hiermee verwijdert u een bestaande Azure Policy-toewijzing. |
| [Remove-AzPolicySetDefinition](/powershell/module/az.resources/Remove-AzPolicySetDefinition) | Hiermee verwijdert u een initiatief. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-AzPolicyDefinition) | Hiermee verwijdert u een definitie. |

## <a name="next-steps"></a>Volgende stappen

- Bekijk aanvullende [Azure Policy](index.md)-voor beelden.
- Meer informatie over [Azure Policy-gast configuratie](../concepts/guest-configuration.md).
- [Azure Policy definitie structuur](../concepts/definition-structure.md)bekijken.
