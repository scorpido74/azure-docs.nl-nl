---
title: Monitor and Tune-grootschalige (Citus)-Azure Database for PostgreSQL
description: In dit artikel worden de functies voor het controleren en afstemmen in Azure Database for PostgreSQL-grootschalige (Citus) beschreven.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d2e9fcd6f6292c1da76e725e90deda4547b3682d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74975513"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-grootschalige (Citus) bewaken en afstemmen

Het bewaken van gegevens over uw servers helpt u bij het oplossen en optimaliseren van uw werk belasting. Grootschalige (Citus) biedt verschillende bewakings opties om inzicht te krijgen in het gedrag van knoop punten in een server groep.

## <a name="metrics"></a>Metrische gegevens

Grootschalige (Citus) geeft metrische gegevens voor elk knoop punt in een server groep. De metrische gegevens bieden inzicht in het gedrag van ondersteunende resources. Elke metriek wordt verzonden met een frequentie van één minuut en heeft tot wel 30 dagen aan geschiedenis.

Naast het weer geven van grafieken van de metrische gegevens kunt u waarschuwingen configureren. Zie voor stapsgewijze instructies voor [het instellen van waarschuwingen](howto-hyperscale-alert-on-metric.md).  Andere taken zijn onder andere het instellen van geautomatiseerde acties, het uitvoeren van geavanceerde analyses en het archiveren van geschiedenis. Zie het overzicht van Azure- [metrische](../monitoring-and-diagnostics/monitoring-overview-metrics.md)gegevens voor meer informatie.

### <a name="list-of-metrics"></a>Lijst met metrische gegevens

Deze metrische gegevens zijn beschikbaar voor grootschalige-knoop punten (Citus):

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Beschrijving|
|---|---|---|---|
|active_connections|Actieve verbindingen|Count|Het aantal actieve verbindingen met de server.|
|cpu_percent|CPU-percentage|Percentage|Het percentage CPU-gebruik.|
|IOPS|IOPS|Count|De [IOPS-definitie](../virtual-machines/linux/premium-storage-performance.md#iops) en [grootschalige-door Voer](concepts-hyperscale-configuration-options.md) bekijken|
|memory_percent|Geheugen percentage|Percentage|Het percentage geheugen dat in gebruik is.|
|network_bytes_ingress|Netwerk in|Bytes|Netwerk in meerdere actieve verbindingen.|
|network_bytes_egress|Netwerk uit|Bytes|Netwerk uit over actieve verbindingen.|
|storage_percent|Opslag percentage|Percentage|Het percentage van de opslag ruimte dat uit het maximum van de server wordt gebruikt.|
|storage_used|Gebruikte opslag|Bytes|De hoeveelheid opslag ruimte die wordt gebruikt. De opslag die door de service wordt gebruikt, kan de database bestanden, transactie logboeken en de server logboeken bevatten.|

Azure levert geen statistische metrische gegevens voor het cluster als geheel, maar de metrische gegevens voor meerdere knoop punten kunnen in dezelfde grafiek worden geplaatst.

## <a name="next-steps"></a>Volgende stappen

- Zie [waarschuwingen instellen](howto-hyperscale-alert-on-metric.md) voor hulp bij het maken van een waarschuwing op metrische gegevens.
