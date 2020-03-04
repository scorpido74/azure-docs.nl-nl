---
title: Bewaking-Azure Database for MariaDB
description: In dit artikel worden de metrische gegevens beschreven voor bewaking en waarschuwingen voor Azure Database for MariaDB, inclusief CPU-, opslag-en verbindings statistieken.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 4f94ef66610b56d8843d59e5ca72a48143b742e8
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251399"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Bewaking in Azure Database for MariaDB
Het bewaken van gegevens over uw servers helpt u bij het oplossen en optimaliseren van uw werk belasting. Azure Database for MariaDB biedt diverse metrische gegevens die inzicht geven in het gedrag van uw server.

## <a name="metrics"></a>Metrische gegevens
Alle metrische gegevens van Azure hebben een frequentie van één minuut, en elke metriek biedt een geschiedenis van 30 dagen. U kunt waarschuwingen configureren voor de metrische gegevens. Andere taken zijn onder andere het instellen van geautomatiseerde acties, het uitvoeren van geavanceerde analyses en het archiveren van de geschiedenis. Zie het overzicht van Azure- [metrische](../monitoring-and-diagnostics/monitoring-overview-metrics.md)gegevens voor meer informatie.

Zie voor stapsgewijze instructies voor [het instellen van waarschuwingen](howto-alert-metric.md).

### <a name="list-of-metrics"></a>Lijst met metrische gegevens
Deze metrische gegevens zijn beschikbaar voor Azure Database for MariaDB:

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
|active_connections|Actieve verbindingen|Count|Het aantal actieve verbindingen met de server.|
|connections_failed|Mislukte verbindingen|Count|Het aantal mislukte verbindingen met de server.|
|network_bytes_egress|Netwerk uit|Bytes|Netwerk uit over actieve verbindingen.|
|network_bytes_ingress|Netwerk in|Bytes|Netwerk in meerdere actieve verbindingen.|

## <a name="server-logs"></a>Serverlogboeken

U kunt logboek registratie van langzame query's inschakelen op uw server. Deze logboeken zijn ook beschikbaar via Diagnostische logboeken van Azure in Azure Monitor-logboeken, Event Hubs en opslag account. Ga naar de pagina [Server logboeken](concepts-server-logs.md) voor meer informatie over logboek registratie.

## <a name="query-store"></a>Query Store

In het [query archief](concepts-query-store.md) worden de query prestaties in de loop van de tijd bijgehouden, inclusief de statistieken voor query runtime en wacht gebeurtenissen. De functie bewaart query runtime-prestatie gegevens in het **MySQL** -schema. U kunt het verzamelen en opslaan van gegevens beheren via verschillende configuratie knoppen.

## <a name="query-performance-insight"></a>Inzicht in queryprestaties

[Query Performance Insight](concepts-query-performance-insight.md) werkt in combi natie met query Store om visualisaties toegankelijk te maken vanuit de Azure Portal. Met deze grafieken kunt u belang rijke query's identificeren die van invloed zijn op de prestaties. Query Performance Insight is toegankelijk in het gedeelte **intelligente prestaties** van de portal pagina van de Azure database for MariaDB-server.

## <a name="performance-recommendations"></a>Aanbevelingen voor prestaties

De functie voor [prestatie aanbevelingen](concepts-performance-recommendations.md) identificeert mogelijkheden voor het verbeteren van de prestaties van de werk belasting. Prestatie aanbevelingen bieden u aanbevelingen voor het maken van nieuwe indexen die de prestaties van uw workloads kunnen verbeteren. Voor het produceren van index aanbevelingen, neemt de functie rekening met diverse database kenmerken, waaronder het schema en de werk belasting zoals gerapporteerd door de query Store. Na de implementatie van de aanbevolen prestaties, moeten klanten de prestaties testen om de impact van deze wijzigingen te evalueren.

## <a name="planned-maintenance-notification"></a>Melding over gepland onderhoud

Met **geplande onderhouds meldingen** kunt u waarschuwingen ontvangen voor gepland onderhoud naar uw Azure database for MariaDB. Deze meldingen zijn geïntegreerd met [het geplande onderhoud van service Health](../service-health/overview.md) en bieden u de mogelijkheid om het geplande onderhoud voor uw abonnementen op één plek weer te geven. Het helpt ook om de melding te schalen naar de juiste doel groep voor verschillende resource groepen, omdat u mogelijk verschillende contact personen hebt die verantwoordelijk zijn voor verschillende resources. U ontvangt de melding over het komende onderhoud van 72 uur vóór de gebeurtenis.

> [!Note]
> Elke keer dat er wordt gewaarschuwd voor **gepland onderhouds melding** 72 uur voor alle gebeurtenissen. In het geval van essentiële of beveiligings patches kunnen meldingen echter dichter bij het evenement worden verzonden of worden wegge laten.

### <a name="to-receive-planned-maintenance-notification"></a>Een melding over gepland onderhoud ontvangen

1. Selecteer in de [portal](https://portal.azure.com) **service Health**.
2. Selecteer in de sectie **waarschuwingen** **Health Alerts**.
3. Selecteer **+ service Health alert toevoegen** en vul de velden in.
4. Vul de vereiste velden in. 
5. Kies het **gebeurtenis type**, selecteer **gepland onderhoud** of **Alles selecteren**
6. Geef in **actie groepen** op hoe u de waarschuwing wilt ontvangen (ontvang een e-mail, Activeer een logische app, enz.)  
7. Zorg ervoor dat regel inschakelen bij maken is ingesteld op Ja.
8. Selecteer **waarschuwings regel maken** om uw waarschuwing te volt ooien

Zie [waarschuwingen voor activiteiten logboek maken voor service meldingen](../service-health/alerts-activity-log-service-notifications.md)voor gedetailleerde stappen voor het maken van **service Health-waarschuwingen**.

> [!IMPORTANT]
> Geplande onderhouds meldingen zijn momenteel beschikbaar als preview-versie

## <a name="next-steps"></a>Volgende stappen

- Zie het [overzicht van Azure-metrische](../monitoring-and-diagnostics/monitoring-overview-metrics.md)gegevens voor meer informatie over het openen en exporteren van metrische gegevens met behulp van de Azure Portal, rest API of cli.
  - Zie [waarschuwingen instellen](howto-alert-metric.md) voor hulp bij het maken van een waarschuwing op metrische gegevens.
