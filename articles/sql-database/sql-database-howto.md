---
title: Configureren en beheren
description: Meer informatie over het configureren en beheren van Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 5773ea37585bb5c63736aaa4432243dc1d41f7bc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811181"
---
# <a name="how-to-use-azure-sql-database"></a>Azure SQL Database gebruiken

In deze sectie vindt u verschillende hand leidingen, scripts en uitleg die u kunnen helpen bij het beheren en configureren van uw Azure SQL Database. U kunt ook specifieke hand leidingen vinden voor [één data base](sql-database-howto-single-database.md) en een [beheerd exemplaar](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Gegevens laden

- [Eén data base of gegroepeerde Data base in azure kopiëren](sql-database-copy.md)
- [Een Data Base importeren vanuit een BACPAC](sql-database-import.md)
- [Een Data Base exporteren naar BACPAC](sql-database-export.md)
- [Gegevens laden met BCP](sql-database-load-from-csv-with-bcp.md)
- [Gegevens laden met ADF](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Gegevenssynchronisatie

- [SQL Data Sync](sql-database-sync-data.md)
- [Data Sync-agent](sql-database-data-sync-agent.md)
- [Schema wijzigingen repliceren](sql-database-update-sync-schema.md)
- [Controleren met OMS](sql-database-sync-monitor-oms.md)
- [Aanbevolen procedures voor gegevens synchronisatie](sql-database-best-practices-data-sync.md)
- [Problemen met gegevens synchronisatie oplossen](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>Bewaking en afstemming

- [Hand matige afstemming](sql-database-performance-guidance.md)
- [Dmv's gebruiken om prestaties te bewaken](sql-database-monitoring-with-dmvs.md)
- [Query opslag gebruiken om de prestaties te bewaken](sql-database-operate-query-store.md)
- [Prestaties oplossen met Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Intelligent Insights diagnostische logboek gebruiken](sql-database-intelligent-insights-use-diagnostics-log.md)
- [OLTP-ruimte in het geheugen bewaken](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>Uitgebreide gebeurtenissen

- [Uitgebreide gebeurtenissen](sql-database-xevent-db-diff-from-svr.md)
- [Uitgebreide gebeurtenissen opslaan in gebeurtenis bestand](sql-database-xevent-code-event-file.md)
- [Uitgebreide gebeurtenissen opslaan in de ring buffer](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>Functies configureren

- [Azure AD-verificatie configureren](sql-database-aad-authentication-configure.md)
- [Voorwaardelijke toegang configureren](sql-database-conditional-access.md)
- [Multi-factor AAD-verificatie](sql-database-ssms-mfa-authentication.md)
- [Multi-factor Authentication configureren](sql-database-ssms-mfa-authentication-configure.md)
- [Tijdelijke Bewaar beleid configureren](sql-database-temporal-tables-retention-policy.md)
- [TDE configureren met BYOK](transparent-data-encryption-byok-azure-sql-configure.md)
- [TDE BYOK-sleutels draaien](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [TDE-beveiliging verwijderen](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [OLTP in het geheugen configureren](sql-database-in-memory-oltp-migration.md)
- [Azure Automation configureren](sql-database-manage-automation.md)

## <a name="develop-applications"></a>Toepassingen ontwikkelen

- [Connectiviteit](sql-database-libraries.md)
- [Spark-connector gebruiken](sql-database-spark-connector.md)
- [App verifiëren](sql-database-client-id-keys.md)
- [Foutberichten](sql-database-develop-error-messages.md)
- [Batch verwerking gebruiken voor betere prestaties](sql-database-use-batching-to-improve-performance.md)
- [Connectiviteitsrichtlijnen](sql-database-connectivity-issues.md)
- [DNS-aliassen](dns-alias-overview.md)
- [DNS-alias instellen Power shell](dns-alias-powershell.md)
- [Poorten - ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C en C ++](sql-database-develop-cplusplus-simple.md)
- [Excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>Toepassingen ontwerpen

- [Ontwerpen voor herstel na noodgeval](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Ontwerpen voor elastische Pools](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Ontwerpen voor app-upgrades](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Multi tenant SaaS-toepassingen ontwerpen

- [SaaS-ontwerp patronen](saas-tenancy-app-design-patterns.md)
- [SaaS-video indexer](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS-app-beveiliging](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [hand leidingen voor beheerde exemplaren](sql-database-howto-managed-instance.md).
- Meer informatie over [hand leidingen voor afzonderlijke data bases](sql-database-howto-single-database.md).
