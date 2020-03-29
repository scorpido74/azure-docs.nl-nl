---
title: Tijdreeksgegevens analyseren met Azure Data Explorer
description: Meer informatie over het analyseren van tijdreeksgegevens in de cloud met Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 3873b25394f91ce1c1601c348de2098198ba7fdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74765480"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Analyse van tijdreeksen in Azure Data Explorer

Azure Data Explorer (ADX) voert doorlopende verzameling telemetriegegevens uit van cloudservices of IoT-apparaten. Deze gegevens kunnen worden geanalyseerd voor verschillende inzichten, zoals het bewaken van de status van de service, fysieke productieprocessen en gebruikstrends. De analyse wordt uitgevoerd op tijdreeksen geselecteerde statistieken om een afwijking in het patroon te vinden in vergelijking met het typische basislijnpatroon.
ADX bevat native ondersteuning voor het maken, manipuleren en analyseren van meerdere tijdreeksen. Leer in dit onderwerp hoe ADX wordt gebruikt om **duizenden tijdreeksen in seconden**te maken en te analyseren, waardoor near real-time bewakingsoplossingen en workflows mogelijk zijn.

## <a name="time-series-creation"></a>Het maken van tijdreeksen

In deze sectie maken we eenvoudig en intuïtief een grote set `make-series` reguliere tijdreeksen met behulp van de operator en vullen we ontbrekende waarden in als dat nodig is.
De eerste stap in de analyse van de tijdreeksen is het partitioneren en transformeren van de oorspronkelijke telemetrietabel naar een reeks tijdreeksen. De tabel bevat meestal een tijdstempelkolom, contextuele dimensies en optionele statistieken. De afmetingen worden gebruikt om de gegevens te partitioneren. Het doel is om duizenden tijdreeksen per partitie te maken op regelmatige tijdstippen.

De invoertabel *demo_make_series1* bevat 600K records van willekeurig webserviceverkeer. Gebruik de onderstaande opdracht om 10 records te bekijken:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03MTo0vTi3KTC02VKhRKAFyFQwNADOyzKUbAAAA)**\]**

```kusto
demo_make_series1 | take 10 
```

De resulterende tabel bevat een tijdstempelkolom, drie kolommen met contextuele dimensies en geen statistieken:

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Tijdstempel | BrowserVer BrowserVer | OsVer OsVer | Land/regio |
|   | 2016-08-25 09:12:35.4020000 | Chrome 51.0 | Windows 7 | Verenigd Koninkrijk |
|   | 2016-08-25 09:12:41.1120000 | Chrome 52.0 | Windows 10 |   |
|   | 2016-08-25 09:12:46.2300000 | Chrome 52.0 | Windows 7 | Verenigd Koninkrijk |
|   | 2016-08-25 09:12:46.5100000 | Chrome 52.0 | Windows 10 | Verenigd Koninkrijk |
|   | 2016-08-25 09:12:46.5570000 | Chrome 52.0 | Windows 10 | Republiek Litouwen |
|   | 2016-08-25 09:12:47.0470000 | Chrome 52.0 | Windows 8.1 | India |
|   | 2016-08-25 09:12:51.3600000 | Chrome 52.0 | Windows 10 | Verenigd Koninkrijk |
|   | 2016-08-25 09:12:51.6930000 | Chrome 52.0 | Windows 7 | Nederland |
|   | 2016-08-25 09:12:56.4240000 | Chrome 52.0 | Windows 10 | Verenigd Koninkrijk |
|   | 2016-08-25 09:13:08.7230000 | Chrome 52.0 | Windows 10 | India |

Aangezien er geen statistieken zijn, kunnen we alleen een reeks tijdreeksen maken die het aantal verkeer zelf vertegenwoordigen, verdeeld door besturingssysteem met behulp van de volgende query:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPwQrCMBAE0Hu/Yo4NVLBn6Td4ULyWtV1tMJtIsoEq/XhbC4J48jgw+5h1rBDrW0UDDakjR7HsWUIrdOM2cbScakxIWYSiffJSL49W+KAkd2N2hVsMGv8yaPw2furFhCVu1gifpelC9loa9Hyh7LTZInh8FFiPSP7K5fufap1UoR4Mzg/s04njjEb2PUfofNYNFPUFtJiguAEBAAA=)**\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- Gebruik [`make-series`](/azure/kusto/query/make-seriesoperator) de operator om een set van drie tijdreeksen te maken, waarbij:
    - `num=count()`: tijdreeks van het verkeer
    - `range(min_t, max_t, 1h)`: tijdreekswordt gemaakt in opslaglocaties van 1 uur in het tijdsbereik (oudste en nieuwste tijdstempels van tabelrecords)
    - `default=0`: geef de vulmethode op voor ontbrekende opslaglocaties om normale tijdreeksen te maken. U kunt [`series_fill_const()`](/azure/kusto/query/series-fill-constfunction) [`series_fill_forward()`](/azure/kusto/query/series-fill-forwardfunction)ook [`series_fill_backward()`](/azure/kusto/query/series-fill-backwardfunction) [`series_fill_linear()`](/azure/kusto/query/series-fill-linearfunction) gebruik maken van , en voor wijzigingen
    - `byOsVer`: partitie per besturingssysteem
- De gegevensstructuur werkelijke tijdreeksen is een numerieke array van de geaggregeerde waarde per elke keeropslag. We `render timechart` gebruiken voor visualisatie.

In de bovenstaande tabel hebben we drie partities. We kunnen een aparte tijdreeks maken: Windows 10 (rood), 7 (blauw) en 8.1 (groen) voor elke OS-versie zoals te zien in de grafiek:

![Tijdreekspartitie](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Analysefuncties voor tijdreeksen

In deze sectie voeren we typische serieverwerkingsfuncties uit.
Zodra een set tijdreeksen is gemaakt, ondersteunt ADX een groeiende lijst met functies om ze te verwerken en te analyseren die te vinden zijn in de documentatie van de [tijdreeksen.](/azure/kusto/query/machine-learning-and-tsa) We beschrijven een paar representatieve functies voor het verwerken en analyseren van tijdreeksen.

### <a name="filtering"></a>Filteren

Filteren is een gangbare praktijk bij signaalverwerking en handig voor tijdreeksverwerkingstaken (bijvoorbeeld een luidruchtig signaal vloeiend signaal, wijzigingsdetectie).
- Er zijn twee algemene filterfuncties:
    - [`series_fir()`](/azure/kusto/query/series-firfunction): Toepassing van FIR filter. Wordt gebruikt voor eenvoudige berekening van het voortschrijdend gemiddelde en differentiatie van de tijdreeksen voor wijzigingsdetectie.
    - [`series_iir()`](/azure/kusto/query/series-iirfunction): IIR-filter toepassen. Wordt gebruikt voor exponentiële smoothing en cumulatieve som.
- `Extend`de tijdreeksen die zijn ingesteld door een nieuwe voortschrijdende gemiddelde reeks van grootte 5-opslaglocaties (met de naam *ma_num)* aan de query toe te voegen:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPQavCMBCE7/6KOSYQ4fXgSfobPDx517C2q4bXpLLZQBV/vKkFQTx5WRh25tvZgRUxJK9ooWPuaCAxPcfRR/pnn1kC5wZ35BIjSbjxbDf7EPlXKV6s3a6GmUHTVwya3hkf9tUds1wvEqnEthtLUmPR85HKoO0PxoQXBSFBKJ3YPP9xSyWH5mxxuGKX/1gqlCfl1Neln5EL3R+DmCodhC9MahqHjXVQKbxMW5NScyzQerA7k+gDa1tswzsBAAA=)**\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![Tijdreeksfiltering](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>Regressieanalyse

ADX ondersteunt gesegmenteerde lineaire regressieanalyse om de trend van de tijdreeks te schatten.
- Gebruik [series_fit_line()](/azure/kusto/query/series-fit-linefunction) om de beste lijn aan te passen aan een tijdreeks voor algemene trenddetectie.
- Gebruik [series_fit_2lines()](/azure/kusto/query/series-fit-2linesfunction) om trendwijzigingen ten opzichte van de basislijn te detecteren die nuttig zijn voor het bewaken van scenario's.

Voorbeeld `series_fit_line()` van `series_fit_2lines()` en functies in een tijdreeksquery:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuKqUUitKEnNS1GACMSnZZbEG+Vk5qUWa1Rq6iCLggSBYkAdRUD1qUUKIIHkjMSiEoXyzJIMjYrk/JzS3DzbCk0AUIIJ02EAAAA=)**\]**

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Regressie van tijdreeksen](media/time-series-analysis/time-series-regression.png)

- Blauw: originele tijdreeks
- Groen: ingerichte lijn
- Rood: twee ingerichte lijnen

> [!NOTE]
> De functie nauwkeurig gedetecteerd de sprong (niveau verandering) punt.

### <a name="seasonality-detection"></a>Seizoensdetectie

Veel statistieken volgen seizoensgebonden (periodieke) patronen. Gebruikersverkeer van cloudservices bevat meestal dagelijkse en wekelijkse patronen die het hoogst zijn rond het midden van de werkdag en het laagst 's nachts en in het weekend. IoT-sensoren meten in periodieke intervallen. Fysieke metingen zoals temperatuur, druk of vochtigheid kunnen ook seizoensgebonden gedrag vertonen.

In het volgende voorbeeld wordt seizoensdetectie gebruikt op een maand verkeer van een webservice (opslaglocaties van 2 uur):

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuaqUShKzUtJLVIoycxNTc5ILCoBAHrjE80fAAAA)**\]**

```kusto
demo_series3
| render timechart 
```

![Seizoensinvloeden voor tijdreeksen](media/time-series-analysis/time-series-seasonality.png)

- Gebruik [series_periods_detect()](/azure/kusto/query/series-periods-detectfunction) om de perioden in de tijdreeks automatisch te detecteren. 
- Gebruik [series_periods_validate()](/azure/kusto/query/series-periods-validatefunction) als we weten dat een statistiek een specifieke specifieke periode(en) moet hebben en we willen controleren of deze bestaan.

> [!NOTE]
> Het is een anomalie als specifieke verschillende perioden niet bestaan

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12OwQ6CMBBE737FHKmpVtAr39IguwkYyzZ0IZj48TZSLx533szOEAfxieeR0/XwRpzlwb2iilkSShapl5mTQYvd5QvxxJqd1bQEi8vZor6RawaLxsA5FewcOjBKBOP0PXUMXL7lyrCeeIvdRPjrzIw35Qyoe6W2GY4qJMv9yb91xtX0AS7N323BAAAA)**\]**

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mv-expand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | Perioden | Scores | Dagen |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1 |

De functie detecteert dagelijkse en wekelijkse seizoensgebondenheid. De dagelijkse scores minder dan de wekelijkse omdat weekend dagen zijn verschillend van weekdagen.

### <a name="element-wise-functions"></a>Element-gewijze functies

Rekenkundige en logische bewerkingen kunnen worden gedaan op een tijdreeks. Met behulp van [series_subtract()](/azure/kusto/query/series-subtractfunction) kunnen we een resttijdreeks berekenen, dat wil zeggen het verschil tussen originele ruwe metrische en een gladgestreken, en zoeken naar afwijkingen in het restsignaal:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WQQU/DMAyF7/sVT5waqWjrgRPqb+AAgmPltR6LSNLJcdhA+/G4izRAnLhEerbfl2cHVkSfBkUPnfNIgaSZOM5DpDceMovn3OGMXGIk8Z+8jDdPPvKjUjw4d78KC4NO/2LQ6Tfjz/jqjEXeVolUYj/OJWnjMPGOStB+gznhSoFPEEqv3Fz2aWukFt3eYfuBh/zMYlA+KafJmsOCrPRh56Ux2UL4wKRN1+LOtVApXF/37RTOfioUfvpz2arQqBVS2Q7rtc6wa4wlkPLVCLXIqE7DHvcsXOOh73Hz4tM0HzO6zQ1gDOx8UOvZrtayst0Y7z4babkkYQxMyQbGPYnCiGIxTS/fXGpfwk+n7uQBAAA=)**\]**

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

![Bewerkingen voor tijdreeksen](media/time-series-analysis/time-series-operations.png)

- Blauw: originele tijdreeks
- Rood: gladgestreken tijdreeks
- Groen: resttijdreeks

## <a name="time-series-workflow-at-scale"></a>Werkstroom voor tijdreeksen op schaal

Het onderstaande voorbeeld laat zien hoe deze functies op schaal kunnen worden uitgevoerd op duizenden tijdreeksen in seconden voor anomaliedetectie. Als u een paar voorbeeldtelemetrierecords van de leestellingsstatistiek van een DB-service gedurende vier dagen wilt bekijken, voert u de volgende query uit:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKEnMTlUwAQArfAiiGgAAAA==)**\]**

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | Tijdstempel | Loc | anonOp (anonOp) | Db | Gegevenslezen |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | -865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 371921734563783410 | 255 | 0 |

En eenvoudige statistieken:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVcgrzbVNzi/NK9HQ1FHIzcyLL7EFkhohnr6uwSGOvgEg0cQKkGhiBZIoAEq2dK9VAAAA)**\]**

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | num | min\_t | max\_t |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

Het bouwen van een tijdreeks in 1 uur bakken van de gelezen metrische (totaal vier dagen * 24 uur = 96 punten), resulteert in normale patroonfluctuatie:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPMQvCMBSE9/6KGxOoYGfpIOjgUBDtXh7twwabFF6ittIfb2rBQSfHg+8+7joOsMZVATlC72vqSFTDtq8subHyLIZ9hgn+Zi2JefKMq/JQ7M/ltjhqvQGSbrbQ8JeFhm/LTyGZInbl1RIhTI3P6X5ROwp0ikmjd/hYYByE3IXV+1G6TEqRtTqahF3DgmAs1y1JwMOEVo0Rzdf6BbBH5FAHAQAA)**\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Tijdreeksen op schaal](media/time-series-analysis/time-series-at-scale.png)

Het bovenstaande gedrag is misleidend, omdat de enkele normale tijdreeks wordt samengevoegd uit duizenden verschillende instanties die abnormale patronen kunnen hebben. Daarom maken we een tijdreeks per exemplaar. Een instantie wordt gedefinieerd door Loc (locatie), anonOp (bediening) en DB (specifieke machine).

Hoeveel tijdseries kunnen we maken?

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVUiqVPDJT9ZR8C/QUXBxAkol55fmlQAAWEsFxjQAAAA=)**\]**

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | Count |
|   | 18339 |

We gaan een set van 18339 tijdreeksen maken van de meetstatistiek voor het tellen van de gelezen telling. We voegen `by` de clausule toe aan de instructie make-series, passen lineaire regressie toe en selecteren de bovenste twee tijdreeksen met de belangrijkste dalende trend:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPsU7DQBBE+3zFdLmTTGHSgFAKUCiQiIKIe2u5rJ0T9l3YWwcH5eO5JBIFVJSzmnmz07Gi96FWzKExOepIzIb7WPcUDnVi8ZxKHJGGvifxX3yym+pp+biu7pcv1t4Bk+5EofFfFBp/U/4EJsdse+eri4QwbdKc9q1ZkNJrVhYx4IcCHyAUWjbnRcXlpQLl1uLtgOfoCqx2BRYPGcyjctjASPoYSLhA6uKObR5waasbr3XnA5tzrc0RjTtcn0hnKyg55KtkDAvU9+y2JIpPr1ujXjueT9cse+8YlVDTeIfVoNQymiiZ5ENSCi4vM3FQxAblzWx2a6f2G2UcBRyWAQAA)**\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![Tijdserie top twee](media/time-series-analysis/time-series-top-2.png)

Geef de instanties weer:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPvW4CMRCEe55iSlsyBWkjChApIoESAb21udsQg38O26AD8fDx3SEUJVXKWc18s2M5wxmvM6bIIVVkKYqaXdCO/EUnjobTBDekk3MUzZU7u9i+rl4229nqXcpnYGQ7CrX/olD7m/InMLoV24HHg0RkqtOUzjuxoEzroiSCx4MC4xHJ71j0i9TwksLkS+LjgmWoFN4ahcW8gLnN7GuImI4niqyQbGhYlgFDm/40WVvjWfS1skRyaPDUkXorKFXl2MSw5yr/pN9Z31SyxuhbAQAA)**\]**

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
|   | Loc | Op | Db | Helling |
|   | Loc 15 | 37 | 1151 | -102743.910227889 |
|   | Loc 13 | 37 | 1249 | -86303.2334644601 |

In minder dan twee minuten analyseerde ADX bijna 20.000 tijdreeksen en detecteerde twee abnormale tijdreeksen waarin het aantal leesgegevens plotseling daalde.

Deze geavanceerde mogelijkheden in combinatie met ADX-snelle prestaties bieden een unieke en krachtige oplossing voor tijdreeksanalyse.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [detectie en prognoses van tijdreeksen](/azure/data-explorer/anomaly-detection) in Azure Data Explorer.
* Meer informatie over [machineleermogelijkheden](/azure/data-explorer/machine-learning-clustering) in Azure Data Explorer.