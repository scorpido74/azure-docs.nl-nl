---
title: Power shell-voor beeld-Failovergroep-Azure SQL Database afzonderlijke data base
description: Azure PowerShell voorbeeld script om een Azure SQL Database afzonderlijke Data Base te maken, deze toe te voegen aan een failovergroep en failover te testen.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 7315f747fb7319521b25e2eb6e7af72449867888
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691783"
---
# <a name="use-powershell-to-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Power shell gebruiken om een Azure SQL Database afzonderlijke data base toe te voegen aan een failovergroep 

In dit voor beeld van een Power shell-script maakt u één data base, maakt u een failovergroep, voegt u de data base toe en wordt de failover getest. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om Power shell lokaal te installeren en te gebruiken, hebt u voor deze zelf studie AZ Power shell 1.4.0 of hoger nodig. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="sample-scripts"></a>Voorbeeldscripts

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewall regel voor een logische server. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Hiermee maakt u een nieuwe Azure SQL Database afzonderlijke data base. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Hiermee maakt u een nieuwe failovergroep. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Hiermee worden een of meer SQL-data bases opgehaald. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Hiermee voegt u een of meer Azure SQL-data bases toe aan een failovergroep. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee worden Azure SQL Database failover-groepen opgehaald of weer gegeven. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Hiermee wordt een failover uitgevoerd van een Azure SQL Database failovergroep. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resource groep | 

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/azure/overview) voor meer informatie over Azure PowerShell.

Aanvullende voorbeelden van SQL Database PowerShell-scripts vindt u in [Azure SQL Database PowerShell-scripts](../sql-database-powershell-samples.md).
