---
title: Vormgebeurtenissen - Azure Time Series Insights | Microsoft Documenten
description: Meer informatie over aanbevolen procedures en het vormgeven van gebeurtenissen voor query's in Azure Time Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: 99a2f32c3f76d7fec475c9b299f7208b4db29cfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650920"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Gebeurtenissen vormgeven met de preview-voor-dag van Azure Time Series Insights

In dit artikel worden aanbevolen procedures gedefinieerd om uw JSON-payloads vorm te geven voor opname in Azure Time Series Insights en om de efficiëntie van uw Preview-query's te maximaliseren.

## <a name="best-practices"></a>Aanbevolen procedures

U het beste zorgvuldig overwegen hoe u gebeurtenissen naar uw Time Series Insights Preview-omgeving verzendt. 

Algemene best practices zijn onder andere:

* Stuur gegevens zo efficiënt mogelijk via het netwerk.
* Sla uw gegevens op een manier op die u helpt deze beter in te bouwen voor uw scenario.

Houd u voor de beste queryprestaties aan de volgende vuistregels:

* Stuur geen onnodige eigendommen. Time Series Insights Preview rekeningen op gebruik. U het beste alleen de gegevens opslaan en verwerken die u opvraagt.
* Instantievelden gebruiken voor statische gegevens. Deze praktijk helpt om te voorkomen dat statische gegevens via het netwerk worden verzonden. Instantievelden, een onderdeel van het tijdreeksmodel, werken als referentiegegevens in de Time Series Insights-service die algemeen beschikbaar is. Lees [Time Series Model](./time-series-insights-update-tsm.md)voor meer informatie over instantievelden.
* Dimensieeigenschappen delen tussen twee of meer gebeurtenissen. Deze praktijk helpt u om gegevens efficiënter via het netwerk te verzenden.
* Gebruik geen deep array nesten. Time Series Insights Preview ondersteunt maximaal twee niveaus van geneste arrays die objecten bevatten. Time Series Insights Preview maakt arrays in berichten af in meerdere gebeurtenissen met eigenschapswaardeparen.
* Als er slechts een paar maatregelen bestaan voor alle of de meeste gebeurtenissen, is het beter om deze metingen als afzonderlijke eigenschappen binnen hetzelfde object te verzenden. Als u ze afzonderlijk verzendt, worden het aantal gebeurtenissen verminderd en kunnen de queryprestaties worden verbeterd omdat er minder gebeurtenissen hoeven te worden verwerkt.

## <a name="column-flattening"></a>Kolomafvlakking

Tijdens inname worden payloads die geneste objecten bevatten, afgevlakt, zodat de kolomnaam een enkele waarde is met een aflineator.

* Bijvoorbeeld, de volgende geneste JSON:

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   Wordt: `data_flow` wanneer afgevlakt.

> [!IMPORTANT]
> * Azure Time Series Insights Preview`_`gebruikt onderdoelpunten ( ) voor kolomafbakening.
> * Let op het verschil met algemene`.`beschikbaarheid die in plaats daarvan perioden ( ) gebruikt.

Meer complexe scenario's worden hieronder geïllustreerd.

#### <a name="example-1"></a>Voorbeeld 1:

Het volgende scenario heeft twee (of meer) apparaten die de metingen (signalen) verzenden: *Stroomsnelheid*, *motoroliedruk*, *temperatuur*en *vochtigheid*.

Er wordt één Azure IoT Hub-bericht verzonden waarin de buitenste array een gedeeld gedeelte van gemeenschappelijke dimensiewaarden bevat (let op de twee apparaatvermeldingen in het bericht).

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

**Afhaalrestaurants:**

* Het voorbeeld JSON heeft een externe array die gebruikmaakt van [time series instance-gegevens](./time-series-insights-update-tsm.md#time-series-model-instances) om de efficiëntie van het bericht te verhogen. Hoewel de apparaatmetagegevens van Time Series Instances waarschijnlijk niet zullen worden gewijzigd, biedt het vaak nuttige eigenschappen voor gegevensanalyse.

* De JSON combineert twee of meer berichten (een van elk apparaat) in een enkele payload besparing op bandbreedte in de tijd.

* Afzonderlijke seriegegevenspunten voor elk apparaat worden gecombineerd tot één **reeksattribuut,** waardoor updates voor elk apparaat continu hoeven te worden gestreamd.

> [!TIP]
> Als u het aantal berichten wilt verminderen dat nodig is om gegevens te verzenden en telemetrie efficiënter te maken, u overwegen om gemeenschappelijke dimensiewaarden en metagegevens van timeseries-instanties in één JSON-payload te plaatsen.

#### <a name="time-series-instance"></a>Instantie tijdreeks 

Laten we eens kijken hoe u [Time Series Instance kunt](./time-series-insights-update-tsm.md#time-series-model-instances) gebruiken om uw JSON beter vorm te geven. 

> [!NOTE]
> De [time series ID's](./time-series-insights-update-how-to-id.md) hieronder zijn *deviceIds*.

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

Time Series Insights Preview voegt een tabel (na afvlakking) toe tijdens de querytijd. De tabel bevat extra kolommen, zoals **Type**.

| deviceId  | Type | L1 | L2 | tijdstempel | series_Flow Tarief ft3/s | series_Engine oliedruk psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | Simulator | Batterijsysteem | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | Simulator |   Batterijsysteem |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA GEMEENSCHAPPELIJK | Simulator |    Batterijsysteem |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  De voorgaande tabel vertegenwoordigt de queryweergave in de [Time Series Preview Explorer](./time-series-insights-update-explorer.md).

**Afhaalrestaurants:**

* In het voorgaande voorbeeld worden statische eigenschappen opgeslagen in Time Series Insights Preview om gegevens die via het netwerk worden verzonden te optimaliseren.
* Time Series Insights Preview-gegevens worden tijdens querytijd samengevoegd via de time series-id die in de instantie is gedefinieerd.
* Er worden twee lagen nesten gebruikt. Dit nummer is het meest dat Time Series Insights Preview ondersteunt. Het is van cruciaal belang om diep geneste arrays te vermijden.
* Omdat er weinig maatregelen zijn, worden ze verzonden als afzonderlijke eigenschappen binnen hetzelfde object. In het voorbeeld zijn **series_Flow Rate psi**, **series_Engine Oil Pressure psi**en **series_Flow Rate ft3/s** unieke kolommen.

>[!IMPORTANT]
> Instantievelden worden niet opgeslagen met telemetrie. Ze worden opgeslagen met metadata in het Time Series Model.

#### <a name="example-2"></a>Voorbeeld 2:

Overweeg de volgende JSON:

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

In het bovenstaande voorbeeld `data["flow"]` zou de afgeplatte eigenschap `data_flow` een naamgevingsbotsing met de eigenschap opleveren.

In dit geval zou de *laatste* eigenschapwaarde de eerdere waarde overschrijven. 

> [!TIP]
> Neem contact op met het Time Series Insights-team voor meer hulp!

> [!WARNING] 
> * In gevallen waarin dubbele eigenschappen aanwezig zijn in dezelfde (enkelvoud) gebeurtenispayload als gevolg van afvlakking of een ander mechanisme, wordt de laatste > eigenschapswaarde opgeslagen, waarbij eerdere waarden worden overschreven.
> * Reeks gecombineerde gebeurtenissen zullen elkaar niet overschrijven.

## <a name="next-steps"></a>Volgende stappen

* Als u deze richtlijnen in de praktijk wilt brengen, leest u [de syntaxis van de querysyntaxis van Azure Time Series Insights Preview](./time-series-insights-query-data-csharp.md). U vindt meer informatie over de querysyntaxis voor de TIME Series Insights [Preview REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview) voor gegevenstoegang.

* Combineer JSON best practices met [How to Time Series Model](./time-series-insights-update-how-to-tsm.md).
