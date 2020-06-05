---
title: 'PowerShell-voorbeeld: een beheerd exemplaar maken in Azure SQL Database'
description: Azure PowerShell-voorbeeldscript voor het maken van een beheerd exemplaar in Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: e8df39b5bd6c2948fa0f4392b7417a9ffb10a03f
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772553"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>PowerShell gebruiken om een door Azure SQL Database beheerd exemplaar te maken

In dit voorbeeld van PowerShell-script wordt een Azure SQL Database beheerd exemplaar gemaakt in een toegewezen subnet binnen een nieuw virtueel netwerk. Er wordt ook een routetabel en een netwerkbeveiligingsgroep voor het virtuele netwerk geconfigureerd. Zodra het script is uitgevoerd, kunt u toegang krijgen tot het beheerde exemplaar vanuit het virtuele netwerk of vanuit een on-premises omgeving. Zie [Azure VM configureren om verbinding te maken met een Azure SQL Database beheerd exemplaar](../sql-database-managed-instance-configure-vm.md) en [On-premises een punt-naar-site-verbinding met een Azure SQL Database beheerd exemplaar configureren](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Zie [ondersteunde regio's](../sql-database-managed-instance-resource-limits.md#supported-regions) en [ondersteunde abonnementstypen](../sql-database-managed-instance-resource-limits.md#supported-subscription-types) voor beperkingen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor deze zelfstudie de AZ PowerShell 1.4.0 of hoger vereist. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden enkele van de volgende opdrachten gebruikt. Voor meer informatie over de gebruikte en andere opdrachten in de onderstaande tabel klikt u op de koppelingen naar documentatie over een specifieke opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u een virtueel netwerk |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Hiermee voegt u een virtueel netwerk/subnet toe |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Hiermee stelt u de doelstatus voor een virtueel netwerk in |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Hiermee haalt u een subnet in een virtueel netwerk op |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Hiermee configureert u de doelstatus voor een subnetconfiguratie in een virtueel netwerk |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Hiermee maakt u een routetabel |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Hiermee haalt u routetabellen op |
| [Get-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Hiermee stelt u de doelstatus voor een routetabel in |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Hiermee maakt u een beheerd Azure SQL Database-exemplaar |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/azure/overview) voor meer informatie over Azure PowerShell.

Aanvullende voorbeelden van SQL Database PowerShell-scripts vindt u in [Azure SQL Database PowerShell-scripts](../sql-database-powershell-samples.md).
