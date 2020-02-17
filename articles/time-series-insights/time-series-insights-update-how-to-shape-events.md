---
title: Vorm gebeurtenissen-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over aanbevolen procedures en het vorm geven van gebeurtenissen voor het uitvoeren van query's in azure time Insights preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/14/2020
ms.custom: seodec18
ms.openlocfilehash: e814d9be4a0db2852bd9e21f3d3c1d54a45bd268
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368641"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Shapegebeurtenissen met de Azure Time Series Insights Preview

Dit artikel helpt u bij het vorm geven van een JSON-bestand voor opname en het maximaliseren van de efficiëntie van uw Azure Time Series Insights preview-query's.

## <a name="best-practices"></a>Aanbevolen procedures

Denk na over hoe u gebeurtenissen naar Time Series Insights preview verzendt. Dat wil zeggen moet u altijd:

* gegevens zo efficiënt mogelijk via het netwerk verzenden.
* Uw gegevens op een manier waarmee u meer naar behoren aggregeren voor uw scenario Store.

Ga als volgt te werk voor de beste prestaties van query's:

* Geen overbodige eigenschappen niet verzenden. Time Series Insights Preview worden er kosten op uw gebruik. Het is raadzaam opslaan en verwerken van de gegevens die u hebt een query.
* Exemplaarvelden gebruiken voor statische gegevens. Met deze procedure kunt u statische gegevens verzenden via het netwerk voorkomen. Exemplaar velden, een onderdeel van het time series-model, werken als referentie gegevens in de Time Series Insights-service die algemeen beschikbaar is. Lees het [Time Series-model](./time-series-insights-update-tsm.md)voor meer informatie over exemplaar velden.
* Dimensie-eigenschappen tussen twee of meer gebeurtenissen delen. Met deze procedure kunt u gegevens efficiënter via het netwerk verzenden.
* Gebruik geen grondige matrix nesten. Time Series Insights preview ondersteunt Maxi maal twee niveaus van geneste matrices die objecten bevatten. Time Series Insights Preview effent matrices in berichten in meerdere gebeurtenissen met eigenschap-waardeparen.
* Als er slechts een paar metingen bestaan voor alle of de meeste gebeurtenissen, is het beter om het verzenden van deze metingen als afzonderlijke eigenschappen binnen hetzelfde object. Als u ze afzonderlijk verzendt, vermindert het aantal gebeurtenissen en kunnen query's sneller worden uitgevoerd omdat er minder gebeurtenissen moeten worden verwerkt.

Tijdens opname worden nettoladingen met nesten afgevlakt, zodat de naam van de kolom één waarde met een afbakening is. In Time Series Insights preview worden onderstrepings tekens gebruikt voor een overzicht. Let op: dit is een wijziging ten opzichte van de GA-versie van het product dat gebruik maakt van peri Oden. Tijdens de preview-periode is er sprake van een voor beeld van het afvlakken, dat wordt weer gegeven in de tweede afbeelding hieronder.

## <a name="examples"></a>Voorbeelden

Het volgende voorbeeld is gebaseerd op een scenario waarbij twee of meer apparaten metingen of signalen verzenden. De metingen of signalen kunnen een *stroom snelheid*, *olie belasting*, *Tempe ratuur*en *vochtigheid*zijn.

In het voor beeld is er één Azure IoT Hub-bericht waarbij de buitenste matrix een gedeeld gedeelte van algemene dimensie waarden bevat. Time Series-Instantiegegevens door de buitenste matrix wordt gebruikt om de efficiëntie van het bericht. 

Het exemplaar van de tijd reeks bevat meta gegevens van het apparaat. Deze meta gegevens worden niet met elke gebeurtenis gewijzigd, maar het biedt wel nuttige eigenschappen voor gegevens analyse. Als u wilt besparen op bytes die via de kabel worden verzonden en het bericht efficiënter wilt maken, kunt u gebruikmaken van algemene dimensie waarden en de meta gegevens van Time Series-exemplaren.

### <a name="example-1"></a>Voorbeeld 1:

```JSON
[
  {
    "deviceId":"FXXX",
    "timestamp":"2018-01-17T01:17:00Z",
    "series":[
      {
        "Flow Rate ft3/s":1.0172575712203979,
        "Engine Oil Pressure psi ":34.7
      },
      {
        "Flow Rate ft3/s":2.445906400680542,
        "Engine Oil Pressure psi ":49.2
      }
    ]
  },
  {
    "deviceId":"FYYY",
    "timestamp":"2018-01-17T01:18:00Z",
    "series":[
      {
        "Flow Rate ft3/s":0.58015072345733643,
        "Engine Oil Pressure psi ":22.2
      }
    ]
  }
]
```

### <a name="time-series-instance"></a>Time Series-exemplaar 

> [!NOTE]
> De time series-ID is *deviceId*.

```JSON
[
  {
    "timeSeriesId":[
      "FXXX"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"REVOLT SIMULATOR",
      "L2":"Battery System"
    }
  },
  {
    "timeSeriesId":[
      "FYYY"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"COMMON SIMULATOR",
      "L2":"Battery System"
    }
  }
]
```

Time Series Insights Preview lid wordt van een tabel (na afvlakken) tijdens het uitvoeren van query's. De tabel bevat aanvullende kolommen, zoals **type**. In het volgende voor beeld ziet u hoe u uw telemetrie-gegevens kunt [vorm](./time-series-insights-send-events.md#supported-json-shapes) geven.

| deviceId  | Type | L1 | L2 | tijdstempel | series_Flow frequentie FT3/s | series_Engine olie druk psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | Accu System | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34,7 |
| `FXXX` | Default_Type | SIMULATOR |   Accu System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49,2 |
| `FYYY` | ALGEMENE LINE_DATA | SIMULATOR |    Accu System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

In het voorgaande voorbeeld, houd rekening met de volgende punten:

* Statische eigenschappen worden opgeslagen in de Preview van tijd Series Insights om te worden verzonden via het netwerk optimaliseren.
* Time Series Insights preview-gegevens worden samen met de tijd reeks-ID die in het exemplaar is gedefinieerd, gekoppeld aan de query tijd.
* Er worden twee geneste lagen gebruikt. Dit nummer is het meest dat Time Series Insights preview ondersteunt. Het is essentieel om te voorkomen dat diep geneste matrices.
* Omdat er enkele metingen, wordt deze zijn verzonden als afzonderlijke eigenschappen binnen hetzelfde object. In het voor beeld **Series_Flow frequentie psi**, **series_Engine olie druk psi**en **series_Flow frequentie FT3/s** zijn unieke kolommen.

>[!IMPORTANT]
> Exemplaar velden worden niet opgeslagen met telemetrie. Ze worden opgeslagen met meta gegevens in het time series-model.
> De voorgaande tabel vertegenwoordigt de queryweergave.

### <a name="example-2"></a>Voorbeeld 2:

Bekijk de volgende JSON:

```JSON
{
  "deviceId": "FXXX",
  "timestamp": "2019-01-18T01:17:00Z",
  "data": {
        "flow": 1.0172575712203979,
    },
  "data_flow" : 1.76435072345733643
}
```
In het bovenstaande voor beeld zou de eigenschap plated `data_flow` een naam conflict met de eigenschap `data_flow` presen teren. In dit geval zou de *meest recente* eigenschaps waarde het vorige overschrijven. Als dit gedrag een uitdaging voor uw bedrijfs scenario's oplevert, neemt u contact op met het TSI-team.

> [!WARNING] 
> In gevallen waarin dubbele eigenschappen aanwezig zijn in dezelfde gebeurtenis lading als gevolg van afvlakking of een ander mechanisme, wordt de meest recente eigenschaps waarde opgeslagen overwritting alle vorige waarden.


## <a name="next-steps"></a>Volgende stappen

Lees [Azure time series Insights voorbeeld query syntaxis](./time-series-insights-query-data-csharp.md)als u deze richt lijnen in de praktijk wilt plaatsen. Meer informatie over de query syntaxis voor de Time Series Insights preview-REST API voor gegevens toegang.
