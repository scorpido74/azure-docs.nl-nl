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
ms.openlocfilehash: cd6ea6d4967e024ddf88fb9572d5efae8b7a7815
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495330"
---
### <a name="property-limits"></a>Eigenschaps limieten

Azure Time Series Insights eigenschaps limieten zijn verhoogd tot 1.000 van een maximale limiet van 800 in gen1. De opgegeven gebeurtenis eigenschappen hebben bijbehorende JSON-, CSV-en grafiek kolommen die u kunt weer geven in de [Azure time series Insights Gen2 Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Maximum aantal eigenschappen |
| --- | --- |
| Gen2 (L1) | 1.000-eigenschappen (kolommen) |
| Gen1 (S1) | 600-eigenschappen (kolommen) |
| Gen1 (S2) | 800-eigenschappen (kolommen) |

### <a name="event-sources"></a>Gebeurtenisbronnen

Er worden Maxi maal twee gebeurtenis bronnen per instantie ondersteund.

* Meer informatie over het [toevoegen van een event hub bron](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* [Een IOT hub-bron](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)configureren.

Standaard [ondersteunen Gen2-omgevingen ingangs snelheden](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-throughput-limitations) van Maxi maal **1 MB per seconde (MB/s) per omgeving**. Klanten kunnen hun omgevingen zo nodig schalen tot een door Voer van **16 MB/s** . Er is ook een limiet van **0,5 MB/s**per partitie.

### <a name="api-limits"></a>API-limieten

REST API limieten voor Azure Time Series Insights Gen2 zijn opgegeven in de [referentie documentatie van rest API](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1).
