---
title: Monitoren en afstemmen - Hyperscale (Citus) - Azure Database voor PostgreSQL
description: In dit artikel worden monitoring- en tuningfuncties beschreven in Azure Database voor PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d2e9fcd6f6292c1da76e725e90deda4547b3682d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975513"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database voor PostgreSQL - Hyperscale (Citus) bewaken en afstemmen

Door gegevens over uw servers te bewaken, u problemen oplossen en optimaliseren voor uw werkbelasting. Hyperscale (Citus) biedt verschillende bewakingsopties om inzicht te geven in het gedrag van knooppunten in een servergroep.

## <a name="metrics"></a>Metrische gegevens

Hyperscale (Citus) biedt statistieken voor elk knooppunt in een servergroep. De statistieken geven inzicht in het gedrag van ondersteunende resources. Elke statistiek wordt uitgezonden op een frequentie van één minuut en heeft tot 30 dagen geschiedenis.

Naast het bekijken van grafieken van de statistieken, u waarschuwingen configureren. Zie [Waarschuwingen instellen](howto-hyperscale-alert-on-metric.md)voor stapsgewijze richtlijnen.  Andere taken zijn het instellen van geautomatiseerde acties, het uitvoeren van geavanceerde analyses en het archiveren van geschiedenis. Zie het overzicht [van Azure Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md)voor meer informatie.

### <a name="list-of-metrics"></a>Lijst met statistieken

Deze statistieken zijn beschikbaar voor Citus-knooppunten (Hyperscale):

|Gegevens|Metrische weergavenaam|Eenheid|Beschrijving|
|---|---|---|---|
|active_connections|Actieve verbindingen|Count|Het aantal actieve verbindingen met de server.|
|cpu_percent|CPU-percentage|Percentage|Het percentage CPU in gebruik.|
|iops iops|IOPS|Count|Zie de [IOPS-definitie](../virtual-machines/linux/premium-storage-performance.md#iops) en [Hyperscale-doorvoer](concepts-hyperscale-configuration-options.md)|
|memory_percent|Geheugenpercentage|Percentage|Het percentage geheugen in gebruik.|
|network_bytes_ingress|Netwerk in|Bytes|Netwerk In over actieve verbindingen.|
|network_bytes_egress|Netwerk uit|Bytes|Netwerk out via actieve verbindingen.|
|storage_percent|Opslagpercentage|Percentage|Het percentage opslagruimte dat buiten het maximum van de server wordt gebruikt.|
|storage_used|Gebruikte opslag|Bytes|De hoeveelheid opslagruimte die in gebruik is. De opslag die door de service wordt gebruikt, kan de databasebestanden, transactielogboeken en de serverlogboeken bevatten.|

Azure levert geen geaggregeerde statistieken voor het cluster als geheel, maar metrische gegevens voor meerdere knooppunten kunnen op dezelfde grafiek worden geplaatst.

## <a name="next-steps"></a>Volgende stappen

- Bekijk [hoe u waarschuwingen instelt](howto-hyperscale-alert-on-metric.md) voor richtlijnen voor het maken van een waarschuwing op een statistiek.
