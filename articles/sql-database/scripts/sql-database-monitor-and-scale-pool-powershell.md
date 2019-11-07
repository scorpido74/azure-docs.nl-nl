---
title: Power shell-voor beeld-monitor-Scale-elastische pool-Azure SQL Database
description: Microsoft Azure PowerShell-voorbeeldscript voor het bewaken en schalen van een elastische pool in Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 6ab361b67741f2b96f593d04dafbabe355fc9121
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691617"
---
# <a name="use-powershell-to-monitor-and-scale-an-elastic-pool-in-azure-sql-database"></a>Gebruik PowerShell voor het bewaking en schalen van een elastische pool in Azure SQL Database

Met dit PowerShell-script worden de prestatiemetrieken gecontroleerd van een elastische pool, waarna de database naar een grotere rekenkracht wordt geschaald en er een waarschuwingsregel voor een van de prestatiemetrieken wordt gemaakt.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om Power shell lokaal te installeren en te gebruiken, hebt u voor deze zelf studie AZ Power shell 1.4.0 of hoger nodig. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/monitor-and-scale-pool/monitor-and-scale-pool.ps1?highlight=17-18 "Monitor and scale a single SQL Database")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een SQL Database-server die een individuele database of elastische pool host. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Hiermee maakt u een elastische pool. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Maakt een individuele database of database in een elastische pool. |
| [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) | Toont de gebruikte grootte voor een database.|
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Hiermee voegt u een waarschuwingsregel op basis van metrische gegevens in, of werkt u deze bij. |
| [Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) | Hiermee werkt u eigenschappen van elastische pools bij |
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Hiermee stelt u een waarschuwingsregel in om automatisch DTU's in de toekomst te bewaken. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle ingesloten resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/azure/overview) voor meer informatie over Azure PowerShell.

Aanvullende voorbeelden van SQL Database PowerShell-scripts vindt u in [Azure SQL Database PowerShell-scripts](../sql-database-powershell-samples.md).
