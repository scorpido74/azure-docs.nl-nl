---
title: Machine Learning-modellen bijwerken met Azure Data Factory
description: Beschrijft hoe u voorspellende pijplijnen maakt met Azure Data Factory en Azure Machine Learning
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: afc79badd19fa180e631f1f8fa9735567a0b1e33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978710"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Azure Machine Learning-modellen bijwerken met resourceactiviteit bijwerken

> [!div class="op_single_selector" title1="Transformatieactiviteiten"]
> * [Hive Activiteit](data-factory-hive-activity.md) 
> * [Varkensactiviteit](data-factory-pig-activity.md)
> * [Activiteit kaart verminderen](data-factory-map-reduce.md)
> * [Hadoop streaming activiteit](data-factory-hadoop-streaming-activity.md)
> * [Vonkactiviteit](data-factory-spark.md)
> * [Machine Learning-batchuitvoeringsactiviteit](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning-activiteit resources bijwerken](data-factory-azure-ml-update-resource-activity.md)
> * [Opgeslagen procedureactiviteit](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-activiteit](data-factory-usql-activity.md)
> * [.NET Aangepaste activiteit](data-factory-use-custom-activities.md)


> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de datafabriekservice gebruikt, raadpleegt u [machine learning-modellen bijwerken in Gegevensfabriek](../update-machine-learning-models.md).

Dit artikel vormt een aanvulling op het belangrijkste azure-gegevensfabriek - Azure Machine Learning-integratieartikel: [Voorspellende pijplijnen maken met Azure Machine Learning en Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). Als u dit nog niet hebt gedaan, bekijk dan het hoofdartikel voordat u dit artikel leest. 

## <a name="overview"></a>Overzicht
Na verloop van tijd moeten de voorspellende modellen in de Azure ML-scoringsexperimenten worden omgeschoold met behulp van nieuwe invoergegevenssets. Nadat u klaar bent met omscholing, wilt u de scoringwebservice bijwerken met het omgetrainde ML-model. De typische stappen om Azure ML-modellen via webservices omte scholen en bijwerken in te schakelen zijn:

1. Maak een experiment in [Azure Machine Learning Studio (klassiek).](https://studio.azureml.net)
2. Wanneer u tevreden bent met het model, gebruikt u Azure Machine Learning Studio (klassiek) om webservices te publiceren voor zowel het **trainingsexperiment** als het scoren/**voorspellend experiment.**

In de volgende tabel worden de webservices beschreven die in dit voorbeeld worden gebruikt.  Zie [Machine Learning-modellen programmatisch omscholen](../../machine-learning/machine-learning-retrain-models-programmatically.md) voor meer informatie.

- **Webservice trainen** - Ontvangt trainingsgegevens en produceert getrainde modellen. De uitvoer van de omscholing is een .ilearner-bestand in een Azure Blob-opslag. Het **standaardeindpunt** wordt automatisch voor u gemaakt wanneer u het trainingsexperiment als webservice publiceert. U meer eindpunten maken, maar in het voorbeeld wordt alleen het standaardeindpunt gebruikt.
- **Webservice scoren** - Ontvangt voorbeelden van niet-gelabelde gegevens en maakt voorspellingen. De uitvoer van voorspelling kan verschillende vormen hebben, zoals een CSV-bestand of rijen in een Azure SQL-database, afhankelijk van de configuratie van het experiment. Het standaardeindpunt wordt automatisch voor u gemaakt wanneer u het voorspellende experiment als webservice publiceert. 

De volgende afbeelding toont de relatie tussen trainings- en scoringseindpunten in Azure ML.

![Webservices](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

U de **trainingswebservice** aanroepen met behulp van de **Azure ML Batch Execution Activity**. Een beroep doen op een trainingswebservice is hetzelfde als een beroep doen op een Azure ML-webservice (webservice scoren) voor het scoren van gegevens. De voorgaande secties hebben betrekking op het in detail aanroepen van een Azure ML-webservice uit een Azure Data Factory-pijplijn. 

U de **scoringswebservice** aanroepen door de **Azure ML Update Resource Activity** te gebruiken om de webservice bij te werken met het nieuw getrainde model. In de volgende voorbeelden worden gekoppelde servicedefinities gegeven: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Scoring web service is een klassieke webservice
Als de scoringswebservice een **klassieke webservice**is, maakt u het tweede **niet-standaard en updatable eindpunt** met behulp van de Azure-portal. Zie [Artikel Eindpunten maken](../../machine-learning/machine-learning-create-endpoint.md) voor stappen. Nadat u het niet-standaard updatable eindpunt hebt gemaakt, gaat u de volgende stappen uitvoeren:

* Klik **op BATCH-UITVOERING** om de URI-waarde voor de eigenschap **MLEndpoint** JSON op te halen.
* Klik **op de** koppeling RESOURCE BIJWERKEN om de URI-waarde voor de eigenschap **UPDATEResourceEndpoint** JSON op te halen. De API-toets bevindt zich op de eindpuntpagina zelf (in de rechterbenedenhoek).

![updatable eindpunt](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

In het volgende voorbeeld wordt een voorbeeld van JSON-definitie voor de gekoppelde AzureML-service gegeven. De gekoppelde service gebruikt de apiKey voor verificatie.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Scoring webservice is Azure Resource Manager webservice 
Als de webservice het nieuwe type webservice is dat een Azure Resource Manager-eindpunt blootlegt, hoeft u het tweede **niet-standaardeindpunt** niet toe te voegen. Het **updateResourceEndpoint** in de gekoppelde service is van de indeling: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

U waarden voor plaatshouders in de URL ophalen wanneer u de webservice opvraagt op de [Azure Machine Learning Web Services Portal.](https://services.azureml.net/) Voor het nieuwe type eindpunt voor updatebronnen is een AAD-token (Azure Active Directory) vereist. Geef **servicePrincipalId** en **servicePrincipalKey** op in de gekoppelde Azure Machine Learning-service. Bekijk [hoe u serviceprincipal maakt en machtigingen toewijst voor het beheer van Azure-bronnen.](../../active-directory/develop/howto-create-service-principal-portal.md) Hier is een voorbeeld van AzureML linked service definition: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

Het volgende scenario geeft meer details. Het heeft een voorbeeld voor het omscholen en bijwerken van Azure ML-modellen vanuit een Azure Data Factory-pijplijn.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Scenario: een Azure ML-model omscholen en bijwerken
In deze sectie wordt een voorbeeldpijplijn opgenomen die de **azure ML-batchuitvoeringsactiviteit** gebruikt om een model opnieuw te trainen. De pijplijn gebruikt ook de **Azure ML Update Resource-activiteit** om het model in de scoringswebservice bij te werken. De sectie biedt ook JSON-fragmenten voor alle gekoppelde services, gegevenssets en pijplijnen in het voorbeeld.

Hier is de diagramweergave van de voorbeeldpijplijn. Zoals u zien, neemt de Azure ML Batch Execution Activity de trainingsinvoer en produceert een trainingsuitvoer (iLearner-bestand). De Azure ML Update Resource Activity voert deze trainingsuitvoer uit en werkt het model bij in het eindpunt van de scoringswebservice. De activiteit Resource bijwerken levert geen uitvoer op. De tijdelijke aanduidingBlob is slechts een uitvoergegevensset voor dummy's die vereist is door de Azure Data Factory-service om de pijplijn uit te voeren.

![pijplijndiagram](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Gekoppelde Azure Blob-opslagservice:
De Azure Storage bevat de volgende gegevens:

* opleidingsgegevens. De invoergegevens voor de webservice Azure ML training.  
* iLearner-bestand. De uitvoer van de Azure ML-trainingswebservice. Dit bestand is ook de invoer voor de activiteit Resource bijwerken.  

Hier is de voorbeeld JSON definitie van de gekoppelde dienst:

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>Gegevensset voor trainingsinvoer:
De volgende gegevensset vertegenwoordigt de invoertrainingsgegevens voor de webservice Azure Machine Learning training. De activiteit Azure Machine Learning Batch Execution neemt deze gegevensset als invoer.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

### <a name="training-output-dataset"></a>Gegevensset voor trainingsuitvoer:
De volgende gegevensset vertegenwoordigt het uitvoer-iLearner-bestand van de webservice Azure ML training. De Azure ML Batch Execution Activity produceert deze gegevensset. Deze gegevensset is ook de ingang voor de Azure ML Update Resource-activiteit.

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-azure-machine-learning-training-endpoint"></a>Gekoppelde service voor azure Machine Learning-trainingseindpunt
In het volgende JSON-fragment wordt een azure Machine Learning-gekoppelde service gedefinieerd die verwijst naar het standaardeindpunt van de trainingswebservice.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

Ga in **Azure Machine Learning Studio (klassiek)** als volgt te werk om waarden voor **mlEndpoint** en **apiKey**te krijgen:

1. Klik op **WEB SERVICES** in het linkermenu.
2. Klik op de **webservice voor training** in de lijst met webservices.
3. Klik op kopiëren naast het tekstvak **API-sleutel.** Plak de toets in het klembord in de JSON-editor van Data Factory.
4. Klik in de **Azure Machine Learning Studio (klassiek)** op **BATCH-uitvoeringskoppeling.**
5. Kopieer de **URI aanvragen** vanuit de sectie **Aanvraag** en plak deze in de JSON-editor van de gegevensfabriek.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Gekoppelde service voor Azure ML updatable scoring endpoint:
In het volgende JSON-fragment wordt een azure Machine Learning-gekoppelde service gedefinieerd die verwijst naar het niet-standaard updatable eindpunt van de scoringswebservice.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>Uitvoergegevensset tijdelijke aanduiding:
De azure ML-updatebronactiviteit genereert geen uitvoer. Azure Data Factory vereist echter een uitvoergegevensset om de planning van een pijplijn te sturen. Daarom gebruiken we in dit voorbeeld een dummy/tijdelijke aanduidingsgegevensset.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>Pijplijn
De pijplijn heeft twee activiteiten: **AzureMLBatchExecution** en **AzureMLUpdateResource**. De azure ML Batch Execution-activiteit neemt de trainingsgegevens als invoer en produceert een iLearner-bestand als uitvoer. De activiteit beroept zich op de trainingswebservice (trainingsexperiment dat als webservice wordt weergegeven) met de invoertrainingsgegevens en ontvangt het ilearner-bestand van de webservice. De tijdelijke aanduidingBlob is slechts een uitvoergegevensset voor dummy's die vereist is door de Azure Data Factory-service om de pijplijn uit te voeren.

![pijplijndiagram](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```
