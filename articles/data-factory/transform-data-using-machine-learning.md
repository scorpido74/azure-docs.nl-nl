---
title: Voorspellende gegevenspijplijnen maken
description: Meer informatie over het maken van een voorspellende pijplijn met Azure Machine Learning - Batch Execution Activity in Azure Data Factory.
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/20/2019
ms.openlocfilehash: 3587ee711864eb33fea9bc4e61fe226562e8f612
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418861"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Voorspellende pijplijnen maken met Azure Machine Learning en Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Huidige versie](transform-data-using-machine-learning.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) stelt u in staat om voorspellende analyseoplossingen te bouwen, te testen en te implementeren. Vanuit het oogpunt van hoog niveau gebeurt dit in drie stappen:

1. **Maak een trainingsexperiment**. Dit doe je stap met de Azure Machine Learning Studio (klassiek). Azure Machine Learning Studio (klassiek) is een collaboratieve visuele ontwikkelomgeving die u gebruikt om een voorspellend analysemodel te trainen en te testen met behulp van trainingsgegevens.
2. **Converteer het naar een voorspellend experiment.** Zodra uw model is getraind met bestaande gegevens en u klaar bent om het te gebruiken om nieuwe gegevens te scoren, bereidt u uw experiment voor op het scoren.
3. **Implementeer het als een webservice.** U uw scoreexperiment publiceren als een Azure-webservice. U gegevens naar uw model verzenden via dit eindpunt van de webservice en resultaatvoorspellingen van het model ontvangen.

### <a name="data-factory-and-machine-learning-together"></a>Data Factory en Machine Learning samen
Met Azure Data Factory u eenvoudig pijplijnen maken die gebruikmaken van een gepubliceerde [Azure Machine Learning-webservice](https://azure.microsoft.com/documentation/services/machine-learning) voor voorspellende analyses. Met behulp van de **batchuitvoeringsactiviteit** in een Azure Data Factory-pijplijn u een Azure Machine Learning Studio -webservice (klassieke) webservice aanroepen om voorspellingen te doen over de gegevens in batch.

Na verloop van tijd moeten de voorspellende modellen in de Azure Machine Learning Studio (klassieke) scoringsexperimenten worden omgeschoold met behulp van nieuwe invoergegevenssets. U een model opnieuw trainen vanuit een datafabriekpijplijn door de volgende stappen uit te voeren:

1. Publiceer het trainingsexperiment (geen voorspellend experiment) als een webservice. U doet deze stap in de Azure Machine Learning Studio (klassiek) zoals u deed om voorspellend experiment als webservice in het vorige scenario bloot te leggen.
2. Gebruik de Azure Machine Learning Studio (klassieke) batchuitvoeringsactiviteit om de webservice voor het trainingsexperiment aan te roepen. In principe u de activiteit Azure Machine Learning Studio (klassieke) batchuitvoeringsactiviteit gebruiken om zowel de webservice voor training als de webservice voor het scoren aan te roepen.

Nadat u klaar bent met omscholing, werkt u de scoringswebservice (voorspellend experiment dat als webservice wordt weergegeven) bij met het nieuw getrainde model met behulp van de **Azure Machine Learning Studio (klassieke) Update Resource Activity**. Zie Modellen bijwerken met het artikel [Resourceactiviteit bijwerken](update-machine-learning-models.md) voor meer informatie.

## <a name="azure-machine-learning-linked-service"></a>Gekoppelde Azure Machine Learning-service

U maakt een **Azure Machine Learning-gekoppelde** service om een Azure Machine Learning Web Service te koppelen aan een Azure-gegevensfabriek. De gekoppelde service wordt gebruikt door Azure Machine Learning Batch Execution Activity en [Update Resource Activity](update-machine-learning-models.md).

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Zie [artikel Gekoppelde services berekenen](compute-linked-services.md) voor beschrijvingen over eigenschappen in de JSON-definitie.

Azure Machine Learning ondersteunt zowel Classic Web Services als New Web Services voor uw voorspellende experiment. U de juiste kiezen om te gebruiken in Data Factory. Als u de informatie wilt krijgen die nodig https://services.azureml.netis om de Gekoppelde Azure Machine Learning-service te maken, gaat u naar , waar al uw (nieuwe) WebServices en Classic Web Services worden vermeld. Klik op de webservice die u wilt openen en klik op **Pagina Consumeren.** Primaire **sleutel kopiëren** voor de eigenschap **apiKey** en **Batchaanvragen** voor de eigenschap **MLEndpoint.**

![Azure Machine Learning Web Services](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Azure Machine Learning Batch Uitvoeringsactiviteit

In het volgende JSON-fragment wordt een Azure Machine Learning Batch Execution-activiteit gedefinieerd. De activiteitsdefinitie heeft een verwijzing naar de azure Machine Learning-gekoppelde service die u eerder hebt gemaakt.

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path1"
            },
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path2"
            }
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path3"
            },
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path4"
            }
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```

| Eigenschap          | Beschrijving                              | Vereist |
| :---------------- | :--------------------------------------- | :------- |
| name              | Naam van de activiteit in de pijplijn     | Ja      |
| description       | Tekst waarin wordt beschreven wat de activiteit doet.  | Nee       |
| type              | Voor Data Lake Analytics U-SQL-activiteit is het activiteitstype **AzureMLBatchExecution**. | Ja      |
| linkedServiceName | Gekoppelde services aan de Azure Machine Learning Linked Service. Zie Artikel [Gekoppelde services](compute-linked-services.md) berekenen voor meer informatie over deze gekoppelde service. | Ja      |
| webServiceInputs  | Sleutel- en waardeparen, de namen van Azure Machine Learning Web Service-ingangen in kaart brengen. De sleutel moet overeenkomen met de invoerparameters die zijn gedefinieerd in de gepubliceerde Azure Machine Learning Web Service. Waarde is een Azure Storage Linked Services en FilePath-eigenschappenpaar waarin de invoerbloblocaties worden opgegeven. | Nee       |
| webServiceOutputs | Sleutel- en waardeparen, de namen van Azure Machine Learning Web Service-uitvoer in kaart brengen. De sleutel moet overeenkomen met de uitvoerparameters die zijn gedefinieerd in de gepubliceerde Azure Machine Learning Web Service. Waarde is een Azure Storage Linked Services en FilePath-eigenschappenpaar waarin de uitvoerbloblocaties worden opgegeven. | Nee       |
| globalParameters  | Sleutel, Waardeparen die moeten worden doorgegeven aan het eindpunt van de Azure Machine Learning Studio (klassieke) Batch Execution Service. Toetsen moeten overeenkomen met de namen van de parameters van de webservice die zijn gedefinieerd in de gepubliceerde Azure Machine Learning Studio (klassieke) webservice. Waarden worden doorgegeven in de eigenschap GlobalParameters van de azure machine learning studio (klassieke) batchuitvoeringsaanvraag | Nee       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenario 1: Experimenten met webservice-ingangen/uitvoer die verwijzen naar gegevens in Azure Blob Storage

In dit scenario voert de Webservice Azure Machine Learning voorspellingen uit met behulp van gegevens uit een bestand in een Azure blob-opslag en slaat de voorspellingsresultaten op in de blob-opslag. De volgende JSON definieert een Data Factory-pijplijn met een AzureMLBatchExecution-activiteit. De invoer- en uitvoergegevens in Azure Blog Storage worden verwezen met behulp van een LinkedName- en FilePath-paar. In het voorbeeld dat Linked Service van ingangen en uitvoer verschillend is, u verschillende Linked Services gebruiken voor elk van uw ingangen/uitvoer voor Data Factory om de juiste bestanden op te halen en naar Azure Machine Learning Studio (klassieke) webservice te verzenden.

> [!IMPORTANT]
> In uw Azure Machine Learning Studio (klassiek) experiment hebben invoer- en uitvoerpoorten van webservice en globale parameters standaardnamen ('input1', 'input2') die u aanpassen. De namen die u gebruikt voor webServiceInputs, webServiceOutputs en globalParameters-instellingen moeten exact overeenkomen met de namen in de experimenten. U de payload van de voorbeeldaanvraag bekijken op de help-pagina batchuitvoering voor uw Azure Machine Learning Studio -eindpunt (klassiek) om de verwachte toewijzing te verifiëren.
>
>

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in1.csv"
            },
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in2.csv"
            }
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out1.csv"
            },
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out2.csv"
            }
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenario 2: Experimenten met reader/writer modules om te verwijzen naar gegevens in verschillende opslagen
Een ander veelvoorkomend scenario bij het maken van Azure Machine Learning Studio (klassieke) experimenten is het gebruik van importgegevens en uitvoergegevensmodules. De module Gegevens importeren wordt gebruikt om gegevens in een experiment te laden en de module Uitvoergegevens is om gegevens uit uw experimenten op te slaan. Zie [Onderwerpen Gegevens importeren](https://msdn.microsoft.com/library/azure/dn905997.aspx) en uitvoergegevens importeren op MSDN-bibliotheek voor meer informatie over modules voor gegevens importeren en [uitvoergegevens.](https://msdn.microsoft.com/library/azure/dn905984.aspx)

Bij het gebruik van de modules Gegevens importeren en uitvoergegevens is het een goede gewoonte om voor elke eigenschap van deze modules een parameter voor webservice te gebruiken. Met deze webparameters u de waarden tijdens runtime configureren. U bijvoorbeeld een experiment maken met een module Gegevens importeren die een Azure SQL-database gebruikt: XXX.database.windows.net. Nadat de webservice is geïmplementeerd, wilt u de consumenten van de webservice `YYY.database.windows.net`inschakelen om een andere Azure SQL Server met de naam . U een parameter Webservice gebruiken om deze waarde te configureren.

> [!NOTE]
> De invoer en uitvoer van webserviceverschillen van de parameters van webservice. In het eerste scenario hebt u gezien hoe een invoer en uitvoer kan worden opgegeven voor een Azure Machine Learning Studio (klassieke) webservice. In dit scenario geeft u parameters door voor een webservice die overeenkomen met eigenschappen van modules Gegevens importeren/uitvoergegevens.
>
>

Laten we eens kijken naar een scenario voor het gebruik van webserviceparameters. U hebt een geïmplementeerde Azure Machine Learning-webservice die een leesmodule gebruikt om gegevens te lezen uit een van de gegevensbronnen die worden ondersteund door Azure Machine Learning (bijvoorbeeld: Azure SQL Database). Nadat de batch-uitvoering is uitgevoerd, worden de resultaten geschreven met behulp van een Writer-module (Azure SQL Database).  Er worden geen webservice-ingangen en uitvoer gedefinieerd in de experimenten. In dit geval raden we u aan relevante webserviceparameters voor de lezer- en schrijvermodules te configureren. Met deze configuratie kunnen de lees-/writer-modules worden geconfigureerd wanneer u de AzureMLBatchExecution-activiteit gebruikt. U geeft als volgt webserviceparameters op in de sectie **globaal Parameters** in de activiteit JSON.

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```

> [!NOTE]
> De parameters van de webservice zijn hoofdlettergevoelig, dus zorg ervoor dat de namen die u opgeeft in de activiteit JSON overeenkomen met de namen die door de webservice worden weergegeven.
>

Nadat u klaar bent met omscholing, werkt u de scoringswebservice (voorspellend experiment dat als webservice wordt weergegeven) bij met het nieuw getrainde model met behulp van de **Azure Machine Learning Studio (klassieke) Update Resource Activity**. Zie Modellen bijwerken met het artikel [Resourceactiviteit bijwerken](update-machine-learning-models.md) voor meer informatie.

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
