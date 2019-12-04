---
title: Time Series-gegevens analyseren met Azure Data Explorer
description: Meer informatie over het analyseren van Time Series-gegevens in de Cloud met behulp van Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 3873b25394f91ce1c1601c348de2098198ba7fdd
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765480"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Time Series-analyse in azure Data Explorer

Azure Data Explorer (ADX) voert een voortdurende verzameling van telemetriegegevens uit van Cloud Services of IoT-apparaten. Deze gegevens kunnen worden geanalyseerd op verschillende inzichten, zoals het bewaken van service status, fysieke productie processen en gebruiks trends. De analyse wordt uitgevoerd op de tijd reeks van geselecteerde metrische gegevens om een afwijking in het patroon te vinden vergeleken met het typische basislijn patroon.
ADX bevat systeem eigen ondersteuning voor het maken, bewerken en analyseren van meerdere tijd reeksen. In dit onderwerp leert u hoe ADX wordt gebruikt voor het maken en analyseren **van duizenden tijd reeksen in enkele seconden**, waardoor bijna realtime-bewakings oplossingen en-werk stromen mogelijk zijn.

## <a name="time-series-creation"></a>Tijd reeks maken

In deze sectie maken we een grote set reguliere tijd reeksen en worden ze intuïtief gebruikt voor het gebruik van de `make-series` operator en worden de ontbrekende waarden indien nodig ingevuld.
De eerste stap in de tijd reeks analyse is het partitioneren en transformeren van de oorspronkelijke telemetrie-tabel naar een set tijd reeksen. De tabel bevat meestal een time stamp-kolom, contextuele dimensies en optionele metrische gegevens. De dimensies worden gebruikt voor het partitioneren van de gegevens. Het doel is om met regel matige tijds intervallen duizenden tijd reeksen per partitie te maken.

De invoer tabel *demo_make_series1* bevat 600K records voor wille keurige webservice-verkeer. Gebruik de onderstaande opdracht om 10 records te bemonsteren:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03MTo0vTi3KTC02VKhRKAFyFQwNADOyzKUbAAAA) **\]**

```kusto
demo_make_series1 | take 10 
```

De resulterende tabel bevat een time stamp-kolom, drie contextuele dimensie kolommen en geen metrische gegevens:

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Neem | BrowserVer | OsVer | Land/regio |
|   | 2016-08-25 09:12:35.4020000 | Chrome 51,0 | Windows 7 | Verenigd Koninkrijk |
|   | 2016-08-25 09:12:41.1120000 | Chrome 52,0 | Windows 10 |   |
|   | 2016-08-25 09:12:46.2300000 | Chrome 52,0 | Windows 7 | Verenigd Koninkrijk |
|   | 2016-08-25 09:12:46.5100000 | Chrome 52,0 | Windows 10 | Verenigd Koninkrijk |
|   | 2016-08-25 09:12:46.5570000 | Chrome 52,0 | Windows 10 | Litouwen |
|   | 2016-08-25 09:12:47.0470000 | Chrome 52,0 | Windows 8.1 | India |
|   | 2016-08-25 09:12:51.3600000 | Chrome 52,0 | Windows 10 | Verenigd Koninkrijk |
|   | 2016-08-25 09:12:51.6930000 | Chrome 52,0 | Windows 7 | Nederland |
|   | 2016-08-25 09:12:56.4240000 | Chrome 52,0 | Windows 10 | Verenigd Koninkrijk |
|   | 2016-08-25 09:13:08.7230000 | Chrome 52,0 | Windows 10 | India |

Omdat er geen metrische gegevens zijn, kunnen we alleen een set tijd reeksen maken die het aantal verkeer zelf vertegenwoordigen, gepartitioneerd door het besturings systeem met behulp van de volgende query:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPwQrCMBAE0Hu/Yo4NVLBn6Td4ULyWtV1tMJtIsoEq/XhbC4J48jgw+5h1rBDrW0UDDakjR7HsWUIrdOM2cbScakxIWYSiffJSL49W+KAkd2N2hVsMGv8yaPw2furFhCVu1gifpelC9loa9Hyh7LTZInh8FFiPSP7K5fufap1UoR4Mzg/s04njjEb2PUfofNYNFPUFtJiguAEBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- Gebruik de operator [`make-series`](/azure/kusto/query/make-seriesoperator) om een set van drie tijd reeksen te maken, waarbij:
    - `num=count()`: tijd reeks verkeer
    - `range(min_t, max_t, 1h)`: er wordt een tijd reeks gemaakt in een opslag ruimte van 1 uur in het tijds bereik (oudste en meest recente tijds tempels van tabel records)
    - `default=0`: Geef de methode Fill op voor ontbrekende opslag locaties om een reguliere tijd reeks te maken. U kunt ook [`series_fill_const()`](/azure/kusto/query/series-fill-constfunction), [`series_fill_forward()`](/azure/kusto/query/series-fill-forwardfunction), [`series_fill_backward()`](/azure/kusto/query/series-fill-backwardfunction) en [`series_fill_linear()`](/azure/kusto/query/series-fill-linearfunction) gebruiken om wijzigingen aan te brengen
    - `byOsVer`: partitie per besturings systeem
- De gegevens structuur van de werkelijke tijd reeks is een numerieke matrix van de geaggregeerde waarde per tijd-bak. We gebruiken `render timechart` voor visualisatie.

In de bovenstaande tabel zijn er drie partities. We kunnen een afzonderlijke tijd reeks maken: Windows 10 (rood), 7 (blauw) en 8,1 (groen) voor elke versie van het besturings systeem, zoals wordt weer gegeven in de grafiek:

![Time Series-partitie](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Functies voor time series-analyse

In deze sectie worden typische functies voor het verwerken van reeksen uitgevoerd.
Zodra een set tijd reeksen is gemaakt, ondersteunt ADX een groeiende lijst met functies die u kunt verwerken en analyseren die in de [Time Series-documentatie](/azure/kusto/query/machine-learning-and-tsa)kan worden gevonden. Er worden enkele representatieve functies beschreven voor het verwerken en analyseren van tijd reeksen.

### <a name="filtering"></a>Filteren

Filteren is een veelvoorkomende procedure bij het verwerken van signalen en handig voor het verwerkings taken van Time Series
- Er zijn twee algemene filter functies:
    - [`series_fir()`](/azure/kusto/query/series-firfunction): het Toep assen van het FIR-filter. Wordt gebruikt voor eenvoudige berekening van het zwevende gemiddelde en differentiatie van de tijd reeks voor de detectie van wijzigingen.
    - [`series_iir()`](/azure/kusto/query/series-iirfunction): het Toep assen van IIR-filter. Wordt gebruikt voor exponentiële afvlakking en cumulatief totaal.
- `Extend` de tijd reeks die is ingesteld door een nieuwe zwevende gemiddelde serie van grootte 5 bakken (met de naam *ma_num*) toe te voegen aan de query:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPQavCMBCE7/6KOSYQ4fXgSfobPDx517C2q4bXpLLZQBV/vKkFQTx5WRh25tvZgRUxJK9ooWPuaCAxPcfRR/pnn1kC5wZ35BIjSbjxbDf7EPlXKV6s3a6GmUHTVwya3hkf9tUds1wvEqnEthtLUmPR85HKoO0PxoQXBSFBKJ3YPP9xSyWH5mxxuGKX/1gqlCfl1Neln5EL3R+DmCodhC9MahqHjXVQKbxMW5NScyzQerA7k+gDa1tswzsBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![Filteren van tijd reeksen](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>Regressie analyse

ADX ondersteunt Gesegmenteerde lineaire regressie analyse om de trend van de tijd reeks te schatten.
- Gebruik [series_fit_line ()](/azure/kusto/query/series-fit-linefunction) om de beste lijn aan te passen aan een tijd reeks voor algemene trend detectie.
- Gebruik [series_fit_2lines ()](/azure/kusto/query/series-fit-2linesfunction) voor het detecteren van trend wijzigingen ten opzichte van de basis lijn die nuttig zijn voor bewakings scenario's.

Voor beeld van `series_fit_line()`-en `series_fit_2lines()` functies in een time series-query:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuKqUUitKEnNS1GACMSnZZbEG+Vk5qUWa1Rq6iCLggSBYkAdRUD1qUUKIIHkjMSiEoXyzJIMjYrk/JzS3DzbCk0AUIIJ02EAAAA=) **\]**

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Regressie van tijd reeks](media/time-series-analysis/time-series-regression.png)

- Blauw: oorspronkelijke tijd reeks
- Groen: gemonteerde lijn
- Rood: twee bijpassende lijnen

> [!NOTE]
> De functie heeft het punt om de Jump (niveau wijziging) nauw keurig gedetecteerd.

### <a name="seasonality-detection"></a>Seizoensgebonden detectie

Veel metrische gegevens volgen het seizoen (periodieke) patronen. Gebruikers verkeer van Cloud Services bevat meestal dagelijkse en wekelijkse patronen die het beste rond het midden van de zakelijke dag zijn, en in de loop van het weekend. IoT Sens oren meten regel matig. Fysieke metingen, zoals de Tempe ratuur, de druk of de vochtigheids graad, kunnen ook seizoensgebonden gedrag tonen.

In het volgende voor beeld wordt de seizoensgebonden detectie toegepast op één maand verkeer van een webservice (2-uurs opslag locaties):

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuaqUShKzUtJLVIoycxNTc5ILCoBAHrjE80fAAAA) **\]**

```kusto
demo_series3
| render timechart 
```

![Seizoensgebonden tijd reeks](media/time-series-analysis/time-series-seasonality.png)

- Gebruik [series_periods_detect ()](/azure/kusto/query/series-periods-detectfunction) voor het automatisch detecteren van de peri Oden in de tijd reeks. 
- Gebruik [series_periods_validate ()](/azure/kusto/query/series-periods-validatefunction) als we weten dat een metriek een specifieke periode (n) moet hebben en we willen controleren of ze bestaan.

> [!NOTE]
> Het is een afwijkingen als er geen specifieke afzonderlijke Peri Oden bestaan

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12OwQ6CMBBE737FHKmpVtAr39IguwkYyzZ0IZj48TZSLx533szOEAfxieeR0/XwRpzlwb2iilkSShapl5mTQYvd5QvxxJqd1bQEi8vZor6RawaLxsA5FewcOjBKBOP0PXUMXL7lyrCeeIvdRPjrzIw35Qyoe6W2GY4qJMv9yb91xtX0AS7N323BAAAA) **\]**

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mv-expand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | Peri | scores | resterende |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1 |

De functie detecteert dagelijkse en wekelijkse seizoensgebondenheid. De dagelijkse scores zijn kleiner dan het wekelijks, omdat weekend dagen verschillen van werk dagen.

### <a name="element-wise-functions"></a>Element-functies

Reken kundige en logische bewerkingen kunnen worden uitgevoerd op een tijd reeks. Met behulp van [series_subtract ()](/azure/kusto/query/series-subtractfunction) kunnen we een resterende tijd reeks berekenen, dat wil zeggen, het verschil tussen de oorspronkelijke ruwe metriek en een vloeiende vorm, en zoeken naar afwijkingen in het residuele signaal:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WQQU/DMAyF7/sVT5waqWjrgRPqb+AAgmPltR6LSNLJcdhA+/G4izRAnLhEerbfl2cHVkSfBkUPnfNIgaSZOM5DpDceMovn3OGMXGIk8Z+8jDdPPvKjUjw4d78KC4NO/2LQ6Tfjz/jqjEXeVolUYj/OJWnjMPGOStB+gznhSoFPEEqv3Fz2aWukFt3eYfuBh/zMYlA+KafJmsOCrPRh56Ux2UL4wKRN1+LOtVApXF/37RTOfioUfvpz2arQqBVS2Q7rtc6wa4wlkPLVCLXIqE7DHvcsXOOh73Hz4tM0HzO6zQ1gDOx8UOvZrtayst0Y7z4babkkYQxMyQbGPYnCiGIxTS/fXGpfwk+n7uQBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| extend residual_num=series_subtract(num, ma_num) //to calculate residual time series
| where OsVer == "Windows 10"   // filter on Win 10 to visualize a cleaner chart 
| render timechart
```

![Tijdreeks bewerkingen](media/time-series-analysis/time-series-operations.png)

- Blauw: oorspronkelijke tijd reeks
- Rood: vloeiende tijd reeks
- Groen: rest tijd reeks

## <a name="time-series-workflow-at-scale"></a>Time Series-werk stroom op schaal

In het onderstaande voor beeld ziet u hoe deze functies op schaal kunnen worden uitgevoerd op duizenden tijd reeksen in seconden voor afwijkings detectie. Voer de volgende query uit om een aantal voor beelden van telemetriegegevens van de metrische gegevens over het aantal lees bewerkingen van de DB-service over vier dagen te bekijken:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKEnMTlUwAQArfAiiGgAAAA==) **\]**

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | Neem | Loc | anonOp | Base | DataRead |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | -865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 371921734563783410 | 255 | 0 |

En eenvoudige statistieken:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVcgrzbVNzi/NK9HQ1FHIzcyLL7EFkhohnr6uwSGOvgEg0cQKkGhiBZIoAEq2dK9VAAAA) **\]**

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | steen | min\_t | maximum\_t |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

Het bouwen van een tijd reeks in een opslag locatie van 1 uur van de Lees metriek (totaal vier dagen * 24 uur = 96 punten), resulteert in een normale schommeling van het patroon:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPMQvCMBSE9/6KGxOoYGfpIOjgUBDtXh7twwabFF6ittIfb2rBQSfHg+8+7joOsMZVATlC72vqSFTDtq8subHyLIZ9hgn+Zi2JefKMq/JQ7M/ltjhqvQGSbrbQ8JeFhm/LTyGZInbl1RIhTI3P6X5ROwp0ikmjd/hYYByE3IXV+1G6TEqRtTqahF3DgmAs1y1JwMOEVo0Rzdf6BbBH5FAHAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Tijd reeks op schaal](media/time-series-analysis/time-series-at-scale.png)

Het bovenstaande gedrag is misleidend omdat de enkele normale tijd reeks wordt geaggregeerd van duizenden verschillende instanties die mogelijk abnormale patronen hebben. Daarom maken we een tijd reeks per instantie. Een exemplaar wordt gedefinieerd op basis van Loc (locatie), anonOp (Operation) en DB (specifieke machine).

Hoeveel tijd reeksen kan er worden gemaakt?

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVUiqVPDJT9ZR8C/QUXBxAkol55fmlQAAWEsFxjQAAAA=) **\]**

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | Aantal |
|   | 18339 |

Nu gaan we een set van 18339 tijd reeksen van de metriek voor het lees aantal maken. We voegen de `by`-component toe aan de instructie make-Series, passen lineaire regressie toe en selecteren de bovenste twee tijd reeksen die de meest significante dalende trend hadden:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPsU7DQBBE+3zFdLmTTGHSgFAKUCiQiIKIe2u5rJ0T9l3YWwcH5eO5JBIFVJSzmnmz07Gi96FWzKExOepIzIb7WPcUDnVi8ZxKHJGGvifxX3yym+pp+biu7pcv1t4Bk+5EofFfFBp/U/4EJsdse+eri4QwbdKc9q1ZkNJrVhYx4IcCHyAUWjbnRcXlpQLl1uLtgOfoCqx2BRYPGcyjctjASPoYSLhA6uKObR5waasbr3XnA5tzrc0RjTtcn0hnKyg55KtkDAvU9+y2JIpPr1ujXjueT9cse+8YlVDTeIfVoNQymiiZ5ENSCi4vM3FQxAblzWx2a6f2G2UcBRyWAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![Tijd reeks bovenste twee](media/time-series-analysis/time-series-top-2.png)

De exemplaren weer geven:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPvW4CMRCEe55iSlsyBWkjChApIoESAb21udsQg38O26AD8fDx3SEUJVXKWc18s2M5wxmvM6bIIVVkKYqaXdCO/EUnjobTBDekk3MUzZU7u9i+rl4229nqXcpnYGQ7CrX/olD7m/InMLoV24HHg0RkqtOUzjuxoEzroiSCx4MC4xHJ71j0i9TwksLkS+LjgmWoFN4ahcW8gLnN7GuImI4niqyQbGhYlgFDm/40WVvjWfS1skRyaPDUkXorKFXl2MSw5yr/pN9Z31SyxuhbAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc
| project Loc, Op, DB, slope 
```

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Loc | BTW | Base | helling |
|   | Loc 15 | 37 | 1151 | -102743,910227889 |
|   | Loc 13 | 37 | 1249 | -86303.2334644601 |

In minder dan twee minuten ADX de analyse van de tijd reeks van dichtbij tot 20.000 en ontdekt twee abnormale tijd reeksen waarin het aantal lees bewerkingen plotseling is verwijderd.

Deze geavanceerde mogelijkheden in combi natie met ADX snelle prestaties bieden een unieke en krachtige oplossing voor time series-analyses.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [afwijkings detectie en prognose van tijd reeksen](/azure/data-explorer/anomaly-detection) in azure Data Explorer.
* Meer informatie over [machine learning-mogelijkheden](/azure/data-explorer/machine-learning-clustering) in azure Data Explorer.