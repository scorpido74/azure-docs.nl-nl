---
title: Machine learning-mogelijkheden in Azure Data Explorer
description: Machine learning-clustering gebruiken voor hoofdoorzaakanalyse in Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: fe72031ef9ade7473dc4d5de7e090e92ef2a6843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769928"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Machine learning-mogelijkheden in Azure Data Explorer

Azure Data Explorer, een Big Data-analyseplatform, wordt gebruikt om servicestatus, QoS of slecht functionerende apparaten te controleren op afwijkend gedrag met behulp van ingebouwde [anomaliedetectie- en prognosefuncties.](/azure/data-explorer/anomaly-detection) Zodra een afwijkend patroon wordt gedetecteerd, wordt een Root Cause Analysis (RCA) uitgevoerd om de anomalie te beperken of op te lossen.

Het diagnoseproces is complex en langdurig en wordt uitgevoerd door domeinexperts. Het proces omvat het ophalen en samenvoegen van aanvullende gegevens uit verschillende bronnen voor hetzelfde tijdsbestek, op zoek naar wijzigingen in de verdeling van waarden op meerdere dimensies, het in kaart brengen van extra variabelen en andere technieken op basis van domeinkennis en Intuïtie. Aangezien deze diagnosescenario's gebruikelijk zijn in Azure Data Explorer, zijn machine learning-plug-ins beschikbaar om de diagnosefase eenvoudiger te maken en de duur van de RCA te verkorten.

Azure Data Explorer heeft drie [`autocluster`](/azure/kusto/query/autoclusterplugin)Machine [`basket`](/azure/kusto/query/basketplugin)Learning-plug-ins: , en [`diffpatterns`](/azure/kusto/query/diffpatternsplugin). Alle plug-ins implementeren clusteralgoritmen. De `autocluster` `basket` en plugins cluster een `diffpatterns` enkele record set en de plugin clusters van de verschillen tussen twee record sets.

## <a name="clustering-a-single-record-set"></a>Een enkele recordset clusteren

Een veelvoorkomend scenario bevat een gegevensset die is geselecteerd door een specifieke criteria, zoals het tijdvenster, waarin afwijkend gedrag, metingen van apparaten met hoge temperaturen, opdrachten voor lange duur en gebruikers met de beste uitgaven worden weergegeven. We willen graag een eenvoudige en snelle manier om gemeenschappelijke patronen (segmenten) in de gegevens te vinden. Patronen zijn een subset van de gegevensset waarvan de records dezelfde waarden delen over meerdere dimensies (categorische kolommen). De volgende query bouwt en toont een tijdreeks serviceuitzonderingen over een week in opslaglocaties van tien minuten:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPsaoCQQyF4d6nCFa7oHCtZd9B0F6G8ajByWTJZHS5+PDOgpVgYRn485EkOAnno9NAriWGFKw7QfQYUy0O43zZ0JNKFQnG/5jrbmeIXHBgwd6DjH2/JVqk2QrTL1aYvlifa4tni29YlzaiUK4yRK3Zu54006dBZ1N5/+X6PqpRI23+pFGGfIKRtz5egzk92K+dsycMyz3szhGEKWJ01lxI760O9ABuq0bMcvV2hqFoqnOz7F9BdSHlSgEAAA==)**\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Tijddiagram voor serviceuitzonderingen](media/machine-learning-clustering/service-exceptions-timechart.png)

Het aantal serviceuitzonderingen correleert met het totale serviceverkeer. U duidelijk zien het dagelijkse patroon, voor werkdagen van maandag tot vrijdag, met een stijging van de service uitzondering telt mid-dag, en daalt in telt tijdens de nacht. Vlakke lage tellingen zijn zichtbaar in het weekend. Uitzonderingspieken kunnen worden gedetecteerd met behulp van [tijdreeksanomaliedetectie](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) in Azure Data Explorer.

De tweede piek in de gegevens vindt plaats op dinsdagmiddag. De volgende query wordt gebruikt om deze piek verder te diagnosticeren. Gebruik de query om de grafiek rond de piek in hogere resolutie (acht uur in opslaglocaties van één minuut) opnieuw te tekenen om te controleren of het een scherpe piek is en de randen ervan te bekijken.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAyXNwQrCMBAE0Hu/YvHUooWkghSl/yDoyUsJyWpCk2xJNnjx403pbeYwbzwyBBdnnoxiZBewHYS89GLshzNIeRWiuzUGA83al8yYXPzI5gdBLdjnWjFDLGHSVCK3HVCEe0LtMj4r9mAVVngnCvsLMO3hOFqo2goyVCxhNJhgu9dWJYavY9uyY4/T4UV1XVm2CEM0kFe34AnkBhXGOs7kCzuKh+4P3/XM5M8AAAA=)**\]**

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Focus op spike-tijddiagram](media/machine-learning-clustering/focus-spike-timechart.png)

We zien een smalle piek van twee minuten van 15:00 tot 15:02. Tel in de volgende query de uitzonderingen in dit venster van twee minuten:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVHIzcyLL0hNzI4vsU1JLEktycxN1TAyMDTTNbDQNTJWMDS1MjDQtObKASlNrCCk1AioNCU1Nz8+Oae0uCS1KDMv3ZCrRqE8I7UoVSGgKDU5szg1BKgvuCQxt0AhKbWkPDU1TwPhBj09hCWaQI3J+aV5JQACnQoRpwAAAA==)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Count |
|---------|
|972    |

In de volgende query, voorbeeld 20 uitzonderingen van 972:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4XOsQrCMBSF4b1Pccd2aLmJKKL4DoLu4doeNDSJJb1SBx/eOHV0/37OCVCKPrkJMjo9DaJQH1FbNruW963dkNkemJtjFX5U3v+oLXRAfLo+vGZF9uluqg8tD2TQOaP3M66lu6jEiW7QBUj1+qHr1pGmhCojyPIX7QHvzakAAAA=)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | Regio | ScaleUnit | DeploymentId                     | Tracepoint | ServiceHost (ServiceHost)                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | scus scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | scus scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | scus scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | scus scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | scus scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | scus scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2 eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus (ncus)   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | scus scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | scus scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | scus scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | scus scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | scus scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus (ncus)   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | scus scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | scus scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | scus scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2 eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | scus scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | scus scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>Autocluster() gebruiken voor clustering met één recordset

Hoewel er minder dan duizend uitzonderingen zijn, is het nog steeds moeilijk om gemeenschappelijke segmenten te vinden, omdat er meerdere waarden in elke kolom zijn. U kunt [`autocluster()`](/azure/kusto/query/autoclusterplugin) plug-in gebruiken om direct een kleine lijst met gemeenschappelijke segmenten te extraheren en de interessante clusters te vinden binnen de twee minuten van de piek, zoals te zien is in de volgende query:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQrCMBRF937FG5OhJYkoovQfBN1DbC8aTNqSvlgHP94IQkf3c+65AUzRD3aCe1hue8dgHyGM0rta7WuzIb09KCWPVfii7vUPNQXtEUfbhTwzkh9uunrTckcCnRI6P+NSvDO7ONEVvACDWD80zRqRRcTThVxa5DKPv00hP81KL1+4AAAA)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| SegmentId SegmentId | Count | Percentage | Regio | ScaleUnit | DeploymentId | ServiceHost (ServiceHost) |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus (ncus) | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | Weu | su4 | be1d6d7ac9574cbc9a22cb8e20f16fc |  |

Uit de bovenstaande resultaten u zien dat het meest dominante segment 65,74% van de totale uitzonderingsrecords bevat en vier dimensies deelt. Het volgende segment komt veel minder vaak voor, bevat slechts 9,67% van de records en deelt drie dimensies. De andere segmenten komen nog minder vaak voor. 

Autocluster maakt gebruik van een eigen algoritme voor het ontginnen van meerdere dimensies en het extraheren van interessante segmenten. "Interessant" betekent dat elk segment een aanzienlijke dekking heeft van zowel de records die zijn ingesteld als de ingestelde functies. De segmenten zijn ook verschillend, wat betekent dat elk aanzienlijk verschilt van de andere. Een of meer van deze segmenten kunnen relevant zijn voor het RCA-proces. Om segmentbeoordeling en -beoordeling te minimaliseren, haalt autocluster slechts een kleine segmentlijst uit.

### <a name="use-basket-for-single-record-set-clustering"></a>Mandje() gebruiken voor clustering met één recordset

U de [`basket()`](/azure/kusto/query/basketplugin) plug-in ook gebruiken zoals te zien is in de volgende query:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQ6CMBgGd57iH9sB0tZojMZ3MNG9KfBFG1og7Y84+PDWidH9LncBTNGPdoYbLF96x2AfIYzSh1oda7MjvT8pJc9V+KHu/Q81Be0RJ9uFJTOSHx+6+tD6RAJdEzqfcS/ejV2cqQWvwCi2h6bZIrKIeLmwlBa1Lg9gIb9KJv2TswAAAA==)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| SegmentId SegmentId | Count | Percentage | Regio | ScaleUnit | DeploymentId | Tracepoint | ServiceHost (ServiceHost) |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | eau eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | eau eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | eau eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scus scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus (ncus) | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | Weu | su4 | be1d6d7ac9574cbc9a22cb8e20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

Basket implementeert het Apriori-algoritme voor het maken van itemsetmining en haalt alle segmenten waarvan de dekking van de recordset boven een drempel waardeligt (standaard 5%). U zien dat er meer segmenten zijn geëxtraheerd met vergelijkbare segmenten (bijvoorbeeld segmenten 0,1 of 2,3).

Beide plug-ins zijn krachtig en gemakkelijk te gebruiken, maar hun aanzienlijke beperking is dat ze cluster een enkele record ingesteld op een onbewaakte manier (zonder labels). Het is daarom onduidelijk of de geëxtraheerde patronen kenmerkend zijn voor de geselecteerde recordset (de afwijkende records) of de wereldrecordset.

## <a name="clustering-the-difference-between-two-records-sets"></a>Het verschil tussen twee recordssets clusteren

De [`diffpatterns()`](/azure/kusto/query/diffpatternsplugin) plugin overwint `autocluster` de `basket`beperking van en . `Diffpatterns`neemt twee recordsets en haalt de belangrijkste segmenten eruit die tussen hen verschillen. Een set bevat meestal de afwijkende record set wordt `autocluster` `basket`onderzocht (een geanalyseerd door en ). De andere set bevat de referentierecordset (basislijn). 

In de onderstaande `diffpatterns` query gebruiken we om interessante clusters te vinden binnen de twee minuten van de piek, die anders zijn dan clusters binnen de basislijn. We definiëren het basislijnvenster als de acht minuten voor 15:00 uur (toen de piek begon). We moeten ook uitbreiden door een binaire kolom (AB) aan geeft aan of een specifieke record behoort tot de basislijn of tot de afwijkende set. `Diffpatterns`implementeert een begeleid leeralgoritme, waarbij de twee klassenlabels zijn gegenereerd door de afwijkende versus de basislijnvlag (AB).

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA42QzU+DQBDF7/wVcwOi5UtrmhJM4OzBRO9kWqbtpssuYacfGv94t0CrxFTd02by5jfvPUkMtVBlQ7gtOauQiUVNXhLFD5NoNknuIJ7Oo8hPHXmS4vEvaXKWWuoCDUmh6Jr8fj79Tv6HfOanEIbwRLgnQFhjAwviA5EC3hCcCYCq6gamEVsC1oB7LfoRt6iMYKEVvGtFQXfeNFKc7mXe2MjNVzl+mARR6lRU63Ipd4apFWodOx9w2FBL4D23tBSGXi3mhbG+OPPGVQTB+ITvg24dGN7vlN5JTxhc+dYAHZls4LzIxGr1k/B4iXcLbq50jfLNtd9i8OB2jD3KnW0dKstokG08Zby8uLbyCfX/tG46AgAA)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
let min_baseline_t=datetime(2016-08-23 14:50);
let max_baseline_t=datetime(2016-08-23 14:58); // Leave a gap between the baseline and the spike to avoid the transition zone.
let splitime=(max_baseline_t+min_peak_t)/2.0;
demo_clustering1
| where (PreciseTimeStamp between(min_baseline_t..max_baseline_t)) or
        (PreciseTimeStamp between(min_peak_t..max_peak_t))
| extend AB=iff(PreciseTimeStamp > splitime, 'Anomaly', 'Baseline')
| evaluate diffpatterns(AB, 'Anomaly', 'Baseline')
```

| SegmentId SegmentId | CountA | CountB | PercentA PercentA | PercentB (PercentB) | PercentDiffAB PercentDiffAB | Regio | ScaleUnit | DeploymentId | Tracepoint |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | eau eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scus scus |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9.26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncus (ncus) | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | Weu | su4 | be1d6d7ac9574cbc9a22cb8e20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

Het meest dominante segment is hetzelfde `autocluster`segment dat werd geëxtraheerd door , de dekking op de twee minuten afwijkende venster is ook 65,74%. Maar de dekking op de acht minuten baseline venster is slechts 1,7%. Het verschil is 64,04%. Dit verschil lijkt te worden gerelateerd aan de afwijkende piek. U deze veronderstelling verifiëren door de oorspronkelijke grafiek op te splitsen in de records die behoren tot dit problematische segment ten opzichte van de andere segmenten zoals te zien in de onderstaande query:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WRsWrDMBCG9zzF4cmGGuJUjh2Ktw7tUkLTzuEsnRNRnRQkuSQlD185yRTo0EWIO913/J8MRWBttxE6iC5INOhzRey20owhktd2V8EZwsiMXv/Q9Dpfe5I60Idm2kTkQ1E8AczMxMLjf1h4/IN1PzY7Ax0jWQWBdomvhyF/p512FroOMsIxA0zdTdpKn1bHSzmMzbX8TAfjTkw2vqpLp69VpYQaatEogXOBsqrbtl5WDake6yabXWjkv7WkFxeuPGqG5VzWqhQrIUqx6B/L1WKB6aBViy01imT2ANnau94QT9c35xlNVqQAjF9UhpSHAtiRO+lGG/MCUoZ7CTB4x7ePie5mNbk4QDVn6E+ThUT0SQh5iGlM7tHHX4WFgLHOAQAA)**\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![Tijddiagram 'diffpatroon' valideren](media/machine-learning-clustering/validating-diffpattern-timechart.png)

Deze grafiek stelt ons in staat om te zien dat de piek op `diffpatterns` dinsdagmiddag was vanwege uitzonderingen uit dit specifieke segment, ontdekt met behulp van de plugin.

## <a name="summary"></a>Samenvatting

De Azure Data Explorer Machine Learning-plug-ins zijn handig voor veel scenario's. De `autocluster` `basket` en implementeren zonder toezicht leren algoritme en zijn gemakkelijk te gebruiken. `Diffpatterns`implementeert begeleid leeralgoritme en hoewel complexer, is het krachtiger in het extraheren van differentiatiesegmenten voor RCA.

Deze plug-ins worden interactief gebruikt in ad-hoc scenario's en in automatische near real-time monitoringservices. In Azure Data Explorer wordt tijdreeksanomaliedetectie gevolgd door een diagnoseproces dat sterk is geoptimaliseerd om te voldoen aan de vereiste prestatienormen.
