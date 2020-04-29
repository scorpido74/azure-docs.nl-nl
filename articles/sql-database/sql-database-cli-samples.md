---
title: Voor beelden van Azure CLI-script
description: Azure CLI-scriptvoorbeelden voor het maken en beheren van Azure SQL Database-servers, elastische pools, databases en firewalls.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 459a5ea69e11a8614c572f68fce039b6cabbb1ed
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061718"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Azure CLI-voorbeelden voor Azure SQL Database

Azure SQL Database kan worden geconfigureerd met <a href="/cli/azure">Azure CLI</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

# <a name="single-database--elastic-pools"></a>[Eén data base & elastische Pools](#tab/single-database)

| | |
|---|---|
|**Een individuele database en een elastische pool maken**||
| [Een individuele database maken en een firewallregel configureren](scripts/sql-database-create-and-configure-database-cli.md) | Hiermee maakt u een Azure-SQL database en configureert u een firewall regel op server niveau. |
| [Elastische pools maken en pooldatabases verplaatsen](scripts/sql-database-move-database-between-pools-cli.md) | Maakt elastische SQL-Pools, verplaatst gegroepeerde Azure SQL-data bases en wijzigt de berekenings grootte. |
|**Een individuele database en een elastische pool schalen**||
| [Een individuele database schalen](scripts/sql-database-monitor-and-scale-database-cli.md) | Hiermee wordt een Azure-SQL database geschaald naar een andere reken grootte na het uitvoeren van een query op de grootte-informatie voor de data base. |
| [De schaal van een elastische pool aanpassen](scripts/sql-database-scale-pool-cli.md) | Hiermee wordt een elastische SQL-pool geschaald naar een andere reken grootte. |
|**Geo-replicatie en failover configureren**||
| [Eén data base aan een failovergroep toevoegen](scripts/sql-database-add-single-db-to-failover-group-cli.md)| Hiermee maakt u een Data Base en een failovergroep, voegt u de data base aan de groep failover toe en test u de failover naar de secundaire server. |
| [Een failovergroep voor een elastische pool configureren](scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Hiermee maakt u een Data Base, voegt u deze toe aan een elastische pool, voegt u de elastische pool toe aan de failovergroep en wordt de failover naar de secundaire server getest. |
| [PowerShell gebruiken voor het configureren van actieve geo-replicatie voor één Azure SQL-database](scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Hiermee configureert u actieve geo-replicatie voor een Azure-SQL database en voert u een failover uit naar de secundaire replica. |
| [PowerShell gebruiken voor het configureren van actieve geo-replicatie voor een Azure SQL-pooldatabase](scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Hiermee configureert u actieve geo-replicatie voor een Azure-SQL database in een elastische SQL-pool en voert u de failover uit naar de secundaire replica. |
| **Controle en bedreigingen detecteren** |
| [Controle en detectie van bedreigingen configureren](scripts/sql-database-auditing-and-threat-detection-cli.md)| Hiermee configureert u het beleid voor controle en detectie van bedreigingen voor een Azure-SQL database. |
| **Een Data Base maken, herstellen, kopiëren en importeren**||
| [Back-up maken van een Data Base](scripts/sql-database-backup-database-cli.md)| Maakt een back-up van een Azure-SQL database naar een back-up van Azure Storage. |
| [Database herstellen](scripts/sql-database-restore-database-cli.md)| Hiermee wordt een Azure-SQL database teruggezet vanuit een geografisch redundante back-up en wordt een verwijderde Azure-SQL database teruggezet naar de meest recente back-up. |
| [Database kopiëren naar nieuwe server](scripts/sql-database-copy-database-to-new-server-cli.md) | Hiermee maakt u een kopie van een bestaande Azure-SQL database in een nieuwe Azure SQL-Server. |
| [Een Data Base uit een Bacpac-bestand importeren](scripts/sql-database-import-from-bacpac-cli.md)| Hiermee wordt een Data Base vanuit een *. Bacpac* -bestand naar een Azure SQL-Server geïmporteerd. |
|||

Meer informatie over de [Azure cli API voor één data base](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

# <a name="managed-instance"></a>[Beheerd exemplaar](#tab/managed-instance)

De volgende tabel bevat koppelingen naar Azure CLI-scriptvoorbeelden voor Azure SQL Database - Beheerd exemplaar.

| | |
|---|---|
| **Een beheerd exemplaar maken**||
| [Een beheerd exemplaar maken](scripts/sql-database-create-configure-managed-instance-cli.md)| Hiermee maakt u een beheerd exemplaar. |
| **Transparent Data Encryption (TDE) configureren**||
| [Transparent Data Encryption beheren in een beheerd exemplaar met behulp van Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Hiermee configureert u Transparent Data Encryption (TDE) in Azure SQL Managed instance met behulp van Azure Key Vault met verschillende belang rijke scenario's. |
|**Een failovergroep configureren**||
| [Een failovergroep voor een beheerd exemplaar configureren](scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Maakt twee beheerde instanties, voegt deze toe aan een failovergroep en test vervolgens de failover van het primaire beheerde exemplaar naar het secundaire beheerde exemplaar. |
|||

Zie voor meer voor beelden van Managed instances het item [maken](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/), [bijwerken](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/), [verplaatsen van een Data Base](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/), [werken met](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) scripts.

Meer informatie over de [Azure cli API van het beheerde exemplaar](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances).

---
