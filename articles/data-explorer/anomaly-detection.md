---
title: Anomalie detectie en prognose van tijd Series in azure Data Explorer
description: Meer informatie over het analyseren van Time Series-gegevens voor anomalie detectie en prognoses met behulp van Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 0e06569a3a6948836201b333501bf2de0416d4ca
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766035"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Anomalie detectie en prognose in azure Data Explorer

Azure Data Explorer voert een voortdurende verzameling telemetrie-gegevens uit vanuit Cloud Services of IoT-apparaten. Deze gegevens worden geanalyseerd op verschillende inzichten, zoals het bewaken van service status, fysieke productie processen, gebruiks trends en het laden van prognoses. De analyse wordt uitgevoerd op de tijd reeks geselecteerde metrische gegevens om een afwijkings patroon te vinden van de metriek ten opzichte van het typische normale basislijn patroon. Azure Data Explorer bevat systeem eigen ondersteuning voor het maken, bewerken en analyseren van meerdere tijd reeksen. Het kan duizenden tijd reeksen in seconden maken en analyseren, waardoor realtime-bewakings oplossingen en-werk stromen kunnen worden uitgevoerd.

In dit artikel vindt u meer informatie over de mogelijkheden voor het afwijkings detectie en de prognose van Azure Data Explorer time series. De betreffende tijdreeks functies zijn gebaseerd op een robuust model met bekende ontleding, waarbij elke oorspronkelijke tijd reeks wordt opgesplitst in seizoen-, trend-en rest-onderdelen. Afwijkingen worden gedetecteerd door uitbijtingen van het residuele onderdeel, terwijl er prognoses worden gemaakt door de seizoen-en trend onderdelen te extrapoleren. De Azure Data Explorer-implementatie verbetert het basis model van de ontleding door automatische seizoensgebonden detectie, robuuste uitschieter analyse en gevectore implementatie om duizenden tijd reeksen in seconden te verwerken.

## <a name="prerequisites"></a>Vereisten

Lees de [tijd reeks analyse in Azure Data Explorer](/azure/data-explorer/time-series-analysis) voor een overzicht van de mogelijkheden van tijd reeksen.

## <a name="time-series-decomposition-model"></a>Model voor decompositie van Time Series

Azure Data Explorer systeem eigen implementatie voor time series-voor spelling en anomalie detectie maakt gebruik van een goed bekend ontledings model. Dit model wordt toegepast op tijds reeksen van metrische gegevens die naar verwachting het periodieke en trend gedrag van manifesten, zoals service verkeer, onderdeel heartbeats en IoT periodieke metingen, voor het voors pellen van toekomstige metrische waarden en het detecteren van afwijkend. De veronderstelling van dit regressie proces is dat anders dan het eerder bekende seizoen-en trend gedrag, de tijd reeks wille keurig wordt gedistribueerd. U kunt vervolgens toekomstige metrische waarden voor de seizoen-en trend onderdelen, met de naam basis lijn, en het rest gedeelte negeren. U kunt afwijkende waarden ook detecteren op basis van uitschieter-analyse met alleen het residuele gedeelte.
Als u een ontledings model wilt maken, gebruikt u de functie [`series_decompose()`](/azure/kusto/query/series-decomposefunction). De functie `series_decompose()` gebruikt een set tijd reeksen en stelt automatisch elke tijd reeks op de onderdelen seizoen, trend, rest en baseline. 

U kunt bijvoorbeeld verkeer van een interne webservice afbreken met behulp van de volgende query:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=) **\]**

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

* De oorspronkelijke tijd reeks heeft de label **num** (rood). 
* Het proces wordt gestart door de automatische detectie van de seizoensgebondenheid met behulp van de functie [`series_periods_detect()`](/azure/kusto/query/series-periods-detectfunction) en het **seizoen** patroon (in paars) op te halen.
* Het seizoen patroon wordt afgetrokken van de oorspronkelijke tijd reeks en er wordt een lineaire regressie uitgevoerd met behulp van de functie [`series_fit_line()`](/azure/kusto/query/series-fit-linefunction) om het **trend** onderdeel te vinden (licht blauw).
* De functie trekt de trend af en de rest is het **rest** onderdeel (groen).
* Ten slotte voegt de functie de onderdelen seizoen en trend toe om de **basis lijn** te genereren (in het blauw).

## <a name="time-series-anomaly-detection"></a>Anomalie detectie tijd Series

De functie [`series_decompose_anomalies()`](/azure/kusto/query/series-decompose-anomaliesfunction) afwijkende punten vindt voor een set tijd reeksen. Deze functie roept `series_decompose()` op om het ontledings model te bouwen en voert vervolgens [`series_outliers()`](/azure/kusto/query/series-outliersfunction) uit voor het residuele onderdeel. `series_outliers()` afwijkingen van de afwijkings scores voor elk punt van het residuele onderdeel berekent met de Fence-test van Tukey. Afwijkende scores van meer dan 1,5 of minder dan 1,5 duiden op een milde afwijkings toename of afname. Afwijkende scores van meer dan 3,0 of minder dan 3,0 geven een sterke afwijking aan. 

Met de volgende query kunt u afwijkingen in het interne webservice-verkeer detecteren:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==) **\]**

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

![Anomalie detectie tijd Series](media/anomaly-detection/series-anomaly-detection.png)

* De oorspronkelijke tijd reeks (in het rood). 
* Het onderdeel basis lijn (seizoen en trend) (blauw).
* De afwijkende punten (in paars) boven op de oorspronkelijke tijd reeks. De afwijkende punten komen aanzienlijk af van de verwachte basislijn waarden.

## <a name="time-series-forecasting"></a>Tijd reeks prognose

De functie [`series_decompose_forecast()`](/azure/kusto/query/series-decompose-forecastfunction) voor spelt toekomstige waarden van een set tijd reeksen. Deze functie roept `series_decompose()` op om het ontledings model te bouwen en vervolgens voor elke tijd reeks de basislijn component in de toekomst te extrapoleren.

Met de volgende query kunt u het webservice-verkeer van de volgende week voors pellen:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==) **\]**

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

![Tijd reeks prognose](media/anomaly-detection/series-forecasting.png)

* Oorspronkelijke metrische waarde (in het rood). Toekomstige waarden ontbreken standaard en zijn ingesteld op 0.
* Extrapolatie het basislijn onderdeel (blauw) om de waarden van de volgende week te voors pellen.

## <a name="scalability"></a>Schaalbaarheid

Met de query taal syntaxis van Azure Data Explorer kan één aanroep meerdere tijd reeksen verwerken. De unieke geoptimaliseerde implementatie maakt snelle prestaties mogelijk, wat essentieel is voor effectieve detectie en prognoses bij het bewaken van duizenden tellers in bijna realtime scenario's.

Met de volgende query wordt de verwerking van drie tijd reeksen tegelijk weer gegeven:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA) **\]**

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

![Schaal baarheid van de tijd reeks](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Samenvatting

In dit document vindt u informatie over de systeem eigen Azure Data Explorer-functies voor het detecteren van afwijkingen en prognoses van tijd reeksen. Elke oorspronkelijke tijd reeks wordt opgebouwd uit seizoen-, trend-en rest-onderdelen voor het detecteren van afwijkingen en/of prognoses. Deze functies kunnen worden gebruikt voor bijna realtime bewakings scenario's, zoals fout detectie, voor speld onderhoud en vraag-en belasting prognoses.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [machine learning-mogelijkheden](/azure/data-explorer/machine-learning-clustering) in azure Data Explorer.
