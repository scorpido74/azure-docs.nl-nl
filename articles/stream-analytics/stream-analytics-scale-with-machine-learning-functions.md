---
title: Machine Learning functies in Azure Stream Analytics schalen
description: In dit artikel wordt beschreven hoe u Stream Analytics taken kunt schalen die gebruikmaken van Machine Learning-functies door partitionering en stroom eenheden te configureren.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: c03b9fbd06b0a9bc4a70b0fb3a78bc9ce136b704
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835577"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Uw Stream Analytics-taak schalen met de functies Azure Machine Learning Studio (klassiek)

> [!TIP]
> Het is raadzaam om [Azure machine learning UDFs](machine-learning-udf.md) te gebruiken in plaats van Azure machine learning Studio (klassiek) UDF voor verbeterde prestaties en betrouw baarheid.

In dit artikel wordt beschreven hoe u Azure Stream Analytics taken die gebruikmaken van Azure Machine Learning functies efficiënt kunt schalen. Zie voor meer informatie over het schalen van Stream Analytics taken in het algemeen de taken voor het [schalen](stream-analytics-scale-jobs.md)van artikelen.

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Wat is een Azure Machine Learning-functie in Stream Analytics?

Een Machine Learning functie in Stream Analytics kan worden gebruikt als een reguliere functie aanroep in de Stream Analytics query taal. Achter de schermen worden deze functie aanroepen echter eigenlijk Azure Machine Learning aanvragen voor webservices.

U kunt de door Voer van Machine Learning aanvragen voor webservices verbeteren door meerdere rijen samen in de dezelfde webservice-API-aanroep te ' batcheren '. Deze groepering wordt een mini-batch genoemd. Zie [Azure machine learning Studio (Classic) Web Services (Engelstalig)](../machine-learning/studio/consume-web-services.md)voor meer informatie. Ondersteuning voor Azure Machine Learning Studio (klassiek) in Stream Analytics is in de preview-versie.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Een Stream Analytics-taak met Machine Learning functies configureren

Er zijn twee para meters voor het configureren van de Machine Learning-functie die wordt gebruikt door uw Stream Analytics taak:

* Batch grootte van de functie aanroepen van Machine Learning.
* Het aantal streaming-eenheden (SUs) dat is ingericht voor de Stream Analytics taak.

Als u de juiste waarden voor SUs wilt bepalen, moet u beslissen of u de latentie van de Stream Analytics taak of de door Voer van elke SU wilt optimaliseren. SUs kan altijd worden toegevoegd aan een taak om de door Voer van een goed gepartitioneerde Stream Analytics query te verg Roten. Bij extra SUs worden de kosten voor het uitvoeren van de taak verhoogd.

Bepaal de latentie *tolerantie* voor uw stream Analytics taak. Door de Batch grootte te verhogen, wordt de latentie van uw Azure Machine Learning-aanvragen en de latentie van de Stream Analytics taak verg root.

Door de Batch grootte te verg Roten, kan de Stream Analytics taak **meer gebeurtenissen** met **hetzelfde aantal** machine learning aanvragen voor webservices verwerken. De toename van de latentie van Machine Learning webservices is meestal sublineair voor de verhoging van de Batch grootte. 

Het is belang rijk dat u rekening houdt met de meest efficiënte Batch grootte voor een Machine Learning-webservice in een bepaalde situatie. De standaard Batch grootte voor web service-aanvragen is 1000. U kunt deze standaard grootte wijzigen met behulp van de [Stream Analytics rest API](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "REST-API voor Stream Analytics") of de [Power shell-client voor stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Zodra u hebt besloten over een batch grootte, kunt u het aantal streaming-eenheden (SUs) instellen, op basis van het aantal gebeurtenissen dat de functie per seconde moet verwerken. Zie [Stream Analytics Scale-taken](stream-analytics-scale-jobs.md)voor meer informatie over streaming-eenheden.

Elke 6 SUs krijgt 20 gelijktijdige verbindingen met de Machine Learning-webservice. 1 SU-taak en 3 SU-taken krijgen echter 20 gelijktijdige verbindingen.  

Als uw toepassing 200.000 gebeurtenissen per seconde genereert en de Batch grootte 1000 is, is de resulterende webservice-latentie 200 MS. Dit betekent dat elke verbinding elke seconde vijf aanvragen kan doen voor de Machine Learning-webservice. Met 20 verbindingen kan de Stream Analytics-taak in een seconde 20.000 gebeurtenissen in 200 MS en 100.000 gebeurtenissen verwerken.

Voor het verwerken van 200.000 gebeurtenissen per seconde, is voor de Stream Analytics-taak 40 gelijktijdige verbindingen nodig die aan 12 SUs worden geleverd. In het volgende diagram ziet u de aanvragen van de Stream Analytics-taak naar het eind punt van de Machine Learning-webservice: elke 6 SUs heeft 20 gelijktijdige verbindingen met Machine Learning web-service op max.

![Stream Analytics schalen met Machine Learning functies twee taak voorbeeld](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Stream Analytics schalen met Machine Learning functies twee taak voorbeeld")

In het algemeen, ***B*** voor Batch grootte, ***L*** voor de latentie van de webservice bij Batch grootte B in milliseconden, is de door Voer van een stream Analytics-taak met ***N*** SUs:

![Stream Analytics schalen met Machine Learning functies formule](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Stream Analytics schalen met Machine Learning functies formule")

U kunt ook de ' Maxi maal aantal gelijktijdige aanroepen ' op de Machine Learning-webservice configureren. Het wordt aanbevolen om deze para meter in te stellen op de maximum waarde (200 op dit moment).

Raadpleeg het artikel over het [schalen van machine learning-webservices](../machine-learning/studio/create-endpoint.md)voor meer informatie over deze instelling.

## <a name="example--sentiment-analysis"></a>Voor beeld – Sentimentanalyse
Het volgende voor beeld bevat een Stream Analytics job met de functie sentiment Analysis Machine Learning, zoals beschreven in de [zelf studie over Stream Analytics machine learning-integratie](stream-analytics-machine-learning-integration-tutorial.md).

De query is een eenvoudige, volledig gepartitioneerde query, gevolgd door de functie **sentiment** , zoals wordt weer gegeven in het volgende voor beeld:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Laten we de configuratie bekijken die nodig is om een Stream Analytics-taak te maken, die sentiment analyse van tweets met een snelheid van 10.000 tweets per seconde.

Als u 1 SU gebruikt, kan deze Stream Analytics taak het verkeer verwerken? De taak kan de invoer blijven gebruiken met de standaard Batch grootte van 1000. De standaard latentie van de sentiment Analysis Machine Learning-webservice (met een standaard Batch grootte van 1000) maakt niet meer dan een seconde van de latentie.

De **totale** of end-to-end-latentie van de stream Analytics-taak is doorgaans een paar seconden. Neem een gedetailleerdere weer gave in deze Stream Analytics-taak, *met name* de machine learning functie aanroepen. Met een batch grootte van 1000 duurt een door Voer van 10.000 gebeurtenissen ongeveer 10 aanvragen voor de webservice. Zelfs met één SU zijn er voldoende gelijktijdige verbindingen om dit invoer verkeer aan te bieden.

Als de invoer gebeurtenis frequentie toeneemt door 100x, moet de Stream Analytics-taak 1.000.000 tweets per seconde verwerken. Er zijn twee opties om de schaal te verg Roten:

1. Verg root de Batch grootte.
2. Partitioneer de invoer stroom om de gebeurtenissen parallel te verwerken.

Met de eerste optie neemt de taak **latentie** toe.

Met de tweede optie moet u meer SUs inrichten om meer gelijktijdige aanvragen voor webservices te Machine Learning. Dit grotere aantal SUs verhoogt de taak **kosten**.

Laten we de schaal bekijken met behulp van de volgende latentie metingen voor elke batch grootte:

| Latentie | Batchgrootte |
| --- | --- |
| 200 MS | 1000-gebeurtenissen batches of lager |
| 250 MS | 5.000-gebeurtenis batches |
| 300 MS | 10.000-gebeurtenis batches |
| 500 ms | 25.000-gebeurtenis batches |

1. Met de eerste optie (**niet** meer SUs inrichten). De Batch grootte kan worden verhoogd naar **25.000**. Door de Batch grootte op deze manier te verg Roten, kan de taak 1.000.000 gebeurtenissen verwerken met 20 gelijktijdige verbindingen met de Machine Learning-webservice (met een latentie van 500 MS per aanroep). Daarom wordt de extra latentie van de Stream Analytics taak als gevolg van de sentiment-functie aanvragen voor de Machine Learning aanvragen van de webservice verhoogd van **200 MS** tot **500 MS**. Batch grootte kan echter **niet** onbeperkt worden verhoogd omdat de machine learning-webservices vereist dat de payload-grootte van een aanvraag 4 MB of kleiner is, en time-out van webservice-aanvragen na 100 seconden van de bewerking.
1. Als u de tweede optie gebruikt, wordt de Batch grootte op 1000, met een latentie van 200 MS-webservice, elke 20 gelijktijdige verbindingen met de webservice kunnen verwerken 1000 * 20 * 5 gebeurtenissen = 100.000 per seconde. Voor het verwerken van 1.000.000 gebeurtenissen per seconde zou de taak 60 SUs nodig hebben. Vergeleken met de eerste optie, neemt Stream Analytics taak meer batch aanvragen voor webservices op, zodat er meer kosten in rekening worden gebracht.

Hieronder ziet u een tabel voor de door Voer van de Stream Analytics-taak voor verschillende SUs-en batch grootten (in aantal gebeurtenissen per seconde).

| Batch grootte (ML-latentie) | 500 (200 MS) | 1.000 (200 MS) | 5.000 (250 MS) | 10.000 (300 MS) | 25.000 (500 MS) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2500 |5.000 |20.000 |30.000 |50,000 |
| **3 SUs** |2500 |5.000 |20.000 |30.000 |50,000 |
| **6 SUs** |2500 |5.000 |20.000 |30.000 |50,000 |
| **12 SUs** |5.000 |10.000 |40,000 |60.000 |100.000 |
| **18 SUs** |7.500 |15.000 |60.000 |90,000 |150.000 |
| **24-SUs** |10.000 |20.000 |80,000 |120.000 |200.000 |
| **…** |… |… |… |… |… |
| **60-SUs** |25,000 |50,000 |200.000 |300.000 |500.000 |

Nu moet u al een goed idee hebben van de werking van Machine Learning functies in Stream Analytics. Waarschijnlijk begrijpt u ook dat Stream Analytics taken pull-gegevens uit gegevens bronnen en elke pull-taak een batch gebeurtenissen retourneert voor het verwerken van de Stream Analytics. Wat is de invloed van dit pull-model op de Machine Learning-aanvragen voor webservices?

Normaal gesp roken is de Batch grootte die we voor Machine Learning-functies hebben ingesteld, niet precies deelbaar door het aantal gebeurtenissen dat door elke Stream Analytics taak wordt geretourneerd. Als dit gebeurt, wordt de Machine Learning-webservice aangeroepen met ' gedeeltelijke ' batches. Door gedeeltelijke batches te gebruiken, voor komt u extra taak latentie overhead in samenvoegings gebeurtenissen van pull-to-pull.

## <a name="new-function-related-monitoring-metrics"></a>Nieuwe functionele metrische bewakings gegevens
In het bewakings gebied van een Stream Analytics-taak zijn er drie extra metrische gegevens over de functie toegevoegd. Ze zijn **functie aanvragen**, **functie gebeurtenissen** en **mislukte functie aanvragen**, zoals wordt weer gegeven in de onderstaande afbeelding.

![Stream Analytics schalen met metrische gegevens over Machine Learning functions](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Stream Analytics schalen met metrische gegevens over Machine Learning functions")

De zijn als volgt gedefinieerd:

**Functie-aanvragen**: het aantal functie aanvragen.

**Functie gebeurtenissen**: de aantal gebeurtenissen in de functie aanvragen.

**MISLUKTE functie aanvragen**: het aantal mislukte functie aanvragen.

## <a name="key-takeaways"></a>Key Takeaways

Houd rekening met de volgende factoren om een Stream Analytics-taak te schalen met Machine Learning functies:

1. De invoer gebeurtenis frequentie.
2. De getolerantiede latentie voor de actieve Stream Analytics-taak (en dus de Batch grootte van de Machine Learning aanvragen van de webservice).
3. De ingerichte Stream Analytics SUs en het aantal Machine Learning aanvragen voor webservices (de aanvullende kosten met betrekking tot de functie).

Een volledig gepartitioneerde Stream Analytics query is als voor beeld gebruikt. Als er een complexere query nodig is, is de [pagina van micro soft Q&een vraag voor Azure stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html) een fantastische bron voor het verkrijgen van aanvullende hulp van het Stream Analytics team.

## <a name="next-steps"></a>Volgende stappen
Voor meer informatie over Stream Analytics raadpleegt u:

* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)
