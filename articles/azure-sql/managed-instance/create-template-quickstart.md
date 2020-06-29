---
title: 'Azure Resource Manager: Een Azure SQL Managed Instance maken'
description: Meer informatie over het maken van een Azure SQL Managed Instance met behulp van een Azure Resource Manager-sjabloon.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/22/2020
ms.openlocfilehash: 01c6c37d31d41f88b370face372555536724adde
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85256068"
---
# <a name="quickstart-create-an-azure-sql-managed-instance-using-an-azure-resource-manager-template"></a>Quickstart: Een Azure SQL Managed Instance maken met behulp van een Azure Resource Manager-sjabloon

Deze quickstart is gericht op het implementeren van een Resource Manager-sjabloon voor het maken van een Azure SQL Managed Instance en vNet.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Als u nog geen abonnement op Azure hebt, [maak dan een gratis account](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

Geen.

## <a name="create-an-azure-sql-managed-instance"></a>Een Azure SQL Managed Instance maken

[Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) is een intelligente, volledig beheerde, schaalbare clouddatabase met bijna 100% functiepariteit met de database-engine van SQL Server.

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure Quick Start-sjablonen](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).

:::code language="json" source="~/quickstart-templates/101-sqlmi-new-vnet/azuredeploy.json":::

Deze resources worden in de sjabloon gedefinieerd:

- [**Microsoft.Sql/managedinstances**](/azure/templates/microsoft.sql/managedinstances)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.Network/virtualNetworks)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.Network/routeTables)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.Network/networkSecurityGroups)



Meer voorbeelden van sjablonen vindt u in [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>De sjabloon implementeren

Selecteer **Proberen** in het volgende PowerShell-codeblok om Azure Cloud Shell te openen.

> [!IMPORTANT]
> Het implementeren van een beheerd exemplaar is een langdurende bewerking. De implementatie van het eerste exemplaar in het subnet duurt doorgaans veel langer dan de implementatie in een subnet met bestaande beheerde exemplaren. Raadpleeg [Bewerkingen voor SQL Managed Instance-beheer](sql-managed-instance-paas-overview.md#management-operations) voor de gemiddelde inrichtingsduur.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

* * *

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Ga naar de [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) en controleer of het beheerde exemplaar zich in de geselecteerde resourcegroep bevindt. Omdat het maken van een beheerd exemplaar enige tijd kan duren, moet u mogelijk de koppeling **Implementaties** op de pagina **Overzicht** van uw resourcegroep controleren.

- Zie [Een verbinding vanaf een virtuele Azure-machine configureren](connect-vm-instance-configure.md) voor een quickstart over verbinding maken met SQL Managed Instance vanaf een virtuele Azure-machine.
- Zie [Een punt-naar-site-verbinding configureren](point-to-site-p2s-configure.md) voor een quickstart over verbinding maken met SQL Managed Instance vanaf een on-premises clientcomputer met behulp van een punt-naar-site-verbinding.

## <a name="clean-up-resources"></a>Resources opschonen

Behoud het beheerde exemplaar als u verder wilt gaan met de [volgende stappen](#next-steps), maar verwijder het beheerde exemplaar en de bijbehorende resources nadat u eventuele andere zelfstudies hebt voltooid. Zie [Een subnet verwijderen na het verwijderen van een beheerd exemplaar](virtual-cluster-delete.md) nadat u een beheerd exemplaar hebt verwijderd.


Zo verwijdert u de resourcegroep:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

* * *

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Azure virtuele machine configureren om verbinding te maken met SQL Managed Instance](connect-vm-instance-configure.md)
