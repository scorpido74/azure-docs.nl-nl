---
title: 'PowerShell: Geo-back-up voor met Azure SQL beheerd exemplaar herstellen'
description: Azure PowerShell-voorbeeldscript voor het terugzetten van een met Azure SQL beheerd database-exemplaar uit een geografisch redundante back-up.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 07/03/2019
ms.openlocfilehash: 0c6ca03f535201059f107d6b53bc423b92d1c1c1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073360"
---
# <a name="use-powershell-to-restore-an-azure-sql-managed-instance-database-to-another-geo-region"></a>Gebruik PowerShell om een met Azure SQL beheerd-exemplaardatabase te herstellen naar een ander geografisch gebied

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Met dit PowerShell-voorbeeldscript wordt een met Azure SQL Database beheerd exemplaar vanuit een afgelegen geografisch gebied teruggezet (geo-restore).  

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor deze zelfstudie Azure PowerShell 1.4.0 of hoger vereist. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="sample-script"></a>Voorbeeldscript

```azurepowershell-interactive
# Connect-AzAccount
# The SubscriptionId in which to create these objects
$SubscriptionId = '<put subscription_id here>'
# Set the information for your managed instance
$SourceResourceGroupName = "myResourceGroup-$(Get-Random)"
$SourceInstanceName = "myManagedInstance-$(Get-Random)"
$SourceDatabaseName = "myInstanceDatabase-$(Get-Random)"

# Set the information for your destination managed instance
$TargetResourceGroupName = "myTargetResourceGroup-$(Get-Random)"
$TargetInstanceName = "myTargetManagedInstance-$(Get-Random)"
$TargetDatabaseName = "myTargetInstanceDatabase-$(Get-Random)"

Connect-AzAccount
Set-AzContext -SubscriptionId $SubscriptionId

$backup = Get-AzSqlInstanceDatabaseGeoBackup `
-ResourceGroupName $SourceResourceGroupName `
-InstanceName $SourceInstanceName `
-Name $SourceDatabaseName

$backup | Restore-AzSqlInstanceDatabase -FromGeoBackup `
-TargetInstanceDatabaseName $TargetDatabaseName `
-TargetInstanceName $TargetInstanceName `
-TargetResourceGroupName $TargetResourceGroupName

```

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die eraan zijn gekoppeld te verwijderen.

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName $TargetResourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/New-AzResourceGroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [Get-AzSqlInstanceDatabaseGeoBackup](/powershell/module/az.sql/Get-AzSqlInstanceDatabaseGeoBackup) | Hiermee maakt u een geografisch redundante back-up van de met SQL beheerd database-exemplaar. |
| [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/Restore-AzSqlInstanceDatabase) | Hiermee maakt u een database op een met SQL beheerd exemplaar vanuit een geo-back-up. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep, met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie over Azure PowerShell](/powershell/azure/) voor meer informatie over PowerShell.

Aanvullende voorbeelden van PowerShell-scripts voor Azure SQL Database vindt u in [Azure SQL Database PowerShell-scripts](../../database/powershell-script-content-guide.md).
