---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 12/06/2019
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 54796f0c0810afd5559af836c566bf66b430223f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74981515"
---
Hieronder vindt u een overzicht van de belangrijkste limieten voor algemene Beschik baarheid.

### <a name="sku-ingress-rates-and-capacities"></a>Tarieven en capaciteit van de SKU

De snelheid en capaciteit van de SKU van de sku's van de S2 bieden flexibiliteit bij het configureren van een nieuwe Time Series Insights-omgeving.

| Capaciteit van S1-SKU | Ingangs frequentie | Maximale opslag capaciteit
| --- | --- | --- |
| 1 | 1 GB (1.000.000 gebeurtenissen) | 30 GB (30.000.000 gebeurtenissen) per maand |
| 10 | 10 GB (10.000.000 gebeurtenissen) | 300 GB (300.000.000 gebeurtenissen) per maand |

| Capaciteit van S2-SKU | Ingangs frequentie | Maximale opslag capaciteit
| --- | --- | --- |
| 1 | 10 GB (10.000.000 gebeurtenissen) | 300 GB (300.000.000 gebeurtenissen) per maand |
| 10 | 100 GB (100.000.000 gebeurtenissen) | 3 TB (3.000.000.000 gebeurtenissen) per maand |

> [!NOTE]
> Capaciteit schaalt lineair, dus een S1-SKU met capaciteit 2 ondersteunt 2 GB (2.000.000) gebeurtenissen per dag ingangs snelheden en 60 GB (60.000.000 gebeurtenissen) per maand.

S2-SKU-omgevingen ondersteunen aanzienlijk meer gebeurtenissen per maand en hebben een aanzienlijk hogere ingangs capaciteit.

| SKU  | Aantal gebeurtenissen per maand  | Gebeurtenis grootte per maand  | Aantal gebeurtenissen per minuut | Gebeurtenis grootte per minuut  |
|---------|---------|---------|---------|---------|
| S1     |   30.000.000     |  30 GB     |  720    |  720 KB   |
 |S2     |   300.000.000    |   300 GB   | 7\.200   | 7\.200 KB  |

### <a name="property-limits"></a>Eigenschaps limieten

Eigenschaps limieten voor GA zijn afhankelijk van de geselecteerde SKU-omgeving. De opgegeven gebeurtenis eigenschappen hebben bijbehorende JSON-, CSV-en grafiek kolommen die kunnen worden weer gegeven in de [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart).

| SKU | Maximum aantal eigenschappen |
| --- | --- |
| S1 | 600-eigenschappen (kolommen) |
| S2 | 800-eigenschappen (kolommen) |

### <a name="event-sources"></a>Gebeurtenisbronnen

Er worden Maxi maal twee gebeurtenis bronnen per instantie ondersteund. 

* Meer informatie over het [toevoegen van een event hub bron](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* [Een IOT hub-bron](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)configureren.

### <a name="api-limits"></a>API-limieten

REST API limieten voor Time Series Insights algemene Beschik baarheid worden opgegeven in de [referentie documentatie van rest API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits).