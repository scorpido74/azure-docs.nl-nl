---
title: Machine learning-mogelijkheden in azure Data Explorer
description: Gebruik machine learning Clustering voor het analyseren van hoofd oorzaken in azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: fe72031ef9ade7473dc4d5de7e090e92ef2a6843
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769928"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Machine learning-mogelijkheden in azure Data Explorer

Azure Data Explorer, een platform voor Big data-analyse, wordt gebruikt voor het bewaken van de service status, QoS of het defecte apparaat voor afwijkend gedrag met behulp van ingebouwde [anomalie detectie-en Voorspellings](/azure/data-explorer/anomaly-detection) functies. Zodra een afwijkend patroon wordt gedetecteerd, wordt er een analyse van de hoofd oorzaak (RCA) uitgevoerd om de afwijkingen te verhelpen of op te lossen.

Het diagnose proces is complex en lang en wordt uitgevoerd door domein experts. Het proces omvat het ophalen en samen voegen van extra gegevens uit verschillende bronnen voor dezelfde periode, op zoek naar wijzigingen in de verdeling van waarden op meerdere dimensies, het diagram toevoegen van aanvullende variabelen en andere technieken op basis van domein kennis en intuition. Omdat deze diagnose scenario's gebruikelijk zijn in azure Data Explorer, zijn er machine learning-invoeg toepassingen beschikbaar om de diagnose fase gemakkelijker te maken en de duur van de RCA te verkorten.

Azure Data Explorer heeft drie Machine Learning plugins: [`autocluster`](/azure/kusto/query/autoclusterplugin), [`basket`](/azure/kusto/query/basketplugin)en [`diffpatterns`](/azure/kusto/query/diffpatternsplugin). Alle invoeg toepassingen implementeren cluster algoritmen. De `autocluster`-en `basket`-invoeg toepassingen clusteren een enkele Recordset en de `diffpatterns`-invoeg toepassing clusters de verschillen tussen twee record sets.

## <a name="clustering-a-single-record-set"></a>Een set met één recordset clusteren

Een veelvoorkomend scenario bevat een gegevensset die is geselecteerd op basis van specifieke criteria, zoals een tijd venster, waarmee afwijkend gedrag, het aantal bewaarden voor een hoog temperatuur niveau, opdrachten voor lange duur en best uitgave gebruikers worden vertonen. We willen een gemakkelijke en snelle manier om algemene patronen (segmenten) te vinden in de gegevens. Patronen zijn een subset van de gegevensset waarvan records dezelfde waarden delen in meerdere dimensies (categorische columns). De volgende query bouwt en toont een tijd reeks van service-uitzonde ringen gedurende een week in bakken van tien minuten:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPsaoCQQyF4d6nCFa7oHCtZd9B0F6G8ajByWTJZHS5+PDOgpVgYRn485EkOAnno9NAriWGFKw7QfQYUy0O43zZ0JNKFQnG/5jrbmeIXHBgwd6DjH2/JVqk2QrTL1aYvlifa4tni29YlzaiUK4yRK3Zu54006dBZ1N5/+X6PqpRI23+pFGGfIKRtz5egzk92K+dsycMyz3szhGEKWJ01lxI760O9ABuq0bMcvV2hqFoqnOz7F9BdSHlSgEAAA==) **\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Service-uitzonde ringen timechart](media/machine-learning-clustering/service-exceptions-timechart.png)

Het aantal service-uitzonde ringen vercorrelt met het algehele service verkeer. U kunt het dagelijks patroon duidelijk zien, voor werk dagen van maandag tot en met vrijdag, met een toename in het aantal service-uitzonde ringen van middel grote aantallen en daalt tijdens de nacht. Vlakke lage aantallen zijn in het weekend zichtbaar. Uitzonderings pieken kunnen worden gedetecteerd met behulp van [anomalie detectie van tijd reeksen](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) in azure Data Explorer.

De tweede Prikker in de gegevens vindt plaats op dinsdag middag. De volgende query wordt gebruikt om deze piek te onderzoeken. Gebruik de query om de grafiek opnieuw te tekenen rond de Prikker in een hogere resolutie (acht uur in bakken van één minuut) om te controleren of het een scherpe piek is en de randen ervan weer te geven.

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAyXNwQrCMBAE0Hu/YvHUooWkghSl/yDoyUsJyWpCk2xJNnjx403pbeYwbzwyBBdnnoxiZBewHYS89GLshzNIeRWiuzUGA83al8yYXPzI5gdBLdjnWjFDLGHSVCK3HVCEe0LtMj4r9mAVVngnCvsLMO3hOFqo2goyVCxhNJhgu9dWJYavY9uyY4/T4UV1XVm2CEM0kFe34AnkBhXGOs7kCzuKh+4P3/XM5M8AAAA=) **\]**

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Focus op Prikker timechart](media/machine-learning-clustering/focus-spike-timechart.png)

We zien een smalle piek van twee minuten van 15:00 tot 15:02. In de volgende query telt u de uitzonde ringen in dit venster van twee minuten:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVHIzcyLL0hNzI4vsU1JLEktycxN1TAyMDTTNbDQNTJWMDS1MjDQtObKASlNrCCk1AioNCU1Nz8+Oae0uCS1KDMv3ZCrRqE8I7UoVSGgKDU5szg1BKgvuCQxt0AhKbWkPDU1TwPhBj09hCWaQI3J+aV5JQACnQoRpwAAAA==) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Aantal |
|---------|
|972    |

In de volgende query worden 20 uitzonde ringen van 972 voor beeld:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4XOsQrCMBSF4b1Pccd2aLmJKKL4DoLu4doeNDSJJb1SBx/eOHV0/37OCVCKPrkJMjo9DaJQH1FbNruW963dkNkemJtjFX5U3v+oLXRAfLo+vGZF9uluqg8tD2TQOaP3M66lu6jEiW7QBUj1+qHr1pGmhCojyPIX7QHvzakAAAA=) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | Regio | ScaleUnit | DeploymentId                     | Tracerings punt | ServiceHost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | SCUS   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | SCUS   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | SCUS   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | SCUS   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | SCUS   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>Autocluster () gebruiken voor clustering van enkelvoudige record sets

Hoewel er minder dan duizend uitzonde ringen zijn, is het nog steeds moeilijk om algemene segmenten te vinden, aangezien er meerdere waarden in elke kolom zijn. U kunt [`autocluster()`](/azure/kusto/query/autoclusterplugin) -invoeg toepassing gebruiken om direct een kleine lijst met algemene segmenten op te halen en de interessante clusters binnen de twee minuten van de Prikker te vinden, zoals wordt weer gegeven in de volgende query:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQrCMBRF937FG5OhJYkoovQfBN1DbC8aTNqSvlgHP94IQkf3c+65AUzRD3aCe1hue8dgHyGM0rta7WuzIb09KCWPVfii7vUPNQXtEUfbhTwzkh9uunrTckcCnRI6P+NSvDO7ONEVvACDWD80zRqRRcTThVxa5DKPv00hP81KL1+4AAAA) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| Segment | Aantal | Procent | Regio | ScaleUnit | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | SCUS | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | SCUS | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

U kunt in de bovenstaande resultaten zien dat het meest dominante segment 65,74% van het totale aantal uitzonderings records bevat en vier dimensies deelt. Het volgende segment is veel minder gangbaar, bevat slechts 9,67% van de records en deelt drie dimensies. De andere segmenten zijn nog minder gangbaar. 

Bij autocluster wordt een eigen algoritme gebruikt voor de analyse van meerdere dimensies en het extra heren van interessante segmenten. ' Interessant ' betekent dat elk segment een aanzienlijke dekking heeft van zowel de recordsset als de functies die zijn ingesteld. De segmenten zijn ook afwijkend, wat betekent dat elke segment significant afwijkt van de andere. Een of meer van deze segmenten kunnen relevant zijn voor het RCA-proces. Om de segment controle en-evaluatie te minimaliseren, extraheert autocluster alleen een kleine segment lijst.

### <a name="use-basket-for-single-record-set-clustering"></a>Mand gebruiken () voor het clusteren van afzonderlijke record sets

U kunt ook de [`basket()`](/azure/kusto/query/basketplugin) -invoeg toepassing gebruiken, zoals wordt weer gegeven in de volgende query:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQ6CMBgGd57iH9sB0tZojMZ3MNG9KfBFG1og7Y84+PDWidH9LncBTNGPdoYbLF96x2AfIYzSh1oda7MjvT8pJc9V+KHu/Q81Be0RJ9uFJTOSHx+6+tD6RAJdEzqfcS/ejV2cqQWvwCi2h6bZIrKIeLmwlBa1Lg9gIb9KJv2TswAAAA==) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| Segment | Aantal | Procent | Regio | ScaleUnit | DeploymentId | Tracerings punt | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | SCUS | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | SCUS | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | SCUS |  |  |  |  |
| 9 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

In het mandje wordt het apriori-algoritme geïmplementeerd voor de analyse van itemsset en worden alle segmenten waarvan de dekking van de recordset boven een drempel waarde ligt, geëxtraheerd (standaard 5%). U kunt zien dat er meer segmenten zijn geëxtraheerd met vergelijk bare records (bijvoorbeeld segmenten 0, 1 of 2, 3).

Beide invoeg toepassingen zijn krachtig en eenvoudig te gebruiken, maar hun aanzienlijke beperking is dat ze één record op een onbewaakte manier (zonder labels) clusteren. Daarom is het niet duidelijk of de geëxtraheerde patronen de geselecteerde recordset (de afwijkende records) of de set globale records kenmerken.

## <a name="clustering-the-difference-between-two-records-sets"></a>Het verschil tussen twee record sets clusteren

De [`diffpatterns()`](/azure/kusto/query/diffpatternsplugin) -invoeg toepassing overwint de beperking van `autocluster` en `basket`. `Diffpatterns` neemt twee record sets op en extraheert de hoofd segmenten die onderling verschillen. Een set bevat meestal de afwijkende recordset die wordt onderzocht (één geanalyseerd door `autocluster` en `basket`). De andere set bevat de verwijzings recordset (basis lijn). 

In de onderstaande query gebruiken we `diffpatterns` om interessante clusters binnen de twee minuten van de Prikker te vinden, die afwijken van de clusters in de basis lijn. Het venster basis lijn wordt gedefinieerd als de acht minuten vóór 15:00 (wanneer de Prikker is gestart). Ook moet worden uitgebreid met een binaire kolom (AB) waarmee wordt aangegeven of een specifieke record behoort tot de basis lijn of de afwijkende set. `Diffpatterns` implementeert een algoritme voor leren met Super visie, waarbij de twee klassen labels zijn gegenereerd door afwijkende tegenover de basislijn vlag (AB).

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA42QzU+DQBDF7/wVcwOi5UtrmhJM4OzBRO9kWqbtpssuYacfGv94t0CrxFTd02by5jfvPUkMtVBlQ7gtOauQiUVNXhLFD5NoNknuIJ7Oo8hPHXmS4vEvaXKWWuoCDUmh6Jr8fj79Tv6HfOanEIbwRLgnQFhjAwviA5EC3hCcCYCq6gamEVsC1oB7LfoRt6iMYKEVvGtFQXfeNFKc7mXe2MjNVzl+mARR6lRU63Ipd4apFWodOx9w2FBL4D23tBSGXi3mhbG+OPPGVQTB+ITvg24dGN7vlN5JTxhc+dYAHZls4LzIxGr1k/B4iXcLbq50jfLNtd9i8OB2jD3KnW0dKstokG08Zby8uLbyCfX/tG46AgAA) **\]**

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

| Segment | CountA | CountB | Percentagea | PercentB | PercentDiffAB | Regio | ScaleUnit | DeploymentId | Tracerings punt |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65,74 | 1,7 | 64,04 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17,18 | 44,16 | 26,97 | SCUS |  |  |  |
| 2 | 92 | 356 | 9,47 | 28,9 | 19,43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9,26 | 27,27 | 18,01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8,44 | 25,81 | 17,38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5,66 | 20,45 | 14,8 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5,86 | 16,56 | 10,69 |  |  |  |  |

Het meest dominante segment is hetzelfde segment dat is geëxtraheerd door `autocluster`, het dekkings percentage van het afwijkings venster van twee minuten is ook 65,74%. De dekking voor het basis venster van acht minuten is slechts 1,7%. Het verschil is 64,04%. Dit verschil lijkt te zijn gerelateerd aan de afwijkende piek. U kunt deze hypo these controleren door de oorspronkelijke grafiek te splitsen in de records die deel uitmaken van dit problematische segment, vergeleken met de andere segmenten, zoals wordt weer gegeven in de onderstaande query:

**\[** [**Klik om de query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WRsWrDMBCG9zzF4cmGGuJUjh2Ktw7tUkLTzuEsnRNRnRQkuSQlD185yRTo0EWIO913/J8MRWBttxE6iC5INOhzRey20owhktd2V8EZwsiMXv/Q9Dpfe5I60Idm2kTkQ1E8AczMxMLjf1h4/IN1PzY7Ax0jWQWBdomvhyF/p512FroOMsIxA0zdTdpKn1bHSzmMzbX8TAfjTkw2vqpLp69VpYQaatEogXOBsqrbtl5WDake6yabXWjkv7WkFxeuPGqG5VzWqhQrIUqx6B/L1WKB6aBViy01imT2ANnau94QT9c35xlNVqQAjF9UhpSHAtiRO+lGG/MCUoZ7CTB4x7ePie5mNbk4QDVn6E+ThUT0SQh5iGlM7tHHX4WFgLHOAQAA) **\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![Diffpattern segment timechart valideren](media/machine-learning-clustering/validating-diffpattern-timechart.png)

In deze grafiek kan worden weer geven dat de Prikker op dinsdag middag door uitzonde ringen van dit specifieke segment werd gedetecteerd met behulp van de `diffpatterns`-invoeg toepassing.

## <a name="summary"></a>Samenvatting

De Azure Data Explorer Machine Learning-invoeg toepassingen zijn handig voor veel scenario's. Het `autocluster` en `basket` het implementeren van een algoritme voor leren zonder toezicht en zijn gemakkelijk te gebruiken. `Diffpatterns` implementeert een algoritme voor leren met Super visie en hoewel complexere, is het krachtiger in het extra heren van differentiatie segmenten voor RCA.

Deze invoeg toepassingen worden interactief gebruikt in ad hoc scenario's en in automatische nagenoeg real-time bewakings Services. In azure Data Explorer wordt de afwijkings detectie van Time Series gevolgd door een diagnose proces dat zeer is geoptimaliseerd om te voldoen aan de vereiste prestatie normen.
