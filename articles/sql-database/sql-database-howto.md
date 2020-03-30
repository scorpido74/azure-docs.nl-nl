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
ms.date: 11/14/2019
ms.openlocfilehash: c3f7b33e4b42b08334cfb687024985c878dc3713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209422"
---
# <a name="how-to-use-azure-sql-database"></a>Azure SQL-database gebruiken

In deze sectie vindt u verschillende hulplijnen, scripts en uitleg waarmee u uw Azure SQL Database beheren en configureren. U ook specifieke handleidingen voor [afzonderlijke database](sql-database-howto-single-database.md) en [beheerde instantie](sql-database-howto-managed-instance.md)vinden.

## <a name="load-data"></a>Gegevens laden

- [Eén database of samengevoegde database binnen Azure kopiëren](sql-database-copy.md)
- [Een DB uit een BACPAC importeren](sql-database-import.md)
- [Een DB naar een BACPAC exporteren](sql-database-export.md)
- [Gegevens laden met BCP](sql-database-load-from-csv-with-bcp.md)
- [Gegevens laden met ADF](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Gegevenssynchronisatie

- [SQL Data Sync](sql-database-sync-data.md)
- [Data Sync-agent](sql-database-data-sync-agent.md)
- [Schemawijzigingen repliceren](sql-database-update-sync-schema.md)
- [Bewaken met OMS](sql-database-sync-monitor-oms.md)
- [Best practices voor Data Sync](sql-database-best-practices-data-sync.md)
- [Problemen met Data Sync oplossen](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>Bewaking en afstemming

- [Handmatig instellen](sql-database-performance-guidance.md)
- [DMV's gebruiken voor het bewaken van prestaties](sql-database-monitoring-with-dmvs.md)
- [Query Store gebruiken voor het bewaken van prestaties](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Prestatieproblemen oplossen met Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Diagnostisch logboek van Intelligent Insights gebruiken](sql-database-intelligent-insights-use-diagnostics-log.md)
- [OLTP-ruimte in het geheugen bewaken](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>Uitgebreide gebeurtenissen

- [Uitgebreide gebeurtenissen](sql-database-xevent-db-diff-from-svr.md)
- [Uitgebreide gebeurtenissen opslaan in gebeurtenisbestand](sql-database-xevent-code-event-file.md)
- [Uitgebreide gebeurtenissen opslaan in ringbuffer](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>Functies configureren

- [Azure AD-verificatie configureren](sql-database-aad-authentication-configure.md)
- [Voorwaardelijke toegang configureren](sql-database-conditional-access.md)
- [Multi-Factor AAD-verificatie](sql-database-ssms-mfa-authentication.md)
- [Multi-Factor Authentication instellen](sql-database-ssms-mfa-authentication-configure.md)
- [Tijdelijk bewaarbeleid configureren](sql-database-temporal-tables-retention-policy.md)
- [TDE met BYOK (Bring Your Own Key) configureren](transparent-data-encryption-byok-azure-sql-configure.md)
- [TDE BYOK-sleutels roteren](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [TDE-beveiliging verwijderen](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [OLTP in het geheugen configureren](sql-database-in-memory-oltp-migration.md)
- [Azure-automatisering configureren](sql-database-manage-automation.md)

## <a name="develop-applications"></a>Toepassingen ontwikkelen

- [Connectiviteit](sql-database-libraries.md)
- [Spark-connector gebruiken](sql-database-spark-connector.md)
- [App verifiëren](sql-database-client-id-keys.md)
- [Batching gebruiken voor betere prestaties](sql-database-use-batching-to-improve-performance.md)
- [Connectiviteitsrichtlijnen](sql-database-connectivity-issues.md)
- [DNS-aliassen](dns-alias-overview.md)
- [DNS-alias PowerShell instellen](dns-alias-powershell.md)
- [Poorten - ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C en C ++](sql-database-develop-cplusplus-simple.md)
- [Excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>Toepassingen ontwerpen

- [Ontwerpen voor herstel na noodgeval](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Ontwerpen voor elastische pools](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Ontwerpen voor app-upgrades](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Ontwerp Multi-tenant SaaS-toepassingen

- [SaaS-ontwerppatronen](saas-tenancy-app-design-patterns.md)
- [Indexeerfunctie voor SaaS-video](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Beveiliging van SaaS-app](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [handleidingen voor beheerde instanties](sql-database-howto-managed-instance.md).
- Meer informatie over [handleidingen voor afzonderlijke databases](sql-database-howto-single-database.md).
