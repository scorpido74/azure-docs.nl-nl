---
title: Machine Learning-eindpunten gebruiken in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u door de gebruiker van Machinelanguage gedefinieerde functies gebruikt in Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 239955025f21d8679cbcf0bbfe68f9070f0217c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426187"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics-preview"></a>Azure Machine Learning Studio (klassieke) integratie in Stream Analytics (Preview)
Stream Analytics ondersteunt door de gebruiker gedefinieerde functies die oproepen tot Azure Machine Learning Studio (klassieke) eindpunten. REST API-ondersteuning voor deze functie wordt beschreven in de [STREAM Analytics REST API-bibliotheek.](https://msdn.microsoft.com/library/azure/dn835031.aspx) Dit artikel bevat aanvullende informatie die nodig is voor een succesvolle implementatie van deze mogelijkheid in Stream Analytics. Een tutorial is ook gepost en is [hier](stream-analytics-machine-learning-integration-tutorial.md)beschikbaar .

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>Overzicht: Azure Machine Learning Studio (klassieke) terminologie
Microsoft Azure Machine Learning Studio (klassiek) biedt een samenwerkingstool voor slepen en neerzetten die u gebruiken om voorspellende analyseoplossingen op uw gegevens te bouwen, testen en implementeren. Deze tool wordt de *Azure Machine Learning Studio (klassiek) genoemd.* De studio wordt gebruikt om te communiceren met de Machine Learning-bronnen en eenvoudig uw ontwerp te bouwen, te testen en te herhalen. Deze bronnen en hun definities zijn hieronder.

* **Werkruimte:** de *werkruimte* is een container die alle andere Machine Learning-resources bij elkaar houdt in een container voor beheer en beheer.
* **Experiment**: *Experimenten* worden gemaakt door gegevenswetenschappers om datasets te gebruiken en een machine learning-model te trainen.
* **Eindpunt:** *Eindpunten* zijn het Azure Machine Learning Studio -object (klassiek) object dat wordt gebruikt om functies als invoer te gebruiken, een opgegeven machine learning-model toe te passen en de uitvoer van retourpunten toe te passen.
* **Webservice scoren**: Een *scorende webservice* is een verzameling eindpunten zoals hierboven vermeld.

Elk eindpunt heeft api's voor batchuitvoering en synchrone uitvoering. Stream Analytics maakt gebruik van synchrone uitvoering. De specifieke service wordt een [request/response-service](../machine-learning/studio/consume-web-services.md) genoemd in Azure Machine Learning Studio (klassiek).

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Machine Learning-resources die nodig zijn voor Stream Analytics-taken
Voor de verwerking van de taak van Stream Analytics zijn een eindpunt voor aanvragen/antwoorden, een [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md)en een branie-definitie allemaal nodig voor een succesvolle uitvoering. Stream Analytics heeft een extra eindpunt dat de url voor branieeindpunt construeert, de interface opzoekt en een standaard UDF-definitie naar de gebruiker retourneert.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Een Stream Analytics en Machine Learning UDF configureren via REST API
Door REST API's te gebruiken, u uw taak configureren om Azure Machine Language-functies aan te roepen. De stappen zijn als volgt:

1. Een Stream Analytics-taak maken
2. Een invoer definiëren
3. Een uitvoer definiëren
4. Een door de gebruiker gedefinieerde functie (UDF) maken
5. Schrijf een Stream Analytics-transformatie die de UDF aanroept
6. Taak starten

## <a name="creating-a-udf-with-basic-properties"></a>Een UDF maken met basiseigenschappen
Als voorbeeld maakt de volgende voorbeeldcode een scalaire UDF met de naam *newudf* die wordt gekoppeld aan een Azure Machine Learning Studio (klassiek) eindpunt. Houd er rekening mee dat het *eindpunt* (service URI) te vinden is op de API-helppagina voor de gekozen service en dat de *apiKey* te vinden is op de hoofdpagina van Services.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Voorbeeldaanvraaginstantie:

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

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Call RetrieveDefaultDefinition-eindpunt voor standaard UDF
Zodra het skelet UDF is gemaakt de volledige definitie van de UDF nodig is. Met het eindpunt RetrieveDefaultDefinition u de standaarddefinitie krijgen voor een scalaire functie die is gekoppeld aan een Azure Machine Learning Studio (klassiek) eindpunt. De payload hieronder vereist dat u de standaard UDF-definitie krijgt voor een scalaire functie die is gekoppeld aan een Azure Machine Learning-eindpunt. Het geeft niet het werkelijke eindpunt op, omdat het al is opgegeven tijdens PUT-aanvraag. Stream Analytics roept het eindpunt aan dat in de aanvraag wordt opgegeven als het expliciet wordt verstrekt. Anders gebruikt het oorspronkelijk verwezen. Hier neemt de UDF een enkele tekenreeksparameter (een zin) en retourneert een enkele uitvoer van typetekenreeks die het label 'sentiment' voor die zin aangeeft.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Voorbeeldaanvraaginstantie:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Een voorbeeld output van deze zou er iets als hieronder.

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

## <a name="patch-udf-with-the-response"></a>Patch UDF met de respons
Nu moet de UDF worden gepatcht met het vorige antwoord, zoals hieronder wordt weergegeven.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Aanvraaghoofdtekst (uitvoer van RetrieveDefaultDefinition):

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

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Stream Analytics-transformatie implementeren om de UDF aan te roepen
Query er nu de UDF (hier genaamd scoreTweet) voor elke invoergebeurtenis en schrijf een antwoord voor die gebeurtenis op een uitvoer.

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
