---
title: Azure PowerShell-voorbeeldscripts
description: Azure PowerShell-voorbeeldscript voor het maken en beheren van resources voor Azure SQL Database en door Azure SQL beheerde exemplaren
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 93fac2e3ae8d83f0fa49f94a3ba7880a2ecc2b4c
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84193889"
---
# <a name="azure-powershell-samples-for-azure-sql-database-and-azure-sql-managed-instance"></a>Azure PowerShell-voorbeelden voor Azure SQL Database en door Azure SQL beheerde exemplaren
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Met Azure SQL Database en door Azure SQL beheerde exemplaren kunt u uw databases, instanties en pools met behulp van Azure PowerShell configureren.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor deze zelfstudie de AZ PowerShell 1.4.0 of hoger vereist. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

De volgende tabel bevat koppelingen naar Azure PowerShell-voorbeeldscripts voor Azure SQL Database.

| |  |
|---|---|
|**Individuele databases en elastische pools maken en configureren**||
| [Een individuele database maken en een firewallregel op serverniveau configureren](scripts/create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script wordt een individuele database gemaakt en een IP-firewallregel op serverniveau geconfigureerd. |
| [Elastische pools maken en pooldatabases verplaatsen](scripts/move-database-between-elastic-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script worden elastische pools gemaakt, pooldatabases verplaatst en rekenkracht gewijzigd.|
|**Geo-replicatie en failover configureren**||
| [PowerShell gebruiken voor het configureren van actieve geo-replicatie voor één Azure SQL-database](scripts/setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt actieve geo-replicatie geconfigureerd voor een individuele database en wordt overgeschakeld naar de secundaire replica. |
| [PowerShell gebruiken voor het configureren van actieve geo-replicatie voor een Azure SQL-pooldatabase](scripts/setup-geodr-and-failover-elastic-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt actieve geo-replicatie geconfigureerd voor een database in een elastische pool en wordt overgeschakeld naar de secundaire replica. |
|**Een failovergroep configureren**||
| [Een failovergroep configureren voor een individuele database](scripts/add-database-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script maakt u een database en een failovergroep, voegt u de database toe aan de failovergroep en test u een failover naar de secundaire server. |
| [Een failovergroep voor een elastische pool configureren](scripts/add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script maakt u een database, voegt u deze toe aan een elastische pool, voegt u de elastische pool toe aan de failovergroep en test u een failover naar de secundaire server. |
|**Een individuele database en een elastische pool schalen**||
| [Een individuele database schalen](scripts/monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script worden de prestatiemetrieken gecontroleerd van een individuele database, waarna de database naar een grotere rekenkracht wordt geschaald en er een waarschuwingsregel voor een van de prestatiemetrieken wordt gemaakt. |
| [Een elastische pool schalen](scripts/monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script worden de prestatiemetrieken gecontroleerd van een elastische pool, waarna de database naar een grotere rekenkracht wordt geschaald en er een waarschuwingsregel voor een van de prestatiemetrieken wordt gemaakt. |
| **Controle en bedreigingen detecteren** |
| [Controle en detectie van bedreigingen configureren](scripts/auditing-threat-detection-powershell-configure.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt beleid voor controle en detectie van bedreigingen geconfigureerd voor een database. |
| **Database herstellen, kopiëren en importeren**||
| [Database herstellen](scripts/restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt een database teruggezet vanuit een geo-redundante back-up en wordt een verwijderde database naar de laatste back-up hersteld. |
| [Database kopiëren naar nieuwe server](scripts/copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt een kopie van een bestaande database gemaakt in een nieuwe server. |
| [Database uit een BACPAC-bestand importeren](scripts/import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt een database vanuit een bacpac-bestand naar Azure SQL Database geïmporteerd. |
| **Gegevens tussen databases synchroniseren**||
| [Gegevens tussen SQL-databases synchroniseren](scripts/sql-data-sync-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script wordt Data Sync geconfigureerd voor het synchroniseren van gegevens tussen meerdere databases in Azure SQL Database. |
| [Gegevens synchroniseren tussen SQL Database en SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script wordt Data Sync geconfigureerd voor het synchroniseren van gegevens tussen een database in Azure SQL Database en een SQL Server-database. |
| [Synchronisatieschema van SQL Data Sync bijwerken](scripts/update-sync-schema-in-sync-group.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script worden items aan het synchronisatieschema van Data Sync toegevoegd of eruit verwijderd. |
|||

Meer informatie over de [Azure PowerShell API voor individuele databases](single-database-manage.md#powershell). 

## <a name="azure-sql-managed-instance"></a>[Azure SQL Managed Instance](#tab/managed-instance)

De volgende tabel bevat links naar Azure PowerShell-voorbeeldscripts voor Azure SQL - Beheerde exemplaren

| |  |
|---|---|
|**Beheerde exemplaren maken en configureren**||
| [Een beheerd exemplaar maken en beheren](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | Dit PowerShell-script laat zien hoe u een beheerd exemplaar maakt en beheert met Azure PowerShell |
| [Een beheerd exemplaar maken met behulp van een Azure Resource Manager-sjabloon](../managed-instance/scripts/create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Dit PowerShell-script laat zien hoe u een beheerd exemplaar maakt en beheert met Azure PowerShell en een Azure Resource Manager-sjabloon.|
| [Een database herstellen naar een beheerd exemplaar in een ander geografisch gebied](../managed-instance/scripts/restore-geo-backup.md) | Met dit PowerShell-script wordt een back-up van een database en teruggezet in een andere regio. Dit staat bekend als herstel na noodgevallen met geo-herstel. |
| **Transparent Data Encryption (TDE) configureren**||
| [Transparent Data Encryption beheren in een beheerd exemplaar met behulp van uw eigen sleutel uit Azure Key Vault](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Dit PowerShell-script configureert Transparant Data Encryption (TDE) in een Bring Your Own Key-scenario voor een beheerd exemplaar van Azure SQL, met behulp van een sleutel uit Azure Key Vault|
|**Een failovergroep configureren**||
| [Een failovergroep configureren voor een beheerd exemplaar](../managed-instance/scripts/add-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Dit PowerShell-script maakt twee beheerde exemplaren, voegt ze toe aan een failovergroep, en test vervolgens failover van het primaire beheerde exemplaar naar het secundaire beheerde exemplaar. |
|||

Meer informatie over [Power shell-cmdlets voor beheerde exemplaren van Azure SQL](../managed-instance/api-references-create-manage-instance.md#powershell-create-and-manage-managed-instances).

---

## <a name="additional-resources"></a>Aanvullende bronnen

In de voorbeelden die op deze pagina worden weergegeven, worden de [PowerShell-cmdlets](/powershell/module/az.sql/) gebruikt voor het maken en beheren van Azure SQL-resources. Aanvullende cmdlets voor het uitvoeren van query's en het uitvoeren van veel databasetaken bevinden zich in de module [sqlserver](/powershell/module/sqlserver/). Zie [SQL Server PowerShell](/sql/powershell/sql-server-powershell/)voor meer informatie.
