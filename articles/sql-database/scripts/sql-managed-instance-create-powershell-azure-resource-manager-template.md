---
title: 'Sjabloonvoorbeeld: een beheerd exemplaar maken in Azure SQL Database'
description: Gebruik dit Azure PowerShell-voorbeeldscript voor het maken van een beheerd exemplaar in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-dt-2019
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: eccc3dcb2731183719833ae99f475eb27e6a05c1
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774512"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>PowerShell gebruiken met een Azure Resource Manager-sjabloon om een beheerd exemplaar te maken in Azure SQL Database

Een MI kan worden gemaakt met behulp van een Azure PowerShell-bibliotheek en Azure Resource Manager-sjablonen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor deze zelfstudie de AZ PowerShell 1.4.0 of hoger vereist. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

Azure PowerShell-opdrachten kunnen een implementatie starten met behulp van een vooraf gedefinieerde Azure Resource Manager-sjabloon. De volgende eigenschappen kunnen worden opgegeven in de sjabloon:

- Exemplaarnaam
- Gebruikersnaam en wachtwoord van SQL-beheerder.
- Grootte van het exemplaar (aantal kernen en maximale opslaggrootte).
- VNet en subnet waar het exemplaar wordt geplaatst.
- Sortering op serverniveau van het exemplaar (preview).

Exemplaarnaam, gebruikersnaam van SQL-beheerder, VNet/subnet en sortering kunnen later niet worden gewijzigd. Andere exemplaareigenschappen kunnen worden gewijzigd.

## <a name="prerequisites"></a>Vereisten

In dit voorbeeld wordt ervan uitgegaan dat u[ een geldige netwerkomgeving hebt gemaakt](../sql-database-managed-instance-create-vnet-subnet.md) of [ een bestaand VNet](../sql-database-managed-instance-configure-vnet-subnet.md) hebt gewijzigd van uw beheerde exemplaar. In het voorbeeld worden de cmdlets [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) en [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) gebruikt, dus zorg ervoor dat u de volgende PowerShell-modules hebt geïnstalleerd:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

De volgende inhoud moet worden geplaatst in een bestand dat een sjabloon representeert die wordt gebruikt om het exemplaar te maken:

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

Hierbij wordt verondersteld dat Azure VNet al bestaat met het correct geconfigureerde subnet. Als u geen correct geconfigureerd subnet hebt, bereid de netwerkomgeving dan voor met behulp van een afzonderlijke [Azure Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) die onafhankelijk of vanuit deze sjabloon kan worden uitgevoerd.

Sla de inhoud van dit bestand op als een JSON-bestand, plaats het bestandspad in het volgende PowerShell-script en verander de namen van de objecten in het script:

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed Instance properties
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

# Deploy Instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

Nadat het script is uitgevoerd, is de SQL Database toegankelijk via alle Azure-services en het geconfigureerde IP-adres.

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/azure/overview) voor meer informatie over Azure PowerShell.

Aanvullende voorbeelden van SQL Database PowerShell-scripts vindt u in [Azure SQL Database PowerShell-scripts](../sql-database-powershell-samples.md).
