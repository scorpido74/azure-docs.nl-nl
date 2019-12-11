---
title: Machine Learning modellen bijwerken met behulp van Azure Data Factory
description: Hierin wordt beschreven hoe u voorspellende pijp lijnen maakt met behulp van Azure Data Factory en Azure Machine Learning
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
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978710"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Azure Machine Learning modellen bijwerken met resource activiteit bijwerken

> [!div class="op_single_selector" title1="Transformatie activiteiten"]
> * [Hive-activiteit](data-factory-hive-activity.md) 
> * [Pig-activiteit](data-factory-pig-activity.md)
> * [MapReduce-activiteit](data-factory-map-reduce.md)
> * [Hadoop streaming-activiteit](data-factory-hadoop-streaming-activity.md)
> * [Spark-activiteit](data-factory-spark.md)
> * [Machine Learning-batchuitvoeringsactiviteit](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning-activiteit resources bijwerken](data-factory-azure-ml-update-resource-activity.md)
> * [Opgeslagen procedureactiviteit](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-activiteit](data-factory-usql-activity.md)
> * [Aangepaste .NET-activiteit](data-factory-use-custom-activities.md)


> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [machine learning modellen in Data Factory bijwerken](../update-machine-learning-models.md).

Dit artikel vormt een aanvulling op het belangrijkste Azure Data Factory-Azure Machine Learning-integratie artikel: [Maak voorspellende pijp lijnen met behulp van Azure machine learning en Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). Als u dit nog niet hebt gedaan, raadpleegt u het hoofd artikel voordat u dit artikel leest. 

## <a name="overview"></a>Overzicht
Na verloop van tijd moeten de voorspellende modellen in de Score experimenten van Azure ML opnieuw worden getraind met behulp van nieuwe invoer gegevens sets. Wanneer u klaar bent met opnieuw trainen, wilt u de Score-webservice bijwerken met het opnieuw getrainde ML-model. De gebruikelijke stappen voor het inschakelen van het opnieuw trainen en bijwerken van Azure ML-modellen via webservices zijn:

1. Maak een experiment in [Azure machine learning Studio (klassiek)](https://studio.azureml.net).
2. Wanneer u tevreden bent met het model, gebruikt u Azure Machine Learning Studio (klassiek) voor het publiceren van webservices voor zowel het **trainings experiment** als het scoren/**voorspellende experiment**.

In de volgende tabel worden de webservices beschreven die in dit voor beeld worden gebruikt.  Zie [machine learning-modellen programmatisch opnieuw trainen](../../machine-learning/machine-learning-retrain-models-programmatically.md) voor meer informatie.

- **Training-webservice** : ontvangt trainings gegevens en produceert getrainde modellen. De uitvoer van het opnieuw trainen is een. ilearner-bestand in een Azure Blob-opslag. Het **standaard eindpunt** wordt automatisch voor u gemaakt wanneer u het trainings experiment als een webservice publiceert. U kunt meer eind punten maken, maar in het voor beeld wordt alleen het standaard eindpunt gebruikt.
- **Scores-webservice** : ontvangt voor beelden zonder gelabeld gegevens en maakt voor spellingen. De uitvoer van voor spellingen kan verschillende vormen hebben, zoals een CSV-bestand of rijen in een Azure-SQL database, afhankelijk van de configuratie van het experiment. Het standaard eindpunt wordt automatisch voor u gemaakt wanneer u het voorspellende experiment als een webservice publiceert. 

In de volgende afbeelding ziet u de relatie tussen training en Score-eind punten in azure ML.

![Webservices](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

U kunt de **training-webservice** aanroepen met behulp van de **Azure ml batch Execution-activiteit**. Het aanroepen van een training-webservice is hetzelfde als het aanroepen van een Azure ML-webservice (Score Web Service) voor het scoren van gegevens. De voor gaande secties bevatten informatie over het aanroepen van een Azure ML-webservice vanuit een Azure Data Factory pijp lijn. 

U kunt de **Score-webservice** aanroepen met behulp van de **activiteit Azure ml update resource** om de webservice bij te werken met het nieuwe getrainde model. De volgende voor beelden bieden gekoppelde service definities: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Scores-webservice is een klassieke webservice
Als de Score-webservice een **klassieke webservice**is, maakt u het tweede **niet-standaard en bijwerk bare eind punt** met behulp van de Azure Portal. Zie het artikel [endpoints maken](../../machine-learning/machine-learning-create-endpoint.md) voor de stappen. Nadat u het niet-standaard bijwerk bare eind punt hebt gemaakt, voert u de volgende stappen uit:

* Klik op **batch-uitvoering** om de URI-waarde voor de JSON-eigenschap **mlEndpoint** op te halen.
* Klik op **resource koppeling bijwerken** om de URI-waarde voor de JSON-eigenschap **updateResourceEndpoint** op te halen. De API-sleutel bevindt zich op de eindpunt pagina zelf (in de rechter benedenhoek).

![bijwerk bare eind punt](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

In het volgende voor beeld wordt een JSON-voorbeeld definitie voor de gekoppelde service van AzureML geboden. De gekoppelde service maakt gebruik van de apiKey voor authenticatie.  

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

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Scores-webservice is Azure Resource Manager web service 
Als de webservice het nieuwe type webservice is dat een Azure Resource Manager-eind punt beschikbaar maakt, hoeft u niet het tweede **niet-standaard** eindpunt toe te voegen. De **updateResourceEndpoint** in de gekoppelde service heeft de volgende indeling: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

U kunt in de URL waarden ophalen voor de plaatsings houders bij het uitvoeren van een query op de webservice op de [Portal Azure machine learning](https://services.azureml.net/)-webservices. Voor het nieuwe type update resource-eind punt is een AAD-token (Azure Active Directory) vereist. Geef **servicePrincipalId** en **servicePrincipalKey** op in de gekoppelde Azure machine learning service. Zie [Service-Principal maken en machtigingen toewijzen om Azure-resources te beheren](../../active-directory/develop/howto-create-service-principal-portal.md). Hier volgt een voor beeld van een gekoppelde service definitie voor AzureML: 

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

Het volgende scenario bevat meer informatie. Het bevat een voor beeld voor het opnieuw trainen en bijwerken van Azure ML-modellen vanuit een Azure Data Factory-pijp lijn.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Scenario: een Azure ML-model opnieuw trainen en bijwerken
Deze sectie bevat een voor beeld van een pijp lijn die gebruikmaakt **van de Azure ml batch Execution-activiteit** voor het opnieuw trainen van een model. De pijp lijn gebruikt ook de **activiteit Azure ml update resource** om het model bij te werken in de Score-webservice. De sectie bevat ook JSON-fragmenten voor alle gekoppelde services, gegevens sets en pijp lijnen in het voor beeld.

Hier volgt de diagram weergave van de voorbeeld pijplijn. Zoals u kunt zien, neemt de activiteit Azure ML batch-uitvoering de trainings invoer en produceert hij een training (iLearner-bestand). Met de resource activiteit voor Azure ML update worden de uitvoer van deze training uitgevoerd en wordt het model bijgewerkt in het eind punt van de Score-webservice. De activiteit resource bijwerken produceert geen uitvoer. De placeholderBlob is slechts een dummy-uitvoer gegevensset die wordt vereist door de Azure Data Factory-service om de pijp lijn uit te voeren.

![pijplijn diagram](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Gekoppelde Azure Blob Storage-service:
De Azure Storage bevatten de volgende gegevens:

* trainings gegevens. De invoer gegevens voor de Azure ML training-webservice.  
* iLearner-bestand. De uitvoer van de Azure ML training-webservice. Dit bestand is ook de invoer voor de activiteit resource bijwerken.  

Hier volgt een voor beeld van de JSON-definitie van de gekoppelde service:

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

### <a name="training-input-dataset"></a>Gegevensset voor invoer van training:
De volgende gegevensset vertegenwoordigt de invoer trainings gegevens voor de webservice van de Azure Machine Learning-training. De activiteit voor het uitvoeren van Azure Machine Learning batch neemt deze gegevensset als invoer.

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

### <a name="training-output-dataset"></a>Gegevensset voor trainings uitvoer:
De volgende gegevensset vertegenwoordigt het uitvoer iLearner-bestand van de Azure ML training-webservice. Met de activiteit Azure ML batch Execution wordt deze gegevensset gegenereerd. Deze gegevensset is ook de invoer voor de activiteit Azure ML update resource.

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

### <a name="linked-service-for-azure-machine-learning-training-endpoint"></a>Gekoppelde service voor Azure Machine Learning-trainings eindpunt
Het volgende JSON-code fragment definieert een Azure Machine Learning gekoppelde service die naar het standaard eindpunt van de training-webservice verwijst.

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

In **Azure machine learning Studio (klassiek)** gaat u als volgt te werk om waarden op te halen voor **mlEndpoint** en **apiKey**:

1. Klik op **WEBservices** in het menu links.
2. Klik op de training-webservice in de lijst met **webservices** .
3. Klik op Kopiëren naast tekstvak **API-sleutel** . Plak de sleutel in het klem bord in de Data Factory JSON-editor.
4. Klik in de **Azure machine learning Studio (klassiek)** op **batch-uitvoerings** koppeling.
5. Kopieer de **aanvraag-URI** uit de sectie **Request** en plak deze in de Data Factory JSON-editor.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Gekoppelde service voor het bijwerk bare Score eindpunt van Azure ML:
In het volgende JSON-code fragment wordt een Azure Machine Learning gekoppelde service gedefinieerd die verwijst naar het niet-standaard bijwerk bare eind punt van de Score-webservice.  

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

### <a name="placeholder-output-dataset"></a>Tijdelijke gegevensset voor uitvoer:
Met de activiteit Azure ML update resource wordt geen uitvoer gegenereerd. Azure Data Factory echter een uitvoer gegevensset vereist om de planning van een pijp lijn te verzorgen. Daarom gebruiken we een dummy/tijdelijke aanduiding gegevensset in dit voor beeld.  

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
De pijp lijn heeft twee activiteiten: **AzureMLBatchExecution** en **AzureMLUpdateResource**. De activiteit Azure ML batch Execution neemt de trainings gegevens als invoer en produceert een iLearner-bestand als uitvoer. De activiteit roept de training-webservice (trainings experiment beschikbaar als een webservice) aan met de gegevens voor het invoeren van de training en ontvangt het ilearner-bestand van de webservice. De placeholderBlob is slechts een dummy-uitvoer gegevensset die wordt vereist door de Azure Data Factory-service om de pijp lijn uit te voeren.

![pijplijn diagram](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

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
