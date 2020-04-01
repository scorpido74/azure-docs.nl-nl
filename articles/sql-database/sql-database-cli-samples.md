---
title: Voorbeelden van Azure CLI-script
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061718"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Azure CLI-voorbeelden voor Azure SQL Database

Azure SQL Database kan worden geconfigureerd met <a href="/cli/azure">Azure CLI</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

# <a name="single-database--elastic-pools"></a>[Eén database & elastische pools](#tab/single-database)

| | |
|---|---|
|**Een individuele database en een elastische pool maken**||
| [Een individuele database maken en een firewallregel configureren](scripts/sql-database-create-and-configure-database-cli.md) | Hiermee maakt u een Azure SQL-database en configureert u een firewallregel op serverniveau. |
| [Elastische pools maken en pooldatabases verplaatsen](scripts/sql-database-move-database-between-pools-cli.md) | Hiermee maakt u SQL-elastische groepen, worden gepoolde Azure SQL-databases verplaatst en wijzigingen gewijzigd. |
|**Een individuele database en een elastische pool schalen**||
| [Een individuele database schalen](scripts/sql-database-monitor-and-scale-database-cli.md) | Hiermee schaalt u een Azure SQL-database op naar een andere rekengrootte nadat de groottegegevens voor de database zijn opgevraagd. |
| [De schaal van een elastische pool aanpassen](scripts/sql-database-scale-pool-cli.md) | Hiermee schaalt u een SQL-elastische pool naar een andere rekengrootte. |
|**Geo-replicatie en failover configureren**||
| [Eén database toevoegen aan failovergroep](scripts/sql-database-add-single-db-to-failover-group-cli.md)| Hiermee maakt u een database en een failovergroep, voegt u de database toe aan de failovergroep en test vervolgens failover naar de secundaire server. |
| [Een failovergroep configureren voor een elastische groep](scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Hiermee maakt u een database, voegt u deze toe aan een elastische groep, voegt u de elastische groep toe aan de failovergroep en test vervolgens failover naar de secundaire server. |
| [PowerShell gebruiken voor het configureren van actieve geo-replicatie voor één Azure SQL-database](scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Hiermee configureert u actieve georeplicatie voor een Azure SQL-database en wordt deze niet overgezet naar de secundaire replica. |
| [PowerShell gebruiken voor het configureren van actieve geo-replicatie voor een Azure SQL-pooldatabase](scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Hiermee configureert u actieve georeplicatie voor een Azure SQL-database in een SQL-elastische groep en mislukt deze vervolgens naar de secundaire replica. |
| **Controle en bedreigingen detecteren** |
| [Controle en detectie van bedreigingen configureren](scripts/sql-database-auditing-and-threat-detection-cli.md)| Hiermee configureert u het beleid voor controle- en bedreigingsdetectie voor een Azure SQL-database. |
| **Een database back-upt- of back-ups maken, kopiëren en importeren**||
| [Een back-up van een database maken](scripts/sql-database-backup-database-cli.md)| Maak een back-up van een Azure SQL-database naar een Azure-opslagback-up. |
| [Database herstellen](scripts/sql-database-restore-database-cli.md)| Hiermee herstelt u een Azure SQL-database van een georedundante back-up en herstelt u een verwijderde Azure SQL-database naar de nieuwste back-up. |
| [Database kopiëren naar nieuwe server](scripts/sql-database-copy-database-to-new-server-cli.md) | Hiermee maakt u een kopie van een bestaande Azure SQL-database in een nieuwe Azure SQL-server. |
| [Database uit een BACPAC-bestand importeren](scripts/sql-database-import-from-bacpac-cli.md)| Hiermee importeert u een database naar een Azure SQL-server vanuit een *bacpac-bestand.* |
|||

Meer informatie over de [Azure CLI API met één database](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

# <a name="managed-instance"></a>[Beheerde instantie](#tab/managed-instance)

De volgende tabel bevat koppelingen naar Azure CLI-scriptvoorbeelden voor Azure SQL Database - Beheerd exemplaar.

| | |
|---|---|
| **Een beheerd exemplaar maken**||
| [Een beheerde instantie maken](scripts/sql-database-create-configure-managed-instance-cli.md)| Hiermee maakt u een beheerde instantie. |
| **Transparent Data Encryption (TDE) configureren**||
| [Transparante gegevensversleuteling beheren in een beheerde instantie met Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Hiermee configureert U Transparante gegevensversleuteling (TDE) in Azure SQL Managed Instance met Azure Key Vault met verschillende belangrijke scenario's. |
|**Een failovergroep configureren**||
| [Een failovergroep configureren voor een beheerde instantie](scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Hiermee maakt u twee beheerde exemplaren, voegt u deze toe aan een failovergroep en test vervolgens failover van de primaire beheerde instantie naar de secundaire beheerde instantie. |
|||

Zie voor aanvullende voorbeelden van beheerde instanties het [maken,](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/) [bijwerken](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/), [verplaatsen van een database](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/), het werken [met](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) scripts.

Meer informatie over de [beheerde instantie Azure CLI API](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances).

---
