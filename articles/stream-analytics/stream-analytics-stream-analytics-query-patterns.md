---
title: Algemene query patronen in Azure Stream Analytics
description: In dit artikel worden een aantal veelgebruikte query patronen en ontwerpen beschreven die handig zijn voor Azure Stream Analytics taken.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/16/2019
ms.openlocfilehash: 61f9e128fa9299a743012e18882fe32591fdd3f0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369946"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Query voorbeelden voor algemene Stream Analytics gebruiks patronen

Query's in Azure Stream Analytics worden uitgedrukt in een SQL-achtige query taal. De taal constructies worden beschreven in de naslag gids voor de [Stream Analytics-query taal](/stream-analytics-query/stream-analytics-query-language-reference) . 

In het ontwerp van de query kunt u eenvoudige Pass-Through logica gebruiken om gebeurtenis gegevens van de ene invoer stroom te verplaatsen naar een uitvoer gegevens opslag, of een uitgebreide patroon vergelijking en tijdelijke analyse voor het berekenen van aggregaties over diverse tijd Vensters als in het [bouwen van een IOT-oplossing met behulp van stream Analytics](stream-analytics-build-an-iot-solution-using-stream-analytics.md) Guide. U kunt gegevens uit meerdere invoer koppelen om streaming-gebeurtenissen te combi neren en u kunt opzoeken op basis van statische referentie gegevens om de gebeurtenis waarden te verrijken. U kunt ook gegevens schrijven naar meerdere uitvoer.

In dit artikel vindt u een overzicht van oplossingen voor verschillende veelgebruikte query patronen op basis van Real-World-scenario's.

## <a name="work-with-complex-data-types-in-json-and-avro"></a>Werken met complexe gegevenstypen in JSON en Avro

Azure Stream Analytics ondersteunt het verwerken van gebeurtenissen in CSV-, JSON-en Avro-gegevens indelingen.

JSON en Avro kunnen complexe typen bevatten, zoals geneste objecten (records) of matrices. Raadpleeg het artikel [JSON-en Avro-gegevens parseren](stream-analytics-parsing-json.md) voor meer informatie over het werken met deze complexe gegevens typen.

## <a name="query-example-convert-data-types"></a>Query voorbeeld: gegevens typen converteren

**Beschrijving**: Geef de typen eigenschappen op de invoer stroom op. Het gewicht van de auto is bijvoorbeeld beschikbaar op de invoer stroom als teken reeksen en moet worden geconverteerd naar **int** om de **som te berekenen**.

**Invoer**:

| Maken | Tijd | Gewicht |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000 Z |"2000" |

**Uitvoer**:

| Maken | Gewicht |
| --- | --- |
| Honda |3000 |

**Oplossing**:

```SQL
    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**Uitleg**: gebruik een **cast** -instructie in het veld **Weight** om het gegevens type op te geven. Zie de lijst met ondersteunde gegevens typen in [gegevens typen (Azure stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Query voorbeeld: LIKE/niet gebruiken om patroon overeenkomsten te vergelijken

**Beschrijving**: Controleer of een veld waarde voor de gebeurtenis overeenkomt met een bepaald patroon.
Controleer bijvoorbeeld of het resultaat een licentie plaat retourneert dat begint met een en eindigt met 9.

**Invoer**:

| Maken | LicensePlate | Tijd |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**Uitvoer**:

| Maken | LicensePlate | Tijd |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**Oplossing**:

```SQL
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'
```

**Uitleg**: gebruik de instructie **like** om de waarde van het veld **LicensePlate** te controleren. Deze moet beginnen met de letter A, vervolgens een wille keurige teken reeks van nul of meer tekens hebben en eindigen met het cijfer 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Query voorbeeld: logica opgeven voor verschillende cases/waarden (CASE-instructies)

**Beschrijving**: Geef een andere berekening voor een veld op op basis van een bepaald criterium. Geef bijvoorbeeld een teken reeks beschrijving op voor het aantal auto's van hetzelfde merk dat is door gegeven, met een speciaal geval voor 1.

**Invoer**:

| Maken | Tijd |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**Uitvoer**:

| CarsPassed | Tijd |
| --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000 Z |
| 2 Toyota |2015-01-01T00:00:10.0000000 Z |

**Oplossing**:

```SQL
    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp() AS AsaTime
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**Uitleg**: de **Case** -expressie vergelijkt een expressie met een reeks eenvoudige expressies om het resultaat te bepalen. In dit voor beeld maakt het Voer tuig een aantal van 1 dat een andere teken reeks beschrijving heeft dan een ander aantal dan 1.

## <a name="query-example-send-data-to-multiple-outputs"></a>Query voorbeeld: gegevens verzenden naar meerdere uitvoer

**Beschrijving**: gegevens verzenden naar meerdere uitvoer doelen vanuit één taak. U kunt bijvoorbeeld gegevens analyseren voor een waarschuwing op basis van drempel en alle gebeurtenissen in Blob Storage archiveren.

**Invoer**:

| Maken | Tijd |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Honda |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**Output1**:

| Maken | Tijd |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Honda |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**Output2**:

| Maken | Tijd | Aantal |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000 Z |3 |

**Oplossing**:

```SQL
    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp() AS AsaTime,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3
```

**Uitleg**: de component **into** vertelt stream Analytics van welke uitvoer de gegevens moeten worden geschreven van deze instructie. De eerste query is een door gang van de gegevens die zijn ontvangen in een uitvoer met de naam **ArchiveOutput**. Met de tweede query worden enkele eenvoudige aggregatie en filters toegepast en worden de resultaten verzonden naar een downstream-waarschuwings systeem, **AlertOutput**.

U kunt ook de resultaten van de algemene tabel expressies (Cte's) (zoals **with** -instructies) opnieuw gebruiken in meerdere uitvoer instructies. Deze optie biedt het extra voor deel van het openen van minder lezers aan de invoer bron.

Bijvoorbeeld: 

```SQL
    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'
```

## <a name="query-example-count-unique-values"></a>Query voorbeeld: unieke waarden tellen

**Beschrijving**: het aantal unieke veld waarden tellen dat binnen een tijd venster in de stroom wordt weer gegeven. Hoe worden er bijvoorbeeld veel unieke auto's door gegeven via de telefoon stand in een periode van twee seconden?

**Invoer**:

| Maken | Tijd |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Honda |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**De uitvoer:**

| CountMake | Tijd |
| --- | --- |
| 2 |2015-01-01T00:00:02.000 Z |
| 1 |2015-01-01T00:00:04.000 Z |

**Oplossing:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP() AS AsaTIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```


**Uitleg:** 
**aantal (DISTINCT make)** retourneert het aantal afzonderlijke waarden in de kolom **maken** binnen een tijd venster.

## <a name="query-example-determine-if-a-value-has-changed"></a>Query voorbeeld: bepalen of een waarde is gewijzigd

**Beschrijving**: Bekijk een vorige waarde om te bepalen of deze anders is dan de huidige waarde. Is de vorige auto bijvoorbeeld op de niet-actieve weg hetzelfde als de huidige auto?

**Invoer**:

| Maken | Tijd |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |

**Uitvoer**:

| Maken | Tijd |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000 Z |

**Oplossing**:

```SQL
    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

**Uitleg**: gebruik **lag** om één gebeurtenis terug in de invoer stroom te bekijken en de waarde **maken** op te halen. Vergelijk deze vervolgens met de waarde maken voor de huidige gebeurtenis en **Voer** de gebeurtenis uit als deze verschillend zijn.

## <a name="query-example-find-the-first-event-in-a-window"></a>Query voorbeeld: de eerste gebeurtenis in een venster zoeken

**Beschrijving**: Zoek de eerste auto in elke periode van tien minuten.

**Invoer**:

| LicensePlate | Maken | Tijd |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

**Uitvoer**:

| LicensePlate | Maken | Tijd |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000 Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000 Z |

**Oplossing**:

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1
```

We gaan nu het probleem wijzigen en vinden de eerste auto van een bepaalde versie in elke periode van tien minuten.

| LicensePlate | Maken | Tijd |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000 Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000 Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

**Oplossing**:

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

## <a name="query-example-find-the-last-event-in-a-window"></a>Query voorbeeld: de laatste gebeurtenis in een venster zoeken

**Beschrijving**: Zoek de laatste auto in elke periode van tien minuten.

**Invoer**:

| LicensePlate | Maken | Tijd |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

**Uitvoer**:

| LicensePlate | Maken | Tijd |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000 Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

**Oplossing**:

```SQL
    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime
```

**Uitleg**: er zijn twee stappen in de query. Het eerste item vindt de laatste tijds tempel in Windows 10 minuten. De tweede stap neemt de resultaten van de eerste query samen met de oorspronkelijke stroom om de gebeurtenissen te vinden die overeenkomen met de laatste tijds tempels in elk venster. 

## <a name="query-example-locate-correlated-events-in-a-stream"></a>Query voorbeeld: gecorreleerde gebeurtenissen in een stroom zoeken

**Beschrijving**: gevonden gerelateerde gebeurtenissen in een stroom. Hebt u bijvoorbeeld twee opeenvolgende auto's van hetzelfde merk in de afgelopen 90 seconden de weg gekomen.

**Invoer**:

| Maken | LicensePlate | Tijd |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000 Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000 Z |

**Uitvoer**:

| Maken | Tijd | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000 Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000 Z |

**Oplossing**:

```SQL
    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**Uitleg**: gebruik **lag** om één gebeurtenis terug in de invoer stroom te bekijken en de waarde **maken** op te halen. Vergelijk het met de waarde **maken** in de huidige gebeurtenis en voer de gebeurtenis vervolgens uit als deze hetzelfde zijn. U kunt ook **vertraging** gebruiken om gegevens over de vorige auto op te halen.

## <a name="query-example-detect-the-duration-between-events"></a>Query voorbeeld: de duur tussen gebeurtenissen detecteren

**Beschrijving**: de duur van een bepaalde gebeurtenis zoeken. U kunt bijvoorbeeld een web-clickstream gebruiken om de tijd te bepalen die aan een functie is besteed.

**Invoer**:  

| Gebruiker | Functie | Gebeurtenis | Tijd |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Beginnen |2015-01-01T00:00:01.0000000 Z |
| user@location.com |RightMenu |Beëindigen |2015-01-01T00:00:08.0000000 Z |

**Uitvoer**:  

| Gebruiker | Functie | Duur |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Oplossing**:

```SQL
    SELECT
        [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
```

**Uitleg**: gebruik de **laatste** functie om de laatste **tijd** op te halen bij het **starten**van het gebeurtenis type. De **laatste** functie maakt gebruik **van Partition by [gebruiker]** om aan te geven dat het resultaat wordt berekend per unieke gebruiker. De query heeft een maximum drempel van 1 uur voor het tijds verschil tussen **Start** -en **Stop** gebeurtenissen, maar kan indien nodig worden geconfigureerd **(limiet duur (uur, 1)** .

## <a name="query-example-detect-the-duration-of-a-condition"></a>Query voorbeeld: de duur van een voor waarde detecteren
**Beschrijving**: Ontdek hoe lang een voor waarde heeft plaatsgevonden.
Stel bijvoorbeeld dat een bug heeft geresulteerd in alle auto's met een onjuist gewicht (meer dan 20.000 ponden) en de duur van die fout moet worden berekend.

**Invoer**:

| Maken | Tijd | Gewicht |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000 Z |25000 |
| Honda |2015-01-01T00:00:03.0000000 Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000 Z |25000 |
| Honda |2015-01-01T00:00:05.0000000 Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000 Z |25000 |
| Honda |2015-01-01T00:00:07.0000000 Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000 Z |2000 |

**Uitvoer**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000 Z |2015-01-01T00:00:07.000 Z |

**Oplossing**:

```SQL
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
```

**Uitleg**: gebruik **lag** om de invoer stroom 24 uur weer te geven en te zoeken naar instanties waarbij **StartFault** en **StopFault** worden omspannen met het gewicht < 20000.

## <a name="query-example-fill-missing-values"></a>Query voorbeeld: ontbrekende waarden vullen

**Beschrijving**: voor de stroom van gebeurtenissen met ontbrekende waarden, produceert u een stroom gebeurtenissen met regel matige intervallen. Genereer bijvoorbeeld elke 5 seconden een gebeurtenis die het meest recent weer gegeven gegevens punt rapporteert.

**Invoer**:

| t | waarde |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Uitvoer (eerste 10 rijen)** :

| windowend | lastevent. t | lastevent. waarde |
| --- | --- | --- |
| 2014-01-01T14:01:00.000 Z |2014-01-01T14:01:00.000 Z |1 |
| 2014-01-01T14:01:05.000 Z |2014-01-01T14:01:05.000 Z |2 |
| 2014-01-01T14:01:10.000 Z |2014-01-01T14:01:10.000 Z |3 |
| 2014-01-01T14:01:15.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:20.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:25.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:30.000 Z |2014-01-01T14:01:30.000 Z |5 |
| 2014-01-01T14:01:35.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:40.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:45.000 Z |2014-01-01T14:01:35.000 Z |6 |

**Oplossing**:

```SQL
    SELECT
        System.Timestamp() AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)
```

**Uitleg**: met deze query worden elke vijf seconden gebeurtenissen gegenereerd en wordt de laatste gebeurtenis uitgevoerd die eerder is ontvangen. De duur van het [verspringen-venster](/stream-analytics-query/hopping-window-azure-stream-analytics) bepaalt hoe ver terug de query zoekt naar de laatste gebeurtenis (300 seconden in dit voor beeld).


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Query-voor beeld: correlatie van twee gebeurtenis typen binnen dezelfde stroom

**Beschrijving**: soms moeten waarschuwingen worden gegenereerd op basis van meerdere gebeurtenis typen die in een bepaald tijds bereik zijn opgetreden. Een voor beeld: in een IoT-scenario voor Home ovens moet een waarschuwing worden gegenereerd wanneer de ventilator temperatuur kleiner is dan 40 en de maximale kracht in de afgelopen 3 minuten minder is dan 10.

**Invoer**:

| tijd | deviceId | sensorName | waarde |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" | "temp" |120 |
| "2018-01-01T16:01:00" | "Oven1" | /uitschakelaar |15 |
| "2018-01-01T16:02:00" | "Oven1" | "temp" |100 |
| "2018-01-01T16:02:00" | "Oven1" | /uitschakelaar |15 |
| "2018-01-01T16:03:00" | "Oven1" | "temp" |70 |
| "2018-01-01T16:03:00" | "Oven1" | /uitschakelaar |15 |
| "2018-01-01T16:04:00" | "Oven1" | "temp" |50 |
| "2018-01-01T16:04:00" | "Oven1" | /uitschakelaar |15 |
| "2018-01-01T16:05:00" | "Oven1" | "temp" |30 |
| "2018-01-01T16:05:00" | "Oven1" | /uitschakelaar |8 |
| "2018-01-01T16:06:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:06:00" | "Oven1" | /uitschakelaar |8 |
| "2018-01-01T16:07:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:07:00" | "Oven1" | /uitschakelaar |8 |
| "2018-01-01T16:08:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:08:00" | "Oven1" | /uitschakelaar |8 |

**Uitvoer**:

| eventTime | deviceId | temp | alertMessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | "Short circuit-verwarmings elementen" |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | "Short circuit-verwarmings elementen" |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | "Short circuit-verwarmings elementen" |15 |

**Oplossing**:

```SQL
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp() AS windowTime,
        deviceId,
        max(value) as maxPower
    FROM
        input TIMESTAMP BY t
    WHERE 
        sensorName = 'power' 
    GROUP BY 
        deviceId, 
        SlidingWindow(minute, 3) 
)

SELECT 
    t1.t AS eventTime,
    t1.deviceId, 
    t1.value AS temp,
    'Short circuit heating elements' as alertMessage,
    t2.maxPower AS maxPowerDuringLast3mins
    
INTO resultsr

FROM input t1 TIMESTAMP BY t
JOIN max_power_during_last_3_mins t2
    ON t1.deviceId = t2.deviceId 
    AND t1.t = t2.windowTime
    AND DATEDIFF(minute,t1,t2) between 0 and 3
    
WHERE
    t1.sensorName = 'temp'
    AND t1.value <= 40
    AND t2.maxPower > 10
```

**Uitleg**: de eerste query `max_power_during_last_3_mins`, gebruikt het [Schuif venster](/stream-analytics-query/sliding-window-azure-stream-analytics) om de maximale waarde van de stroom sensor voor elk apparaat te vinden, in de afgelopen 3 minuten. De tweede query wordt gekoppeld aan de eerste query om de energie waarde in het meest recente venster te vinden dat relevant is voor de huidige gebeurtenis. En vervolgens aan de voor waarden wordt voldaan, wordt er een waarschuwing gegenereerd voor het apparaat.

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>Query-voor beeld: proces gebeurtenissen onafhankelijk van de klok scheefheid van het apparaat (substreamen)

**Beschrijving**: gebeurtenissen kunnen te laat of in aflopende volg orde arriveren als gevolg van de klok tussen de gebeurtenissen van de producent, de klok tussen partities of netwerk latentie. In het volgende voor beeld is de klok van het apparaat voor TollID 2 vijf seconden achter TollID 1 en de klok van het apparaat voor TollID 3 tien seconden achter TollID 1. 

**Invoer**:

| LicensePlate | Maken | Tijd | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:01.0000000 Z | 1 |
| YHN 6970 |Toyota |2015-07-27T00:00:05.0000000 Z | 1 |
| QYF 9358 |Honda |2015-07-27T00:00:01.0000000 Z | 2 |
| GXF 9462 |BMW |2015-07-27T00:00:04.0000000 Z | 2 |
| VFE 1616 |Toyota |2015-07-27T00:00:10.0000000 Z | 1 |
| RMV 8282 |Honda |2015-07-27T00:00:03.0000000 Z | 3 |
| MDR 6128 |BMW |2015-07-27T00:00:11.0000000 Z | 2 |
| YZK 5704 |Ford |2015-07-27T00:00:07.0000000 Z | 3 |

**Uitvoer**:

| TollID | Aantal |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Oplossing**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**Uitleg**: de [time stamp by](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) -component bekijkt elke tijd lijn van elk apparaat afzonderlijk met substreams. De uitvoer gebeurtenissen voor elke TollID worden gegenereerd wanneer ze worden berekend, wat inhoudt dat de gebeurtenissen zich bevinden ten opzichte van elke TollID in plaats van dat ze opnieuw moeten worden gerangschikt alsof alle apparaten dezelfde klok hebben.

## <a name="query-example-remove-duplicate-events-in-a-window"></a>Query voorbeeld: dubbele gebeurtenissen in een venster verwijderen

**Beschrijving**: wanneer u een bewerking uitvoert, zoals het berekenen van de gemiddelden van gebeurtenissen in een bepaald tijd venster, moeten dubbele gebeurtenissen worden gefilterd. In het volgende voor beeld is de tweede gebeurtenis een duplicaat van de eerste.

**Invoer**:  

| DeviceId | Tijd | Kenmerk | Waarde |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000 Z |Temperatuur |50 |
| 1 |2018-07-27T00:00:01.0000000 Z |Temperatuur |50 |
| 2 |2018-07-27T00:00:01.0000000 Z |Temperatuur |40 |
| 1 |2018-07-27T00:00:05.0000000 Z |Temperatuur |60 |
| 2 |2018-07-27T00:00:05.0000000 Z |Temperatuur |50 |
| 1 |2018-07-27T00:00:10.0000000 Z |Temperatuur |100 |

**Uitvoer**:  

| AverageValue | DeviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Oplossing**:

```SQL
With Temp AS (
    SELECT
        COUNT(DISTINCT Time) AS CountTime,
        Value,
        DeviceId
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Value,
        DeviceId,
        SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**Uitleg**: [aantal (DISTINCT time)](/stream-analytics-query/count-azure-stream-analytics) retourneert het aantal DISTINCT-waarden in de tijd kolom binnen een tijd venster. U kunt vervolgens de uitvoer van deze stap gebruiken om het gemiddelde per apparaat te berekenen door dubbele waarden te verwijderen.

## <a name="geofencing-and-geospatial-queries"></a>Geoomheiningen en georuimtelijke query's
Azure Stream Analytics biedt ingebouwde georuimtelijke functies die kunnen worden gebruikt voor het implementeren van scenario's zoals vloot beheer, het delen van wijzigingen, verbonden auto's en het bijhouden van activa. Georuimtelijke gegevens kunnen in geojson-of WKT-indelingen worden opgenomen als onderdeel van de gebeurtenis stroom of referentie gegevens. Raadpleeg voor meer informatie de scenario's voor [geoomheining en georuimtelijke aggregatie met Azure stream Analytics](geospatial-scenarios.md) artikel.

## <a name="language-extensibility-through-javascript-and-c"></a>Taal uitbreid baarheid via Java script enC#
Azure stream Ananlytics query langugae kan worden uitgebreid met aangepaste functies die zijn geschreven in C# java script of talen. Raadpleeg de foolowing-artikelen voor meer informatie:
* [Door de gebruiker gedefinieerde Java script-functies Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Door de gebruiker gedefinieerde Java script-functies Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md)
* [Door de gebruiker gedefinieerde .NET Standard-functies ontwikkelen voor Azure Stream Analytics Edge-taken](stream-analytics-edge-csharp-udf-methods.md)

## <a name="get-help"></a>Hulp krijgen

Voor verdere ondersteuning kunt u proberen onze [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)

