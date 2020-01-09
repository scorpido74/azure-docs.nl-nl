---
title: Machine Learning-eindpunten in Azure Stream Analytics gebruiken
description: In dit artikel wordt beschreven hoe u Machine taal door de gebruiker gedefinieerde functies gebruiken in Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 239955025f21d8679cbcf0bbfe68f9070f0217c6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426187"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics-preview"></a>Integratie van Azure Machine Learning Studio (klassiek) in Stream Analytics (preview-versie)
Stream Analytics ondersteunt door de gebruiker gedefinieerde functies die naar Azure Machine Learning Studio (klassieke) eind punten aanroepen. REST-API-ondersteuning voor deze functie wordt beschreven in de [REST-API voor Stream Analytics-bibliotheek](https://msdn.microsoft.com/library/azure/dn835031.aspx). In dit artikel bevat aanvullende informatie die nodig zijn voor een succesvolle implementatie van deze mogelijkheid in Stream Analytics. Een zelfstudie is ook geboekt en is beschikbaar [hier](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>Overzicht: Azure Machine Learning Studio (klassiek) terminologie
Microsoft Azure Machine Learning Studio (klassiek) biedt een hulp programma dat u kunt gebruiken om predictive analytics oplossingen voor uw gegevens te bouwen, te testen en te implementeren. Dit hulp programma wordt de *Azure machine learning Studio (klassiek)* genoemd. De studio wordt gebruikt om te communiceren met de Machine Learning-resources eenvoudig bouwen, testen en herhalen op uw ontwerp. Deze resources en de bijbehorende definities staan hieronder.

* **Werkruimte**: de *werkruimte* is een container met alle andere Machine Learning-resources samen in een container voor controle en beheer.
* **Experiment**: *experimenten* zijn gemaakt door de datawetenschappers gebruikmaken van gegevenssets en een machine learning-model te trainen.
* **Eind punt**: *eind punten* zijn het Azure machine learning Studio (klassiek)-object dat wordt gebruikt om functies als invoer te gebruiken, een opgegeven machine learning model toe te passen en de gescoorde uitvoer te retour neren.
* **Scoring-Webservice**: A *scoring-webservice* is een verzameling van eindpunten, zoals hierboven vermeld.

Elk eindpunt heeft API's voor Batchuitvoering en synchrone uitvoering. Stream Analytics maakt gebruik van synchrone uitvoering. De specifieke service heet een [aanvraag/antwoord service](../machine-learning/studio/consume-web-services.md) in azure machine learning Studio (klassiek).

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Machine Learning-resources die nodig zijn voor Stream Analytics-taken
Taak voor de doeleinden van Stream Analytics verwerkt, een verzoek/reactie-eindpunt, een [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md), en een swagger-definitie zijn alle die nodig zijn voor de uitvoering is geslaagd. Stream Analytics is een extra eindpunt dat de swagger-eindpunt-url samenstelt, zoekt de interface en retourneert een standaard UDF-definitie voor de gebruiker.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Configureren van een Stream Analytics en Machine Learning-UDF via REST-API
U kunt de taak voor het aanroepen van functies van de Azure machinetaal configureren met behulp van REST-API's. De stappen zijn als volgt:

1. Een Stream Analytics-taak maken
2. Invoer definiëren
3. Uitvoer definiëren
4. Een door de gebruiker gedefinieerde functie (UDF's) maken
5. Schrijven van een Stream Analytics-transformatie die de UDF-aanroepen
6. Taak starten

## <a name="creating-a-udf-with-basic-properties"></a>Het maken van een UDF met basiseigenschappen
Met de volgende voorbeeld code wordt bijvoorbeeld een scalaire UDF gemaakt met de naam *newudf* die aan een Azure machine learning Studio (klassiek)-eind punt is gekoppeld. Houd er rekening mee dat de *eindpunt* (service-URI) kunt u vinden op de API help-pagina voor de gekozen service en de *apiKey* vindt u op de hoofdpagina van Services.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Voorbeeld van de aanvraag hoofdtekst:

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

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Aanroep RetrieveDefaultDefinition-eindpunt voor standaard UDF
Zodra het basisproject UDF is gemaakt wordt de volledige definitie van de UDF nodig. Het RetrieveDefaultDefinition-eind punt helpt u de standaard definitie te verkrijgen voor een scalaire functie die is gebonden aan een Azure Machine Learning Studio (klassiek)-eind punt. De nettolading van de onderstaande moet u de standaard UDF-definitie voor een scalaire functie die is gekoppeld aan een Azure Machine Learning-eindpunt ophalen. Hierin worden de werkelijke eindpunt niet opgegeven als deze al is opgegeven tijdens de PUT-aanvraag. Stream Analytics roept het eindpunt dat is opgegeven in de aanvraag als het expliciet is opgegeven. Als deze wordt gebruikt met een oorspronkelijk waarnaar wordt verwezen. De UDF-wordt één parameter (een zin) en retourneert één uitvoer van het type tekenreeks tekenreeks hier wat het label 'gevoel' voor de zin dat aangeeft.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Voorbeeld van de aanvraag hoofdtekst:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Een voorbeeld van uitvoer van deze wilt zoekt u naar iets dat hieronder.

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

## <a name="patch-udf-with-the-response"></a>Patch UDF met de reactie
De UDF moet nu worden gevuld met het vorige antwoord, zoals hieronder wordt weergegeven.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Aanvraagtekst (uitvoer van de RetrieveDefaultDefinition):

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

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Stream Analytics-transformatie om aan te roepen de UDF implementeren
Nu de UDF (hier met de naam scoreTweet) op te vragen voor elke invoer gebeurtenis en een antwoord voor die gebeurtenis naar uitvoer schrijven.

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>Hulp krijgen
Voor verdere hulp kunt u mogelijk terecht op het [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)
