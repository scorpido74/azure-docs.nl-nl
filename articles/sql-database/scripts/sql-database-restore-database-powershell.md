---
title: 'Power shell-voor beeld: Restore-Backup-Azure SQL database'
description: Azure PowerShell voorbeeld script om een Azure SQL-Data Base te herstellen naar een eerder tijdstip vanuit automatische back-ups
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/27/2019
ms.openlocfilehash: da4236e138bd75237ca10b85dc1586fecd1cece4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691550"
---
# <a name="use-powershell-to-restore-an-azure-sql-single-database-to-an-earlier-point-in-time"></a>Power shell gebruiken om één Azure SQL-Data Base te herstellen naar een eerder tijdstip

In dit voor beeld van een Power shell-script wordt een Azure-SQL database teruggezet naar een bepaald tijdstip.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om Power shell lokaal te installeren en te gebruiken, hebt u voor deze zelf studie AZ Power shell 1.4.0 of hoger nodig. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een SQL Database-server die een individuele database of elastische pool host. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Hiermee maakt u een database op een SQL Database-server als een zelfstandige of een pooldatabase. |
[Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) | Hiermee wordt een geografisch redundante back-up van een zelfstandige of pooldatabase opgehaald. |
| [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) | Hiermee wordt een zelfstandige of pooldatabase hersteld. |
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) | Hiermee wordt een zelfstandige of Azure SQL-pooldatabase verwijderd. |
| [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Hiermee haalt u een verwijderde zelfstandige of pooldatabase op die u kunt herstellen. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle ingesloten resources. |

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/azure/overview) voor meer informatie over Azure PowerShell.

Aanvullende voorbeelden van SQL Database PowerShell-scripts vindt u in [Azure SQL Database PowerShell-scripts](../sql-database-powershell-samples.md).
