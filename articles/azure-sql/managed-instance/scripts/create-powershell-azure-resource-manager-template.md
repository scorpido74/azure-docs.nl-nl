---
title: Een beheerd exemplaar maken (ARM-sjabloon en PowerShell)
titleSuffix: Azure SQL Managed Instance
description: Gebruik dit Azure PowerShell-voorbeeldscript voor het maken van een beheerd exemplaar.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-dt-2019
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 8ac742c42a32e8b864c4be71e786c292ceb1514d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073386"
---
# <a name="use-powershell-with-an-azure-resource-manager-template-to-create-a-managed-instance"></a>PowerShell gebruiken met een Azure Resource Manager-sjabloon om een beheerd exemplaar te maken

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

U kunt een beheerd exemplaar maken met behulp van de Azure PowerShell-bibliotheek en Azure Resource Manager-sjablonen.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor deze zelfstudie Azure PowerShell 1.4.0 of hoger vereist. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, voer dan `Connect-AzAccount` uit om verbinding te maken met Azure.

Azure PowerShell-opdrachten kunnen een implementatie starten met behulp van een vooraf gedefinieerde Azure Resource Manager-sjabloon. De volgende eigenschappen kunnen worden opgegeven in de sjabloon:

- Naam van het beheerde exemplaar
- Gebruikersnaam en wachtwoord van SQL-beheerder.
- Grootte van het exemplaar (aantal kernen en maximale opslaggrootte).
- VNet en subnet waar het exemplaar wordt geplaatst.
- Sortering op serverniveau van het exemplaar (preview).

Exemplaarnaam, gebruikersnaam van SQL-beheerder, VNet/subnet en sortering kunnen later niet worden gewijzigd. Andere exemplaareigenschappen kunnen worden gewijzigd.

## <a name="prerequisites"></a>Vereisten

In dit voorbeeld wordt ervan uitgegaan dat u[ een geldige netwerkomgeving hebt gemaakt](../virtual-network-subnet-create-arm-template.md) of [ een bestaand VNet](../vnet-existing-add-subnet.md) hebt gewijzigd voor uw beheerde exemplaar. U kunt, indien nodig, de netwerkomgeving voorbereiden met een afzonderlijke [Azure Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment). 


In het voorbeeld worden de cmdlets [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) en [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) gebruikt, dus zorg ervoor dat u de volgende PowerShell-modules hebt geïnstalleerd:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon


Sla het volgende script op in een .JSON-bestand en noteer de bestandslocatie: 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen5",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

Werk het volgende PowerShell-script bij met het juiste bestandspad voor het JSON-bestand dat u eerder hebt opgeslagen en verander de namen van de objecten in het script:

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

Nadat het script is uitgevoerd, is het beheerd exemplaar toegankelijk via alle Azure-services en het geconfigureerde IP-adres.

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/azure/) voor meer informatie over Azure PowerShell.

Extra voorbeelden van PowerShell-scripts voor een met Azure SQL beheerd exemplaar kunt u vinden in [Azure SQL Managed Instance PowerShell Scripts](../../database/powershell-script-content-guide.md).
