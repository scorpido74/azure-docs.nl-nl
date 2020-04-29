---
title: 'Power shell-voor beeld: een beheerd exemplaar maken in Azure SQL Database'
description: Azure PowerShell voorbeeld script voor het maken van een beheerd exemplaar in Azure SQL Database
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73691640"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>Power shell gebruiken voor het maken van een Azure SQL Database beheerd exemplaar

Met dit Power shell-voorbeeld script wordt een Azure SQL Database beheerd exemplaar gemaakt in een toegewezen subnet binnen een nieuw virtueel netwerk. Er worden ook een route tabel en een netwerk beveiligings groep voor het virtuele netwerk geconfigureerd. Zodra het script is uitgevoerd, kunt u toegang krijgen tot het beheerde exemplaar vanuit het virtuele netwerk of vanuit een on-premises omgeving. Zie [Configure Azure VM to Connect to a Azure SQL database Managed instance](../sql-database-managed-instance-configure-vm.md) (Engelstalig) en [Configureer een punt-naar-site-verbinding met een Azure SQL database beheerde instantie van on-premises](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Zie [ondersteunde regio's](../sql-database-managed-instance-resource-limits.md#supported-regions) en [ondersteunde abonnements typen](../sql-database-managed-instance-resource-limits.md#supported-subscription-types)voor beperkingen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om Power shell lokaal te installeren en te gebruiken, hebt u voor deze zelf studie AZ Power shell 1.4.0 of hoger nodig. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u een virtueel netwerk |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Hiermee voegt u een subnet configuratie toe aan een virtueel netwerk |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Hiermee wordt een virtueel netwerk in een resource groep opgehaald |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Hiermee stelt u de doel status voor een virtueel netwerk in |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Hiermee wordt een subnet in een virtueel netwerk opgehaald |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Hiermee configureert u de doel status voor een subnet-configuratie in een virtueel netwerk |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Hiermee maakt u een route tabel |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Hiermee worden route tabellen opgehaald |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Hiermee stelt u de doel status voor een route tabel in |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Hiermee maakt u een Azure SQL Database beheerd exemplaar |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/azure/overview) voor meer informatie over Azure PowerShell.

Aanvullende voorbeelden van SQL Database PowerShell-scripts vindt u in [Azure SQL Database PowerShell-scripts](../sql-database-powershell-samples.md).
