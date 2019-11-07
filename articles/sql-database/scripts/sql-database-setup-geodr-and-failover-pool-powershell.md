---
title: Power shell-voor beeld-actieve geo-replicatie-gepoolde Azure-SQL database
description: Azure PowerShell-voorbeeldscript voor het instellen van actieve geo-replicatie voor een pooldatabase in Azure SQL Database en het uitvoeren van een failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 3ff5d41c8d37745cbb06dbb98b5ce6e9b6a19ed4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691481"
---
# <a name="use-powershell-to-configure-active-geo-replication-for-a-pooled-database-in-azure-sql-database"></a>PowerShell gebruiken voor het configureren van actieve geo-replicatie voor een pooldatabase in Azure SQL Database

In dit voorbeeld van een PowerShell-script configureert u actieve geo-replicatie voor een pooldatabase in Azure SQL Database en voert u een failover uit naar een secundaire replica van de database.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om Power shell lokaal te installeren en te gebruiken, hebt u voor deze zelf studie AZ Power shell 1.4.0 of hoger nodig. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="sample-scripts"></a>Voorbeeldscripts

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-elastic-pool.ps1?highlight=17-20 "Set up active geo-replication for elastic pool")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen.

```powershell
Remove-AzResourceGroup -ResourceGroupName $primaryresourcegroupname
Remove-AzResourceGroup -ResourceGroupName $secondaryresourcegroupname
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Hiermee maakt u een elastische pool. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Hiermee wordt een enkele of pooldatabase gemaakt. |
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Hiermee worden database-eigenschappen bijgewerkt of worden databasegegevens verplaatst naar, uit of tussen elastische pools. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary)| Hiermee maakt u een secundaire database voor een bestaande database en wordt gegevensreplicatie gestart. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)| Hiermee haalt u een of meer databases op. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary)| Hiermee wordt overgeschakeld op een secundaire database als primaire om de failover te initiëren.|
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) | Hiermee haalt u de geo-replicatiekoppelingen tussen een Azure SQL Database en een resourcegroep of SQL Server op. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle ingesloten resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/azure/overview) voor meer informatie over Azure PowerShell.

Aanvullende voorbeelden van SQL Database PowerShell-scripts vindt u in [Azure SQL Database PowerShell-scripts](../sql-database-powershell-samples.md).
