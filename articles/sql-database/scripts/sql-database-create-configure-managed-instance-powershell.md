---
title: PowerShell-voorbeeld - een beheerde instantie maken in Azure SQL Database
description: Azure PowerShell-voorbeeldscript om een beheerde instantie in Azure SQL-database te maken
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 3e72a2f6754ad8e9c5bcfabe7eeee299468fa8f3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691640"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>PowerShell gebruiken om een door Azure SQL Database beheerde instantie te maken

Met dit PowerShell-scriptvoorbeeld wordt een Azure SQL Database-exemplaar gemaakt in een speciaal subnet binnen een nieuw virtueel netwerk. Het configureert ook een routetabel en een netwerkbeveiligingsgroep voor het virtuele netwerk. Zodra het script is uitgevoerd, kan de beheerde instantie worden geopend vanuit het virtuele netwerk of vanuit een on-premises omgeving. Zie [Azure VM configureren om verbinding te maken met een Azure SQL Database Managed Instance](../sql-database-managed-instance-configure-vm.md) en [Configureer een point-to-site verbinding met een Azure SQL Database Managed Instance vanuit on-premises](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Zie ondersteunde [regio's](../sql-database-managed-instance-resource-limits.md#supported-regions) en [ondersteunde abonnementstypen](../sql-database-managed-instance-resource-limits.md#supported-subscription-types)voor beperkingen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de PowerShell lokaal te installeren en te gebruiken, vereist deze zelfstudie AZ PowerShell 1.4.0 of hoger. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle bijbehorende resources te verwijderen.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Een virtueel netwerk maken |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Voegt een subnetconfiguratie toe aan een virtueel netwerk |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Krijgt een virtueel netwerk in een resourcegroep |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Stelt de doelstatus in voor een virtueel netwerk |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Krijgt een subnet in een virtueel netwerk |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Configureert de doelstatus voor een subnetconfiguratie in een virtueel netwerk |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Een routetabel maken |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Krijgt routetabellen |
| [Set-azroutetabel](/powershell/module/az.network/Set-AzRouteTable) | Stelt de doelstatus in voor een routetabel |
| [Nieuw-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Hiermee maakt u een azure SQL Database-beheerde instantie |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/azure/overview) voor meer informatie over Azure PowerShell.

Aanvullende voorbeelden van SQL Database PowerShell-scripts vindt u in [Azure SQL Database PowerShell-scripts](../sql-database-powershell-samples.md).
