---
title: Machine Learning-functies schalen in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u Stream Analytics-taken schalen die machine learning-functies gebruiken, door partitionering- en streameenheden te configureren.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 5b08625d055063b3804a35a3344ff01c7edb79de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067010"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Uw Stream Analytics-taak schalen met azure machine learning studio(klassieke) functies

> [!TIP]
> Het wordt ten zeerste aanbevolen om [Azure Machine Learning UDF's](machine-learning-udf.md) te gebruiken in plaats van Azure Machine Learning Studio (klassieke) UDF voor betere prestaties en betrouwbaarheid.

In dit artikel wordt besproken hoe azure stream analytics-taken met Azure Machine Learning-functies efficiënt kunnen worden geschaald. Zie het artikel [Taken schalen](stream-analytics-scale-jobs.md)voor informatie over het schalen van Stream Analytics-taken in het algemeen.

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Wat is een Azure Machine Learning-functie in Stream Analytics?

Een Machine Learning-functie in Stream Analytics kan worden gebruikt als een gewone functieaanroep in de querytaal Stream Analytics. Achter de schermen zijn deze functieaanroepen echter eigenlijk Azure Machine Learning Web Service-aanvragen.

U de doorvoer van Machine Learning-webserviceaanvragen verbeteren door meerdere rijen samen te voegen in dezelfde API-aanroep voor webservice. Deze groepering wordt een mini-batch genoemd. Zie [Azure Machine Learning Studio (klassieke) Web Services voor](../machine-learning/studio/consume-web-services.md)meer informatie. Ondersteuning voor Azure Machine Learning Studio (klassiek) in Stream Analytics is in preview.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Een Functie Stream Analytics configureren met Machine Learning-functies

Er zijn twee parameters om de Machine Learning-functie te configureren die wordt gebruikt door uw Stream Analytics-taak:

* Batchgrootte van de functie Machine Learning roept aan.
* Het aantal Streaming Units (SU's) dat is ingericht voor de streamanalytics-taak.

Als u de juiste waarden voor SU's wilt bepalen, bepaalt u of u de latentie van de taak Stream Analytics of de doorvoer van elke SU wilt optimaliseren. SU's kunnen altijd worden toegevoegd aan een taak om de doorvoer van een goed verdeelde Stream Analytics-query te verhogen. Extra SU's verhogen de kosten van het uitvoeren van de taak.

Bepaal de *latentietolerantie* voor je Stream Analytics-taak. Als u de batchgrootte verhoogt, wordt de latentie van uw Azure Machine Learning-aanvragen en de latentie van de taak Stream Analytics vergroot.

Als u de batchgrootte verhoogt, kan de taak Stream Analytics **meer gebeurtenissen** verwerken met **hetzelfde aantal** Webserviceaanvragen voor Machine Learning. De toename van machine learning web service latentie is meestal sublineair aan de toename van batch grootte. 

Het is belangrijk om de meest kostenefficiënte batchgrootte voor een Machine Learning-webservice in een bepaalde situatie te overwegen. De standaardbatchgrootte voor webserviceaanvragen is 1000. U deze standaardgrootte wijzigen met de [Stream Analytics REST API](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "REST-API voor Stream Analytics") of de [PowerShell-client voor Stream Analytics.](stream-analytics-monitor-and-manage-jobs-use-powershell.md)

Zodra u een batchgrootte hebt bepaald, u het aantal streaming-eenheden (SU's) instellen op basis van het aantal gebeurtenissen dat de functie per seconde moet verwerken. Zie [Stream Analytics schaaltaken](stream-analytics-scale-jobs.md)voor meer informatie over streaming-eenheden.

Elke 6 SU's krijgen 20 gelijktijdige verbindingen met de Machine Learning-webservice. Echter, 1 SU baan en 3 SU banen krijgen 20 gelijktijdige verbindingen.  

Als uw toepassing 200.000 gebeurtenissen per seconde genereert en de batchgrootte 1000 is, is de resulterende latentie van webservice 200 ms. Dit tarief betekent dat elke verbinding elke seconde vijf aanvragen kan indienen bij de Machine Learning-webservice. Met 20 verbindingen kan de stream analytics-taak 20.000 gebeurtenissen in 200 ms en 100.000 gebeurtenissen in een seconde verwerken.

Om 200.000 gebeurtenissen per seconde te verwerken, heeft de Stream Analytics-taak 40 gelijktijdige verbindingen nodig, die uitkomen op 12 SU's. Het volgende diagram illustreert de aanvragen van de functie Stream Analytics naar het eindpunt van de Machine Learning-webservice : elke 6 SU's heeft maximaal 20 gelijktijdige verbindingen met machine learning-webservice.

![Schaalstream-analyse met machine learning-functies twee taakvoorbeeld](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Schaalstream-analyse met machine learning-functies twee taakvoorbeeld")

In het algemeen, ***B*** voor batchgrootte, ***L*** voor de webservicelatentie bij batchgrootte B in milliseconden, is de doorvoer van een Stream Analytics-taak met ***N*** SU's:

![Scale Stream Analytics met Machine Learning-functies Formule](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Scale Stream Analytics met Machine Learning-functies Formule")

U ook de 'max concurrent calls' configureren op de Machine Learning-webservice. Het wordt aanbevolen om deze parameter in te stellen op de maximale waarde (200 op dit moment).

Voor meer informatie over deze instelling, bekijk het [artikel Schalen voor Machine Learning Web Services](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Voorbeeld – Sentimentanalyse
In het volgende voorbeeld wordt een Stream Analytics-taak met de functie Machine Learning voor sentimentanalyse gebruikt, zoals beschreven in de handleiding voor de [integratie van Stream Analytics Machine Learning.](stream-analytics-machine-learning-integration-tutorial.md)

De query is een eenvoudige volledig gepartitioneerde query gevolgd door de **sentimentfunctie,** zoals weergegeven in het volgende voorbeeld:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Laten we eens kijken naar de configuratie die nodig is om een Stream Analytics-taak te maken, die sentimentanalyse van tweets doet met een snelheid van 10.000 tweets per seconde.

Met behulp van 1 SU, kan deze Stream Analytics taak omgaan met het verkeer? De taak kan de invoer bijhouden met behulp van de standaardbatchgrootte van 1000. De standaardlatentie van de machine learning-webservice voor sentimentanalyse (met een standaardbatchgrootte van 1000) creëert niet meer dan een seconde latentie.

De **algemene** of end-to-end latentie van de Stream Analytics-taak zou doorgaans enkele seconden zijn. Neem een meer gedetailleerde blik op deze Stream Analytics-taak, *met name* de Machine Learning-functie aanroept. Met een batchgrootte van 1000, een doorvoer van 10.000 gebeurtenissen duurt ongeveer 10 aanvragen naar de webservice. Zelfs met één SU zijn er voldoende gelijktijdige verbindingen om dit invoerverkeer aan te kunnen.

Als de invoergebeurtenissnelheid met 100x toeneemt, moet de taak Stream Analytics 1.000.000 tweets per seconde verwerken. Er zijn twee opties om de verhoogde schaal te bereiken:

1. Verhoog de batchgrootte.
2. Partitie de invoerstroom om de gebeurtenissen parallel te verwerken.

Met de eerste optie neemt de **taaklatentie** toe.

Met de tweede optie moet u meer SU's inrichten om meer gelijktijdige Machine Learning-webserviceaanvragen te hebben. Dit grotere aantal SU's, verhoogt de **kosten**van de baan .

Laten we eens kijken naar de schaling met behulp van de volgende latentiemetingen voor elke batchgrootte:

| Latentie | Batchgrootte |
| --- | --- |
| 200 ms | Batches van 1000 gebeurtenissen of lager |
| 250 ms | Batches voor 5.000 gebeurtenissen |
| 300 ms | Batches voor 10.000 gebeurtenissen |
| 500 ms | Batches voor 25.000 gebeurtenissen |

1. Met behulp van de eerste optie **(niet** inrichten van meer SU's). De batchgrootte kan worden verhoogd tot **25.000**. Als u de batchgrootte op deze manier verhoogt, kan de taak 1.000.000 gebeurtenissen verwerken met 20 gelijktijdige verbindingen met de Machine Learning-webservice (met een latentie van 500 ms per gesprek). Dus de extra latentie van de Stream Analytics-taak als gevolg van de sentimentfunctieaanvragen ten opzichte van de Machine Learning-webserviceaanvragen zou worden verhoogd van **200 ms** naar **500 ms.** De batchgrootte kan echter **niet** oneindig worden vergroot, omdat de Machine Learning-webservices de laadgrootte van een aanvraag vereist van 4 MB of kleiner en webservice een time-out aanvraagt na 100 seconden gebruik.
1. Met behulp van de tweede optie, de batch grootte wordt overgelaten aan 1000, met 200-ms web service latentie, elke 20 gelijktijdige verbindingen met de webservice in staat zou zijn om 1000 * 20 * 5 gebeurtenissen = 100.000 per seconde te verwerken. Dus om 1.000.000 gebeurtenissen per seconde te verwerken, zou de taak 60 SU's nodig hebben. In vergelijking met de eerste optie zou de taak Stream Analytics meer webservicebatchaanvragen doen, waardoor de kosten stijgen.

Hieronder vindt u een tabel voor de doorvoer van de taak Stream Analytics voor verschillende SU's en batchformaten (in aantal gebeurtenissen per seconde).

| batchgrootte (ML-latentie) | 500 (200 ms) | 1.000 (200 ms) | 5.000 (250 ms) | 10.000 (300 ms) | 25.000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2500 |5.000 |20.000 |30,000 |50,000 |
| **3 SUs** |2500 |5.000 |20.000 |30,000 |50,000 |
| **6 SUs** |2500 |5.000 |20.000 |30,000 |50,000 |
| **12 SU's** |5.000 |10.000 |40,000 |60.000 |100.000 |
| **18 SU's** |7.500 |15.000 |60.000 |90,000 |150.000 |
| **24 SUs** |10.000 |20.000 |80,000 |120,000 |200.000 |
| **…** |… |… |… |… |… |
| **60 SU's** |25,000 |50,000 |200.000 |300,000 |500.000 |

Inmiddels moet je al een goed inzicht hebben in hoe Machine Learning functioneert in Stream Analytics. U begrijpt waarschijnlijk ook dat Stream Analytics-taken gegevens uit gegevensbronnen 'ophalen' en dat elke 'pull', een batch gebeurtenissen retourneert die de taak Stream Analytics wilt verwerken. Welke invloed heeft dit pull-model op de webserviceaanvragen van Machine Learning?

Normaal gesproken is de batchgrootte die we instellen voor Machine Learning-functies niet bepaald deelbaar door het aantal gebeurtenissen dat wordt geretourneerd door elke Stream Analytics-taak 'pull'. Wanneer dit gebeurt, wordt de Webservice Machine Learning aangeroepen met 'gedeeltelijke' batches. Het gebruik van gedeeltelijke batches voorkomt extra taaklatentie overhead in samensmeltende gebeurtenissen van pull tot pull.

## <a name="new-function-related-monitoring-metrics"></a>Nieuwe functiegerelateerde monitoringstatistieken
In het monitorgebied van een Stream Analytics-taak zijn drie extra functiegerelateerde statistieken toegevoegd. Het zijn **functieaanvragen,** **functiegebeurtenissen** en **mislukte functieaanvragen,** zoals in de onderstaande afbeelding.

![Schaalstream-analyses met statistieken over machine learning-functies](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Schaalstream-analyses met statistieken over machine learning-functies")

De worden als volgt gedefinieerd:

**FUNCTIEAANVRAGEN**: Het aantal functieaanvragen.

**FUNCTIEGEBEURTENISSEN**: De nummergebeurtenissen in de functieaanvragen.

**FUNCTIEAANVRAGEN MISLUKT:** het aantal mislukte functieaanvragen.

## <a name="key-takeaways"></a>Belangrijkste afhaalmaaltijden

Als u een Stream Analytics-taak wilt schalen met Machine Learning-functies, moet u rekening houden met de volgende factoren:

1. De invoergebeurtenissnelheid.
2. De getolereerde latentie voor de lopende Stream Analytics-taak (en dus de batchgrootte van de WebServiceaanvragen van Machine Learning).
3. De ingerichte Stream Analytics-sU's en het aantal Machine Learning-webserviceaanvragen (de extra functiegerelateerde kosten).

Een volledig gepartitioneerde Stream Analytics-query is als voorbeeld gebruikt. Als er een complexere query nodig is, is het [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) een geweldige bron voor het verkrijgen van extra hulp van het Stream Analytics-team.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over Stream Analytics:

* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)
