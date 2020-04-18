---
title: Aanbevolen procedures voor het vormgeven van JSON - Azure Time Series Insights-query's | Microsoft Documenten
description: Meer informatie over het verbeteren van de efficiëntie van uw Azure Time Series Insights-query door JSON vorm te geven.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 04/17/2020
ms.custom: seodec18
ms.openlocfilehash: 63a708f80ad18309269e37c354b047c304a260d3
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641295"
---
# <a name="shape-json-to-maximize-query-performance"></a>VORM JSON om de queryprestaties te maximaliseren

In dit artikel vindt u richtlijnen voor het vormgeven van JSON om de efficiëntie van uw Azure Time Series Insights-query's te maximaliseren.

## <a name="video"></a>Video

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Leer best practices voor het vormgeven van JSON om aan uw opslagbehoeften te voldoen.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Aanbevolen procedures

Denk na over hoe je evenementen naar Time Series Insights stuurt. Namelijk, je altijd:

1. Stuur gegevens zo efficiënt mogelijk via het netwerk.
1. Zorg ervoor dat uw gegevens worden opgeslagen op een manier, zodat u aggregaties uitvoeren die geschikt zijn voor uw scenario.
1. Zorg ervoor dat u de maximale eigenschapslimieten van Time Series Insights niet bereikt:
   - 600 eigenschappen (kolommen) voor S1-omgevingen.
   - 800 eigenschappen (kolommen) voor S2-omgevingen.

> [!TIP]
> Bekijk [limieten en planning](time-series-insights-update-plan.md) in Azure Time Series Insights Preview.

De volgende richtlijnen helpen om de best mogelijke queryprestaties te garanderen:

1. Gebruik geen dynamische eigenschappen, zoals een tag-id, als eigenschapsnaam. Dit gebruik draagt bij aan het bereiken van de maximale eigenschappenlimiet.
1. Stuur geen onnodige eigendommen. Als een queryeigenschap niet vereist is, u deze het beste niet verzenden. Op deze manier vermijdt u opslagbeperkingen.
1. Gebruik [referentiegegevens](time-series-insights-add-reference-data-set.md) om te voorkomen dat statische gegevens via het netwerk worden verzonden.
1. Deel dimensieeigenschappen tussen meerdere gebeurtenissen om gegevens efficiënter via het netwerk te verzenden.
1. Gebruik geen deep array nesten. Time Series Insights ondersteunt maximaal twee niveaus van geneste arrays die objecten bevatten. Time Series Insights maakt arrays in de berichten af in meerdere gebeurtenissen met eigenschapswaardeparen.
1. Als er slechts een paar maatregelen bestaan voor alle of de meeste gebeurtenissen, is het beter om deze metingen als afzonderlijke eigenschappen binnen hetzelfde object te verzenden. Als u ze afzonderlijk verzendt, worden het aantal gebeurtenissen verminderd en kunnen de queryprestaties worden verbeterd omdat er minder gebeurtenissen hoeven te worden verwerkt. Wanneer er verschillende maatregelen zijn, minimaliseert het verzenden van deze als waarden in één eigenschap de mogelijkheid om de maximale eigenschapslimiet te bereiken.

## <a name="example-overview"></a>Voorbeeldoverzicht

In de volgende twee voorbeelden wordt uitgelegd hoe u gebeurtenissen verzenden om de eerdere aanbevelingen te markeren. Na elk voorbeeld u bekijken hoe de aanbevelingen zijn toegepast.

De voorbeelden zijn gebaseerd op een scenario waarin meerdere apparaten metingen of signalen verzenden. Metingen of signalen kunnen de stroomsnelheid, motoroliedruk, temperatuur en vochtigheid zijn. In het eerste voorbeeld zijn er een paar metingen op alle apparaten. Het tweede voorbeeld heeft veel apparaten, en elk apparaat stuurt veel unieke metingen.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Scenario één: Er bestaan slechts een paar metingen

> [!TIP]
> We raden u aan elke meting of signaal als een afzonderlijke eigenschap of kolom te verzenden.

In het volgende voorbeeld is er één Azure IoT Hub-bericht waarin de buitenste array een gedeeld gedeelte van gemeenschappelijke dimensiewaarden bevat. De buitenste array gebruikt referentiegegevens om de efficiëntie van het bericht te verhogen. Referentiegegevens bevatten apparaatmetagegevens die niet bij elke gebeurtenis worden gewijzigd, maar bieden wel nuttige eigenschappen voor gegevensanalyse. Het batchnen van gemeenschappelijke dimensiewaarden en het gebruik van referentiegegevens wordt opgeslagen in bytes die via de draad worden verzonden, waardoor het bericht efficiënter wordt.

Overweeg de volgende JSON-payload die naar uw Time Series Insights GA-omgeving wordt verzonden met behulp van een [IoT-apparaatberichtobject](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet) dat is geserialiseerd in JSON wanneer het naar azure-cloud wordt verzonden:


```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

* Referentiegegevenstabel met het apparaat van de **sleuteleigenschapId:**

   | deviceId | messageId | deviceLocatie |
   | --- | --- | --- |
   | FXXX FXXX | REGELGEGEVENS\_ | EU |
   | FYYY (FYYY) | REGELGEGEVENS\_ | VS |

* Gebeurtenistabel Time Series Insights, na afvlakking:

   | deviceId | messageId | deviceLocatie | tijdstempel | Serie. Stroomsnelheid ft3/s | Serie. Motorolie druk psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX FXXX | REGELGEGEVENS\_ | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX FXXX | REGELGEGEVENS\_ | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY (FYYY) | REGELGEGEVENS\_ | VS | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

> [!NOTE]
> - De **deviceId-kolom** fungeert als de kolomkop voor de verschillende apparaten in een vloot. Als u de **waarde van de deviceId** een eigen eigenschapsnaam maakt, worden de totale apparaten beperkt tot 595 (voor S1-omgevingen) of 795 (voor S2-omgevingen) met de andere vijf kolommen.
> - Onnodige eigenschappen worden vermeden (bijvoorbeeld het merk en model informatie). Omdat de eigenschappen in de toekomst niet worden opgevraagd, zorgt het elimineren ervan voor een betere netwerk- en opslagefficiëntie.
> - Referentiegegevens worden gebruikt om het aantal bytes dat via het netwerk wordt overgedragen, te verminderen. De twee kenmerken **messageId** en **deviceLocation** worden samengevoegd met behulp van de sleutel eigenschap **deviceId**. Deze gegevens worden samengevoegd met de telemetriegegevens op inhet begin en worden vervolgens opgeslagen in Time Series Insights voor query's.
> - Er worden twee lagen nesten gebruikt, wat de maximale hoeveelheid nesting is die wordt ondersteund door Time Series Insights. Het is van cruciaal belang om diep geneste arrays te vermijden.
> - Metingen worden verzonden als afzonderlijke eigenschappen binnen hetzelfde object omdat er weinig maatregelen zijn. Hier, **serie. Flow Rate psi** en **serie. Motor oliedruk ft3/s** zijn unieke kolommen.

## <a name="scenario-two-several-measures-exist"></a>Scenario twee: Er bestaan verschillende maatregelen

> [!TIP]
> We raden u aan metingen te verzenden als 'type', 'eenheid' en 'waarde'-tuples.

Voorbeeld JSON payload:

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 1.0172575712203979
            },
            {
                "tagId": "oilPressure",
                "value": 49.2
            },
            {
                "tagId": "pumpRate",
                "value": 2.445906400680542
            },
            {
                "tagId": "oilPressure",
                "value": 34.7
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 0.58015072345733643
            },
            {
                "tagId": "oilPressure",
                "value": 22.2
            }
        ]
    }
]
```

* Referentiegegevenstabel met de belangrijkste eigenschappen **deviceId** en **series.tagId:**

   | deviceId | series.tagId | messageId | deviceLocatie | type | eenheid |
   | --- | --- | --- | --- | --- | --- |
   | FXXX FXXX | pompTarief | REGELGEGEVENS\_ | EU | Debiet | ft3/s |
   | FXXX FXXX | olieDruk | REGELGEGEVENS\_ | EU | Motoroliedruk | psi |
   | FYYY (FYYY) | pompTarief | REGELGEGEVENS\_ | VS | Debiet | ft3/s |
   | FYYY (FYYY) | olieDruk | REGELGEGEVENS\_ | VS | Motoroliedruk | psi |

* Gebeurtenistabel Time Series Insights, na afvlakking:

   | deviceId | series.tagId | messageId | deviceLocatie | type | eenheid | tijdstempel | reeksen.waarde |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX FXXX | pompTarief | REGELGEGEVENS\_ | EU | Debiet | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX FXXX | olieDruk | REGELGEGEVENS\_ | EU | Motoroliedruk | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX FXXX | pompTarief | REGELGEGEVENS\_ | EU | Debiet | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX FXXX | olieDruk | REGELGEGEVENS\_ | EU | Motoroliedruk | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY (FYYY) | pompTarief | REGELGEGEVENS\_ | VS | Debiet | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY (FYYY) | olieDruk | REGELGEGEVENS\_ | VS | Motoroliedruk | psi | 2018-01-17T01:18:00Z | 22.2 |

> [!NOTE]
> - De kolommen **deviceId** en **series.tagId** dienen als de kolomkoppen voor de verschillende apparaten en tags in een vloot. Het gebruik van elk als eigen kenmerk beperkt de query tot 594 (voor S1-omgevingen) of 794 (voor S2-omgevingen) totaal aantal apparaten met de andere zes kolommen.
> - Onnodige eigenschappen werden vermeden, om de reden aangehaald in het eerste voorbeeld.
> - Referentiegegevens worden gebruikt om het aantal bytes dat via het netwerk wordt overgedragen te verminderen door **deviceId**te introduceren , dat wordt gebruikt voor het unieke **paar messageId** en **deviceLocation**. De samengestelde **sleutelserie.tagId** wordt gebruikt voor het unieke **paar type** en **eenheid.** Met de samengestelde sleutel kunnen de **deviceId** en **series.tagId-paar** worden gebruikt om te verwijzen naar vier waarden: **messageId, deviceLocation, type** en **unit**. Deze gegevens worden samengevoegd met de telemetriegegevens op ingress tijd. Het wordt vervolgens opgeslagen in Time Series Insights voor query's.
> - Twee lagen nesten worden gebruikt, om de reden aangehaald in het eerste voorbeeld.

### <a name="for-both-scenarios"></a>Voor beide scenario's

Voor een eigenschap met een groot aantal mogelijke waarden u het beste als afzonderlijke waarden binnen één kolom verzenden in plaats van een nieuwe kolom voor elke waarde te maken. Uit de vorige twee voorbeelden:

  - In het eerste voorbeeld hebben een paar eigenschappen verschillende waarden, dus het is geschikt om elk een afzonderlijke eigenschap te maken.
  - In het tweede voorbeeld worden de metingen niet opgegeven als afzonderlijke eigenschappen. In plaats daarvan zijn ze een array van waarden of metingen onder een algemene reeks eigenschap. De nieuwe **sleuteltagId** wordt verzonden, waardoor de nieuwe **kolomreeks.tagId** in de afgevlakte tabel wordt gemaakt. Het **nieuwe eigenschappentype** en **de eenheid** worden gemaakt met behulp van referentiegegevens, zodat de eigenschapslimiet niet wordt bereikt.

## <a name="next-steps"></a>Volgende stappen

- Lees meer over het verzenden van [IoT Hub-apparaatberichten naar de cloud.](../iot-hub/iot-hub-devguide-messages-construct.md)

- Lees [de querysyntaxis van Azure Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax) voor meer informatie over de syntaxis van query's voor de REST-API voor gegevenstoegang voor timeseries Insights.

- Meer informatie over [het vormgeven van gebeurtenissen](./time-series-insights-send-events.md).
