---
title: Eén database configureren
description: Meer informatie over het configureren en beheren van Azure SQL Database - één database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 01/14/2020
ms.openlocfilehash: 4283e1a2c92b7fe738fb57a8103cea1deb0015a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76027715"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Eén database gebruiken in Azure SQL Database

In deze sectie vindt u verschillende hulplijnen, scripts en uitleg waarmee u uw enkele database beheren en configureren in Azure SQL Database

## <a name="migrate"></a>Migreren

- [Migratie naar SQL Database](sql-database-single-database-migrate.md) : meer informatie over het aanbevolen migratieproces en de hulpprogramma's voor migratie naar een beheerde instantie.
- Meer informatie over het [beheren van SQL-database na migratie](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Functies configureren

- [Transactionele replicatie configureren](replication-to-sql-database.md) om uw datum tussen databases te repliceren.
- [Configureer bedreigingsdetectie](sql-database-threat-detection.md) om Azure SQL Database verdachte activiteiten zoals SQL Injection of toegang vanaf verdachte locaties te laten identificeren.
- [Configureer dynamische gegevensmaskering](sql-database-dynamic-data-masking-get-started-portal.md) om uw gevoelige gegevens te beschermen.
- [Configureer back-upretentie](sql-database-long-term-backup-retention-configure.md) voor een database om uw back-ups in Azure Blob Storage te bewaren. Als alternatief is er [Back-upbehoud configureren met azure vault (afgeschaft)](sql-database-long-term-backup-retention-configure-vault.md) benadering.
- [Configureer georeplicatie](sql-database-geo-replication-portal.md) om een replica van uw database in een andere regio te bewaren.
- [Beveiligingsconfigureren voor georeplica's](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Uw database controleren en afstemmen

- [Automatische afstemming inschakelen](sql-database-automatic-tuning-enable.md) zodat Azure SQL Database de prestaties van uw werkbelasting optimaliseren.
- [Schakel e-mailmeldingen in voor automatisch afstemmen](sql-database-automatic-tuning-email-notifications.md) om informatie te krijgen over tuningaanbevelingen.
- [Pas prestatieaanbevelingen toe](sql-database-advisor-portal.md) en optimaliseer uw database.
- [Maak meldingen](sql-database-insights-alerts-portal.md) aan om meldingen van Azure SQL Database te ontvangen.
- [Problemen met de verbinding oplossen](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md) als u bepaalde verbindingsproblemen tussen de toepassingen en de database opmerkt. U resourcestatus ook gebruiken [voor verbindingsproblemen.](sql-database-resource-health.md)
- [Beheer bestandsruimte](sql-database-file-space-management.md) om het opslaggebruik in uw database te controleren.

## <a name="query-distributed-data"></a>Een query uitvoeren op gedistribueerde gegevens

- [Query verticaal gepartitioneerde gegevens](sql-database-elastic-query-getting-started-vertical.md) in meerdere databases.
- [Rapport over de geschaalde gegevenslaag](sql-database-elastic-query-horizontal-partitioning.md).
- [Query's in tabellen met verschillende schema's](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Taken voor Elastic Database

- [Maken en beheren](elastic-jobs-powershell.md) Elastische databasetaken met PowerShell.
- [Maken en beheren](elastic-jobs-tsql.md) Elastische databasetaken met Transact-SQL.
- [Migreren van oude elastische taak](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Database-sharding

- [Upgrade de elastische databaseclientbibliotheek](sql-database-elastic-scale-upgrade-client-library.md).
- [Sharded app maken](sql-database-elastic-scale-get-started.md).
- [Query horizontaal geshard gegevens](sql-database-elastic-query-getting-started.md).
- [Multishardquery's](sql-database-elastic-scale-multishard-querying.md)uitvoeren.
- [Geshard gegevens verplaatsen](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Configureer beveiliging](sql-database-elastic-scale-split-merge-security-configuration.md) in databaseshards.
- [Voeg een scherf toe](sql-database-elastic-scale-add-a-shard.md) aan de huidige set databasescherven.
- [Fix shard map problems](sql-database-elastic-database-recovery-manager.md).
- [Geshard DB migreren](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Tellers maken](sql-database-elastic-database-perf-counters.md).
- [Gebruik entiteitskader](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) om geshardgegevens op te vragen.
- [Gebruik Dapper framework](sql-database-elastic-scale-working-with-dapper.md) om geshard gegevens op te vragen.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [handleidingen voor beheerde bijvoorbeeld](sql-database-howto-managed-instance.md)
