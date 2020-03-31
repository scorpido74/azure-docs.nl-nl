---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 7bc6938523a6d66a2bc20b37c659568fc5ca494d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77123248"
---
### <a name="general-availability-and-preview-comparison"></a>Algemene beschikbaarheid en preview vergelijking

In de volgende tabel worden enkele belangrijke verschillen samengevat tussen de algemene beschikbaarheid van Azure Time Series Insights (GA) en voorbeeldexemplaren.

| | Algemene beschikbaarheid | Preview |
| --- | --- | ---|
| Eersteklas burger | Event-centric | Time-series-centric |
| Semantisch redeneren | Laag niveau (referentiegegevens) | Hoog niveau (modellen) |
| Contextuele gegevens | Niet-apparaatniveau | Apparaat en niet-apparaatniveau |
| Opslag van rekenlogica | Nee | Opgeslagen in typevariabelen onderdeel van het model |
| Opslag- en toegangscontrole | Nee | Ingeschakeld via model |
| Aggregaties/Bemonstering | Nee | Gewogen evenement en gewogen tijd |
| Signaalreconstructie | Nee | Interpolatie |
| Productie van afgeleide tijdreeksen | Nee | Ja, samenvoegingen en joins |
| Taalflexibiliteit | Niet-composteerbaar | Composable |
| Expressietaal | Predicaattekenreeks | Expressies van tijdreeksen (predicaattekenreeksen, waarden, expressies en functies) |

### <a name="property-limits"></a>Eigenschappenlimieten

De eigenschapslimieten van Time Series Insights zijn verhoogd tot 1.000 van een maximum van 800 in GA. Aangeleverde gebeurtenis-eigenschappen hebben overeenkomstige JSON-, CSV- en grafiekkolommen die u weergeven in de [preview-verkenner voor timeseries insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Maximale eigenschappen |
| --- | --- |
| Voorbeeld van PAYG | 1.000 eigenschappen (kolommen) |
| GA S1 | 600 eigenschappen (kolommen) |
| GA S2 | 800 eigenschappen (kolommen) |

### <a name="event-sources"></a>Gebeurtenisbronnen

Er wordt maximaal twee gebeurtenisbronnen per instantie ondersteund. 

* Meer informatie over het [toevoegen van een gebeurtenishubbron](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Een [IoT-hubbron](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)configureren .

[Preview-omgevingen ondersteunen standaard invallensnelheden](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#ingress-scale-and-preview-limitations) tot **1 megabyte per seconde (MB/s) per omgeving.** Klanten kunnen hun Preview-omgevingen zo nodig schalen tot **16 MB/s-doorvoer.** Er is ook een limiet per partitie van **0,5 MB/s.** 

### <a name="api-limits"></a>API-limieten

REST API-limieten voor Time Series Insights Preview zijn opgegeven in de [REFERENTIEdocumentatie VOOR DE REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits).
