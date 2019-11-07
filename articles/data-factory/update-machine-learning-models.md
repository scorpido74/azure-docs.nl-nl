---
title: machine learning modellen bijwerken met behulp van Azure Data Factory
description: Hierin wordt beschreven hoe u voorspellende pijp lijnen maakt met behulp van Azure Data Factory en machine learning
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 3313c9c362a9b82cf7ed8db63479aaa5cf0c777e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683240"
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Azure Machine Learning modellen bijwerken met resource activiteit bijwerken
Dit artikel vormt een aanvulling op het belangrijkste Azure Data Factory-Azure Machine Learning-integratie artikel: [Maak voorspellende pijp lijnen met behulp van Azure machine learning en Azure Data Factory](transform-data-using-machine-learning.md). Als u dit nog niet hebt gedaan, raadpleegt u het hoofd artikel voordat u dit artikel leest.

## <a name="overview"></a>Overzicht
Als onderdeel van het proces van inzet Azure Machine Learning modellen wordt uw model getraind en opgeslagen. Vervolgens gebruikt u het om een voorspellende webservice te maken. De webservice kan vervolgens worden gebruikt in websites, Dash boards en mobiele apps.

Modellen die u maakt met behulp van Machine Learning zijn doorgaans niet statisch. Wanneer er nieuwe gegevens beschikbaar zijn of wanneer de consumer van de API hun eigen gegevens heeft, moet het model opnieuw worden getraind. Raadpleeg [een machine learning model opnieuw trainen](../machine-learning/machine-learning-retrain-machine-learning-model.md) voor meer informatie over het opnieuw trainen van een model in azure machine learning.

Het opnieuw trainen kan regel matig worden uitgevoerd. Met de activiteit batch-uitvoering en update resource kunt u het Azure Machine Learning model opnieuw trainen en de voorspellende webservice bijwerken met behulp van Data Factory.

In de volgende afbeelding ziet u de relatie tussen training en voorspellende webservices.

![Webservices](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Resource activiteit Azure Machine Learning bijwerken

In het volgende JSON-code fragment wordt een Azure Machine Learning batch uitvoerings activiteit gedefinieerd.

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```

| Eigenschap                      | Beschrijving                              | Vereist |
| :---------------------------- | :--------------------------------------- | :------- |
| naam                          | Naam van de activiteit in de pijp lijn     | Ja      |
| description                   | Tekst die beschrijft wat de activiteit doet.  | Nee       |
| type                          | Voor Azure Machine Learning bron activiteit bijwerken is het type activiteit **AzureMLUpdateResource**. | Ja      |
| linkedServiceName             | Azure Machine Learning gekoppelde service die de eigenschap updateResourceEndpoint bevat. | Ja      |
| trainedModelName              | De naam van de getrainde model module in het web service-experiment dat moet worden bijgewerkt | Ja      |
| trainedModelLinkedServiceName | De naam van Azure Storage gekoppelde service die het ilearner-bestand bewaart dat door de update bewerking is geüpload | Ja      |
| trainedModelFilePath          | Het relatieve bestandspad in trainedModelLinkedService dat het ilearner-bestand vertegenwoordigt dat door de update bewerking is geüpload | Ja      |

## <a name="end-to-end-workflow"></a>End-to-end werkstroom

Het hele proces van inzet het opnieuw trainen van een model en het bijwerken van de voorspellende webservices omvat de volgende stappen:

- Roep de **training-webservice** aan met behulp van de **batch Execution-activiteit**. Het aanroepen van een trainings-webservice is hetzelfde als het aanroepen van een voorspellende webservice die wordt beschreven in [voorspellende pijp lijnen maken met behulp van Azure machine learning en Data Factory batch Execution-activiteit](transform-data-using-machine-learning.md). De uitvoer van de training-webservice is een iLearner-bestand dat u kunt gebruiken om de voorspellende webservice bij te werken.
- Roep het **resource-eind punt** voor het bijwerken van de **voorspellende webservice** aan met de **activiteit resource bijwerken** om de webservice bij te werken met het nieuwe getrainde model.

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning gekoppelde service

Voor de hierboven genoemde end-to-end werk stroom werkt, moet u twee Azure Machine Learning gekoppelde services maken:

1. Een Azure Machine Learning gekoppelde service voor de training-webservice, deze gekoppelde service wordt gebruikt door activiteiten voor batch uitvoering op dezelfde manier als wat wordt vermeld in [voorspellende pijp lijnen maken met behulp van Azure machine learning en Data Factory batch-uitvoering activiteit](transform-data-using-machine-learning.md). Verschil is de uitvoer van de training-webservice is een iLearner-bestand dat vervolgens wordt gebruikt door de activiteit resource bijwerken om de voorspellende webservice bij te werken.
2. Een Azure Machine Learning gekoppelde service aan het resource-eind punt van de update van de voorspellende webservice. Deze gekoppelde service wordt gebruikt door resource activiteit bijwerken om de voorspellende webservice bij te werken met het iLearner-bestand dat is geretourneerd door de bovenstaande stap.

Voor de tweede Azure Machine Learning gekoppelde service is de configuratie anders wanneer uw Azure Machine Learning-webservice een klassieke webservice of een nieuwe webservice is. De verschillen worden afzonderlijk beschreven in de volgende secties.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Web service is nieuw Azure Resource Manager-webservice

Als de webservice het nieuwe type webservice is dat een Azure Resource Manager-eind punt beschikbaar maakt, hoeft u niet het tweede **niet-standaard** eindpunt toe te voegen. De **updateResourceEndpoint** in de gekoppelde service heeft de volgende indeling:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

U kunt in de URL waarden ophalen voor de plaatsings houders bij het uitvoeren van een query op de webservice op de [Portal Azure machine learning](https://services.azureml.net/)-webservices.

Voor het nieuwe type update resource-eind punt is Service-Principal-verificatie vereist. Als u Service-Principal-verificatie wilt gebruiken, registreert u een toepassings entiteit in Azure Active Directory (Azure AD) en geeft u deze de rol **Inzender** of **eigenaar** van het abonnement of de resource groep waarvan de webservice deel uitmaakt. De Zie [Service-Principal maken en machtigingen toewijzen om Azure-resources te beheren](../active-directory/develop/howto-create-service-principal-portal.md). Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:

- Toepassings-id
- Toepassings sleutel
- Tenant-id

Hier volgt een voor beeld van een gekoppelde service definitie:

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
                "type": "SecureString",
                "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

Het volgende scenario bevat meer informatie. Het bevat een voor beeld voor het opnieuw trainen en bijwerken van Azure Machine Learning Studio-modellen van een Azure Data Factory-pijp lijn.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Voor beeld: een Azure Machine Learning model opnieuw trainen en bijwerken

Deze sectie bevat een voorbeeld pijplijn die gebruikmaakt van de **Azure machine learning Studio batch Execution-activiteit** om een model opnieuw te trainen. De pijp lijn gebruikt ook de **activiteit resource van Azure machine learning Studio update** om het model bij te werken in de Score-webservice. De sectie bevat ook JSON-fragmenten voor alle gekoppelde services, gegevens sets en pijp lijnen in het voor beeld.

### <a name="azure-blob-storage-linked-service"></a>Gekoppelde Azure Blob Storage-service:
De Azure Storage bevatten de volgende gegevens:

* trainings gegevens. De invoer gegevens voor de Azure Machine Learning Studio-trainings-webservice.
* iLearner-bestand. De uitvoer van de Azure Machine Learning Studio-trainings-webservice. Dit bestand is ook de invoer voor de activiteit resource bijwerken.

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

### <a name="linked-service-for-azure-machine-learning-studio-training-endpoint"></a>Gekoppelde service voor Azure Machine Learning Studio-trainings eindpunt
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

In **Azure machine learning Studio**gaat u als volgt te werk om waarden op te halen voor **mlEndpoint** en **apiKey**:

1. Klik op **WEBservices** in het menu links.
2. Klik op de training-webservice in de lijst met **webservices** .
3. Klik op Kopiëren naast tekstvak **API-sleutel** . Plak de sleutel in het klem bord in de Data Factory JSON-editor.
4. Klik in de **Azure machine learning Studio**op **batch-uitvoerings** koppeling.
5. Kopieer de **aanvraag-URI** uit de sectie **Request** en plak deze in de Data Factory JSON-editor.

### <a name="linked-service-for-azure-machine-learning-studio-updatable-scoring-endpoint"></a>Gekoppelde service voor het bij te werken Score eindpunt van Azure Machine Learning studio:
Het volgende JSON-code fragment definieert een Azure Machine Learning gekoppelde service die verwijst naar het bijwerk bare eind punt van de Score-webservice.

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

### <a name="pipeline"></a>Pijplijn
De pijp lijn heeft twee activiteiten: **AzureMLBatchExecution** en **AzureMLUpdateResource**. De batch Execution-activiteit neemt de trainings gegevens als invoer en produceert een iLearner-bestand als uitvoer. Met de activiteit resource bijwerken wordt dit iLearner-bestand gebruikt om de voorspellende webservice bij te werken.

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        },
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        }
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/output"
                        }
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                        "type": "LinkedServiceReference",
                        "referenceName": "StorageLinkedService"
                    },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [
                    {
                        "activity": "amlbeGetilearner",
                        "dependencyConditions": [ "Succeeded" ]
                    }
                ]
            }
        ]
    }
}
```
## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeert:

* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Hive-activiteit](transform-data-using-hadoop-hive.md)
* [Pig-activiteit](transform-data-using-hadoop-pig.md)
* [MapReduce-activiteit](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-activiteit](transform-data-using-hadoop-streaming.md)
* [Spark-activiteit](transform-data-using-spark.md)
* [.NET aangepaste activiteit](transform-data-using-dotnet-custom-activity.md)
* [Opgeslagen procedure activiteit](transform-data-using-stored-procedure.md)
