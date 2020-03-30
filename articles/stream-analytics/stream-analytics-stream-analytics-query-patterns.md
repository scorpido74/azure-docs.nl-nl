---
title: Veelvoorkomende querypatronen in Azure Stream Analytics
description: In dit artikel worden verschillende veelvoorkomende querypatronen en -ontwerpen beschreven die nuttig zijn in Azure Stream Analytics-taken.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: aa8bd6e89dd47c4e972a860691d1bc3779ba5bc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982316"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Veelvoorkomende querypatronen in Azure Stream Analytics

Query's in Azure Stream Analytics worden uitgedrukt in een SQL-achtige querytaal. De taalconstructies zijn gedocumenteerd in de [naslaggids voor querytaal van Stream Analytics.](/stream-analytics-query/stream-analytics-query-language-reference) 

Het queryontwerp kan eenvoudige pass-through-logica uitdrukken om gebeurtenisgegevens van één invoerstroom naar een uitvoergegevensarchief te verplaatsen, of het kan uitgebreide patroonmatching en temporele analyse uitvoeren om aggregaten over verschillende tijdvensters te berekenen zoals in de Oplossing voor Een IoT bouwen [met behulp van de streamanalysehandleiding.](stream-analytics-build-an-iot-solution-using-stream-analytics.md) U gegevens van meerdere ingangen deelnemen om streaminggebeurtenissen te combineren en u opzoekingen doen tegen statische referentiegegevens om de gebeurteniswaarden te verrijken. U ook gegevens naar meerdere uitvoers schrijven.

In dit artikel worden oplossingen beschreven voor verschillende veelvoorkomende querypatronen op basis van scenario's in de echte wereld.

## <a name="supported-data-formats"></a>Ondersteunde gegevensindelingen

Azure Stream Analytics ondersteunt verwerkingsgebeurtenissen in CSV-, JSON- en Avro-gegevensindelingen.

Zowel JSON als Avro kunnen complexe typen bevatten, zoals geneste objecten (records) of arrays. Zie voor meer informatie over het werken met deze complexe gegevenstypen het artikel over de [gegevens van Parsing JSON en AVRO.](stream-analytics-parsing-json.md)

## <a name="simple-pass-through-query"></a>Eenvoudige doorgeefquery

Een eenvoudige pass-through query kan worden gebruikt om de invoerstroomgegevens naar de uitvoer te kopiëren. Als bijvoorbeeld een stroom gegevens met realtime voertuiginformatie moet worden opgeslagen in een SQL-database voor briefanalyse, doet een eenvoudige doorreisquery het werk.

**Input**:

| Merk | Time | Gewicht |
| --- | --- | --- |
| Maak1 |2015-01-01T00:00:01.00000000Z |"1000" |
| Maak1 |2015-01-01T00:00:02.00000000Z |"2000" |

**Output**:

| Merk | Time | Gewicht |
| --- | --- | --- |
| Maak1 |2015-01-01T00:00:01.00000000Z |"1000" |
| Maak1 |2015-01-01T00:00:02.00000000Z |"2000" |

**Query:**

```SQL
SELECT
    *
INTO Output
FROM Input
```

Een **SELECT** * query projecteert alle velden van een binnenkomende gebeurtenis en stuurt deze naar de uitvoer. Op dezelfde manier kan **SELECT** ook worden gebruikt om alleen vereiste velden uit de invoer te projecteren. Als voertuig *maken* en *tijd* in dit voorbeeld de enige vereiste velden zijn die moeten worden opgeslagen, kunnen deze velden worden opgegeven in de instructie **SELECT.**

**Input**:

| Merk | Time | Gewicht |
| --- | --- | --- |
| Maak1 |2015-01-01T00:00:01.00000000Z |1000 |
| Maak1 |2015-01-01T00:00:02.00000000Z |2000 |
| Maak2 |2015-01-01T00:00:04.00000000Z |1500 |

**Output**:

| Merk | Time |
| --- | --- |
| Maak1 |2015-01-01T00:00:01.00000000Z |
| Maak1 |2015-01-01T00:00:02.00000000Z |
| Maak2 |2015-01-01T00:00:04.00000000Z |

**Query:**

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```
## <a name="data-aggregation-over-time"></a>Gegevensaggregatie in de loop van de tijd

Om informatie over een tijdvenster te berekenen, kunnen gegevens samen worden samengevoegd. In dit voorbeeld wordt een telling berekend over de laatste 10 minuten van de tijd voor elk specifiek automerk.

**Input**:

| Merk | Time | Gewicht |
| --- | --- | --- |
| Maak1 |2015-01-01T00:00:01.00000000Z |1000 |
| Maak1 |2015-01-01T00:00:02.00000000Z |2000 |
| Maak2 |2015-01-01T00:00:04.00000000Z |1500 |

**Output**:

| Merk | Count |
| --- | --- |
| Maak1 | 2 |
| Maak2 | 1 |

**Query:**

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Deze aggregatie groepeert de auto's door *Make* en telt ze elke 10 seconden. De output heeft het *merk* en *de telling* van auto's die ging door de tol.

TumblingWindow is een vensterfunctie die wordt gebruikt om gebeurtenissen samen te groeperen. Een aggregatie kan worden toegepast op alle gegroepeerde gebeurtenissen. Zie [vensterfuncties voor](stream-analytics-window-functions.md)meer informatie .

Voor meer informatie over aggregatie verwijzen wij u naar [geaggregeerde functies](/stream-analytics-query/aggregate-functions-azure-stream-analytics).

## <a name="data-conversion"></a>Gegevensconversie

Gegevens kunnen in real-time **CAST** worden gegoten met behulp van de CAST-methode. Het gewicht van de auto kan bijvoorbeeld worden omgezet van type **nvarchar(max)** naar **bigint** typen en worden gebruikt bij een numerieke berekening.

**Input**:

| Merk | Time | Gewicht |
| --- | --- | --- |
| Maak1 |2015-01-01T00:00:01.00000000Z |"1000" |
| Maak1 |2015-01-01T00:00:02.00000000Z |"2000" |

**Output**:

| Merk | Gewicht |
| --- | --- |
| Maak1 |3000 |

**Query:**

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

Gebruik een **CAST-instructie** om het gegevenstype op te geven. Zie de lijst met ondersteunde gegevenstypen op [gegevenstypen (Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

Voor meer informatie over [gegevensconversiefuncties](/stream-analytics-query/conversion-functions-azure-stream-analytics).

## <a name="string-matching-with-like-and-not-like"></a>String matching met LIKE en NOT LIKE

**LIKE** en **NOT LIKE** kunnen worden gebruikt om te controleren of een veld overeenkomt met een bepaald patroon. Er kan bijvoorbeeld een filter worden gemaakt om alleen de nummerplaten terug te sturen die beginnen met de letter 'A' en eindigen met het nummer 9.

**Input**:

| Merk | License_plate | Time |
| --- | --- | --- |
| Maak1 |ABC-123 |2015-01-01T00:00:01.00000000Z |
| Maak2 |AAA-999 |2015-01-01T00:00:02.00000000Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.00000000Z |

**Output**:

| Merk | License_plate | Time |
| --- | --- | --- |
| Maak2 |AAA-999 |2015-01-01T00:00:02.00000000Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.00000000Z |

**Query:**

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

Gebruik de **instructie VIND-leuk** om de **License_plate** veldwaarde te controleren. Het moet beginnen met de letter 'A', dan hebben een string van nul of meer tekens, eindigend met het nummer 9.

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>Logica opgeven voor verschillende aanvragen/waarden (CASE-instructies)

**CASE-instructies** kunnen verschillende berekeningen voor verschillende velden bieden, op basis van een bepaald criterium. Wijs bijvoorbeeld rijstrook 'A' toe aan auto's van *Make1* en rijstrook 'B' aan elk ander merk.

**Input**:

| Merk | Time |
| --- | --- |
| Maak1 |2015-01-01T00:00:01.00000000Z |
| Maak2 |2015-01-01T00:00:02.00000000Z |
| Maak2 |2015-01-01T00:00:03.00000000Z |

**Output**:

| Merk |Dispatch_to_lane | Time |
| --- | --- | --- |
| Maak1 |"A" |2015-01-01T00:00:01.00000000Z |
| Maak2 |"B" |2015-01-01T00:00:02.00000000Z |

**Oplossing**:

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

De **expressie CASE** vergelijkt een expressie met een set eenvoudige expressies om het resultaat te bepalen. In dit voorbeeld worden voertuigen van *Make1* naar rijstrook 'A' gestuurd, terwijl voertuigen van een ander merk rijstrook 'B' toegewezen krijgen.

Zie voor meer informatie [de caseexpressie](/stream-analytics-query/case-azure-stream-analytics).

## <a name="send-data-to-multiple-outputs"></a>Gegevens naar meerdere uitvoer verzenden

Meerdere **SELECT-instructies** kunnen worden gebruikt om gegevens naar verschillende uitvoerputten uit te zetten. De **ene SELECT** kan bijvoorbeeld een waarschuwing op basis van drempelwaarden uitvoeren, terwijl een andere waarschuwing kan uitvoeren naar blobopslag.

**Input**:

| Merk | Time |
| --- | --- |
| Maak1 |2015-01-01T00:00:01.00000000Z |
| Maak1 |2015-01-01T00:00:02.00000000Z |
| Maak2 |2015-01-01T00:00:01.00000000Z |
| Maak2 |2015-01-01T00:00:02.00000000Z |
| Maak2 |2015-01-01T00:00:03.00000000Z |

**Output ArchiveOutput**:

| Merk | Time |
| --- | --- |
| Maak1 |2015-01-01T00:00:01.00000000Z |
| Maak1 |2015-01-01T00:00:02.00000000Z |
| Maak2 |2015-01-01T00:00:01.00000000Z |
| Maak2 |2015-01-01T00:00:02.00000000Z |
| Maak2 |2015-01-01T00:00:03.00000000Z |

**Output AlertOutput:**

| Merk | Time | Count |
| --- | --- | --- |
| Maak2 |2015-01-01T00:00:10.00000000Z |3 |

**Query:**

```SQL
SELECT
    *
INTO
    ArchiveOutput
FROM
    Input TIMESTAMP BY Time

SELECT
    Make,
    System.TimeStamp() AS Time,
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

De **INTO-clausule** vertelt Stream Analytics naar welke van de uitgangen de gegevens moeten worden geschreven. De eerste **SELECT** definieert een doorgeefquery die gegevens van de invoer ontvangt en deze naar de uitvoer met de naam **ArchiveOutput**verzendt. De tweede query doet een aantal eenvoudige aggregatie en filtering voordat de resultaten naar een downstream waarschuwingssysteem uitvoer genaamd **AlertOutput**.

Houd er rekening mee dat de **WITH-component** kan worden gebruikt om meerdere subqueryblokken te definiëren. Deze optie heeft het voordeel dat minder lezers naar de invoerbron worden geopend.

**Query:**

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

Voor meer informatie verwijzen wij u naar [ **MET-clausule** ](/stream-analytics-query/with-azure-stream-analytics).

## <a name="count-unique-values"></a>Unieke waarden tellen

**COUNT** en **DISTINCT** kunnen worden gebruikt om het aantal unieke veldwaarden te tellen dat in de stream binnen een tijdvenster wordt weergegeven. Er kan een query worden gemaakt om te berekenen hoeveel unieke *merken* van auto's door de tolpoort in een venster van 2 seconden zijn gegaan.

**Input**:

| Merk | Time |
| --- | --- |
| Maak1 |2015-01-01T00:00:01.00000000Z |
| Maak1 |2015-01-01T00:00:02.00000000Z |
| Maak2 |2015-01-01T00:00:01.00000000Z |
| Maak2 |2015-01-01T00:00:02.00000000Z |
| Maak2 |2015-01-01T00:00:03.00000000Z |

**Output:**

| Count_make | Time |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Query:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**COUNT(DISTINCT Make)** retourneert het aantal afzonderlijke waarden in de kolom **Maken** binnen een tijdvenster.
Zie voor meer informatie de [geaggregeerde functie **TELLEN** ](/stream-analytics-query/count-azure-stream-analytics).

## <a name="calculation-over-past-events"></a>Berekening over gebeurtenissen uit het verleden

De **FUNCTIE LAG** kan worden gebruikt om gebeurtenissen uit het verleden binnen een tijdvenster te bekijken en te vergelijken met de huidige gebeurtenis. Bijvoorbeeld, de huidige auto te maken kan worden outputted als het anders is dan de laatste auto die ging door de tol.

**Input**:

| Merk | Time |
| --- | --- |
| Maak1 |2015-01-01T00:00:01.00000000Z |
| Maak2 |2015-01-01T00:00:02.00000000Z |

**Output**:

| Merk | Time |
| --- | --- |
| Maak2 |2015-01-01T00:00:02.00000000Z |

**Query:**

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

Gebruik **LAG** om een kijkje te nemen in de invoerstream één gebeurtenis terug, waarbij de waarde *Maken* wordt opgehaald en deze wordt vergeleken met de waarde *Maken* van de huidige gebeurtenis en de gebeurtenis worden uitgevoerd.

Zie [**LAG**](/stream-analytics-query/lag-azure-stream-analytics)voor meer informatie .

## <a name="retrieve-the-first-event-in-a-window"></a>De eerste gebeurtenis in een venster ophalen

**IsFirst** kan worden gebruikt om de eerste gebeurtenis in een tijdvenster op te halen. Bijvoorbeeld, het outputting van de eerste auto-informatie op elke 10 minuten interval.

**Input**:

| License_plate | Merk | Time |
| --- | --- | --- |
| DXE 5291 |Maak1 |2015-07-27T00:00:05.00000000Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.00000000Z |
| RMV 8282 |Maak1 |2015-07-27T00:05:01.00000000Z |
| YHN 6970 YHN 6970 |Maak2 |2015-07-27T00:06:00000000Z |
| VFE 1616 |Maak2 |2015-07-27T00:09:31.00000000Z |
| QYF 9358 |Maak1 |2015-07-27T00:12:02.00000000Z |
| MDR 6128 |Maak4 |2015-07-27T00:13:45.00000000Z |

**Output**:

| License_plate | Merk | Time |
| --- | --- | --- |
| DXE 5291 |Maak1 |2015-07-27T00:00:05.00000000Z |
| QYF 9358 |Maak1 |2015-07-27T00:12:02.00000000Z |

**Query:**

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

**IsFirst** kan ook de gegevens verdelen en de eerste gebeurtenis berekenen voor elke specifieke auto *Make* gevonden bij elk interval van 10 minuten.

**Output**:

| License_plate | Merk | Time |
| --- | --- | --- |
| DXE 5291 |Maak1 |2015-07-27T00:00:05.00000000Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.00000000Z |
| YHN 6970 YHN 6970 |Maak2 |2015-07-27T00:06:00000000Z |
| QYF 9358 |Maak1 |2015-07-27T00:12:02.00000000Z |
| MDR 6128 |Maak4 |2015-07-27T00:13:45.00000000Z |

**Query:**

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

Zie voor meer informatie [**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics).

## <a name="return-the-last-event-in-a-window"></a>De laatste gebeurtenis in een venster retourneren

Aangezien gebeurtenissen in realtime door het systeem worden verbruikt, is er geen functie die kan bepalen of een gebeurtenis de laatste is die voor dat tijdsvenster aankomt. Om dit te bereiken, moet de invoerstroom worden samengevoegd met een andere waarbij de tijd van een gebeurtenis de maximale tijd is voor alle gebeurtenissen in dat venster.

**Input**:

| License_plate | Merk | Time |
| --- | --- | --- |
| DXE 5291 |Maak1 |2015-07-27T00:00:05.00000000Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.00000000Z |
| RMV 8282 |Maak1 |2015-07-27T00:05:01.00000000Z |
| YHN 6970 YHN 6970 |Maak2 |2015-07-27T00:06:00000000Z |
| VFE 1616 |Maak2 |2015-07-27T00:09:31.00000000Z |
| QYF 9358 |Maak1 |2015-07-27T00:12:02.00000000Z |
| MDR 6128 |Maak4 |2015-07-27T00:13:45.00000000Z |

**Output**:

| License_plate | Merk | Time |
| --- | --- | --- |
| VFE 1616 |Maak2 |2015-07-27T00:09:31.00000000Z |
| MDR 6128 |Maak4 |2015-07-27T00:13:45.00000000Z |

**Query:**

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
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

De eerste stap op de query vindt de maximale tijdstempel in vensters van 10 minuten, dat is de tijdstempel van de laatste gebeurtenis voor dat venster. De tweede stap voegt de resultaten van de eerste query samen met de oorspronkelijke stream om de gebeurtenis te vinden die overeenkomt met de laatste tijdstempels in elk venster. 

**DATEDIFF** is een datumspecifieke functie die het tijdsverschil tussen twee DateTime-velden vergelijkt en retourneert, voor meer informatie verwijzen naar [datumfuncties](https://docs.microsoft.com/stream-analytics-query/date-and-time-functions-azure-stream-analytics).

Zie [**JOIN**](/stream-analytics-query/join-azure-stream-analytics)voor meer informatie over het samenvoegen van streams.


## <a name="correlate-events-in-a-stream"></a>Gebeurtenissen in een stream correleren

Het correleren van gebeurtenissen in dezelfde stream kan worden gedaan door te kijken naar gebeurtenissen uit het verleden met behulp van de **LAG-functie.** Een uitvoer kan bijvoorbeeld elke keer worden gegenereerd dat twee opeenvolgende auto's van dezelfde *Make* de laatste 90 seconden door de tol gaan.

**Input**:

| Merk | License_plate | Time |
| --- | --- | --- |
| Maak1 |ABC-123 |2015-01-01T00:00:01.00000000Z |
| Maak1 |AAA-999 |2015-01-01T00:00:02.00000000Z |
| Maak2 |DEF-987 |2015-01-01T00:00:03.00000000Z |
| Maak1 |GHI-345 |2015-01-01T00:00:04.00000000Z |

**Output**:

| Merk | Time | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Maak1 |2015-01-01T00:00:02.00000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.00000000Z |

**Query:**

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

De functie **LAG** kan de invoerstroom één gebeurtenis terugbekijken en de *waarde Maken* ophalen, die vergelijken met de waarde *Maken* van de huidige gebeurtenis.  Zodra aan de voorwaarde is voldaan, kunnen gegevens van de vorige gebeurtenis worden geprojecteerd met **LAG** in de **select-instructie.**

Zie [LAG](/stream-analytics-query/lag-azure-stream-analytics)voor meer informatie .

## <a name="detect-the-duration-between-events"></a>De duur tussen gebeurtenissen detecteren

De duur van een gebeurtenis kan worden berekend door te kijken naar de laatste gebeurtenis Start zodra een gebeurtenis Einde is ontvangen. Deze query kan handig zijn om de tijd te bepalen die een gebruiker doorbrengt op een pagina of een functie.

**Input**:  

| Gebruiker | Functie | Gebeurtenis | Time |
| --- | --- | --- | --- |
| user@location.com |RechtsMenu |Starten |2015-01-01T00:00:01.00000000Z |
| user@location.com |RechtsMenu |Beëindigen |2015-01-01T00:00:08000000Z |

**Output**:  

| Gebruiker | Functie | Duur |
| --- | --- | --- |
| user@location.com |RechtsMenu |7 |

**Query:**

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

De functie **LAATSTE** kan worden gebruikt om de laatste gebeurtenis binnen een specifieke voorwaarde op te halen. In dit voorbeeld is de voorwaarde een gebeurtenis van het type Start, waarbij de zoekopdracht wordt verdeeld **op partitie per** gebruiker en functie. Op deze manier wordt elke gebruiker en functie onafhankelijk behandeld bij het zoeken naar de gebeurtenis Start. **LIMIT DUUR** beperkt de zoekopdracht terug in de tijd tot 1 uur tussen de gebeurtenissen Einde en Start.

## <a name="detect-the-duration-of-a-condition"></a>De duur van een aandoening detecteren

Voor omstandigheden die meerdere gebeurtenissen doorlopen, kan de **LAG-functie** worden gebruikt om de duur van die aandoening te bepalen. Stel dat een bug heeft geleid tot een verkeerd gewicht (boven 20.000 pond) en dat de duur van die bug moet worden berekend.

**Input**:

| Merk | Time | Gewicht |
| --- | --- | --- |
| Maak1 |2015-01-01T00:00:01.00000000Z |2000 |
| Maak2 |2015-01-01T00:00:02.00000000Z |25.000 |
| Maak1 |2015-01-01T00:00:03.00000000Z |26000 |
| Maak2 |2015-01-01T00:00:04.00000000Z |25.000 |
| Maak1 |2015-01-01T00:00:05.00000000Z |26000 |
| Maak2 |2015-01-01T00:00:06.00000000Z |25.000 |
| Maak1 |2015-01-01T00:00:07.00000000Z |26000 |
| Maak2 |2015-01-01T00:00:08000000Z |2000 |

**Output**:

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Query:**

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
De eerste **SELECT-instructie** correleert de huidige gewichtsmeting met de vorige meting en projecteert deze samen met de huidige meting. De tweede **SELECT** kijkt terug op de laatste gebeurtenis waar de *previous_weight* minder dan 20000 is, waar het huidige gewicht kleiner is dan 20000 en de *previous_weight* van de huidige gebeurtenis groter was dan 20000.

De End_fault is de huidige niet-defecte gebeurtenis waarbij de vorige gebeurtenis defect was, en de Start_fault is de laatste niet-defecte gebeurtenis daarvoor.

## <a name="periodically-output-values"></a>Periodiek uitvoerwaarden

In het geval van onregelmatige of ontbrekende gebeurtenissen, kan een regelmatige intervaloutput worden gegenereerd uit een meer schaarse gegevensinvoer. Genereer bijvoorbeeld elke 5 seconden een gebeurtenis die het meest recent geziene gegevenspunt rapporteert.

**Input**:

| Time | Waarde |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Uitvoer (eerste 10 rijen):**

| Window_end | Last_event. Tijd | Last_event. Waarde |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

**Query:**

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

Deze query genereert gebeurtenissen om de 5 seconden en geeft de laatste gebeurtenis die eerder is ontvangen, weer. De duur **van het HOPPINGVENSTER** bepaalt hoe ver de query de laatste gebeurtenis zoekt.

Zie het venster [Hopping](/stream-analytics-query/hopping-window-azure-stream-analytics)voor meer informatie .

## <a name="process-events-with-independent-time-substreams"></a>Gebeurtenissen verwerken met onafhankelijke tijd (Substreams)

Gebeurtenissen kunnen te laat of buiten de orde komen als gevolg van klokscheeftrekkingen tussen gebeurtenisproducenten, klokscheefheid tussen partities of netwerklatentie.
De apparaatklok voor *TollID* 2 loopt bijvoorbeeld vijf seconden achter op *TollID* 1 en de apparaatklok voor *TollID* 3 loopt tien seconden achter op *TollID* 1. Een berekening kan onafhankelijk voor elke tol gebeuren, gezien alleen zijn eigen klokgegevens als een tijdstempel.

**Input**:

| Kenteken | Merk | Time | Tol-ID |
| --- | --- | --- | --- |
| DXE 5291 |Maak1 |2015-07-27T00:00:01.00000000Z | 1 |
| YHN 6970 YHN 6970 |Maak2 |2015-07-27T00:00:05.00000000Z | 1 |
| QYF 9358 |Maak1 |2015-07-27T00:00:01.00000000Z | 2 |
| GXF 9462 |Make3 |2015-07-27T00:00:04.00000000Z | 2 |
| VFE 1616 |Maak2 |2015-07-27T00:00:10.00000000Z | 1 |
| RMV 8282 |Maak1 |2015-07-27T00:00:03.00000000Z | 3 |
| MDR 6128 |Make3 |2015-07-27T00:00:11.00000000Z | 2 |
| YZK 5704 |Maak4 |2015-07-27T00:00:07.00000000Z | 3 |

**Output**:

| Tol-ID | Count |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Query:**

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

De **TIMESTAMP OVER BY-component** bekijkt elke tijdlijn van elk apparaat onafhankelijk met behulp van substreams. De uitvoergebeurtenis voor elke *TollID* wordt gegenereerd terwijl ze worden berekend, wat betekent dat de gebeurtenissen in orde zijn met betrekking tot elke *TollID* in plaats van opnieuw te worden geordend alsof alle apparaten op dezelfde klok staan.

Voor meer informatie verwijzen wij u naar [TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering).

## <a name="remove-duplicate-events-in-a-window"></a>Dubbele gebeurtenissen in een venster verwijderen

Bij het uitvoeren van een bewerking, zoals het berekenen van gemiddelden over gebeurtenissen in een bepaald tijdvenster, moeten dubbele gebeurtenissen worden gefilterd. In het volgende voorbeeld is de tweede gebeurtenis een duplicaat van de eerste.

**Input**:  

| DeviceId | Time | Kenmerk | Waarde |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.00000000Z |Temperatuur |50 |
| 1 |2018-07-27T00:00:01.00000000Z |Temperatuur |50 |
| 2 |2018-07-27T00:00:01.00000000Z |Temperatuur |40 |
| 1 |2018-07-27T00:00:05.00000000Z |Temperatuur |60 |
| 2 |2018-07-27T00:00:05.00000000Z |Temperatuur |50 |
| 1 |2018-07-27T00:00:10.00000000Z |Temperatuur |100 |

**Output**:  

| Gemiddelde waarde | DeviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Query:**

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

**COUNT(DISTINCT Time)** retourneert het aantal verschillende waarden in de kolom Tijd binnen een tijdvenster. De uitvoer van de eerste stap kan vervolgens worden gebruikt om het gemiddelde per apparaat te berekenen door duplicaten te verwijderen.

Zie VOOR meer informatie [COUNT(DISTINCT Time)](/stream-analytics-query/count-azure-stream-analytics).

## <a name="session-windows"></a>Sessie Windows

Een sessievenster is een venster dat blijft uitbreiden naarmate gebeurtenissen plaatsvinden en wordt gesloten voor berekening als er geen gebeurtenis wordt ontvangen na een bepaalde tijdof als het venster de maximale duur bereikt.
Dit venster is vooral handig bij het berekenen van gebruikersinteractiegegevens. Een venster begint wanneer een gebruiker begint te interageren met het systeem en sluit wanneer er geen gebeurtenissen meer worden waargenomen, wat betekent dat de gebruiker is gestopt met de interactie.
Een gebruiker heeft bijvoorbeeld interactie met een webpagina waarbij het aantal klikken wordt geregistreerd, een sessievenster kan worden gebruikt om erachter te komen hoe lang de gebruiker interactie heeft gehad met de site.

**Input**:

| User_id | Time | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:000000000Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.00000000Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.00000000Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.00000000Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.00000000Z | "www.example.com/e.html" |

**Output**:

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:000000000Z | 2017-01-26T00:01:10.00000000Z | 70 |
| 1 | 2017-01-26T00:00:55.00000000Z | 2017-01-26T00:01:15.00000000Z | 20 |

**Query:**

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

De **SELECT** projecteert de gegevens die relevant zijn voor de interactie van de gebruiker, samen met de duur van de interactie. De gegevens groeperen op gebruiker en een **sessionwindow** dat wordt gesloten als er geen interactie plaatsvindt binnen 1 minuut, met een maximale venstergrootte van 60 minuten.

Voor meer informatie over **SessionWindow**verwijzen we je door naar [Sessievenster](/stream-analytics-query/session-window-azure-stream-analytics) .

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>Taaluitbreidbaarheid met gebruikersgedefinieerde functie in JavaScript en C #

De querytaal van Azure Stream Analytics kan worden uitgebreid met aangepaste functies die zijn geschreven in JavaScript- of C#-taal. Gebruikersgedefinieerde functies (UDF) zijn aangepaste/complexe berekeningen die niet gemakkelijk kunnen worden uitgedrukt met de **SQL-taal.** Deze UDF's kunnen eenmaal worden gedefinieerd en meerdere keren worden gebruikt in een query. Een UDF kan bijvoorbeeld worden gebruikt om een hexadecimale *nvarcharwaarde (max)* om te zetten in een *bigintwaarde.*

**Input**:

| Device_id | HexValue HexValue HexValue |
| --- | --- |
| 1 | "B4" |
| 2 | "11 B" |
| 3 | "121" |

**Output**:

| Device_id | Decimal |
| --- | --- |
| 1 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

De functie Gebruiker gedefinieerd berekent de *bigintwaarde* van de HexValue op elke verbruikte gebeurtenis.

Zie [JavaScript](/azure/stream-analytics/stream-analytics-javascript-user-defined-functions) en [C#](/azure/stream-analytics/stream-analytics-edge-csharp-udf)voor meer informatie.

## <a name="advanced-pattern-matching-with-match_recognize"></a>Geavanceerde patroonmatching met MATCH_RECOGNIZE

**MATCH_RECOGNIZE** is een geavanceerd patroonmatchingmechanisme dat kan worden gebruikt om een reeks gebeurtenissen aan een goed gedefinieerd regelmatig expressiepatroon aan te passen.
Een geldautomaat wordt bijvoorbeeld in realtime gecontroleerd op storingen, tijdens de werking van de geldautomaat als er twee opeenvolgende waarschuwingsberichten zijn die de beheerder moet worden aangemeld.

**Input**:

| ATM_id | Operation_id | Return_Code | Time |
| --- | --- | --- | --- |
| 1 | "Invoeren Pin" | "Succes" | 2017-01-26T00:10:000000000Z |
| 2 | "Opening Geld Slot" | "Succes" | 2017-01-26T00:10:07.00000000Z |
| 2 | "Sluitingstijd Geld Slot" | "Succes" | 2017-01-26T00:10:11.00000000Z |
| 1 | "Invoeren Intrekken Hoeveelheid" | "Succes" | 2017-01-26T00:10:08.00000000Z |
| 1 | "Opening Geld Slot" | "Waarschuwing" | 2017-01-26T00:10:14.00000000Z |
| 1 | "Printing Bank Saldo" | "Waarschuwing" | 2017-01-26T00:10:19.00000000Z |

**Output**:

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Opening Geld Slot" | 2017-01-26T00:10:14.00000000Z |

```SQL
SELECT *
FROM intput TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    PARTITON BY ATM_id
    LIMIT DURATION(minute, 1)
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

Deze query komt overeen met ten minste twee opeenvolgende foutgebeurtenissen en genereert een alarm wanneer aan de voorwaarden is voldaan.
**PATTERN** definieert de reguliere expressie die moet worden gebruikt op de overeenkomende, in dit geval een willekeurig aantal succesvolle bewerkingen, gevolgd door ten minste twee opeenvolgende fouten.
Succes en mislukking worden gedefinieerd met behulp van Return_Code waarde en zodra aan de voorwaarde is voldaan, worden de **maatregelen** geprojecteerd met *ATM_id,* de eerste waarschuwingsbewerking en de eerste waarschuwingstijd.

Zie voor meer informatie [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics).

## <a name="geofencing-and-geospatial-queries"></a>Geofencing en georuimtelijke query's
Azure Stream Analytics biedt ingebouwde georuimtelijke functies die kunnen worden gebruikt om scenario's te implementeren, zoals fleetmanagement, ritdelen, verbonden auto's en assettracking.
Georuimtelijke gegevens kunnen worden ingenomen in GeoJSON- of WKT-formaten als onderdeel van gebeurtenisstroom- of referentiegegevens.
Bijvoorbeeld, een bedrijf dat gespecialiseerd is in de productie van machines voor het afdrukken van paspoorten, leasen hun machines aan overheden en consulaten. De locatie van deze machines wordt zwaar gecontroleerd om te voorkomen dat het verkeerd worden vervangen en mogelijk worden gebruikt voor het vervalsen van paspoorten. Elke machine is uitgerust met een GPS-tracker, die informatie wordt doorgegeven aan een Azure Stream Analytics-taak.
De fabrikant wil graag bijhouden van de locatie van deze machines en worden gewaarschuwd als een van hen verlaat een geautoriseerd gebied, op deze manier kunnen ze op afstand uitschakelen, waarschuwen autoriteiten en halen de apparatuur.

**Input**:

| Equipment_id | Equipment_current_location | Time |
| --- | --- | --- |
| 1 | "PUNT(-122.13288797982818 47,64082002051315)" | 2017-01-26T00:10:000000000Z |
| 1 | "PUNT(-122.13307252987875 47,64081350934929)" | 2017-01-26T00:11:00000000Z |
| 1 | "PUNT(-122.13308862313283 47,6406508603241)" | 2017-01-26T00:12:00000000Z |
| 1 | "PUNT(-122.13341048821462 47,64043760861279)" | 2017-01-26T00:13:00000000Z |

**Input voor referentiegegevens**:

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "POLYGON(-122.13326028450979 47,640983386794,-122,13261655434621 47,640983386794,-122.13261655434621 47.64061471602751,-122.13326028450979 47,64061471602751,-122,13326028450979 47,6409833866794)" |

**Output**:

| Equipment_id | Equipment_alert_location | Time |
| --- | --- | --- |
| 1 | "PUNT(-122.13341048821462 47,64043760861279)" | 2017-01-26T00:13:00000000Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

De query stelt de fabrikant in staat om de locatie van de machines automatisch te controleren, het ontvangen van waarschuwingen wanneer een machine verlaat de toegestane geofence. De ingebouwde georuimtelijke functie stelt gebruikers in staat om GPS-gegevens binnen de query te gebruiken zonder bibliotheken van derden.

Raadpleeg voor meer informatie de [scenario's voor geofencing en georuimtelijke aggregatie met het](geospatial-scenarios.md) azure stream analytics-artikel.

## <a name="get-help"></a>Help opvragen

Probeer ons Azure [Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)voor meer hulp.

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)
