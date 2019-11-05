---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 10/16/2019
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: dfe16b4e965670d115cfa92f1cb3ca812d6375ad
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990875"
---
### <a name="general-availability-and-preview-comparison"></a>Algemene Beschik baarheid en preview-vergelijking

De volgende tabel bevat een overzicht van verschillende belang rijke verschillen tussen Azure Time Series Insights algemene Beschik baarheid (GA) en preview-exemplaren.

| | Algemene beschikbaarheid | Preview |
| --- | --- | ---|
| Eersteklas burger | Gebeurtenis gerichte | Time-Series-gericht |
| Semantische redenen | Laag-niveau (referentie gegevens) | High-Level (modellen) |
| Gegevens contextualization | Niet-apparaatniveau | Apparaat en niet-apparaatniveau |
| Berekenings logica opslag | Nee | Opgeslagen in type variabelen deel van model |
| Opslag en toegangs beheer | Nee | Ingeschakeld via model |
| Aggregaties/steek proeven | Nee | Gewogen gebeurtenis en tijd gewogen |
| Signaal wederopbouw | Nee | Interpolatie |
| Productie van afgeleide tijd reeks | Nee | Ja, samen voegingen en koppelingen |
| Taal flexibiliteit | Niet-samenstelbaar | Samenstel bare |
| Expressietaal | Predicaat teken reeks | Time Series-expressies (predicaat teken reeksen, waarden, expressies en functies) |

### <a name="property-limits"></a>Eigenschaps limieten

De limieten van Time Series Insights-eigenschappen zijn verhoogd tot 1.000 van een maximum limiet van 800 in GA. De opgegeven gebeurtenis eigenschappen hebben bijbehorende JSON-, CSV-en grafiek kolommen die u kunt weer geven in de [Time Series Insights preview Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Maximum aantal eigenschappen |
| --- | --- |
| Preview-PAYG | 1\.000-eigenschappen (kolommen) |
| GA S1 | 600-eigenschappen (kolommen) |
| GA S2 | 800-eigenschappen (kolommen) |

### <a name="event-sources"></a>Gebeurtenisbronnen

Er worden Maxi maal twee gebeurtenis bronnen per instantie ondersteund. 

* Meer informatie over het [toevoegen van een event hub bron](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* [Een IOT hub-bron](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)configureren.

### <a name="api-limits"></a>API-limieten

REST API limieten voor Time Series Insights preview zijn opgegeven in de [referentie documentatie van rest API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits).