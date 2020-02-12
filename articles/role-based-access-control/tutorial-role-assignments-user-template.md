---
title: 'Zelf studie: gebruikers toegang verlenen tot Azure-resources met RBAC en Resource Manager-sjabloon'
description: Meer informatie over hoe u een gebruiker toegang verleent tot Azure-resources met behulp van het op rollen gebaseerde toegangs beheer (RBAC) met behulp van Azure Resource Manager sjabloon in deze zelf studie.
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
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138202"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Zelf studie: een gebruiker toegang verlenen tot Azure-resources met behulp van RBAC en Resource Manager-sjabloon

[Op rollen gebaseerd toegangsbeheer (RBAC)](overview.md) is de manier waarop u de toegang tot Azure-resources beheert. In deze zelf studie maakt u een resource groep en verleent u een gebruiker toegang tot het maken en beheren van virtuele machines in de resource groep. In deze zelf studie wordt gekeken naar het proces van het implementeren van een resource manager-sjabloon om de toegang te verlenen. Zie de [documentatie van Resource Manager](/azure/azure-resource-manager/) en de [sjabloon verwijzing](/azure/templates/microsoft.authorization/allversions
)voor meer informatie over het ontwikkelen van Resource Manager-sjablonen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Toegang voor een gebruiker toewijzen op het niveau van een resourcegroep
> * De implementatie valideren
> * Opruimen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u roltoewijzingen wilt toevoegen en verwijderen, hebt u het volgende nodig:

* `Microsoft.Authorization/roleAssignments/write`-en `Microsoft.Authorization/roleAssignments/delete` machtigingen, zoals beheerder of [eigenaar](built-in-roles.md#owner) van [gebruikers toegang](built-in-roles.md#user-access-administrator)

## <a name="grant-access"></a>Toegang verlenen

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/)start-sjablonen. Meer met Azure-autorisatie gerelateerde sjablonen vindt u [hier](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

Als u de sjabloon wilt implementeren, selecteert u **proberen** om de Azure Cloud shell te openen en plakt u het volgende Power shell-script in het shell-venster. Als u de code wilt plakken, klikt u met de rechter muisknop op het shell venster en selecteert u vervolgens **Plakken**.

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

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Open de resource groep die u in de laatste procedure hebt gemaakt. De standaard naam is de naam van het project waaraan **RG** is toegevoegd.
1. Selecteer **Toegangsbeheer (IAM)** in het menu links.
1. Selecteer **Roltoewijzingen**. 
1. Voer bij **naam**het e-mail adres in dat u in de laatste procedure hebt getypt. U ziet dat de gebruiker met het e-mail adres de rol **Inzender voor virtuele machines** heeft.

## <a name="clean-up"></a>Opruimen

Als u de resource groep die u in de laatste procedure hebt gemaakt, wilt verwijderen, selecteert u **proberen** om de Azure Cloud shell te openen en plakt u het volgende Power shell-script in het shell-venster.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: een gebruiker toegang verlenen tot Azure-resources met RBAC en Azure PowerShell](tutorial-role-assignments-user-powershell.md)