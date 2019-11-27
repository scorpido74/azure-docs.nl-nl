---
title: Bewaking in Azure Database for MySQL
description: In dit artikel worden de metrische gegevens beschreven voor bewaking en waarschuwingen voor Azure Database for MySQL, inclusief CPU-, opslag-en verbindings statistieken.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: df03f8ba0e522aacd305b6337e506f53e309660a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384050"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Bewaking in Azure Database for MySQL
Het bewaken van gegevens over uw servers helpt u bij het oplossen en optimaliseren van uw werk belasting. Azure Database for MySQL biedt diverse metrische gegevens die inzicht geven in het gedrag van uw server.

## <a name="metrics"></a>Metrische gegevens
Alle metrische gegevens van Azure hebben een frequentie van één minuut, en elke metriek biedt een geschiedenis van 30 dagen. U kunt waarschuwingen configureren voor de metrische gegevens. Zie voor stapsgewijze instructies voor [het instellen van waarschuwingen](howto-alert-on-metric.md). Andere taken zijn onder andere het instellen van geautomatiseerde acties, het uitvoeren van geavanceerde analyses en het archiveren van de geschiedenis. Zie het overzicht van Azure- [metrische](../monitoring-and-diagnostics/monitoring-overview-metrics.md)gegevens voor meer informatie.

### <a name="list-of-metrics"></a>Lijst met metrische gegevens
Deze metrische gegevens zijn beschikbaar voor Azure Database for MySQL:

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Beschrijving|
|---|---|---|---|
|cpu_percent|CPU-percentage|Procent|Het percentage CPU-gebruik.|
|memory_percent|Geheugen percentage|Procent|Het percentage geheugen dat in gebruik is.|
|io_consumption_percent|IO-percentage|Procent|Het percentage i/o dat in gebruik is.|
|storage_percent|Opslag percentage|Procent|Het percentage van de opslag ruimte dat uit het maximum van de server wordt gebruikt.|
|storage_used|Gebruikte opslag|Bytes|De hoeveelheid opslag ruimte die wordt gebruikt. De opslag die door de service wordt gebruikt, kan de database bestanden, transactie logboeken en de server logboeken bevatten.|
|serverlog_storage_percent|Percentage server logboek opslag|Procent|Het percentage server logboek opslag dat is gebruikt voor de maximale server logboek opslag van de server.|
|serverlog_storage_usage|Gebruikte server logboek opslag|Bytes|De hoeveelheid server logboek opslag die in gebruik is.|
|serverlog_storage_limit|Opslag limiet voor server logboek|Bytes|De maximale server logboek opslag voor deze server.|
|storage_limit|Opslag limiet|Bytes|De maximale opslag voor deze server.|
|active_connections|Actieve verbindingen|Aantal|Het aantal actieve verbindingen met de server.|
|connections_failed|Mislukte verbindingen|Aantal|Het aantal mislukte verbindingen met de server.|
|seconds_behind_master|Replicatie vertraging in seconden|Aantal|Het aantal seconden dat de replica server bewaart tegen de hoofd server.|
|network_bytes_egress|Netwerk uit|Bytes|Netwerk uit over actieve verbindingen.|
|network_bytes_ingress|Netwerk in|Bytes|Netwerk in meerdere actieve verbindingen.|
|backup_storage_used|Gebruikte back-upopslag|Bytes|De hoeveelheid back-upopslag die wordt gebruikt.|

## <a name="server-logs"></a>Serverlogboeken
U kunt langzame query-en controle logboek registratie inschakelen op uw server. Deze logboeken zijn ook beschikbaar via Diagnostische logboeken van Azure in Azure Monitor-logboeken, Event Hubs en opslag account. Ga voor meer informatie over logboek registratie naar de artikelen [audit logs](concepts-audit-logs.md) en [Slow query logs](concepts-server-logs.md) .

## <a name="query-store"></a>Query Store
[Query Store](concepts-query-store.md) is een functie waarmee de query prestaties in de loop van de tijd worden bijgehouden, inclusief query's in runtime-statistieken en wacht gebeurtenissen. De functie bewaart query runtime-prestatie gegevens in het **MySQL** -schema. U kunt het verzamelen en opslaan van gegevens beheren via verschillende configuratie knoppen.

## <a name="query-performance-insight"></a>Inzicht in queryprestaties
[Query Performance Insight](concepts-query-performance-insight.md) werkt in combi natie met query Store om visualisaties toegankelijk te maken vanuit de Azure Portal. Met deze grafieken kunt u belang rijke query's identificeren die van invloed zijn op de prestaties. Query Performance Insight is toegankelijk in het gedeelte **intelligente prestaties** van de portal pagina van de Azure database for mysql-server.

## <a name="performance-recommendations"></a>Aanbevelingen voor prestaties
De functie voor [prestatie aanbevelingen](concepts-performance-recommendations.md) identificeert mogelijkheden voor het verbeteren van de prestaties van de werk belasting. Prestatie aanbevelingen bieden u aanbevelingen voor het maken van nieuwe indexen die de prestaties van uw workloads kunnen verbeteren. Voor het produceren van index aanbevelingen, neemt de functie rekening met diverse database kenmerken, waaronder het schema en de werk belasting zoals gerapporteerd door de query Store. Na de implementatie van de aanbevolen prestaties, moeten klanten de prestaties testen om de impact van deze wijzigingen te evalueren.

## <a name="service-health"></a>Servicestatus
[Azure service Health](../service-health/overview.md) biedt een overzicht van alle service status meldingen in uw abonnement. U kunt Service Health waarschuwingen instellen om u te waarschuwen via de communicatie kanalen van uw voor keur wanneer er problemen zijn of wijzigingen die van invloed kunnen zijn op de Azure-Services en-regio's die u gebruikt.

U kunt geplande onderhouds gebeurtenissen voor Azure Database for MySQL weer geven met behulp van het gebeurtenis type **gepland onderhoud** . Ga voor meer informatie over het maken van **service Health-waarschuwingen**naar het artikel [activiteiten logboeken maken op service meldingen](../service-health/alerts-activity-log-service-notifications.md) .

> [!IMPORTANT]
> De geplande onderhouds meldingen zijn alleen beschikbaar als preview-versie voor VS-Oost en alleen UK-zuid.

## <a name="next-steps"></a>Volgende stappen
- Zie [waarschuwingen instellen](howto-alert-on-metric.md) voor hulp bij het maken van een waarschuwing op metrische gegevens.
- Zie het [overzicht van Azure-metrische](../monitoring-and-diagnostics/monitoring-overview-metrics.md)gegevens voor meer informatie over het openen en exporteren van metrische gegevens met behulp van de Azure Portal, rest API of cli.
- Lees onze blog [voor de aanbevolen procedures voor het bewaken van uw server](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/).
