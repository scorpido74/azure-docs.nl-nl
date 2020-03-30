---
title: Monitoren en afstemmen - Azure Database voor PostgreSQL - Single Server
description: In dit artikel worden controle- en tuningfuncties beschreven in Azure Database voor PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 2d2bb5440e6d23a4cb5781244ba33ab4c5be2612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252588"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Azure-database voor PostgreSQL - Single Server bewaken en afstemmen
Door gegevens over uw servers te bewaken, u problemen oplossen en optimaliseren voor uw werkbelasting. Azure Database voor PostgreSQL biedt verschillende bewakingsopties om inzicht te geven in het gedrag van uw server.

## <a name="metrics"></a>Metrische gegevens
Azure Database voor PostgreSQL biedt verschillende statistieken die inzicht geven in het gedrag van de resources die de PostgreSQL-server ondersteunen. Elke statistiek wordt uitgezonden op een frequentie van één minuut en heeft tot 30 dagen geschiedenis. U waarschuwingen configureren op de statistieken. Zie [Waarschuwingen instellen](howto-alert-on-metric.md)voor stapsgewijze richtlijnen. Andere taken zijn het instellen van geautomatiseerde acties, het uitvoeren van geavanceerde analyses en het archiveren van geschiedenis. Zie het overzicht [van Azure Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md)voor meer informatie.

### <a name="list-of-metrics"></a>Lijst met statistieken
Deze statistieken zijn beschikbaar voor Azure Database voor PostgreSQL:

|Gegevens|Metrische weergavenaam|Eenheid|Beschrijving|
|---|---|---|---|
|cpu_percent|CPU-percentage|Percentage|Het percentage CPU in gebruik.|
|memory_percent|Geheugenpercentage|Percentage|Het percentage geheugen in gebruik.|
|io_consumption_percent|IO procent|Percentage|Het percentage IO in gebruik.|
|storage_percent|Opslagpercentage|Percentage|Het percentage opslagruimte dat buiten het maximum van de server wordt gebruikt.|
|storage_used|Gebruikte opslag|Bytes|De hoeveelheid opslagruimte die in gebruik is. De opslag die door de service wordt gebruikt, kan de databasebestanden, transactielogboeken en de serverlogboeken bevatten.|
|storage_limit|Opslaglimiet|Bytes|De maximale opslag voor deze server.|
|serverlog_storage_percent|Opslagpercentage serverlogboeken|Percentage|Het percentage serverlogboekopslag dat wordt gebruikt uit de maximale serverlogboekopslag van de server.|
|serverlog_storage_usage|Serverlogboekopslag gebruikt|Bytes|De hoeveelheid serverlogopslag die wordt gebruikt.|
|serverlog_storage_limit|Opslaglimiet voor serverlogboeken|Bytes|De maximale serverlogboekopslag voor deze server.|
|active_connections|Actieve verbindingen|Count|Het aantal actieve verbindingen met de server.|
|connections_failed|Mislukte verbindingen|Count|Het aantal mislukte verbindingen met de server.|
|network_bytes_egress|Netwerk uit|Bytes|Netwerk out via actieve verbindingen.|
|network_bytes_ingress|Netwerk in|Bytes|Netwerk In over actieve verbindingen.|
|backup_storage_used|Gebruikte back-upopslag|Bytes|De hoeveelheid gebruikte back-upopslag.|
|pg_replica_log_delay_in_bytes|Max Lag Over Replica's|Bytes|De vertraging in bytes tussen de master en de meest achterblijvende replica. Deze statistiek is alleen beschikbaar op de hoofdserver.|
|pg_replica_log_delay_in_seconds|Replica Lag|Seconden|De tijd sinds de laatste afgespeelde transactie. Deze statistiek is alleen beschikbaar voor replicaservers.|

## <a name="server-logs"></a>Serverlogboeken
U logboekregistratie op uw server inschakelen. Deze logboeken zijn ook beschikbaar via Azure Diagnostic Logs in [Azure Monitor logs,](../azure-monitor/log-query/log-query-overview.md)Event Hubs en Storage Account. Ga voor meer informatie over logboekregistratie naar de pagina [serverlogboeken.](concepts-server-logs.md)

## <a name="query-store"></a>Query Store
[Query Store](concepts-query-store.md) houdt de queryprestaties in de loop van de tijd bij, inclusief queryruntimestatistieken en wachtgebeurtenissen. De functie blijft queryruntimeprestatiegegevens bevatten in een systeemdatabase met de naam **azure_sys** onder het query_store-schema. U het verzamelen en opslaan van gegevens regelen via verschillende configuratieknoppen.

## <a name="query-performance-insight"></a>Inzicht in queryprestaties
[QueryPerformance Insight](concepts-query-performance-insight.md) werkt samen met de Query Store om visualisaties te bieden die toegankelijk zijn vanuit de Azure-portal. Met deze grafieken u belangrijke query's identificeren die van invloed zijn op de prestaties. Query Performance Insight is toegankelijk vanuit de sectie **Ondersteuning + probleemoplossing** van de portalpagina van uw Azure Database voor PostgreSQL-server.

## <a name="performance-recommendations"></a>Aanbevelingen voor prestaties
De functie [Prestatieaanbevelingen](concepts-performance-recommendations.md) identificeert mogelijkheden om de prestaties van de werkbelasting te verbeteren. Prestatieaanbevelingen bieden u aanbevelingen voor het maken van nieuwe indexen die de prestaties van uw workloads kunnen verbeteren. Om indexaanbevelingen te produceren, houdt de functie rekening met verschillende databasekenmerken, waaronder het schema en de werkbelasting zoals gerapporteerd door Query Store. Na het implementeren van een prestatieaanbeveling moeten klanten de prestaties testen om de impact van deze wijzigingen te evalueren. 

## <a name="planned-maintenance-notification"></a>Geplande onderhoudsmelding

**Met geplande onderhoudsmeldingen** u waarschuwingen ontvangen voor aankomend gepland onderhoud aan uw Azure Database voor PostgreSQL - Single Server. Deze meldingen zijn geïntegreerd met het geplande onderhoud [van Service Health](../service-health/overview.md) en stellen u in staat om al het geplande onderhoud voor uw abonnementen op één plaats te bekijken. Het helpt ook om de melding te schalen naar de juiste doelgroepen voor verschillende resourcegroepen, omdat u mogelijk verschillende contactpersonen hebt die verantwoordelijk zijn voor verschillende bronnen. U ontvangt de melding over het komende onderhoud 72 uur voor het evenement.

> [!Note]
> We zullen alles in het werk stellen om **geplande onderhoudsmelding** 72 uur van tevoren te verstrekken voor alle evenementen. In geval van kritieke of beveiligingspatches kunnen meldingen echter dichter bij de gebeurtenis worden verzonden of worden weggelaten.

### <a name="to-receive-planned-maintenance-notification"></a>Geplande onderhoudsmelding ontvangen

1. Selecteer **Servicestatus**in de [portal](https://portal.azure.com).
2. Selecteer **gezondheidwaarschuwingen** in de sectie **Waarschuwingen**.
3. Selecteer **+ Servicestatuswaarschuwing toevoegen** en vul de velden in.
4. Vul de vereiste velden in. 
5. Kies het **gebeurtenistype,** selecteer **Gepland onderhoud** of **Selecteer alles**
6. Definieer **in actiegroepen** hoe u de waarschuwing wilt ontvangen (ontvang een e-mail, activeer een logische app enz.)  
7. Zorg ervoor dat de regel inschakelen bij het maken is ingesteld op Ja.
8. Selecteer **Waarschuwingsregel maken** om uw waarschuwing te voltooien

Raadpleeg voor gedetailleerde stappen over het maken van **servicestatuswaarschuwingen**naar [Waarschuwingen voor activiteitenlogboeken maken op servicemeldingen](../service-health/alerts-activity-log-service-notifications.md).

> [!IMPORTANT]
> Geplande onderhoudsmeldingen zijn momenteel in preview

## <a name="next-steps"></a>Volgende stappen
- Bekijk [hoe u waarschuwingen instelt](howto-alert-on-metric.md) voor richtlijnen voor het maken van een waarschuwing op een statistiek.
- Zie het Overzicht van [Azure Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md)voor meer informatie over het openen en exporteren van statistieken met behulp van de Azure-portal, REST API of CLI.
- Lees onze blog over [best practices voor het bewaken van uw server.](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/)
