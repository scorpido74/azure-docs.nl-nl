---
title: Beleid voor niet-compatibele resources maken met Azure PowerShell
description: Gebruik Azure PowerShell om een Azure Policy-toewijzing te maken om niet-conforme resources te identificeren.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/11/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 5962db75fdd640822feb06ddb93bf7d4f2acd4d5
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232317"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Quickstart: Met behulp van Azure PowerShell een beleidstoewijzing maken om niet-conforme resources te identificeren

De eerste stap in het begrijpen van naleving in Azure is het identificeren van de status van uw resources. In deze snelstart maakt u een beleidstoewijzing om te identificeren welke virtuele machines geen beheerde schijven gebruiken. Wanneer u klaar bent, identificeert u virtuele machines die *niet voldoen*aan het beleid.

De module Azure PowerShell wordt gebruikt om Azure-resources te beheren vanaf de opdracht regel of in scripts.
In deze hand leiding wordt uitgelegd hoe u AZ module gebruikt om een beleids toewijzing te maken.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Voordat u begint, moet u ervoor zorgen dat de nieuwste versie van Azure PowerShell is geïnstalleerd. Zie [Azure PowerShell-module installeren](/powershell/azure/install-az-ps) voor gedetailleerde informatie.
- Registreer de Azure Policy Insights-resource provider met behulp van Azure PowerShell. Als u de resourceprovider registreert, controleer dan of uw abonnement ermee werkt. Als u een resourceprovider wilt registreren, moet u toestemming hebben om de bewerking van de resourceprovider te registeren. Deze bewerking is opgenomen in de rollen Inzender en Eigenaar. Voer de volgende opdracht uit om de resourceprovider te registreren:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Zie [Resourceproviders en -typen](../../azure-resource-manager/resource-manager-supported-services.md) voor meer informatie over het registreren en weergeven van resourceproviders.

## <a name="create-a-policy-assignment"></a>Een beleidstoewijzing maken

In deze Quick Start maakt u een beleids toewijzing voor de definitie *virtuele machines controleren zonder Managed disks* . Met deze beleids definitie worden virtuele machines geïdentificeerd die geen beheerde schijven gebruiken.

Voer de volgende opdrachten uit om een nieuwe beleidstoewijzing te maken:

```azurepowershell-interactive
# Get a reference to the resource group that will be the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition that will be assigned
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

In de voorgaande opdrachten wordt de volgende informatie gebruikt:

- **Naam**: de werkelijke naam van de toewijzing. Voor dit voorbeeld is *audit-vm-manageddisks* gebruikt.
- **Weergavenaam**: de weergavenaam voor de beleidstoewijzing. In dit geval gebruikt u de toewijzing *Virtuele machines zonder beheerde schijven controleren*.
- **Definitie**: de beleidsdefinitie, op basis waarvan u de toewijzing maakt. In dit geval is het de id van de beleidsdefinitie *Virtuele machines zonder beheerde schijven controleren*.
- **Bereik**: een bereik bepaalt voor welke resources of groep resources de beleidstoewijzing wordt afgedwongen. Dit kan variëren van een abonnement tot resourcegroepen. Vergeet niet om &lt;scope&gt; te vervangen door de naam van uw resourcegroep.

U bent er nu klaar voor om niet-compatibele resources te identificeren om inzicht te krijgen in de nalevingsstatus van uw omgeving.

## <a name="identify-non-compliant-resources"></a>Niet-compatibele resources identificeren

Gebruik de volgende informatie om te identificeren welke resources niet compatibel zijn met de beleidstoewijzing die u hebt gemaakt. Voer de volgende opdrachten uit:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

Zie [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState)voor meer informatie over het ophalen van de beleids status.

De resultaten zien er ongeveer als volgt uit:

```output
Timestamp                   : 3/9/19 9:21:29 PM
ResourceId                  : /subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmId}
PolicyAssignmentId          : /subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/audit-vm-manageddisks
PolicyDefinitionId          : /providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d
IsCompliant                 : False
SubscriptionId              : {subscriptionId}
ResourceType                : /Microsoft.Compute/virtualMachines
ResourceTags                : tbd
PolicyAssignmentName        : audit-vm-manageddisks
PolicyAssignmentOwner       : tbd
PolicyAssignmentScope       : /subscriptions/{subscriptionId}
PolicyDefinitionName        : 06a78e20-9358-41c9-923c-fb736d382a4d
PolicyDefinitionAction      : audit
PolicyDefinitionCategory    : Compute
ManagementGroupIds          : {managementGroupId}
```

De resultaten komen overeen met wat u ziet in het tabblad **resource naleving** van een beleids toewijzing in de weer gave Azure Portal.

## <a name="clean-up-resources"></a>Resources opschonen

Voer de volgende opdracht uit om de beleidstoewijzing te verwijderen:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een beleidsdefinitie toegewezen om niet-compatibele resources in uw Azure-omgeving te identificeren.

Ga voor meer informatie over het toewijzen van beleid om te controleren of nieuwe resources conform zijn verder met de zelfstudie voor:

> [!div class="nextstepaction"]
> [Beleid maken en beheren](./tutorials/create-and-manage.md)