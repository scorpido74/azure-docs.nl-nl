---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 11469d992e0f5669cd3fc1e3864627dd0b8ae23d
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263337"
---
Het volgende geeft een overzicht van de belangrijkste limieten in algemene beschikbaarheid.

### <a name="sku-ingress-rates-and-capacities"></a>SKU-invallen de tarieven en capaciteiten

S1- en S2 SKU-invallen bieden flexibiliteit bij het configureren van een nieuwe Time Series Insights-omgeving. Uw SKU-capaciteit geeft uw dagelijkse ingressrate aan op basis van het aantal opgeslagen gebeurtenissen of bytes, afhankelijk van wat het eerst komt. Houd er rekening mee dat invallen *per minuut*worden gemeten en **dat throttling** wordt toegepast met behulp van het tokenbucket-algoritme. Binnendringen wordt gemeten in blokken van 1 KB. Een werkelijke gebeurtenis van 0,8 KB wordt bijvoorbeeld gemeten als één gebeurtenis en een gebeurtenis van 2,6 KB wordt als drie gebeurtenissen geteld.

| S1 SKU-capaciteit | Invallend tarief | Maximale opslagcapaciteit
| --- | --- | --- |
| 1 | 1 GB (1 miljoen evenementen) per dag | 30 GB (30 miljoen evenementen) per maand |
| 10 | 10 GB (10 miljoen evenementen) per dag | 300 GB (300 miljoen evenementen) per maand |

| S2 SKU-capaciteit | Invallend tarief | Maximale opslagcapaciteit
| --- | --- | --- |
| 1 | 10 GB (10 miljoen evenementen) per dag | 300 GB (300 miljoen evenementen) per maand |
| 10 | 100 GB (100 miljoen evenementen) per dag | 3 TB (3 miljard evenementen) per maand |

> [!NOTE]
> Capaciteiten schalen lineair, dus een S1 SKU met capaciteit 2 ondersteunt 2 GB (2 miljoen) gebeurtenissen per dag ingress rate en 60 GB (60 miljoen gebeurtenissen) per maand.

S2 SKU-omgevingen ondersteunen aanzienlijk meer gebeurtenissen per maand en hebben een aanzienlijk hogere invallende capaciteit.

| SKU  | Aantal gebeurtenissen per maand  | Aantal gebeurtenissen per minuut | Gebeurtenisgrootte per minuut  |
|---------|---------|---------|---------|---------|
| S1     |   30 miljoen   |  720    |  720 KB   |
 |S2     |   300 miljoen   | 7,200   | 7.200 KB  |

### <a name="property-limits"></a>Eigenschappenlimieten

GA-eigenschappenlimieten zijn afhankelijk van de Geselecteerde SKU-omgeving. Aangeleverde gebeurtenis-eigenschappen hebben overeenkomstige JSON-, CSV- en grafiekkolommen die kunnen worden weergegeven in de [Time Series Insights Explorer.](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart)

| SKU | Maximale eigenschappen |
| --- | --- |
| S1 | 600 eigenschappen (kolommen) |
| S2 | 800 eigenschappen (kolommen) |

### <a name="event-sources"></a>Gebeurtenisbronnen

Er wordt maximaal twee gebeurtenisbronnen per instantie ondersteund. 

* Meer informatie over het [toevoegen van een gebeurtenishubbron](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Een [IoT-hubbron](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)configureren .

### <a name="api-limits"></a>API-limieten

REST API-limieten voor Time Series Insights Algemene beschikbaarheid zijn opgegeven in de [REST API-referentiedocumentatie.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits)