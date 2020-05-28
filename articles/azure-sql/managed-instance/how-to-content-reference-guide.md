---
title: Inhouds referentie configureren & beheren
titleSuffix: Azure SQL Managed Instance
description: Een naslag handleiding voor inhoud die u leert hoe u uw door Azure SQL beheerde instantie kunt configureren en beheren.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: bc0c30e234310a4ee1d013d7a11ca556edd071c6
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041114"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Naslag informatie voor Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In dit artikel vindt u een inhouds verwijzing naar verschillende hand leidingen, scripts en uitleg die u helpen bij het beheren en configureren van uw Azure SQL Managed instance.

## <a name="load-data"></a>Gegevens laden

- [Migreren naar een beheerd exemplaar van Azure SQL](migrate-to-instance-from-sql-server.md) : meer informatie over het aanbevolen migratie proces en hulpprogram ma's voor migratie naar een door Azure SQL beheerd exemplaar.
- [TDe-certificaat migreren naar een beheerd exemplaar van Azure SQL](tde-certificate-migrate.md) : als uw SQL Server-Data Base wordt beveiligd met transparent Data Encryption (TDE), moet u het certificaat migreren dat door een door SQL beheerd exemplaar kan worden gebruikt voor het ontsleutelen van de back-up die u wilt herstellen in Azure.
- [Een DB uit een BACPAC importeren](../database/database-import.md)
- [Een DB naar een BACPAC exporteren](../database/database-export.md)
- [Gegevens laden met BCP](../load-from-csv-with-bcp.md)
- [Gegevens laden met ADF](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>Netwerkconfiguratie

- De grootte van het [subnet bepalen](vnet-subnet-determine-size.md) Omdat de grootte van het subnet niet kan worden gewijzigd nadat het beheerde exemplaar van SQL is geïmplementeerd, moet u berekenen welk IP-adres bereik is vereist voor het aantal en de typen SQL-beheerde exemplaren die u wilt implementeren in het subnet. 
- [Nieuw VNet en subnet maken](virtual-network-subnet-create-arm-template.md) Configureer het virtuele netwerk en subnet volgens de [hier beschreven netwerk vereisten](connectivity-architecture-overview.md#network-requirements) 
- [Bestaand VNet en subnet configureren](vnet-existing-add-subnet.md) Controleer de netwerk vereisten en configureer uw bestaande virtuele netwerk en subnet om het SQL-beheerde exemplaar te implementeren. 
- [Aangepaste DNS configureren](custom-dns-configure.md) Aangepaste DNS configureren om toegang van externe bronnen te verlenen aan aangepaste domeinen van uw SQL Managed instance via een gekoppelde server van DB-mail profielen. 
- [Netwerk configuratie synchroniseren](azure-app-sync-network-configuration.md) Het netwerk configuratie plan vernieuwen als u geen verbinding kunt maken nadat u [uw app hebt geïntegreerd met een Azure-Virtual Network](../../app-service/web-sites-integrate-with-vnet.md)
- [IP-adres van beheer eindpunt zoeken](management-endpoint-find-ip-address.md) Het open bare eind punt bepalen dat door SQL beheerd exemplaar wordt gebruikt voor beheer doeleinden. 
- [Ingebouwde firewall beveiliging controleren](management-endpoint-verify-built-in-firewall.md) Controleer of het SQL Managed instance verkeer alleen toestaat op de benodigde poorten en andere ingebouwde firewall regels. 
- [Toepassingen verbinden](connect-application-instance.md) Meer informatie over verschillende patronen voor het koppelen van de toepassingen aan uw door SQL beheerde instantie.

## <a name="feature-configuration"></a>Configuratie van onderdelen

- [Azure AD-verificatie configureren](../database/authentication-aad-configure.md)
- [Voorwaardelijke toegang configureren](../database/conditional-access-configure.md)
- [Multi-Factor AAD-verificatie](../database/authentication-mfa-ssms-overview.md)
- [Multi-Factor Authentication instellen](../database/authentication-mfa-ssms-configure.md)
- [Tijdelijk bewaarbeleid configureren](../database/temporal-tables-retention-policy.md)
- [TDE met BYOK (Bring Your Own Key) configureren](../database/transparent-data-encryption-byok-configure.md)
- [TDE BYOK-sleutels roteren](../database/transparent-data-encryption-byok-key-rotation.md)
- [TDE-beveiliging verwijderen](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [OLTP in het geheugen configureren](../in-memory-oltp-configure.md)
- [Azure Automation configureren](../database/automation-manage.md)
- Met [transactionele replicatie](replication-between-two-instances-configure-tutorial.md) kunt u uw gegevens repliceren tussen Azure SQL Managed instances of van on-premises SQL Server naar een door SQL beheerd exemplaar en andersom.
- [Detectie van bedreigingen configureren](threat-detection-configure.md) : [bedreigingen detectie](../database/threat-detection-overview.md) is een ingebouwde Azure SQL Managed instance-functie die verschillende mogelijke aanvallen detecteert zoals SQL-injectie of toegang vanaf verdachte locaties. 
- Door [waarschuwingen te maken](alerts-create.md) , kunt u waarschuwingen instellen voor gecontroleerde metrische gegevens, zoals CPU-gebruik, het verbruik van opslag ruimte, IOPS en andere voor een SQL-beheerd exemplaar. 

## <a name="monitoring-and-tuning"></a>Bewaking en afstemming

- [Handmatig instellen](../database/performance-guidance.md)
- [DMV's gebruiken voor het bewaken van prestaties](../database/monitoring-with-dmvs.md)
- [Query Store gebruiken voor het bewaken van prestaties](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Prestatieproblemen oplossen met Intelligent Insights](../database/intelligent-insights-troubleshoot-performance.md)
- [Diagnostisch logboek van Intelligent Insights gebruiken](../database/intelligent-insights-use-diagnostics-log.md)
- [OLTP-ruimte in het geheugen bewaken](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Uitgebreide gebeurtenissen

- [Uitgebreide gebeurtenissen](../database/xevent-db-diff-from-svr.md)
- [Uitgebreide gebeurtenissen opslaan in gebeurtenis bestand](../database/xevent-code-event-file.md)
- [Uitgebreide gebeurtenissen opslaan in de ring buffer](../database/xevent-code-ring-buffer.md)

## <a name="develop-applications"></a>Toepassingen ontwikkelen

- [Connectiviteit](../database/connect-query-content-reference-guide.md#libraries)
- [Spark-connector gebruiken](../../cosmos-db/spark-connector.md)
- [App verifiëren](../database/application-authentication-get-client-id-keys.md)
- [Batch verwerking gebruiken voor betere prestaties](../performance-improve-use-batching.md)
- [Connectiviteitsrichtlijnen](../database/troubleshoot-common-connectivity-issues.md)
- [DNS-aliassen](../database/dns-alias-overview.md)
- [DNS-alias instellen Power shell](../database/dns-alias-powershell-create.md)
- [Poorten - ADO.NET](../database/adonet-v12-develop-direct-route-ports.md)
- [C en C ++](../database/develop-cplusplus-simple.md)
- [Excel](../database/connect-excel.md)

## <a name="design-applications"></a>Toepassingen ontwerpen

- [Ontwerpen voor herstel na noodgeval](../database/designing-cloud-solutions-for-disaster-recovery.md)
- [Ontwerpen voor elastische pools](../database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Ontwerpen voor app-upgrades](../database/manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Multi tenant SaaS-toepassingen ontwerpen

- [SaaS-ontwerppatronen](../database/saas-tenancy-app-design-patterns.md)
- [Indexeerfunctie voor SaaS-video](../database/saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Beveiliging van SaaS-app](../database/saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)



## <a name="next-steps"></a>Volgende stappen

Ga aan de slag door [uw SQL Managed instance te implementeren](instance-create-quickstart.md)
