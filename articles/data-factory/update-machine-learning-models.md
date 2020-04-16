---
title: Machine learning-modellen bijwerken met Azure Data Factory
description: Beschrijft hoe u voorspellende pijplijnen maakt met Azure Data Factory en machine learning
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
ms.openlocfilehash: 4488c174ba5ff35ec2709d7c1b9f3093b4ee90a3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409079"
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Azure Machine Learning-modellen bijwerken met de activiteit Resource bijwerken

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dit artikel vormt een aanvulling op het belangrijkste azure-gegevensfabriek - Azure Machine Learning-integratieartikel: [Voorspellende pijplijnen maken met Azure Machine Learning en Azure Data Factory](transform-data-using-machine-learning.md). Als u dit nog niet hebt gedaan, bekijk dan het hoofdartikel voordat u dit artikel leest.

## <a name="overview"></a>Overzicht
Als onderdeel van het proces van het operationaliseren van Azure Machine Learning-modellen, wordt uw model getraind en opgeslagen. U gebruikt deze vervolgens om een voorspellende webservice te maken. De webservice kan vervolgens worden verbruikt in websites, dashboards en mobiele apps.

Modellen die u maakt met Machine Learning zijn doorgaans niet statisch. Als er nieuwe gegevens beschikbaar komen of wanneer de consument van de API zijn eigen gegevens heeft, moet het model worden omgeschoold. Raadpleeg [een Machine Learning-model omte scholen](../machine-learning/machine-learning-retrain-machine-learning-model.md) voor meer informatie over hoe u een model omscholen in Azure Machine Learning.

Omscholing kan vaak voorkomen. Met batchuitvoeringsactiviteit en Update Resource-activiteit u het Azure Machine Learning-model omscholen en bijwerken met behulp van Gegevensfabriek.

De volgende afbeelding toont de relatie tussen training en voorspellende Web Services.

![Webservices](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Azure Machine Learning-updatebronactiviteit

In het volgende JSON-fragment wordt een Azure Machine Learning Batch Execution-activiteit gedefinieerd.

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
| name                          | Naam van de activiteit in de pijplijn     | Ja      |
| description                   | Tekst waarin wordt beschreven wat de activiteit doet.  | Nee       |
| type                          | Voor Azure Machine Learning Update Resource-activiteit is het activiteitstype **AzureMLUpdateResource**. | Ja      |
| linkedServiceName             | Gekoppelde Azure Machine Learning-service die de eigenschap UpdateResourceEndpoint bevat. | Ja      |
| getraindModelName              | Naam van de module Getraind model in het experiment Webservice dat moet worden bijgewerkt | Ja      |
| getraindModelLinkedServiceName | Naam van de gekoppelde Azure Storage-service met het ilearner-bestand dat wordt geüpload door de updatebewerking | Ja      |
| getraindModelFilePath          | Het relatieve bestandspad in trainedModelLinkedService om het ilearner-bestand weer te geven dat wordt geüpload door de updatebewerking | Ja      |

## <a name="end-to-end-workflow"></a>End-to-end werkstroom

Het hele proces van het operationaliseren van een model en het bijwerken van de voorspellende Web Services omvat de volgende stappen:

- Roep de **trainingswebservice** aan met behulp van de **activiteit Batchexecution**. Een beroep doen op een webservice voor training is hetzelfde als een beroep doen op een voorspellende webservice die wordt beschreven in [Voorspellende pijplijnen maken met Azure Machine Learning en batchuitvoeringsactiviteit voor gegevensfabrieken.](transform-data-using-machine-learning.md) De uitvoer van de trainingswebservice is een iLearner-bestand dat u gebruiken om de voorspellende webservice bij te werken.
- Roep het eindpunt van de **updatebron** van de **voorspellende webservice** aan door de **activiteit Resource bijwerken** te gebruiken om de webservice bij te werken met het nieuw getrainde model.

## <a name="azure-machine-learning-linked-service"></a>Gekoppelde Azure Machine Learning-service

Als de hierboven genoemde end-to-end-werkstroom moet werken, moet u twee gekoppelde Azure Machine Learning-services maken:

1. Deze gekoppelde service voor batchuitvoering is een Azure Machine Learning-gekoppelde service voor de trainingswebservice en wordt op dezelfde manier gebruikt door batchuitvoeringsactiviteiten als in [Voorspellende pijplijnen maken met Azure Machine Learning en Batch Execution-activiteit voor gegevensfabrieken.](transform-data-using-machine-learning.md) Verschil is de uitvoer van de trainingswebservice is een iLearner-bestand dat vervolgens wordt gebruikt door Update Resource-activiteit om de voorspellende webservice bij te werken.
2. Een Azure Machine Learning-service gekoppeld aan het eindpunt van de updatebron van de voorspellende webservice. Deze gekoppelde service wordt gebruikt door Resource-activiteit bijwerken om de voorspellende webservice bij te werken met behulp van het iLearner-bestand dat van bovenstap is geretourneerd.

Voor de tweede Azure Machine Learning-gekoppelde service is de configuratie anders wanneer uw Azure Machine Learning Web Service een klassieke webservice of een nieuwe webservice is. De verschillen worden afzonderlijk besproken in de volgende secties.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Webservice is de nieuwe Azure Resource Manager-webservice

Als de webservice het nieuwe type webservice is dat een Azure Resource Manager-eindpunt blootlegt, hoeft u het tweede **niet-standaardeindpunt** niet toe te voegen. Het **updateResourceEndpoint** in de gekoppelde service is van de indeling:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

U waarden voor plaatshouders in de URL ophalen wanneer u de webservice opvraagt op de [Azure Machine Learning Web Services Portal.](https://services.azureml.net/)

Het nieuwe type updatebroneindpunt vereist serviceprincipal-verificatie. Als u servicehoofdverificatie wilt gebruiken, registreert u een toepassingsentiteit in Azure Active Directory (Azure AD) en verleent u deze de **rol inzender** of **eigenaar** van het abonnement of de brongroep waartoe de webservice behoort. De [serviceprincipal weergeven en machtigingen toewijzen voor het beheren van Azure-bronnen.](../active-directory/develop/howto-create-service-principal-portal.md) Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:

- Toepassings-id
- Toepassingssleutel
- Tenant-id

Hier is een voorbeeld gekoppelde service definitie:

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

Het volgende scenario geeft meer details. Het heeft een voorbeeld voor het omscholen en bijwerken van Azure Machine Learning studiomodellen vanuit een Azure Data Factory-pijplijn.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Voorbeeld: een Azure Machine Learning-model omscholen en bijwerken

In deze sectie wordt een voorbeeldpijplijn opgenomen die de **batchuitvoeringsactiviteit van Azure Machine Learning studio** gebruikt om een model om te scholen. De pijplijn maakt ook gebruik van de **Azure Machine Learning-studio Update Resource-activiteit** om het model in de scoringswebservice bij te werken. De sectie biedt ook JSON-fragmenten voor alle gekoppelde services, gegevenssets en pijplijnen in het voorbeeld.

### <a name="azure-blob-storage-linked-service"></a>Gekoppelde Azure Blob-opslagservice:
De Azure Storage bevat de volgende gegevens:

* opleidingsgegevens. De invoergegevens voor de webservice Azure Machine Learning studio training.
* iLearner-bestand. De uitvoer van de webservice Azure Machine Learning studio training. Dit bestand is ook de invoer voor de activiteit Resource bijwerken.

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

### <a name="linked-service-for-azure-machine-learning-studio-training-endpoint"></a>Gekoppelde service voor eindpunt voor Azure Machine Learning-studiotraining
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

Ga in **Azure Machine Learning-studio**als volgt te werk om waarden voor **mlEndpoint** en **apiKey**op te halen:

1. Klik op **WEB SERVICES** in het linkermenu.
2. Klik op de **webservice voor training** in de lijst met webservices.
3. Klik op kopiëren naast het tekstvak **API-sleutel.** Plak de toets in het klembord in de JSON-editor van Data Factory.
4. Klik in de **Azure Machine Learning-studio**op **batchuitvoeringskoppeling.**
5. Kopieer de **URI aanvragen** vanuit de sectie **Aanvraag** en plak deze in de JSON-editor van de gegevensfabriek.

### <a name="linked-service-for-azure-machine-learning-studio-updatable-scoring-endpoint"></a>Gekoppelde service voor Azure Machine Learning studio updatable scoring endpoint:
In het volgende JSON-fragment wordt een azure Machine Learning-gekoppelde service gedefinieerd die verwijst naar een updatable eindpunt van de scoringswebservice.

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
De pijplijn heeft twee activiteiten: **AzureMLBatchExecution** en **AzureMLUpdateResource**. De activiteit Batchexecution neemt de trainingsgegevens als invoer en produceert een iLearner-bestand als uitvoer. De activiteit Resource bijwerken neemt vervolgens dit iLearner-bestand en gebruikt het om de voorspellende webservice bij te werken.

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
Bekijk de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeren:

* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Hive activiteit](transform-data-using-hadoop-hive.md)
* [Varkensactiviteit](transform-data-using-hadoop-pig.md)
* [Activiteit verkleinen](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activiteit](transform-data-using-hadoop-streaming.md)
* [Vonkactiviteit](transform-data-using-spark.md)
* [.NET aangepaste activiteit](transform-data-using-dotnet-custom-activity.md)
* [Opgeslagen procedureactiviteit](transform-data-using-stored-procedure.md)
