---
title: Power shell-voor beeld geo-backup-Azure SQL Database herstellen
description: Azure PowerShell voorbeeld script om een Azure SQL Managed instance-data base te herstellen vanuit een geografisch redundante back-up.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 07/03/2019
ms.openlocfilehash: 9de8819e82db12cb5625a449e4ea5bf2f028a44d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691412"
---
# <a name="use-powershell-to-restore-a-managed-instance-database-to-another-geo-region"></a>Power shell gebruiken om een Data Base van een beheerd exemplaar te herstellen naar een andere geo-regio

In dit voor beeld van een Power shell-script wordt een Data Base van Azure SQL Managed instance teruggezet vanuit een externe geografische regio (geo-Restore).  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u Power shell lokaal wilt installeren en gebruiken, is voor deze zelf studie AZ Power shell 1.4.0 of later vereist. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

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
-TargetInstanceDatabaseName $TargetInstanceName `
-TargetInstanceName $TargetDatabaseName `
-TargetResourceGroupName $TargetResourceGroupName

```

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen.

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName $TargetResourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/New-AzResourceGroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [Get-AzSqlInstanceDatabaseGeoBackup](/powershell/module/az.sql/Get-AzSqlInstanceDatabaseGeoBackup) | Hiermee maakt u een geografisch redundante back-up van de data base van het beheerde exemplaar. |
| [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/Restore-AzSqlInstanceDatabase) | Hiermee maakt u een Data Base op een beheerd exemplaar van geo-back-up. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle ingesloten resources. |

## <a name="next-steps"></a>Volgende stappen

Zie [Azure PowerShell-documentatie](/powershell/azure/overview)voor meer informatie over Power shell.

Aanvullende voorbeelden van SQL Database PowerShell-scripts vindt u in [Azure SQL Database PowerShell-scripts](../sql-database-powershell-samples.md).
