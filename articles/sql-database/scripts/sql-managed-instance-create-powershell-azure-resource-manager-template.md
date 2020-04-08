---
title: Voorbeeld van sjabloon - een beheerde instantie maken in Azure SQL Database
description: Gebruik dit Azure PowerShell-voorbeeldscript om een beheerde instantie in Azure SQL Database te maken.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: seo-dt-2019
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: e5be8c9441be5ca441a5c0f7c4444c2edbdc7a95
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811215"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>PowerShell gebruiken met Azure Resource Manager-sjabloon om een beheerde instantie in Azure SQL Database te maken

Een MI kan worden gemaakt met behulp van een Azure PowerShell-bibliotheek en Azure Resource Manager-sjablonen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de PowerShell lokaal te installeren en te gebruiken, vereist deze zelfstudie AZ PowerShell 1.4.0 of hoger. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

Azure PowerShell-opdrachten kunnen een implementatie starten met behulp van een vooraf gedefinieerde Azure Resource Manager-sjabloon. De volgende eigenschappen kunnen worden opgegeven in de sjabloon:

- Exemplaarnaam
- Gebruikersnaam en wachtwoord van SQL-beheerder.
- Grootte van het exemplaar (aantal kernen en maximale opslaggrootte).
- VNet en subnet waar het exemplaar wordt geplaatst.
- Sortering op serverniveau van het exemplaar (preview).

Exemplaarnaam, gebruikersnaam van SQL-beheerder, VNet/subnet en sortering kunnen later niet worden gewijzigd. Andere exemplaareigenschappen kunnen worden gewijzigd.

## <a name="prerequisites"></a>Vereisten

In dit voorbeeld wordt ervan uitgegaan dat u[ een geldige netwerkomgeving hebt gemaakt](../sql-database-managed-instance-create-vnet-subnet.md) of [ een bestaand VNet](../sql-database-managed-instance-configure-vnet-subnet.md) hebt gewijzigd van uw beheerde exemplaar. Het voorbeeld maakt gebruik van de cmdlets [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) en [Get-AzVirtualNetwork,](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) dus zorg ervoor dat u de volgende PowerShell-modules hebt geïnstalleerd:

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

Hierbij wordt verondersteld dat Azure VNet al bestaat met het correct geconfigureerde subnet. Als u geen goed geconfigureerd subnet hebt, bereidt u de netwerkomgeving voor met behulp van afzonderlijke [Azure Resource Managed-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) die onafhankelijk van elkaar kan worden uitgevoerd of in deze sjabloon kan worden opgenomen.

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
