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
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: fd2c58b07f3be5d5fa6d99d0c8c64906b81e7de4
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86036981"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Vorm gebeurtenissen met Azure Time Series Insights preview

In dit artikel worden aanbevolen procedures gedefinieerd voor het vorm geven van uw JSON-nettoladingen voor opname in Azure Time Series Insights en om de efficiëntie van uw preview-query's te maximaliseren.

## <a name="best-practices"></a>Aanbevolen procedures

Het is raadzaam om zorgvuldig te overwegen hoe u gebeurtenissen naar uw Time Series Insights-voorbeeld omgeving verzendt. 

Algemene aanbevolen procedures zijn onder andere:

* Verzend zo efficiënt mogelijk gegevens via het netwerk.
* Sla uw gegevens op een manier op waarmee u deze beter kunt samen voegen voor uw scenario.

Voor de beste query prestaties moet u de volgende vuist regels naleven:

* Geen onnodige eigenschappen verzenden. Time Series Insights voor beeld van facturen per gebruik. Het is het beste om alleen de gegevens op te slaan en te verwerken die u doorzoekt.
* Exemplaar velden gebruiken voor statische gegevens. Met deze procedure kunt u voor komen dat statische gegevens via het netwerk worden verzonden. Exemplaar velden, een onderdeel van het time series-model, werken als referentie gegevens in de Time Series Insights-service die algemeen beschikbaar is. Lees het [Time Series-model](./concepts-model-overview.md)voor meer informatie over exemplaar velden.
* Dimensie-eigenschappen delen tussen twee of meer gebeurtenissen. Met deze procedure kunt u gegevens efficiënter verzenden via het netwerk.
* Gebruik geen geneste diepe matrix. Time Series Insights preview ondersteunt Maxi maal twee niveaus van geneste matrices die objecten bevatten. Met Time Series Insights preview worden matrices in berichten samengevoegd in meerdere gebeurtenissen met eigenschaps waarde-paren.
* Als er slechts enkele maat regelen bestaan voor alle of de meeste gebeurtenissen, is het beter om deze metingen als afzonderlijke eigenschappen binnen hetzelfde object te verzenden. Als u ze afzonderlijk verzendt, vermindert het aantal gebeurtenissen en kunnen query's sneller worden uitgevoerd omdat er minder gebeurtenissen moeten worden verwerkt.

## <a name="column-flattening"></a>Kolom afvlakking

Tijdens de opname worden nettoladingen die geneste objecten bevatten, afgevlakt zodat de naam van de kolom één waarde met een afbakening is.

* Bijvoorbeeld de volgende geneste JSON:

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   Wordt als volgt: wanneer het is `data_flow` afgevlakt.

> [!IMPORTANT]
> * Azure Time Series Insights preview gebruikt onderstrepings tekens ( `_` ) voor de kolom inlijning.
> * Let op het verschil van algemene Beschik baarheid, waarbij gebruik wordt gemaakt van peri Oden ( `.` ).

Hieronder vindt u meer complexe scenario's.

#### <a name="example-1"></a>Voorbeeld 1:

Het volgende scenario heeft twee (of meer) apparaten waarmee de metingen (signalen) worden verzonden: *stroom snelheid*, *olie belasting*van de motor, *Tempe ratuur*en *vochtigheid*.

Er is één Azure IoT Hub-bericht verzonden waarbij de buitenste matrix een gedeeld gedeelte van algemene dimensie waarden bevat (Let op de twee apparaatgegevens in het bericht).

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

**Takeaways:**

* De JSON van het voor beeld heeft een buitenste matrix die gebruikmaakt van gegevens van een [Time Series-instantie](./concepts-model-overview.md#time-series-model-instances) om de efficiëntie van het bericht te verhogen. Hoewel time series-exemplaren de meta gegevens van het apparaat niet waarschijnlijk wijzigen, biedt het vaak nuttige eigenschappen voor gegevens analyse.

* In de JSON worden twee of meer berichten (één van elk apparaat) gecombineerd tot één nettolading die gedurende een bepaalde periode wordt bespaard op band breedte.

* Gegevens punten van afzonderlijke reeksen voor elk apparaat worden gecombineerd tot één **reeks** kenmerk, waardoor de nood zaak voor het voortdurend bijwerken van streams voor elk apparaat wordt verkleind.

> [!TIP]
> Als u het aantal berichten wilt beperken dat nodig is om gegevens te verzenden en telemetrie efficiënter te maken, kunt u overwegen om in batches algemene dimensie waarden en meta gegevens van de time series-instantie in één JSON-nettolading op te nemen.

#### <a name="time-series-instance"></a>Time Series-instantie 

Laten we eens kijken hoe u het [Time Series-exemplaar](./concepts-model-overview.md#time-series-model-instances) kunt gebruiken om uw JSON optimaal te vorm geven. 

> [!NOTE]
> De onderstaande [Time Series-id's](./time-series-insights-update-how-to-id.md) zijn *deviceIds*.

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

Time Series Insights preview voegt een tabel toe (na afvlakking) tijdens de query tijd. De tabel bevat aanvullende kolommen, zoals **type**.

| deviceId  | Type | L1 | L2 | tijdstempel | series_Flow frequentie FT3/s | series_Engine olie druk psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | ABSORBEER | Batterij systeem | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34,7 |
| `FXXX` | Default_Type | ABSORBEER |   Batterij systeem |    2018-01-17T01:17:00Z | 2.445906400680542 |  49,2 |
| `FYYY` | LINE_DATA GEBRUIKELIJK | ABSORBEER |    Batterij systeem |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22,2 |

> [!NOTE]
>  De voor gaande tabel vertegenwoordigt de query weergave in de [Preview-versie van Time Series](./time-series-insights-update-explorer.md).

**Takeaways:**

* In het voor gaande voor beeld worden statische eigenschappen opgeslagen in Time Series Insights Preview voor het optimaliseren van gegevens die via het netwerk worden verzonden.
* Time Series Insights preview-gegevens worden samen met de tijd reeks-ID die in het exemplaar is gedefinieerd, gekoppeld aan de query tijd.
* Er worden twee geneste lagen gebruikt. Dit nummer is het meest dat Time Series Insights preview ondersteunt. Het is essentieel om diep geneste matrices te voor komen.
* Omdat er enkele metingen zijn, worden ze verzonden als afzonderlijke eigenschappen binnen hetzelfde object. In het voor beeld **Series_Flow frequentie psi**, **series_Engine olie druk psi**en **series_Flow frequentie FT3/s** zijn unieke kolommen.

>[!IMPORTANT]
> Exemplaar velden worden niet opgeslagen met telemetrie. Ze worden opgeslagen met meta gegevens in het time series-model.

#### <a name="example-2"></a>Voorbeeld 2:

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

In het bovenstaande voor beeld zou de afgevlakt `data["flow"]` eigenschap een naam conflict met de eigenschap presen teren `data_flow` .

In dit geval zou de *meest recente* eigenschaps waarde het vorige overschrijven. 

> [!TIP]
> Neem contact op met het Time Series Insights-team voor meer hulp.

> [!WARNING] 
> * In gevallen waarin dubbele eigenschappen aanwezig zijn in dezelfde (enkelvoudige) nettolading als gevolg van afvlakking of een ander mechanisme, wordt de meest recente waarde van > eigenschap opgeslagen en worden alle vorige waarden overschreven.
> * Een reeks gecombineerde gebeurtenissen overschrijft elkaar niet.

## <a name="next-steps"></a>Volgende stappen

* Lees [Azure time series Insights voorbeeld query syntaxis](./time-series-insights-query-data-csharp.md)als u deze richt lijnen in de praktijk wilt plaatsen. Meer informatie over de query syntaxis voor de Time Series Insights preview- [rest API](https://docs.microsoft.com/rest/api/time-series-insights/preview) voor gegevens toegang.

* Combi neer de aanbevolen procedures voor JSON met de [Time Series model](./time-series-insights-update-how-to-tsm.md).
