---
title: Aanbevolen procedures voor het vorm geven van JSON in Azure Time Series Insights query's | Microsoft Docs
description: Meer informatie over het verbeteren van de efficiëntie van Azure Time Series Insights query's.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 08/09/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: 48e09a64812f7552bd79c529138db693df283790
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947153"
---
# <a name="shape-json-to-maximize-query-performance"></a>JSON van shape om query prestaties te maximaliseren 

In dit artikel vindt u richt lijnen voor het optimaliseren van de effectiviteit van uw Azure Time Series Insights query's.

## <a name="video"></a>Video

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Leer de aanbevolen procedures voor het vorm geven van JSON om te voldoen aan uw opslag behoeften.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Aanbevolen procedures
Denk na over hoe u gebeurtenissen naar Time Series Insights verzendt. Dat wil zeggen dat u altijd:

1. gegevens zo efficiënt mogelijk via het netwerk verzenden.
1. Zorg ervoor dat uw gegevens op een zodanige manier worden opgeslagen dat u aggregaties kunt uitvoeren die geschikt zijn voor uw scenario.
1. Zorg ervoor dat u de Time Series Insights maximum eigenschaps limieten van hebt bereikt:
   - 600-eigenschappen (kolommen) voor S1-omgevingen.
   - 800-eigenschappen (kolommen) voor S2-omgevingen.

De volgende richt lijnen helpen de best mogelijke query prestaties te garanderen:

1. Gebruik geen dynamische eigenschappen, zoals een tag-ID, als eigenschaps naam. Dit gebruik draagt bij aan het bereiken van de maximum limiet voor eigenschappen.
1. Geen overbodige eigenschappen niet verzenden. Als een query-eigenschap niet vereist is, kunt u deze het beste niet verzenden. Zo voor komt u opslag beperkingen.
1. Gebruik [referentie gegevens](time-series-insights-add-reference-data-set.md) om te voor komen dat statische gegevens via het netwerk worden verzonden.
1. Deel dimensie-eigenschappen over meerdere gebeurtenissen om gegevens efficiënter via het netwerk te verzenden.
1. Gebruik geen grondige matrix nesten. Time Series Insights ondersteunt Maxi maal twee niveaus van geneste matrices die objecten bevatten. Time Series Insights worden matrices in de berichten samengevoegd in meerdere gebeurtenissen met eigenschaps waarde-paren.
1. Als er slechts een paar metingen bestaan voor alle of de meeste gebeurtenissen, is het beter om het verzenden van deze metingen als afzonderlijke eigenschappen binnen hetzelfde object. Als u ze afzonderlijk verzendt, vermindert het aantal gebeurtenissen en kunnen query's sneller worden uitgevoerd omdat er minder gebeurtenissen moeten worden verwerkt. Als er verschillende metingen zijn, minimaliseert het verzenden van deze als waarden in één eigenschap de mogelijkheid om de maximale eigenschaps limiet te bereiken.

## <a name="example-overview"></a>Voor beeld-overzicht

De volgende twee voor beelden laten zien hoe u gebeurtenissen kunt verzenden om de vorige aanbevelingen te markeren. Na elk voor beeld kunt u zien hoe de aanbevelingen zijn toegepast.

De voor beelden zijn gebaseerd op een scenario waarbij meerdere apparaten metingen of signalen verzenden. Metingen of signalen kunnen een stroom snelheid, olie belasting, Tempe ratuur en vochtigheid zijn. In het eerste voor beeld zijn er een aantal metingen op alle apparaten. Het tweede voor beeld heeft veel apparaten en elk apparaat verstuurt veel unieke metingen.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Scenario 1: Er bestaan slechts een paar metingen

> [!TIP]
> U kunt het beste elke meting of elk signaal verzenden als een afzonderlijke eigenschap of kolom.

In het volgende voor beeld is er één Azure IoT Hub-bericht waarbij de buitenste matrix een gedeeld gedeelte van algemene dimensie waarden bevat. De buitenste matrix gebruikt referentie gegevens om de efficiëntie van het bericht te verg Roten. Referentie gegevens bevatten meta gegevens van apparaten die niet worden gewijzigd bij elke gebeurtenis, maar biedt nuttige eigenschappen voor gegevens analyse. Het batchiseren van algemene dimensie waarden en het gebruiken van referentie gegevens bespaart u op bytes die via de kabel worden verzonden, waardoor het bericht efficiënter wordt.

Voor beeld van JSON-nettolading:

```json
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

* Referentie gegevens tabel die de sleutel eigenschap **deviceId**heeft:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LIJN\_DATA | EU |
   | FYYY | LIJN\_DATA | US |

* Time Series Insights gebeurtenis tabel na afvlakking:

   | deviceId | messageId | deviceLocation | timestamp | reeks. Stroom tarief ft3/s | reeks. Engine oliedruk psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LIJN\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34,7 |
   | FXXX | LIJN\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49,2 |
   | FYYY | LIJN\_DATA | US | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

Opmerkingen voor deze twee tabellen:

- De kolom **deviceId** fungeert als de kolomkop voor de verschillende apparaten in een vloot. Als u voor de deviceId-waarde een eigen eigenschaps naam instelt, wordt het totale aantal apparaten beperkt tot 595 (voor S1-omgevingen) of 795 (voor S2-omgevingen) met de andere vijf kolommen.
- Onnodige eigenschappen worden vermeden, bijvoorbeeld het merk-en model gegevens. Omdat de eigenschappen niet in de toekomst worden opgevraagd, is het mogelijk om de netwerk-en opslag efficiëntie te verbeteren.
- Referentie gegevens worden gebruikt om het aantal bytes dat via het netwerk wordt overgedragen te verminderen. De twee kenmerken **messageId** en **deviceLocation** worden gekoppeld met behulp van de sleutel eigenschap **deviceId**. Deze gegevens zijn gekoppeld aan de telemetriegegevens bij ingangs tijd en worden vervolgens opgeslagen in Time Series Insights voor het uitvoeren van query's.
- Er worden twee geneste lagen gebruikt. Dit is de maximale hoeveelheid nesten die door Time Series Insights wordt ondersteund. Het is essentieel om te voorkomen dat diep geneste matrices.
- Metingen worden als afzonderlijke eigenschappen binnen hetzelfde object verzonden, omdat er slechts enkele metingen zijn. Hier kunt u de **reeks. Flow-rate psi** en **serie. De FT3/s van de engine olie drukken** zijn unieke kolommen.

## <a name="scenario-two-several-measures-exist"></a>Scenario twee: Er zijn verschillende metingen

> [!TIP]
> U wordt aangeraden metingen te verzenden als ' type ', ' eenheid ' en ' waarde ' Tuples.

Voor beeld van JSON-nettolading:

```json
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

* Referentie gegevens tabel met de sleutel eigenschappen **deviceId** en **Series. tagId**:

   | deviceId | series.tagId | messageId | deviceLocation | type | eenheid |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LIJN\_GEGEVENS | EU | Stroom verhouding | ft3/s |
   | FXXX | oilPressure | LIJN\_GEGEVENS | EU | Olie druk van de motor | psi |
   | FYYY | pumpRate | LIJN\_GEGEVENS | VS | Stroom verhouding | ft3/s |
   | FYYY | oilPressure | LIJN\_GEGEVENS | VS | Olie druk van de motor | psi |

* Time Series Insights gebeurtenis tabel na afvlakking:

   | deviceId | series.tagId | messageId | deviceLocation | type | eenheid | timestamp | reeks. waarde |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LIJN\_GEGEVENS | EU | Stroom verhouding | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | LIJN\_GEGEVENS | EU | Olie druk van de motor | psi | 2018-01-17T01:17:00Z | 34,7 |
   | FXXX | pumpRate | LIJN\_GEGEVENS | EU | Stroom verhouding | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | LIJN\_GEGEVENS | EU | Olie druk van de motor | psi | 2018-01-17T01:17:00Z | 49,2 |
   | FYYY | pumpRate | LIJN\_GEGEVENS | VS | Stroom verhouding | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LIJN\_GEGEVENS | VS | Olie druk van de motor | psi | 2018-01-17T01:18:00Z | 22.2 |

Opmerkingen voor deze twee tabellen:

- De kolommen **deviceId** en **Series. tagId** fungeren als de kolom koppen voor de verschillende apparaten en tags in een vloot. Als een eigen kenmerk wordt gebruikt, wordt de query beperkt tot 594 (voor S1-omgevingen) of 794 (voor S2-omgevingen) met het totale aantal apparaten met de andere zes kolommen.
- Onnodige eigenschappen zijn vermeden, voor de reden die in het eerste voor beeld wordt vermeld.
- Referentie gegevens worden gebruikt voor het verminderen van het aantal bytes dat via het netwerk wordt overgedragen door de introductie van **deviceId**, dat wordt gebruikt voor de unieke combi natie van **messageId** en **deviceLocation**. De samengestelde sleutel **reeks. tagId** wordt gebruikt voor het unieke paar van het **type** en de **eenheid**. Met de samengestelde sleutel kan het **apparaat deviceId** en **Series. tagId** worden gebruikt om te verwijzen naar vier waarden: **messageId, deviceLocation, type** en **Unit**. Deze gegevens zijn gekoppeld aan de telemetriegegevens op het tijdstip van ingangs tijd. Het wordt vervolgens opgeslagen in Time Series Insights voor het uitvoeren van query's.
- Er worden twee geneste lagen gebruikt, voor de reden die in het eerste voor beeld wordt vermeld.

### <a name="for-both-scenarios"></a>Voor beide scenario's

Voor een eigenschap met een groot aantal mogelijke waarden kunt u het beste als DISTINCT-waarden verzenden binnen één kolom in plaats van een nieuwe kolom te maken voor elke waarde. Van de vorige twee voor beelden:

  - In het eerste voor beeld hebben enkele eigenschappen verschillende waarden, dus het is handig om elk een afzonderlijke eigenschap te maken.
  - In het tweede voor beeld worden de metingen niet opgegeven als afzonderlijke eigenschappen. In plaats daarvan zijn ze een matrix met waarden of metingen onder een algemene reeks eigenschap. De nieuwe Key **tagId** wordt verzonden, waarmee de nieuwe kolom reeks wordt gemaakt **. tagId** in de platte tabel. Het nieuwe **type** eigenschappen en de **eenheid** worden gemaakt met behulp van referentie gegevens, zodat de eigenschaps limiet niet wordt bereikt.

## <a name="next-steps"></a>Volgende stappen

- Lees [Azure time series Insights query syntaxis](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax) voor meer informatie over de query syntaxis voor de rest API time series Insights Data Access.
- Meer informatie [over het vorm](./time-series-insights-send-events.md)geven van gebeurtenissen.
