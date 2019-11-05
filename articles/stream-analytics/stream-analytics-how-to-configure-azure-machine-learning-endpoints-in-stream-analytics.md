---
title: Machine Learning-eind punten gebruiken in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u door gebruiker gedefinieerde functies voor computer taal gebruikt in Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 1adb7d58246ea37fd8322cb6fc6ffd53c5f19efb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467816"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics-preview"></a>Integratie van Azure Machine Learning Studio (klassiek) in Stream Analytics (preview-versie)
Stream Analytics ondersteunt door de gebruiker gedefinieerde functies die naar Azure Machine Learning Studio (klassieke) eind punten aanroepen. REST API ondersteuning voor deze functie wordt beschreven in de [Stream Analytics rest API-bibliotheek](https://msdn.microsoft.com/library/azure/dn835031.aspx). Dit artikel bevat aanvullende informatie die nodig is voor een succes volle implementatie van deze functie in Stream Analytics. Er is ook een zelf studie gepubliceerd en deze is [hier](stream-analytics-machine-learning-integration-tutorial.md)beschikbaar.

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>Overzicht: Azure Machine Learning Studio (klassiek) terminologie
Microsoft Azure Machine Learning Studio (klassiek) biedt een hulp programma dat u kunt gebruiken om predictive analytics oplossingen voor uw gegevens te bouwen, te testen en te implementeren. Dit hulp programma wordt de *Azure machine learning Studio (klassiek)* genoemd. De Studio wordt gebruikt om te communiceren met de Machine Learning resources en kan eenvoudig uw ontwerp ontwikkelen, testen en herhalen. Deze resources en de bijbehorende definities staan hieronder.

* **Werk ruimte**: de *werk ruimte* is een container met alle andere machine learning resources samen in een container voor beheer en controle.
* **Experiment**: *experimenten* worden gemaakt door data wetenschappers om gegevens sets te gebruiken en een machine learning model te trainen.
* **Eind punt**: *eind punten* zijn het Azure machine learning Studio (klassiek)-object dat wordt gebruikt om functies als invoer te gebruiken, een opgegeven machine learning model toe te passen en de gescoorde uitvoer te retour neren.
* **Score-webservice**: een *Score-webservice* is een verzameling eind punten zoals hierboven wordt vermeld.

Elk eind punt heeft api's voor batch uitvoering en synchrone uitvoering. Stream Analytics maakt gebruik van synchrone uitvoering. De specifieke service heet een [aanvraag/antwoord service](../machine-learning/studio/consume-web-services.md) in azure machine learning Studio (klassiek).

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Machine Learning resources die nodig zijn voor Stream Analytics taken
Voor het uitvoeren van Stream Analytics taak verwerking, zijn een aanvraag/antwoord-eind punt, een [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md)en een Swagger-definitie nood zakelijk voor een geslaagde uitvoering. Stream Analytics heeft een extra eind punt dat de URL voor het Swagger-eind punt bouwt, de interface opzoekt en een standaard UDF-definitie voor de gebruiker retourneert.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Een Stream Analytics en Machine Learning UDF configureren via REST API
Door REST Api's te gebruiken, kunt u uw taak zo configureren dat deze Azure machine language-functies aanroept. De stappen zijn als volgt:

1. Een Stream Analytics-taak maken
2. Een invoer definiëren
3. Een uitvoer definiëren
4. Een door de gebruiker gedefinieerde functie maken (UDF)
5. Een Stream Analytics trans formatie schrijven die de UDF aanroept
6. Taak starten

## <a name="creating-a-udf-with-basic-properties"></a>Een UDF maken met basis eigenschappen
Met de volgende voorbeeld code wordt bijvoorbeeld een scalaire UDF gemaakt met de naam *newudf* die aan een Azure machine learning Studio (klassiek)-eind punt is gekoppeld. Het *eind punt* (Service-URI) bevindt zich op de Help-pagina van de API voor de gekozen service en de *apiKey* is te vinden op de hoofd pagina van services.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Voor beeld van aanvraag tekst:

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
```

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>RetrieveDefaultDefinition-eind punt aanroepen voor standaard-UDF
Zodra het skelet UDF is gemaakt, is de volledige definitie van de UDF nodig. Het RetrieveDefaultDefinition-eind punt helpt u de standaard definitie te verkrijgen voor een scalaire functie die is gebonden aan een Azure Machine Learning Studio (klassiek)-eind punt. De onderstaande nettolading vereist dat u de standaard UDF-definitie voor een scalaire functie die is gebonden aan een Azure Machine Learning eind punt krijgt. Het werkelijke eind punt wordt niet opgegeven omdat het al is opgegeven tijdens de PUT-aanvraag. Stream Analytics roept het eind punt aan dat in de aanvraag is gegeven als dit expliciet wordt gegeven. Anders wordt de oorspronkelijke verwijzing gebruikt. Hier wordt een enkele teken reeks parameter (een zin) gebruikt en wordt één uitvoer van het type teken reeks geretourneerd waarmee het label "sentiment" voor die zin wordt aangegeven.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Voor beeld van aanvraag tekst:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Een voor beeld van dit resultaat ziet er ongeveer als volgt uit.

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="patch-udf-with-the-response"></a>Patch UDF met het antwoord
Nu moet de UDF worden bijgewerkt met het vorige antwoord, zoals hieronder wordt weer gegeven.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Hoofd tekst van aanvraag (uitvoer van RetrieveDefaultDefinition):

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Stream Analytics transformatie implementeren voor het aanroepen van de UDF
Voer nu een query uit op de UDF (hier met de naam scoreTweet) voor elke invoer gebeurtenis en schrijf een antwoord voor die gebeurtenis naar een uitvoer.

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>Help opvragen
Voor verdere hulp kunt u mogelijk terecht op het [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)
