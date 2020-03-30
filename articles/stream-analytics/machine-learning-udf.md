---
title: Azure Stream Analytics integreren met Azure Machine Learning
description: In dit artikel wordt beschreven hoe u een Azure Stream Analytics-taak integreren met Azure Machine Learning-modellen.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 354b0eae1620c49006b350371aae38f8867c18b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124657"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Azure Stream Analytics integreren met Azure Machine Learning (preview)

U machine learning-modellen implementeren als een door de gebruiker gedefinieerde functie (UDF) in uw Azure Stream Analytics-taken om realtime te scoren en voorspellingen te doen op uw streaminginvoergegevens. [Met Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) u elke populaire open-sourcetool, zoals Tensorflow, scikit-learn of PyTorch, gebruiken om modellen voor te bereiden, te trainen en te implementeren.

> [!NOTE]
> Deze functionaliteit is in openbare preview. U hebt alleen toegang tot deze functie op de Azure-portal via de [koppeling Stream Analytics-portalpreview.](https://aka.ms/asaportalpreview) Deze functionaliteit is ook beschikbaar in de nieuwste versie van [Stream Analytics-tools voor Visual Studio.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)

## <a name="prerequisites"></a>Vereisten

Voer de volgende stappen uit voordat u een machine learning-model als functie toevoegt aan uw Stream Analytics-taak:

1. Gebruik Azure Machine Learning om [uw model te implementeren als webservice.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)

2. Uw scorescript moet [voorbeeldinvoer en -uitvoer](../machine-learning/how-to-deploy-and-where.md#example-entry-script) bevatten die door Azure Machine Learning worden gebruikt om een schemaspecificatie te genereren. Stream Analytics gebruikt het schema om inzicht te krijgen in de functiehandtekening van uw webservice.

3. Zorg ervoor dat uw webservice geserialiseerde gegevens van JSON accepteert en retourneert.

4. Implementeer uw model op [Azure Kubernetes Service](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) voor grootschalige productie-implementaties. Als de webservice het aantal aanvragen van uw taak niet kan verwerken, worden de prestaties van uw Stream Analytics-taak afgebroken, wat gevolgen heeft voor de latentie.

## <a name="add-a-machine-learning-model-to-your-job"></a>Een machine learning-model toevoegen aan uw taak

U Azure Machine Learning-functies rechtstreeks vanuit de Azure-portal aan uw Stream Analytics-taak toevoegen.

1. Navigeer naar uw streamanalytics-taak in de Azure-portal en selecteer **Functies** onder **Taaktopologie**. Selecteer vervolgens **Azure ML Service** in het vervolgkeuzemenu + **Toevoegen.**

   ![Azure ML-uDF toevoegen](./media/machine-learning-udf/add-azureml-udf.png)

2. Vul het **functieformulier Azure Machine Learning Service** in met de volgende eigenschapswaarden:

   ![Azure ML UDF configureren](./media/machine-learning-udf/configure-azureml-udf.png)

In de volgende tabel wordt elke eigenschap van Azure ML-servicefuncties in Stream Analytics beschreven.

|Eigenschap|Beschrijving|
|--------|-----------|
|Functiealias|Voer een naam in om de functie in uw query aan te roepen.|
|Abonnement|Uw Azure-abonnement..|
|Azure ML-werkruimte|De Azure Machine Learning-werkruimte die u hebt gebruikt om uw model als webservice te implementeren.|
|Implementaties|De webservice die uw model host.|
|Functiehandtekening|De handtekening van uw webservice is afgeleid van de schemaspecificatie van de API. Als uw handtekening niet wordt geladen, controleert u of u voorbeeldinvoer en -uitvoer in uw scorescript hebt opgegeven om het schema automatisch te genereren.|
|Aantal parallelle aanvragen per partitie|Dit is een geavanceerde configuratie om de grootschalige doorvoer te optimaliseren. Dit nummer vertegenwoordigt de gelijktijdige aanvragen die van elke partitie van uw taak naar de webservice worden verzonden. Taken met zes streaming-eenheden (SU) en lager hebben één partitie. Taken met 12 SU's hebben twee partities, 18 SU's hebben drie partities enzovoort.<br><br> Als uw taak bijvoorbeeld twee partities heeft en u deze parameter instelt op vier, worden er acht gelijktijdige aanvragen van uw taak ingediend bij uw webservice. Op dit moment van openbare preview, deze waarde standaard op 20 en kan niet worden bijgewerkt.|
|Maximum aantal batch's|Dit is een geavanceerde configuratie voor het optimaliseren van grootschalige doorvoer. Dit getal vertegenwoordigt het maximum aantal gebeurtenissen dat samen wordt gebatched in één aanvraag die naar uw webservice wordt verzonden.|

## <a name="supported-input-parameters"></a>Ondersteunde invoerparameters

Wanneer uw Stream Analytics-query een Azure Machine Learning UDF aanroept, wordt met de taak een JSON-geserialiseerde aanvraag voor de webservice gemaakt. De aanvraag is gebaseerd op een modelspecifiek schema. U moet een voorbeeldinvoer en -uitvoer in uw scorescript leveren om automatisch een schema te [genereren.](../machine-learning/how-to-deploy-and-where.md) Met het schema kan Stream Analytics de geserialiseerde JSON-aanvraag samenstellen voor een van de ondersteunde gegevenstypen zoals numpy, panda's en PySpark. Meerdere invoergebeurtenissen kunnen samen worden gebatched in één aanvraag.

De volgende Stream Analytics-query is een voorbeeld van het aanroepen van een Azure Machine Learning UDF:

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

Stream Analytics ondersteunt slechts één parameter voor Azure Machine Learning-functies. Mogelijk moet u uw gegevens voorbereiden voordat u deze doorgeeft als input voor machine learning UDF.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>Meerdere invoerparameters doorgeven aan de UDF

De meest voorkomende voorbeelden van ingangen voor machine learning-modellen zijn numpy arrays en DataFrames. U een array maken met een JavaScript UDF en een `WITH` JSON-serialized DataFrame maken met behulp van de clausule.

### <a name="create-an-input-array"></a>Een invoerarray maken

U een JavaScript-udf maken die *N-aantal* ingangen accepteert en een array maakt die kan worden gebruikt als invoer voor uw Azure Machine Learning UDF.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

Zodra u de JavaScript UDF aan uw taak hebt toegevoegd, u uw Azure Machine Learning UDF aanroepen met de volgende query:

```SQL
SELECT udf.score(
udf.createArray(vendorid, weekday, pickuphour, passenger, distance)
)
INTO output
FROM input
```

De volgende JSON is een voorbeeldaanvraag:

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Een Panda's of PySpark DataFrame maken

U `WITH` de clausule gebruiken om een JSON serialized DataFrame te maken die kan worden doorgegeven als invoer naar uw Azure Machine Learning UDF, zoals hieronder wordt weergegeven.

Met de volgende query wordt een DataFrame gemaakt door de benodigde velden te selecteren en het DataFrame te gebruiken als invoer voor de Azure Machine Learning UDF.

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM input
```

De volgende JSON is een voorbeeldaanvraag van de vorige query:

```JSON
{
    "data": [{
            "vendorid": "1",
            "weekday": "Mon",
            "pickuphour": "12",
            "passenger": "1",
            "distance": "5.8"
        }, {
            "vendorid": "2",
            "weekday": "Tue",
            "pickuphour": "10",
            "passenger": "2",
            "distance": "10"
        }
    ]
}
```

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Optimaliseer de prestaties voor Azure Machine Learning-udf's

Wanneer u uw model implementeert in Azure Kubernetes Service, u [uw model profileren om het gebruik van resources te bepalen.](../machine-learning/how-to-deploy-and-where.md#profilemodel) U [app-inzichten voor uw implementaties](../machine-learning/how-to-enable-app-insights.md) ook inschakelen om inzicht te krijgen in de aanvraagpercentages, reactietijden en uitvalpercentages.

Als u een scenario hebt met een hoge gebeurtenisdoorvoer, moet u mogelijk de volgende parameters in Stream Analytics wijzigen om optimale prestaties te bereiken met lage end-to-end-latencies:

1. Max batch tellen.
2. Aantal parallelle aanvragen per partitie.

### <a name="determine-the-right-batch-size"></a>De juiste batchgrootte bepalen

Nadat u uw webservice hebt geïmplementeerd, verzendt u voorbeeldaanvragen met verschillende batchgroottes vanaf 50 en verhoogt u deze in volgorde van honderden. Bijvoorbeeld, 200, 500, 1000, 2000 en ga zo maar door. U zult merken dat na een bepaalde batchgrootte de latentie van de respons toeneemt. Het punt waarna latentie van respons verhogingen moet de maximale batch tellen voor uw taak.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>Het aantal parallelle aanvragen per partitie bepalen

Bij optimale schaling moet uw Stream Analytics-taak meerdere parallelle aanvragen naar uw webservice kunnen verzenden en binnen enkele milliseconden een antwoord kunnen krijgen. De latentie van de reactie van de webservice kan rechtstreeks van invloed zijn op de latentie en prestaties van uw Stream Analytics-taak. Als het gesprek van uw taak naar de webservice lang duurt, ziet u waarschijnlijk een toename van de vertraging van het watermerk en kan het aantal backlogged invoergebeurtenissen toenemen.

Als u een dergelijke latentie wilt voorkomen, moet u ervoor zorgen dat uw AKS-cluster (Azure Kubernetes Service) is ingericht met het [juiste aantal knooppunten en replica's.](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli) Het is van cruciaal belang dat uw webservice zeer beschikbaar is en succesvolle reacties retourneert. Als uw taak een service-niet-beschikbare reactie (503) van uw webservice ontvangt, wordt deze voortdurend opnieuw geprobeerd met exponentiële back-off. Een andere reactie dan succes (200) en service niet beschikbaar (503) zal ertoe leiden dat uw taak naar een mislukte status gaat.

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Door gebruiker gedefinieerde JavaScript-functies in Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Uw Stream Analytics-taak schalen met de functie Azure Machine Learning Studio (klassieke) functie](stream-analytics-scale-with-machine-learning-functions.md)

