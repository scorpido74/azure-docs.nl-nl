---
title: Detectie van tijdreeksen anomaliedetectie & prognoses in Azure Data Explorer
description: Meer informatie over het analyseren van tijdreeksgegevens voor anomaliedetectie en -prognoses met Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: a482fef93d43f92257608b65c9c0e2ade535bcca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194154"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Anomaliedetectie en -prognoses in Azure Data Explorer

Azure Data Explorer voert doorlopende verzameling telemetriegegevens uit van cloudservices of IoT-apparaten. Deze gegevens worden geanalyseerd voor verschillende inzichten, zoals het bewaken van de status van de service, fysieke productieprocessen, gebruikstrends en belastingsprognose. De analyse wordt uitgevoerd op tijdreeksen van geselecteerde statistieken om een afwijkingspatroon van de metrische ten opzichte van het normale basislijnpatroon te lokaliseren. Azure Data Explorer bevat native ondersteuning voor het maken, manipuleren en analyseren van meerdere tijdreeksen. Het kan duizenden tijdreeksen in seconden maken en analyseren, waardoor near-time monitoringoplossingen en workflows mogelijk zijn.

In dit artikel worden de detectie- en prognosemogelijkheden voor de azure Data Explorer-tijdreeksen beschreven. De toepasselijke tijdreeksfuncties zijn gebaseerd op een robuust bekend ontbindingsmodel, waarbij elke originele tijdreeks wordt afgebroken tot seizoensgebonden, trend- en restcomponenten. Afwijkingen worden gedetecteerd door uitschieters op de restcomponent, terwijl prognoses worden gedaan door de seizoens- en trendcomponenten te extrapoleren. De Azure Data Explorer-implementatie verbetert het basisontledingsmodel aanzienlijk door automatische seizoensdetectie, robuuste uitschieteranalyse en vectorized implementatie om duizenden tijdreeksen in enkele seconden te verwerken.

## <a name="prerequisites"></a>Vereisten

Lees [de analyse van de tijdreeksen in Azure Data Explorer](/azure/data-explorer/time-series-analysis) voor een overzicht van de mogelijkheden voor tijdreeksen.

## <a name="time-series-decomposition-model"></a>Deontbindingsmodel van de tijdreeks

Azure Data Explorer native implementation voor tijdreeksvoorspelling en anomaliedetectie maakt gebruik van een bekend ontledingsmodel. Dit model wordt toegepast op tijdreeksen met statistieken die naar verwachting periodiek en trendgedrag zullen manifesteren, zoals serviceverkeer, componentheartbeats en IoT-periodieke metingen om toekomstige metrische waarden te voorspellen en afwijkende waarden te detecteren. De aanname van dit regressieproces is dat anders dan het eerder bekende seizoens- en trendgedrag, de tijdreeks willekeurig wordt verdeeld. U vervolgens toekomstige metrische waarden voorspellen van de seizoens- en trendcomponenten, gezamenlijk basislijn genoemd, en het resterende deel negeren. U ook afwijkende waarden detecteren op basis van uitschietersanalyse met alleen het resterende gedeelte.
Als u een ontledingsmodel wilt maken, gebruikt u de functie [`series_decompose()`](/azure/kusto/query/series-decomposefunction). De `series_decompose()` functie neemt een set tijdreeksen en ontbindt elke keerreeksautomatisch naar de seizoensgebonden, trend-, rest- en basislijncomponenten. 

U bijvoorbeeld het verkeer van een interne webservice ontleden met behulp van de volgende query:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![Time Series-ontleding](media/anomaly-detection/series-decompose-timechart.png)

* De oorspronkelijke tijdreeks heeft het label **getal** (in het rood). 
* Het proces begint met automatische detectie van [`series_periods_detect()`](/azure/kusto/query/series-periods-detectfunction) de seizoensgebondenheid met behulp van de functie en haalt het **seizoenspatroon** (in paars) eruit.
* Het seizoenspatroon wordt afgetrokken van de oorspronkelijke tijdreeks en [`series_fit_line()`](/azure/kusto/query/series-fit-linefunction) een lineaire regressie wordt uitgevoerd met behulp van de functie om de **trendcomponent** te vinden (in lichtblauw).
* De functie trekt de trend af en de rest is de **restcomponent** (in het groen).
* Ten slotte voegt de functie de seizoens- en trendcomponenten toe om de **basislijn** (in het blauw) te genereren.

## <a name="time-series-anomaly-detection"></a>Detectie van tijdreeksen anomalieën

De [`series_decompose_anomalies()`](/azure/kusto/query/series-decompose-anomaliesfunction) functie vindt afwijkende punten op een reeks tijdreeksen. Deze functie `series_decompose()` roept op om het [`series_outliers()`](/azure/kusto/query/series-outliersfunction) ontledingsmodel te bouwen en wordt vervolgens uitgevoerd op de restcomponent. `series_outliers()`berekent anomaliescores voor elk punt van de restcomponent met behulp van de afrasteringstest van Tukey. Anomaliescores boven de 1,5 of lager -1,5 duiden op een milde anomaliestijging of -daling. Anomaliescores boven 3,0 of lager -3,0 duiden op een sterke afwijking. 

Met de volgende query u afwijkingen in intern webserviceverkeer detecteren:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![Detectie van tijdreeksen anomalieën](media/anomaly-detection/series-anomaly-detection.png)

* De originele tijdreeks (in het rood). 
* De basislijn (seizoensgebonden + trend) component (in blauw).
* De afwijkende punten (in paars) bovenop de originele tijdreeks. De afwijkende punten wijken aanzienlijk af van de verwachte basislijnwaarden.

## <a name="time-series-forecasting"></a>Prognoses van tijdreeksen

De [`series_decompose_forecast()`](/azure/kusto/query/series-decompose-forecastfunction) functie voorspelt toekomstige waarden van een reeks tijdreeksen. Deze functie `series_decompose()` roept op om het ontledingsmodel te bouwen en extrapoleert vervolgens voor elke tijdreeks de basislijncomponent in de toekomst.

Met de volgende query u het webserviceverkeer van volgende week voorspellen:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decomposition')
```

![Prognoses van tijdreeksen](media/anomaly-detection/series-forecasting.png)

* Originele statistiek (in het rood). Toekomstige waarden ontbreken en standaard ingesteld op 0.
* Extrapoleren van de basislijncomponent (in het blauw) om de waarden van volgende week te voorspellen.

## <a name="scalability"></a>Schaalbaarheid

Met de syntaxis van queryteksten in Azure Data Explorer kan één aanroep meerdere tijdreeksen verwerken. De unieke geoptimaliseerde implementatie zorgt voor snelle prestaties, wat essentieel is voor effectieve anomaliedetectie en -prognoses bij het bewaken van duizenden tellers in bijna realtime scenario's.

In de volgende query wordt de verwerking van drie tijdreeksen tegelijk weergegeven:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![Schaalbaarheid van tijdreeksen](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Samenvatting

In dit document worden de native Azure Data Explorer-functies beschreven voor detectie en prognoses van tijdreeksen. Elke oorspronkelijke tijdreeks wordt afgebroken tot seizoensgebonden, trend- en restcomponenten voor het detecteren van afwijkingen en/of prognoses. Deze functionaliteiten kunnen worden gebruikt voor near real-time monitoringscenario's, zoals foutdetectie, voorspellend onderhoud en vraag- en belastingprognoses.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [machineleermogelijkheden](/azure/data-explorer/machine-learning-clustering) in Azure Data Explorer.
