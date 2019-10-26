---
title: Azure PowerShell-scriptvoorbeelden voor SQL Database | Microsoft Docs
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
ms.openlocfilehash: 27b4a6f5614b79b2e9d8f3730441d702f2d7cc77
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933265"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Azure PowerShell-voorbeelden voor Azure SQL Database

Met Azure SQL Database kunt u uw databases, instanties en pools met behulp van Azure PowerShell configureren.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om Power shell lokaal te installeren en te gebruiken, hebt u voor deze zelf studie AZ Power shell 1.4.0 of hoger nodig. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="single-database-and-elastic-poolstabsingle-database"></a>[Individuele database en elastische Pools](#tab/single-database)

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
| [Een failovergroep configureren voor één data base](scripts/sql-database-add-single-db-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit Power shell-script maakt u een Data Base en een failovergroep, voegt u de data base toe aan de failovergroep en voert u een failover uit naar de secundaire server. | 
| [Een failovergroep voor een elastische pool configureren](scripts/sql-database-add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit Power shell-script maakt u een Data Base, voegt u deze toe aan een elastische pool, voegt u de elastische pool toe aan de failovergroep en voert u een failover uit naar de secundaire server. | 
|**Een individuele database en een elastische pool schalen**||
| [Een individuele database schalen](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script worden de prestatiemetrieken gecontroleerd van een Azure SQL-database, waarna de database naar een grotere rekenkracht wordt geschaald en er een waarschuwingsregel voor een van de prestatiemetrieken wordt gemaakt. |
| [Een elastische pool schalen](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script worden de prestatiemetrieken gecontroleerd van een elastische pool van Azure SQL Database, waarna de database naar een grotere rekenkracht wordt geschaald en er een waarschuwingsregel voor een van de prestatiemetrieken wordt gemaakt. |
| **Controle en bedreigingen detecteren** |
| [Controle en detectie van bedreigingen configureren](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt beleid voor controle en detectie van bedreigingen geconfigureerd voor een Azure SQL-database. |
| **Database herstellen, kopiëren en importeren**||
| [Database herstellen](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt een Azure SQL-database teruggezet vanuit een geo-redundante back-up en wordt een verwijderde Azure SQL-database naar de laatste back-up hersteld. |
| [Database kopiëren naar nieuwe server](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt een kopie van een bestaande Azure SQL-database gemaakt in een nieuwe Azure SQL-server. |
| [Database uit een BACPAC-bestand importeren](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt een database vanuit een BACPAC-bestand naar een Azure SQL-server geïmporteerd. |
| **Gegevens tussen databases synchroniseren**||
| [Gegevens tussen SQL-databases synchroniseren](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script wordt Data Sync geconfigureerd voor het synchroniseren van gegevens tussen meerdere Azure SQL-databases. |
| [Gegevens synchroniseren tussen SQL Database en on-premises SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script wordt Data Sync geconfigureerd voor het synchroniseren van gegevens tussen Azure SQL-database en een on-premises SQL Server-database. |
| [Synchronisatieschema van SQL Data Sync bijwerken](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script worden items aan het synchronisatieschema van Data Sync toegevoegd of eruit verwijderd. |
|||

Meer informatie over de [Azure PowerShell API voor individuele databases](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases).

## <a name="managed-instancetabmanaged-instance"></a>[Beheerd exemplaar](#tab/managed-instance)

De volgende tabel bevat links naar Azure PowerShell-voorbeeldscripts voor Azure SQL Database - Managed Instance.

| |  |
|---|---|
|**Beheerde exemplaren maken en configureren**||
| [Een beheerd exemplaar maken en beheren](scripts/sql-database-create-configure-managed-instance-powershell.md) | Dit PowerShell-script laat zien hoe u een beheerd exemplaar maakt en beheert met Azure PowerShell |
| [Een beheerd exemplaar maken met behulp van een Azure Resource Manager-sjabloon](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Dit PowerShell-script laat zien hoe u een beheerd exemplaar maakt en beheert met Azure PowerShell en een Azure Resource Manager-sjabloon.|
| [Data base herstellen naar een beheerd exemplaar in een andere geografische regio](scripts/sql-managed-instance-restore-geo-backup.md) | Dit Power shell-script maakt een back-up van de ene data base en zet deze terug in een andere regio. Dit wordt ook wel het scenario voor herstel na nood gevallen met geo-herstel. |
| **Transparent Data Encryption (TDE) configureren**||
| [Transparent Data Encryption beheren in een beheerd exemplaar met behulp van uw eigen sleutel uit Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Dit PowerShell-script configureert Transparant Data Encryption (TDE) in een Bring Your Own Key-scenario voor een beheerd exemplaar van Azure SQL, met behulp van een sleutel uit Azure Key Vault|
|**Een failovergroep configureren**||
| [Een failovergroep voor een beheerd exemplaar configureren](scripts/sql-database-add-managed-instance-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit Power shell-script maakt u twee beheerde instanties, voegt u deze toe aan een failovergroep en test u vervolgens de failover van het primaire beheerde exemplaar naar het secundaire beheerde exemplaar. | 
|||

Meer informatie over de [Managed Instance Azure PowerShell API](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances).

---

## <a name="additional-resources"></a>Aanvullende bronnen

De voor beelden die op deze pagina worden weer gegeven, gebruiken de [Azure SQL database-cmdlets](/powershell/module/az.sql/) voor het maken en beheren van Azure SQL-resources. Aanvullende cmdlets voor het uitvoeren van query's en het uitvoeren van veel database taken bevinden zich in de [sqlserver](/powershell/module/sqlserver/) -module. Zie [SQL Server Power shell](/sql/powershell/sql-server-powershell/)(Engelstalig) voor meer informatie.
