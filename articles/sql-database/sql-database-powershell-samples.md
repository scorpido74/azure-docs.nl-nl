---
title: Azure PowerShell-voorbeeldscripts
description: Azure PowerShell-scriptvoorbeelden waarmee u Azure SQL Database-servers, elastische pools, databases en firewalls kunt maken en beheren.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 630769cb9b73a9cbec09085384557bb57ae9c116
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848988"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Azure PowerShell-voorbeelden voor Azure SQL Database

Met Azure SQL Database kunt u uw databases, instanties en pools met behulp van Azure PowerShell configureren.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor deze zelfstudie de AZ PowerShell 1.4.0 of hoger vereist. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="single-database-and-elastic-pools"></a>[Individuele database en elastische pools](#tab/single-database)

De volgende tabel bevat koppelingen naar Azure PowerShell-voorbeeldscripts voor Azure SQL Database.

| |  |
|---|---|
|**Individuele databases en elastische pools maken en configureren**||
| [Een enkele database maken en een firewallregel voor de databaseserver configureren](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script wordt één Azure SQL-database gemaakt en een regel voor een firewall op serverniveau geconfigureerd. |
| [Elastische pools maken en pooldatabases verplaatsen](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script worden elastische pools van Azure SQL Database gemaakt, pooldatabases verplaatst en rekenkracht gewijzigd.|
|**Geo-replicatie en failover configureren**||
| [PowerShell gebruiken voor het configureren van actieve geo-replicatie voor één Azure SQL-database](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt actieve geo-replicatie geconfigureerd voor één Azure SQL-database en wordt overgeschakeld naar de secundaire replica. |
| [PowerShell gebruiken voor het configureren van actieve geo-replicatie voor een Azure SQL-pooldatabase](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt actieve geo-replicatie geconfigureerd voor een Azure SQL-database in een elastische SQL-pool en wordt overgeschakeld naar de secundaire replica. |
|**Een failovergroep configureren**||
| [Een failovergroep configureren voor een individuele database](scripts/sql-database-add-single-db-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script maakt u een database en een failovergroep, voegt u de database toe aan de failovergroep en test u een failover naar de secundaire server. | 
| [Een failovergroep voor een elastische pool configureren](scripts/sql-database-add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script maakt u een database, voegt u deze toe aan een elastische pool, voegt u de elastische pool toe aan de failovergroep en test u een failover naar de secundaire server. | 
|**Een individuele database en een elastische pool schalen**||
| [Een individuele database schalen](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script worden de prestatiemetrieken gecontroleerd van een Azure SQL-database, waarna de database naar een grotere rekenkracht wordt geschaald en er een waarschuwingsregel voor een van de prestatiemetrieken wordt gemaakt. |
| [Een elastische pool schalen](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script worden de prestatiemetrieken gecontroleerd van een elastische pool van Azure SQL Database, waarna de database naar een grotere rekenkracht wordt geschaald en er een waarschuwingsregel voor een van de prestatiemetrieken wordt gemaakt. |
| **Controle en bedreigingen detecteren** |
| [Controle en detectie van bedreigingen configureren](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt beleid voor controle en detectie van bedreigingen geconfigureerd voor een Azure SQL-database. |
| **Database herstellen, kopiëren en importeren**||
| [Database herstellen](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt een Azure SQL-database teruggezet vanuit een geo-redundante back-up en wordt een verwijderde Azure SQL-database naar de laatste back-up hersteld. |
| [Een database kopiëren naar een nieuwe server](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt een kopie van een bestaande Azure SQL-database gemaakt in een nieuwe Azure SQL-server. |
| [Database uit een BACPAC-bestand importeren](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt een database vanuit een BACPAC-bestand naar een Azure SQL-server geïmporteerd. |
| **Gegevens tussen databases synchroniseren**||
| [Gegevens tussen SQL-databases synchroniseren](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script wordt Data Sync geconfigureerd voor het synchroniseren van gegevens tussen meerdere Azure SQL-databases. |
| [Gegevens synchroniseren tussen SQL Database en on-premises SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script wordt Data Sync geconfigureerd voor het synchroniseren van gegevens tussen Azure SQL-database en een on-premises SQL Server-database. |
| [Synchronisatieschema van SQL Data Sync bijwerken](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script worden items aan het synchronisatieschema van Data Sync toegevoegd of eruit verwijderd. |
|||

Meer informatie over de [Azure PowerShell API voor individuele databases](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>[Beheerd exemplaar](#tab/managed-instance)

De volgende tabel bevat links naar Azure PowerShell-voorbeeldscripts voor Azure SQL Database - Managed Instance.

| |  |
|---|---|
|**Beheerde exemplaren maken en configureren**||
| [Een beheerd exemplaar maken en beheren](scripts/sql-database-create-configure-managed-instance-powershell.md) | Dit PowerShell-script laat zien hoe u een beheerd exemplaar maakt en beheert met Azure PowerShell |
| [Een beheerd exemplaar maken met behulp van een Azure Resource Manager-sjabloon](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Dit PowerShell-script laat zien hoe u een beheerd exemplaar maakt en beheert met Azure PowerShell en een Azure Resource Manager-sjabloon.|
| [Een database herstellen naar een beheerd exemplaar in een ander geografisch gebied](scripts/sql-managed-instance-restore-geo-backup.md) | Met dit PowerShell-script wordt een back-up van een database en teruggezet in een andere regio. Dit staat bekend als herstel na noodgevallen met geo-herstel. |
| **Transparent Data Encryption (TDE) configureren**||
| [Transparent Data Encryption beheren in een beheerd exemplaar met behulp van uw eigen sleutel uit Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Dit PowerShell-script configureert Transparant Data Encryption (TDE) in een Bring Your Own Key-scenario voor een beheerd exemplaar van Azure SQL, met behulp van een sleutel uit Azure Key Vault|
|**Een failovergroep configureren**||
| [Een failovergroep configureren voor een beheerd exemplaar](scripts/sql-database-add-managed-instance-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Dit PowerShell-script maakt twee beheerde exemplaren, voegt ze toe aan een failovergroep, en test vervolgens failover van het primaire beheerde exemplaar naar het secundaire beheerde exemplaar. | 
|||

Meer informatie over de [Managed Instance Azure PowerShell API](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances).

---

## <a name="additional-resources"></a>Aanvullende bronnen

In de voorbeelden die op deze pagina worden weergegeven, worden de [Azure SQL Database-cmdlets](/powershell/module/az.sql/) gebruikt voor het maken en beheren van Azure SQL-resources. Aanvullende cmdlets voor het uitvoeren van query's en het uitvoeren van veel databasetaken bevinden zich in de module [sqlserver](/powershell/module/sqlserver/). Zie [SQL Server PowerShell](/sql/powershell/sql-server-powershell/)voor meer informatie.
