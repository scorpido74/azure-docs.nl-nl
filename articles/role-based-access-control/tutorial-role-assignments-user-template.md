---
title: 'Zelfstudie: Gebruikers toegang verlenen tot Azure-bronnen met RBAC- en Resource Manager-sjabloon'
description: Lees in deze zelfstudie hoe u een gebruiker toegang verleent tot Azure-bronnen met behulp van RBAC (Role-based Access Control) met behulp van de sjabloon Azure Resource Manager.
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: 96ca4f65d2def5f5004388c533410f09cc2a71fa
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77138202"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Zelfstudie: Een gebruiker toegang verlenen tot Azure-bronnen met de sjabloon RBAC en Resource Manager

[Op rollen gebaseerd toegangsbeheer (RBAC)](overview.md) is de manier waarop u de toegang tot Azure-resources beheert. In deze zelfstudie maakt u een resourcegroep en verleent u een gebruiker toegang tot het maken en beheren van virtuele machines in de resourcegroep. Deze zelfstudie richt zich op het proces van het implementeren van een Resource Manager-sjabloon om de toegang te verlenen. Zie [Resourcemanager-documentatie](/azure/azure-resource-manager/) en de [sjabloonverwijzing](/azure/templates/microsoft.authorization/allversions
)voor meer informatie over het ontwikkelen van Resource Manager-sjablonen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Toegang voor een gebruiker toewijzen op het niveau van een resourcegroep
> * De implementatie valideren
> * Opruimen

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u roltoewijzingen wilt toevoegen en verwijderen, moet u het als:

* `Microsoft.Authorization/roleAssignments/write`en `Microsoft.Authorization/roleAssignments/delete` machtigingen, zoals [beheerder van gebruikerstoegang](built-in-roles.md#user-access-administrator) of [eigenaar](built-in-roles.md#owner)

## <a name="grant-access"></a>Toegang verlenen

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure quickstart-sjablonen.](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/) Meer Azure-autorisatiegerelateerde sjablonen vindt u [hier.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization)

Als u de sjabloon wilt implementeren, selecteert **u Probeer deze** om de Azure Cloud-shell te openen en plakt u het volgende PowerShell-script in het shell-venster. Als u de code wilt plakken, klikt u met de rechtermuisknop op het shell-venster en selecteert u **Plakken**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$emailAddress = Read-Host -Prompt "Enter your email address that is associated with your Azure subscription (used to find the principal ID)"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

$resourceGroupName = "${projectName}rg"
$roleAssignmentName = New-Guid
$principalId = (Get-AzAdUser -Mail $emailAddress).id
$roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
```

## <a name="validate-the-deployment"></a>De implementatie valideren

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Open de resourcegroep die in de laatste procedure is gemaakt. De standaardnaam is de projectnaam **met rg** toegevoegd.
1. Selecteer **Toegangsbeheer (IAM)** in het menu links.
1. Selecteer **Roltoewijzingen**. 
1. Voer in **Naam**het e-mailadres in dat u in de laatste procedure hebt getypt. U ziet dat de gebruiker met het e-mailadres de rol **Virtuele machinebijdrager** heeft.

## <a name="clean-up"></a>Opruimen

Als u de brongroep wilt verwijderen die in de laatste procedure is gemaakt, selecteert **u Proberen** om de Azure Cloud-shell te openen en plakt u het volgende PowerShell-script in het shell-venster.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Een gebruiker toegang verlenen tot Azure-bronnen met RBAC en Azure PowerShell](tutorial-role-assignments-user-powershell.md)