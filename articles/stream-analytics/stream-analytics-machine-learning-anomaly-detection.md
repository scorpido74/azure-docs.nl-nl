---
title: Anomaliedetectie in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u Azure Stream Analytics en Azure Machine Learning samen gebruiken om afwijkingen op te sporen.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 51b9c827d453eef2e2e75e1aa5222204eaa38d0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525529"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Anomaliedetectie in Azure Stream Analytics

Azure Stream Analytics is beschikbaar in zowel de cloud als Azure IoT Edge en biedt ingebouwde op machine learning gebaseerde mogelijkheden voor anomaliedetectie die kunnen worden gebruikt om de twee meest voorkomende afwijkingen te controleren: tijdelijk en persistent. Met de **AnomalyDetection_SpikeAndDip-** **en AnomalyDetection_ChangePoint** functies u anomaliedetectie rechtstreeks uitvoeren in uw Stream Analytics-taak.

De machine learning-modellen gaan uit van een gelijkmatig bemonsterde tijdreeks. Als de tijdreeks niet uniform is, u een aggregatiestap invoegen met een tuimelvenster voordat u anomaliedetectie aanroept.

De machine learning-bewerkingen ondersteunen op dit moment geen seizoenstrends of multi-variate correlaties.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Anomaliedetectie met behulp van machine learning in Azure Stream Analytics

In de volgende video wordt uitgelegd hoe u een afwijking in realtime detecteren met behulp van machine learning-functies in Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Real-Time-ML-Based-Anomaly-Detection-In-Azure-Stream-Analytics/player]

## <a name="model-behavior"></a>Modelgedrag

Over het algemeen verbetert de nauwkeurigheid van het model met meer gegevens in het schuifvenster. De gegevens in het opgegeven schuifvenster worden behandeld als onderdeel van het normale waardenbereik voor dat tijdsbestek. Het model houdt alleen rekening met de gebeurtenisgeschiedenis boven het schuifvenster om te controleren of de huidige gebeurtenis afwijkend is. Als het schuifvenster beweegt, worden oude waarden uit de training van het model verwijderd.

De functies werken door het vaststellen van een bepaald normaal op basis van wat ze tot nu toe hebben gezien. Uitschieters worden geïdentificeerd door te vergelijken met het vastgestelde normale, binnen het betrouwbaarheidsniveau. De venstergrootte moet worden gebaseerd op de minimale gebeurtenissen die nodig zijn om het model te trainen voor normaal gedrag, zodat wanneer een anomalie optreedt, het in staat zou zijn om het te herkennen.

De reactietijd van het model neemt toe met de grootte van de geschiedenis, omdat het moet worden vergeleken met een hoger aantal gebeurtenissen uit het verleden. Het wordt aanbevolen om alleen het noodzakelijke aantal gebeurtenissen op te nemen voor betere prestaties.

Hiaten in de tijdreeks kunnen het gevolg zijn van het model dat op bepaalde tijdspunten geen gebeurtenissen ontvangt. Deze situatie wordt afgehandeld door Stream Analytics met behulp van imputatielogica. De geschiedenisgrootte en een tijdsduur voor hetzelfde schuifvenster worden gebruikt om de gemiddelde snelheid te berekenen waarmee gebeurtenissen naar verwachting zullen aankomen.

Een anomalie generator [hier](https://aka.ms/asaanomalygenerator) beschikbaar kan worden gebruikt om een Iot Hub voeden met gegevens met verschillende anomalie patronen. Een ASA-taak kan worden ingesteld met deze anomaliedetectiefuncties om te lezen vanuit deze Iot Hub en afwijkingen te detecteren.

## <a name="spike-and-dip"></a>Spike en dip

Tijdelijke afwijkingen in een gebeurtenisstroom van een tijdreeks worden spikes en dips genoemd. Pieken en dalen kunnen worden bewaakt met behulp van de machine learning-operator, [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Voorbeeld van piek- en dipanomalie](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

In hetzelfde schuifvenster, als een tweede piek kleiner is dan de eerste, is de berekende score voor de kleinere piek waarschijnlijk niet significant genoeg in vergelijking met de score voor de eerste piek binnen het opgegeven betrouwbaarheidsniveau. U proberen het betrouwbaarheidsniveau van het model te verlagen om dergelijke afwijkingen op te sporen. Als u echter te veel waarschuwingen krijgt, u een hoger betrouwbaarheidsinterval gebruiken.

In de volgende voorbeeldquery wordt uitgegaan van een uniforme invoersnelheid van één gebeurtenis per seconde in een schuifvenster van 2 minuten met een geschiedenis van 120 gebeurtenissen. De uiteindelijke SELECT-instructie haalt en output de score en anomaliestatus met een betrouwbaarheidsniveau van 95%.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>Punt wijzigen

Aanhoudende afwijkingen in een gebeurtenisstroom van een tijdreeks zijn wijzigingen in de verdeling van waarden in de gebeurtenisstroom, zoals niveauwijzigingen en trends. In Stream Analytics worden dergelijke afwijkingen gedetecteerd met behulp van de machine learning-gebaseerde [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) operator.

Aanhoudende veranderingen duren veel langer dan pieken en dalen en kunnen duiden op catastrofale gebeurtenissen(en). Hardnekkige veranderingen zijn meestal niet zichtbaar voor het blote oog, maar kunnen worden gedetecteerd met de **AnomalyDetection_ChangePoint** operator.

De volgende afbeelding is een voorbeeld van een niveauwijziging:

![Voorbeeld van een anomalie van niveauwijziging](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

De volgende afbeelding is een voorbeeld van een trendverandering:

![Voorbeeld van trendveranderingsanomalie](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

In de volgende voorbeeldquery wordt uitgegaan van een uniforme invoersnelheid van één gebeurtenis per seconde in een schuifvenster van 20 minuten met een geschiedenisgrootte van 1200 gebeurtenissen. De uiteindelijke SELECT-instructie haalt en output de score en anomaliestatus met een betrouwbaarheidsniveau van 80%.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="performance-characteristics"></a>Prestatiekenmerken

De prestaties van deze modellen zijn afhankelijk van de geschiedenisgrootte, vensterduur, gebeurtenisbelasting en of functieniveaupartitionering wordt gebruikt. In deze sectie worden deze configuraties besproken en worden voorbeelden gegeven voor het ondersteunen van opnamepercentages van 1K, 5K en 10K-gebeurtenissen per seconde.

* **Geschiedenisgrootte** - Deze modellen presteren lineair met **geschiedenisgrootte.** Hoe langer de geschiedenis grootte, hoe langer de modellen duren om een nieuwe gebeurtenis te scoren. Dit komt omdat de modellen de nieuwe gebeurtenis vergelijken met elk van de gebeurtenissen uit het verleden in de geschiedenisbuffer.
* **Vensterduur** - De **vensterduur** moet aangeven hoe lang het duurt om zoveel gebeurtenissen te ontvangen als opgegeven door de geschiedenisgrootte. Zonder zoveel gebeurtenissen in het venster zou Azure Stream Analytics ontbrekende waarden aantasten. Vandaar, CPU-verbruik is een functie van de grootte van de geschiedenis.
* **Gebeurtenisbelasting** - Hoe groter de **gebeurtenisbelasting,** hoe meer werk dat door de modellen wordt uitgevoerd, wat het CPU-verbruik beïnvloedt. De taak kan worden opgeschaald door het beschamend parallel, ervan uitgaande dat het zinvol is voor zakelijke logica om meer input partities te gebruiken.
* **Functieniveau partitionering** - **Functieniveau partitionering** wordt gedaan met behulp van ```PARTITION BY``` binnen de functieaanroep anomaliedetectie. Dit type partitionering voegt een overhead toe, omdat de status voor meerdere modellen tegelijkertijd moet worden gehandhaafd. Functieniveau partitionering wordt gebruikt in scenario's zoals apparaatniveau partitionering.

### <a name="relationship"></a>Relatie
De geschiedenisgrootte, vensterduur en totale gebeurtenisbelasting zijn op de volgende manier gerelateerd:

windowDuration (in ms) = 1000 * historySize / (Totaal invoergebeurtenissen per seconde / aantal invoerpartities)

Voeg bij het partitioneren van de functie functie 'PARTITION BY deviceId' toe aan de functieaanroep voor anomaliedetectie.

### <a name="observations"></a>Opmerkingen
De volgende tabel bevat de doorvoerwaarnemingen voor één knooppunt (6 SU) voor de niet-gepartitioneerde aanvraag:

| Geschiedenisgrootte (gebeurtenissen) | Vensterduur (ms) | Totale invoergebeurtenissen per seconde |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2200 |
| 600 | 728 | 1,650 |
| 6.000 | 10,910 | 1100 |

De volgende tabel bevat de doorvoerwaarnemingen voor één knooppunt (6 SU) voor de partitiecase:

| Geschiedenisgrootte (gebeurtenissen) | Vensterduur (ms) | Totale invoergebeurtenissen per seconde | Aantal apparaten |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1100 | 10 |
| 600 | 10,910 | 1100 | 10 |
| 6.000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6.000 | 2,181,819 | <550 | 100 |

Voorbeeldcode voor het uitvoeren van de bovenstaande niet-gepartitioneerde configuraties bevindt zich in de [repo Streaming At Scale](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) van Azure Samples. De code maakt een streamanalytics-taak zonder partitionering op functieniveau, waarbij gebeurtenishub wordt gebruikt als invoer en uitvoer. De invoerbelasting wordt gegenereerd met behulp van testclients. Elke invoergebeurtenis is een json-document van 1 KB. Gebeurtenissen simuleren een IoT-apparaat dat JSON-gegevens verzendt (voor maximaal 1K-apparaten). De geschiedenisgrootte, vensterduur en totale gebeurtenisbelasting zijn gevarieerd over 2 invoerpartities.

> [!Note]
> Voor een nauwkeurigere schatting u de voorbeelden aanpassen aan uw scenario.

### <a name="identifying-bottlenecks"></a>Knelpunten identificeren
Gebruik het deelvenster Statistieken in uw Azure Stream Analytics-taak om knelpunten in uw pijplijn te identificeren. Controleer **invoer-/uitvoergebeurtenissen** voor doorvoer en ['Watermarkdelay'](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) of **Backlogged-gebeurtenissen** om te zien of de taak de invoersnelheid bijhoudt. Zoek voor gebeurtenishub-statistieken naar **geweigerde aanvragen** en pas de drempeleenheden dienovereenkomstig aan. Controleer voor Cosmos **DB-statistieken de verbruikte RU/s per partitiesleutelbereik** onder Doorvoer om ervoor te zorgen dat uw partitiesleutelbereiken gelijkmatig worden verbruikt. Controleer voor Azure SQL DB **Log IO** en **CPU**.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)

