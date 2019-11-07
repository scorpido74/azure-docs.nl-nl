---
title: Azure SQL Database configureren-enkel
description: Meer informatie over het configureren en beheren van Azure SQL Database-één data base
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 02/08/2019
ms.openlocfilehash: 45e25641092fae36cd58a59400cea025f74cbc1f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689898"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Een enkele data base gebruiken in Azure SQL Database

In deze sectie vindt u verschillende hand leidingen, scripts en uitleg die u kunnen helpen bij het beheren en configureren van uw afzonderlijke data base in Azure SQL Database

## <a name="migrate"></a>Migreren

- [Migreren naar SQL database](sql-database-single-database-migrate.md) : meer informatie over het aanbevolen migratie proces en hulpprogram ma's voor migratie naar een beheerd exemplaar.
- Meer informatie over het [beheren van SQL database na de migratie](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Functies configureren

- [Transactionele replicatie configureren](replication-to-sql-database.md) om de datum te repliceren tussen data bases.
- [Configureer detectie van bedreigingen](sql-database-threat-detection.md) zodat Azure SQL database verdachte activiteiten zoals SQL-injectie of toegang vanaf verdachte locaties kunt identificeren.
- [Configureer dynamische gegevens maskering](sql-database-dynamic-data-masking-get-started-portal.md) om uw gevoelige gegevens te beveiligen.
- Configureer het bewaren van [back-ups](sql-database-long-term-backup-retention-configure.md) voor een Data Base om uw back-ups op Azure Blob Storage te bewaren. Als alternatief kunt u de [retentie van de back-up configureren met behulp van de methode van Azure-kluis (afgeschaft)](sql-database-long-term-backup-retention-configure-vault.md) .
- [Configureer geo-replicatie](sql-database-geo-replication-portal.md) om een replica van uw data base in een andere regio te blijven gebruiken.
- [Configureer beveiliging voor geo-replica's](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Uw data base bewaken en afstemmen

- [Schakel automatisch afstemmen](sql-database-automatic-tuning-enable.md) in om de prestaties van uw werk belasting Azure SQL database optimaliseren.
- [Schakel e-mail meldingen voor automatisch afstemmen](sql-database-automatic-tuning-email-notifications.md) in om informatie te krijgen over de aanbevelingen voor het afstemmen.
- [Aanbevelingen voor prestaties Toep assen](sql-database-advisor-portal.md) en uw data base optimaliseren.
- [Waarschuwingen maken](sql-database-insights-alerts-portal.md) voor het ontvangen van meldingen van Azure SQL database.
- [Los problemen met de verbinding](sql-database-troubleshoot-common-connection-issues.md) op als u enkele verbindings problemen tussen de toepassingen en de data base ziet. U kunt ook [resource Health gebruiken om verbindings problemen op te lossen](sql-database-resource-health.md).
- [Bestands ruimte beheren](sql-database-file-space-management.md) om het gebruik van opslag in uw data base te controleren.

## <a name="query-distributed-data"></a>Een query uitvoeren op gedistribueerde gegevens

- [Query's verticaal gepartitioneerde gegevens](sql-database-elastic-query-getting-started-vertical.md) over meerdere data bases.
- [Rapport over een uitgeschaalde gegevenslaag](sql-database-elastic-query-horizontal-partitioning.md).
- [Query's uitvoeren voor meerdere tabellen met verschillende schema's](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Taken voor Elastic Database

- [Maken en beheren](elastic-jobs-powershell.md) Elastic Database taken met behulp van Power shell.
- [Maken en beheren](elastic-jobs-tsql.md) Elastic Database taken met behulp van Transact-SQL.
- [Migreren vanaf een oude elastische taak](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Database-sharding

- [Upgrade van client bibliotheek voor Elastic data base](sql-database-elastic-scale-upgrade-client-library.md).
- [Shard-app maken](sql-database-elastic-scale-get-started.md).
- Een [query uitvoeren op horizontale Shard-gegevens](sql-database-elastic-query-getting-started.md).
- [Query's voor meerdere Shard](sql-database-elastic-scale-multishard-querying.md)uitvoeren.
- [Shard-gegevens verplaatsen](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Configureer beveiliging](sql-database-elastic-scale-split-merge-security-configuration.md) in data base Shards.
- [Voeg een Shard](sql-database-elastic-scale-add-a-shard.md) toe aan de huidige set data base-Shards.
- [Problemen met de Shard-kaart oplossen](sql-database-elastic-database-recovery-manager.md).
- [SHARD DB migreren](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Tellers maken](sql-database-elastic-database-perf-counters.md).
- [Gebruik Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) om Shard-gegevens op te vragen.
- [Gebruik dapper Framework](sql-database-elastic-scale-working-with-dapper.md) om Shard-gegevens op te vragen.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [hand leidingen voor een beheerd exemplaar](sql-database-howto-managed-instance.md)
