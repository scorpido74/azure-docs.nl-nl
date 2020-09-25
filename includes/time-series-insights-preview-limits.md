---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 7259e1981f873c8385a02fe4f353dcdda495f823
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91287407"
---
### <a name="property-limits"></a>Eigenschaps limieten

Azure Time Series Insights eigenschaps limieten zijn verhoogd tot 1.000 van een maximale limiet van 800 in gen1. De opgegeven gebeurtenis eigenschappen hebben bijbehorende JSON-, CSV-en grafiek kolommen die u kunt weer geven in de [Azure time series Insights Gen2 Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Maximum aantal eigenschappen |
| --- | --- |
| Gen2 (L1) | 1.000-eigenschappen (kolommen) |
| Gen1 (S1) | 600-eigenschappen (kolommen) |
| Gen1 (S2) | 800-eigenschappen (kolommen) |

### <a name="streaming-ingestion"></a>Streamingopname

* Er zijn Maxi maal twee [gebeurtenis bronnen](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) per omgeving.

* De aanbevolen procedures en algemene richt lijnen voor gebeurtenis bronnen vindt u [hier](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)

* Standaard kan Azure Time Series Insights Gen2 binnenkomende gegevens opnemen met een snelheid van **Maxi maal 1 MB per seconde (Mbps) per Azure time series Insights Gen2-omgeving**. Er zijn extra beperkingen [per hub-partitie](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits). Er kunnen Maxi maal 8 MBps worden geboden door een ondersteunings ticket in te dienen via de Azure Portal. Lees de [doorvoer limieten](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md)voor het opnemen van gegevens stromen voor meer informatie.

### <a name="api-limits"></a>API-limieten

REST API limieten voor Azure Time Series Insights Gen2 zijn opgegeven in de [referentie documentatie van rest API](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1).
