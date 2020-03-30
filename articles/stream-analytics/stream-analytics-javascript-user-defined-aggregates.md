---
title: JavaScript-door de gebruiker gedefinieerde aggregaten in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u geavanceerde querymechanica uitvoeren met door JavaScript gedefinieerde aggregaten in Azure Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.openlocfilehash: c509d174787a58abeee33e039eb7bbbcbcb43f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531731"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates"></a>Azure Stream Analytics JavaScript-door de gebruiker gedefinieerde aggregaten
 
Azure Stream Analytics ondersteunt door de gebruiker gedefinieerde aggregaten (UDA) die zijn geschreven in JavaScript, waarmee u complexe stateful bedrijfslogica implementeren. Binnen UDA hebt u volledige controle over de statusgegevensstructuur, statusaccumulatie, statusdecumulatie en geaggregeerde resultaatberekening. Het artikel introduceert de twee verschillende JavaScript UDA-interfaces, stappen om een UDA te maken en hoe UDA worden gebruikt met vensterbewerkingen in Stream Analytics-query.The article introduces the two different JavaScript UDA interfaces, steps to create a UDA, and how to use UDA with window-based operations in Stream Analytics query.

## <a name="javascript-user-defined-aggregates"></a>Door JavaScript door de gebruiker gedefinieerde aggregaten

Een door de gebruiker gedefinieerd aggregaat wordt boven op een tijdvensterspecificatie gebruikt om de gebeurtenissen in dat venster samen te voegen en één resultaatwaarde te produceren. Er zijn twee soorten UDA-interfaces die Stream Analytics vandaag ondersteunt, AccumulateOnly en AccumulateDeaccumulate. Beide soorten UDA kunnen worden gebruikt door Tumbling, Hopping, Sliding en Session Window. AccumulateDeaccumulate UDA presteert beter dan AccumulateOnly UDA bij gebruik in combinatie met Hopping, Sliding en Session Window. U kiest een van de twee typen op basis van het algoritme dat u gebruikt.

### <a name="accumulateonly-aggregates"></a>AccumulateOnly aggregaten

AccumulateOnly aggregaten kunnen alleen nieuwe gebeurtenissen in de status verzamelen, het algoritme staat deaccumulatie van waarden niet toe. Kies dit geaggregeerde type wanneer het niet mogelijk is om een gebeurtenisgegevens uit de statuswaarde te deaccumuleren. Hieronder volgt de JavaScript-sjabloon voor AccumulatOnly-aggregaten:

```JavaScript
// Sample UDA which state can only be accumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

### <a name="accumulatedeaccumulate-aggregates"></a>Verzamelverzamelaggregaten

Met accumulerende aggregaten kan bijvoorbeeld een verzameling van een eerdere geaccumuleerde waarde uit de status worden verwijderd, een sleutelwaardepaar uit een lijst met gebeurteniswaarden verwijderen of een waarde aftrekken van een staat van somaggregaat. Hieronder volgt de JavaScript-sjabloon voor accumulateaccumulerenaggregaten:

```JavaScript
// Sample UDA which state can be accumulated and deaccumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.deaccumulate = function (value, timestamp) {
        this.state -= value;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

## <a name="uda---javascript-function-declaration"></a>UDA - JavaScript-functiedeclaratie

Elke JavaScript UDA wordt gedefinieerd door een functieobjectdeclaratie. Hieronder volgen de belangrijkste elementen in een UDA-definitie.

### <a name="function-alias"></a>Functiealias

Functiealias is de UDA-id. Wanneer u wordt opgeroepen in Stream Analytics-query, gebruikt u doma-alias altijd samen met een 'uda'. 'com.microsoft.intune.mam'.

### <a name="function-type"></a>Functietype

Voor UDA moet het functietype **Javascript UDA**zijn.

### <a name="output-type"></a>Uitvoertype

Een specifiek type dat de functie Stream Analytics ondersteunde, of 'Any' als u het type in uw query wilt verwerken.

### <a name="function-name"></a>Functienaam

De naam van dit object Function. De functienaam moet overeenkomen met de UDA-alias.

### <a name="method---init"></a>Methode - init()

De methode init() initialiseert de status van het aggregaat. Deze methode wordt aangeroepen wanneer het venster wordt gestart.

### <a name="method--accumulate"></a>Methode – accumuleren()

De methode accumulate() berekent de UDA-status op basis van de vorige status en de huidige gebeurteniswaarden. Deze methode wordt aangeroepen wanneer een gebeurtenis een tijdvenster binnenkomt (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW of SESSIONWINDOW).

### <a name="method--deaccumulate"></a>Methode – deaccumulate()

De methode deaccumulate() berekent de status opnieuw op basis van de vorige status en de huidige gebeurteniswaarden. Deze methode wordt aangeroepen wanneer een gebeurtenis een SCHUIFVENSTER of SESSIONWINDOW verlaat.

### <a name="method--deaccumulatestate"></a>Methode – deaccumulateState()

De methode deaccumulateState() berekent de status opnieuw op basis van de vorige status en de status van een hop. Deze methode wordt aangeroepen wanneer een reeks gebeurtenissen een HOPPINGWINDOW verlaat.

### <a name="method--computeresult"></a>Methode – computeResult()

De methode computeResult() retourneert het geaggregeerde resultaat op basis van de huidige status. Deze methode wordt aangeroepen aan het einde van een tijdvenster (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW of SESSIONWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>JavaScript UDA ondersteunde invoer- en uitvoergegevenstypen
Raadpleeg voor JavaScript UDA-gegevenstypen sectie **Stream Analytics en JavaScript-typeconversie** van [JavaScript UDF's integreren.](stream-analytics-javascript-user-defined-functions.md)

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Een JavaScript UDA toevoegen vanuit de Azure-portal

Hieronder doorlopen we het proces van het maken van een UDA van Portal. Het voorbeeld dat we hier gebruiken is het berekenen van tijdgewogen gemiddelden.

Laten we nu een JavaScript UDA maken onder een bestaande ASA-taak door stappen te volgen.

1. Meld u aan bij azure portal en zoek uw bestaande Stream Analytics-taak.
1. Klik vervolgens op de koppeling Functies onder **JOB TOPOLOGY**.
1. Klik op het pictogram **Toevoegen** om een nieuwe functie toe te voegen.
1. Selecteer in de nieuwe functieweergave **JavaScript UDA** als functietype en vervolgens wordt er een standaard UDA-sjabloon weergegeven in de editor.
1. Vul "TWA" in als uda-alias en wijzig de functie-implementatie als volgt:

    ```JavaScript
    // Sample UDA which calculate Time-Weighted Average of incoming values.
    function main() {
        this.init = function () {
            this.totalValue = 0.0;
            this.totalWeight = 0.0;
        }

        this.accumulate = function (value, timestamp) {
            this.totalValue += value.level * value.weight;
            this.totalWeight += value.weight;

        }

        // Uncomment below for AccumulateDeaccumulate implementation
        /*
        this.deaccumulate = function (value, timestamp) {
            this.totalValue -= value.level * value.weight;
            this.totalWeight -= value.weight;
        }

        this.deaccumulateState = function (otherState){
            this.state -= otherState.state;
            this.totalValue -= otherState.totalValue;
            this.totalWeight -= otherState.totalWeight;
        }
        */

        this.computeResult = function () {
            if(this.totalValue == 0) {
                result = 0;
            }
            else {
                result = this.totalValue/this.totalWeight;
            }
            return result;
        }
    }
    ```

1. Zodra u op de knop Opslaan klikt, wordt uw UDA weergegeven in de functielijst.

1. Klik op de nieuwe functie TWA, u de functiedefinitie controleren.

## <a name="calling-javascript-uda-in-asa-query"></a>Bellen naar JavaScript UDA in ASA-query

Bewerk in Azure-portal en open uw taak de functie Query en Call TWA() met een mandaatvoorvoegsel 'uda'. Bijvoorbeeld:

```SQL
WITH value AS
(
    SELECT
    NoiseLevelDB as level,
    DurationSecond as weight
FROM
    [YourInputAlias] TIMESTAMP BY EntryTime
)
SELECT
    System.Timestamp as ts,
    uda.TWA(value) as NoseDoseTWA
FROM value
GROUP BY TumblingWindow(minute, 5)
```

## <a name="testing-query-with-uda"></a>Testquery met UDA

Maak een lokaal JSON-bestand met onderstaande inhoud, upload het bestand naar de taak Analytics en test bovenstaande query.

```JSON
[
  {"EntryTime": "2017-06-10T05:01:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 22.0},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 81, "DurationSecond": 37.8},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 85, "DurationSecond": 26.3},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 95, "DurationSecond": 13.7},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 88, "DurationSecond": 10.3},
  {"EntryTime": "2017-06-10T05:05:00-07:00", "NoiseLevelDB": 103, "DurationSecond": 5.5},
  {"EntryTime": "2017-06-10T05:06:00-07:00", "NoiseLevelDB": 99, "DurationSecond": 23.0},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 1.76},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 79, "DurationSecond": 17.9},
  {"EntryTime": "2017-06-10T05:08:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 27.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 91, "DurationSecond": 17.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 115, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 28.3},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 55, "DurationSecond": 18.2},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 25.8},
  {"EntryTime": "2017-06-10T05:11:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 11.4},
  {"EntryTime": "2017-06-10T05:12:00-07:00", "NoiseLevelDB": 89, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 112, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 9.7},
  {"EntryTime": "2017-06-10T05:18:00-07:00", "NoiseLevelDB": 96, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 0.99},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 113, "DurationSecond": 25.1},
  {"EntryTime": "2017-06-10T05:22:00-07:00", "NoiseLevelDB": 110, "DurationSecond": 5.3}
]
```

## <a name="get-help"></a>Help opvragen

Meer hulp vindt u mogelijk op het [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaginformatie over de querytaal van Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Verwijzing naar REST-API voor Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
