---
title: Voorspellende gegevens pijplijnen maken met behulp van Azure Data Factory
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
ms.openlocfilehash: c40b58dfb63ac6bf1b5532eb06bfd2ad0cdccde9
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84022024"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Voorspellende pijplijnen maken met Azure Machine Learning en Azure Data Factory

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

## <a name="introduction"></a>Inleiding
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [gegevens transformeren met behulp van machine learning in Data Factory](../transform-data-using-machine-learning.md).


### <a name="azure-machine-learning"></a>Azure Machine Learning
Met [Azure machine learning](https://azure.microsoft.com/documentation/services/machine-learning/) kunt u Predictive Analytics oplossingen bouwen, testen en implementeren. Vanuit een weergave punt op hoog niveau, wordt deze in drie stappen uitgevoerd:

1. **Een trainings experiment maken**. U voert deze stap uit met behulp van de Azure Machine Learning Studio. Azure Machine Learning Studio is een samen werkende ontwikkel omgeving die u gebruikt om een predictive analytics model te trainen en te testen met behulp van trainings gegevens.
2. **Converteer deze naar een voorspellend experiment**. Zodra uw model is getraind met bestaande gegevens en u klaar bent om het te gebruiken voor het beoordelen van nieuwe gegevens, kunt u uw experiment voorbereiden en stroom lijnen.
3. **Implementeer de service als een webservice**. U kunt uw score experiment publiceren als een Azure-webservice. U kunt gegevens naar uw model verzenden via dit eind punt van de webservice en resultaat voorspellingen van het model ontvangen.

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory is een op de cloud gebaseerde gegevens integratie service waarmee de **verplaatsing** en **trans formatie** van gegevens wordt beheerd en geautomatiseerd. U kunt oplossingen voor gegevens integratie maken met behulp van Azure Data Factory die gegevens uit verschillende gegevens archieven kan opnemen, de gegevens transformeren en verwerken, en de resulterende gegevens naar de gegevens archieven publiceren.

Met Data Factory-service kunt u gegevenspijplijnen maken die gegevens verplaatsen en transformeren, en de pijplijnen vervolgens uitvoeren volgens een opgegeven schema (per uur, dagelijks, wekelijks enz.). U vindt uitgebreide visualisaties om de afkomst en afhankelijkheden tussen uw gegevenspijplijnen weer te geven en al uw gegevenspijplijnen te controleren vanuit één centrale weergave zodat u eenvoudig problemen kunt detecteren en bewakingswaarschuwingen kunt instellen.

Zie [Inleiding tot Azure Data Factory](data-factory-introduction.md) en [bouw uw eerste pijplijn](data-factory-build-your-first-pipeline.md) artikelen om snel aan de slag te gaan met de Azure Data Factory-service.

### <a name="data-factory-and-machine-learning-together"></a>Data Factory en Machine Learning samen
Met Azure Data Factory kunt u eenvoudig pijp lijnen maken die gebruikmaken van een gepubliceerde [Azure machine learning][azure-machine-learning] -webservice voor Predictive Analytics. Met de **batch Execution-activiteit** in een Azure Data Factory pijp lijn kunt u een Azure machine learning Studio-webservice aanroepen om voor spellingen te doen op de gegevens in batch. Zie een Azure Machine Learning Studio-webservice aanroepen met behulp van de sectie batch Execution-activiteit voor meer informatie.

Na verloop van tijd moeten de voorspellende modellen in de Azure Machine Learning Studio-Score experimenten opnieuw worden getraind met nieuwe invoer gegevens sets. U kunt een model van Azure Machine Learning Studio opnieuw trainen vanuit een Data Factory-pijp lijn door de volgende stappen uit te voeren:

1. Publiceer het trainings experiment (niet voorspellend experiment) als een webservice. U kunt deze stap in de Azure Machine Learning Studio uitvoeren zoals u dat zou doen om voorspellende experimenten als een webservice in het vorige scenario beschikbaar te stellen.
2. Gebruik de Azure Machine Learning Studio-activiteit voor uitvoering van batch om de webservice voor het trainings experiment aan te roepen. In principe kunt u de activiteit Azure Machine Learning Studio batch Execution gebruiken voor het aanroepen van zowel trainingen-webservice als Score-webservice.

Wanneer u klaar bent met het opnieuw trainen, werkt u de Score-webservice (voorspellende experiment weer gegeven als een webservice) bij met het nieuwe getrainde model met behulp van de **activiteit Azure machine learning Studio update resource**. Zie [modellen bijwerken met update resource activiteit](data-factory-azure-ml-update-resource-activity.md) artikel voor meer informatie.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Een webservice aanroepen met de activiteit batch uitvoering
U gebruikt Azure Data Factory om gegevens verplaatsing en-verwerking te organiseren en vervolgens batch uitvoering uit te voeren met Azure Machine Learning. Dit zijn de stappen op het hoogste niveau:

1. Een Azure Machine Learning gekoppelde service maken. U hebt de volgende waarden nodig:

   1. De **aanvraag-URI** voor de API voor batch uitvoering. U kunt de aanvraag-URI vinden door te klikken op de koppeling **batch-uitvoering** op de pagina Web Services.
   2. **API-sleutel** voor de gepubliceerde Azure machine learning-webservice. U kunt de API-sleutel vinden door te klikken op de webservice die u hebt gepubliceerd.
   3. Gebruik de activiteit **AzureMLBatchExecution** .

      ![Machine Learning dash board](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Batch-URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenario: experimenten met behulp van invoer/uitvoer van de webservice die verwijst naar gegevens in Azure Blob Storage
In dit scenario maakt de Azure Machine Learning-webservice voor spellingen met behulp van gegevens uit een bestand in een Azure Blob-opslag en slaat de Voorspellings resultaten op in de Blob-opslag. De volgende JSON definieert een Data Factory pijp lijn met een AzureMLBatchExecution-activiteit. De-activiteit heeft de gegevensset **DecisionTreeInputBlob** als invoer en **DecisionTreeResultBlob** als uitvoer. De **DecisionTreeInputBlob** wordt door gegeven als invoer voor de webservice met behulp van de JSON-eigenschap **webserviceinputactivity** . De **DecisionTreeResultBlob** wordt door gegeven als een uitvoer naar de webservice met behulp van de JSON-eigenschap **webServiceOutputs** .

> [!IMPORTANT]
> Als de webservice meerdere invoer heeft, gebruikt u de eigenschap **webServiceInputs** in plaats van **webserviceinputactivity**te gebruiken. Zie de sectie [meerdere invoer](#web-service-requires-multiple-inputs) voor een voor beeld van het gebruik van de eigenschap webServiceInputs.
>
> Gegevens sets waarnaar wordt verwezen door de eigenschappen **webserviceinputactivity** / **webServiceInputs** en **webServiceOutputs** (in **typeProperties**) moeten ook worden opgenomen in de activiteit **invoer** en **uitvoer**.
>
> In uw Azure Machine Learning Studio-experiment hebben de invoer-en uitvoer poorten voor de web-service en de globale para meters standaard namen ("input1", "input2") die u kunt aanpassen. De namen die u gebruikt voor de instellingen webServiceInputs, webServiceOutputs en globalParameters moeten exact overeenkomen met de namen in de experimenten. U kunt de nettolading van de voorbeeld aanvraag bekijken op de Help-pagina voor het uitvoeren van batch uitvoering voor uw Azure Machine Learning Studio-eind punt om de verwachte toewijzing te controleren.
>
>

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```
> [!NOTE]
> Alleen invoer en uitvoer van de AzureMLBatchExecution-activiteit kunnen worden door gegeven als para meters voor de webservice. In het bovenstaande JSON-fragment is DecisionTreeInputBlob bijvoorbeeld een invoer van de AzureMLBatchExecution-activiteit, die wordt door gegeven als invoer voor de webservice via de Webserviceinputactivity-para meter.
>
>

### <a name="example"></a>Voorbeeld
In dit voor beeld wordt Azure Storage gebruikt om zowel de invoer-als uitvoer gegevens te bewaren.

We raden u aan om door te gaan [met het bouwen van uw eerste pijp lijn met Data Factory][adf-build-1st-pipeline] zelf studie voordat u dit voor beeld gaat volgen. Gebruik de Data Factory editor om Data Factory artefacten (gekoppelde services, gegevens sets, pijp lijn) in dit voor beeld te maken.

1. Maak een **gekoppelde service** voor uw **Azure Storage**. Als de invoer-en uitvoer bestanden zich in verschillende opslag accounts bevinden, hebt u twee gekoppelde services nodig. Hier volgt een voor beeld van een JSON:

    ```JSON
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
        }
      }
    }
    ```
2. De **invoer** Azure Data Factory **gegevensset**maken. In tegens telling tot andere Data Factory gegevens sets, moeten deze gegevens sets zowel **FolderPath** -als **filename** -waarden bevatten. U kunt partitioneren gebruiken om de uitvoering van elke batch (elk gegevens segment) te laten verwerken of unieke invoer-en uitvoer bestanden te maken. Mogelijk moet u enige upstream-activiteit toevoegen om de invoer te transformeren naar de CSV-bestands indeling en deze in het opslag account voor elk segment te plaatsen. In dat geval neemt u niet de instellingen voor **externe** en **externalData** op die in het volgende voor beeld worden weer gegeven. uw DecisionTreeInputBlob zou de uitvoer gegevensset van een andere activiteit zijn.

    ```JSON
    {
      "name": "DecisionTreeInputBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/input",
          "fileName": "in.csv",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
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

    Het CSV-bestand van de invoer moet de rij met kolom koppen bevatten. Als u de **Kopieer activiteit** gebruikt om het CSV-bestand te maken of te verplaatsen naar de Blob-opslag, moet u de Sink-eigenschap **blobWriterAddHeader** instellen op **True**. Bijvoorbeeld:

    ```JSON
    sink:
    {
        "type": "BlobSink",
        "blobWriterAddHeader": true
    }
    ```

    Als het CSV-bestand niet de veldnamenrij bevat, ziet u mogelijk de volgende fout: **fout in activiteit: fout bij het lezen van de teken reeks. Onverwacht token: start object. Pad, regel 1, positie 1**.
3. De **uitvoer** Azure Data Factory **gegevensset**maken. In dit voor beeld wordt gepartitioneerd voor het maken van een uniek uitvoerpad voor elke uitvoering van een segment. Zonder partitioneren zou de activiteit het bestand overschrijven.

    ```JSON
    {
      "name": "DecisionTreeResultBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/scored/{folderpart}/",
          "fileName": "{filepart}result.csv",
          "partitionedBy": [
            {
              "name": "folderpart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyyMMdd"
              }
            },
            {
              "name": "filepart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HHmmss"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 15
        }
      }
    }
    ```
4. Maak een **gekoppelde service** van het type: **AzureMLLinkedService**, die de API-sleutel en de URL voor de model batch uitvoering levert.

    ```JSON
    {
      "name": "MyAzureMLLinkedService",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
          "mlEndpoint": "https://[batch execution endpoint]/jobs",
          "apiKey": "[apikey]"
        }
      }
    }
    ```
5. Ontwerp tot slot een pijp lijn met een **AzureMLBatchExecution** -activiteit. Tijdens runtime voert de pijp lijn de volgende stappen uit:

   1. Hiermee wordt de locatie van het invoer bestand opgehaald uit de invoer gegevens sets.
   2. Hiermee wordt de API voor het uitvoeren van Azure Machine Learning-uitvoering aangeroepen
   3. Hiermee wordt de uitvoer van de batch naar de BLOB gekopieerd die in de uitvoer gegevensset is opgegeven.

      > [!NOTE]
      > De AzureMLBatchExecution-activiteit kan nul of meer invoer waarden hebben en een of meer uitvoer.
      >
      >

      ```JSON
      {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [
            {
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [
                {
                    "name": "DecisionTreeInputBlob"
                }
                ],
                "outputs": [
                {
                    "name": "DecisionTreeResultBlob"
                }
                ],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
      }
      ```

      De **begin** -en **eind** datum moeten de [ISO-indeling](https://en.wikipedia.org/wiki/ISO_8601)hebben. Bijvoorbeeld: 2014-10-14T16:32:41Z. De **eind** tijd is optioneel. Als u geen waarde opgeeft voor de eigenschap **End** , wordt deze berekend als '**Start + 48 uur '.** Als u de pijplijn voor onbepaalde tijd wilt uitvoeren, geeft u **9999-09-09** op als waarde voor de eigenschap **end**. Zie de [naslaginformatie voor JSON-scriptverwerking](https://msdn.microsoft.com/library/dn835050.aspx) voor meer informatie over de JSON-eigenschappen.

      > [!NOTE]
      > Het opgeven van een invoer voor de activiteit AzureMLBatchExecution is optioneel.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenario: experimenten met behulp van Reader/Writer modules om te verwijzen naar gegevens in verschillende opslag ruimten
Een ander algemeen scenario bij het maken van Azure Machine Learning Studio experimenten is het gebruik van de modules lezer en Writer. De module lezer wordt gebruikt om gegevens te laden in een experiment en de module schrijver is het opslaan van gegevens van uw experimenten. Zie de onderwerpen over [lezer](https://msdn.microsoft.com/library/azure/dn905997.aspx) en [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) in de MSDN-bibliotheek voor meer informatie over de modules lezer en Writer.

Wanneer u de modules lezer en Writer gebruikt, is het verstandig om een para meter voor de webservice te gebruiken voor elke eigenschap van deze modules voor lezer/Writer. Met deze web-para meters kunt u de waarden tijdens runtime configureren. U kunt bijvoorbeeld een experiment maken met een lees module die gebruikmaakt van een Azure SQL Database: XXX.database.windows.net. Nadat de webservice is geïmplementeerd, wilt u de gebruikers van de webservice in staat stellen een andere logische SQL-Server met de naam YYY.database.windows.net op te geven. U kunt een webservice-para meter gebruiken om deze waarde te kunnen configureren.

> [!NOTE]
> De invoer en uitvoer van webservices verschillen van de para meters van de webservice. In het eerste scenario hebt u gezien hoe u een invoer-en uitvoer kunt opgeven voor een Azure Machine Learning Studio-webservice. In dit scenario geeft u para meters door voor een webservice die overeenkomt met de eigenschappen van de modules lezer/Writer.
>
>

Laten we eens kijken naar een scenario voor het gebruik van web service-para meters. U hebt een geïmplementeerde Azure Machine Learning-webservice die gebruikmaakt van een lees module om gegevens te lezen uit een van de gegevens bronnen die worden ondersteund door Azure Machine Learning (bijvoorbeeld: Azure SQL Database). Nadat de batch uitvoering is uitgevoerd, worden de resultaten geschreven met behulp van een schrijf module (Azure SQL Database).  Er zijn geen ingangen en uitvoer van de webservice gedefinieerd in de experimenten. In dit geval kunt u het beste relevante webservice-para meters configureren voor de modules lezer en Writer. Met deze configuratie kunnen de modules van lezer/Writer worden geconfigureerd wanneer de AzureMLBatchExecution-activiteit wordt gebruikt. U geeft de webservice-para meters op in de sectie **globalParameters** van de JSON van de activiteit als volgt.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

U kunt ook [Data Factory-functies](data-factory-functions-variables.md) gebruiken bij het door geven van waarden voor de para meters van de webservice, zoals wordt weer gegeven in het volgende voor beeld:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> De para meters van de webservice zijn hoofdletter gevoelig, dus zorg ervoor dat de namen die u opgeeft in de JSON van de activiteit overeenkomen met die van de webservice.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Een lezers module gebruiken om gegevens te lezen uit meerdere bestanden in Azure Blob
Big Data-pijp lijnen met activiteiten als varken en Hive kunnen een of meer uitvoer bestanden maken zonder uitbrei dingen. Wanneer u bijvoorbeeld een externe Hive-tabel opgeeft, kunnen de gegevens voor de externe Hive-tabel worden opgeslagen in Azure Blob Storage met de volgende naam 000000_0. U kunt de module lezer in een experiment gebruiken om meerdere bestanden te lezen en ze gebruiken voor voor spellingen.

Wanneer u de module lezer in een Azure Machine Learning experiment gebruikt, kunt u Azure Blob als invoer opgeven. De bestanden in de Azure Blob-opslag kunnen de uitvoer bestanden zijn (bijvoorbeeld: 000000_0) die worden geproduceerd door een Pig-en Hive-script dat wordt uitgevoerd op HDInsight. Met de lezers module kunt u bestanden (zonder extensies) lezen door het **pad naar de container, map/BLOB te**configureren. Het **pad naar de container** verwijst naar de container en de **map/BLOB** wijst naar de map die de bestanden bevat, zoals wordt weer gegeven in de volgende afbeelding. Het sterretje, \* ) **geeft aan dat alle bestanden in de container/map (dat wil zeggen, data-aggregateddata/Year = 2014/maand-6/ \* )** als onderdeel van het experiment worden gelezen.

![Eigenschappen van Azure Blob](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Voorbeeld
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Pijp lijn met AzureMLBatchExecution-activiteit met para meters van webservice

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure Machine Learning studio model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

In het bovenstaande JSON-voor beeld:

* De geïmplementeerde Azure Machine Learning-webservice gebruikt een lezer en een schrijver-module voor het lezen/schrijven van gegevens van/naar een Azure SQL Database. Deze webservice bevat de volgende vier para meters: database server naam, database naam, Server gebruikers accountnaam en wacht woord voor Server gebruikers account.
* De **begin** -en **eind** datum moeten de [ISO-indeling](https://en.wikipedia.org/wiki/ISO_8601)hebben. Bijvoorbeeld: 2014-10-14T16:32:41Z. De **eind** tijd is optioneel. Als u geen waarde opgeeft voor de eigenschap **End** , wordt deze berekend als '**Start + 48 uur '.** Als u de pijplijn voor onbepaalde tijd wilt uitvoeren, geeft u **9999-09-09** op als waarde voor de eigenschap **end**. Zie de [naslaginformatie voor JSON-scriptverwerking](https://msdn.microsoft.com/library/dn835050.aspx) voor meer informatie over de JSON-eigenschappen.

### <a name="other-scenarios"></a>Andere scenario's
#### <a name="web-service-requires-multiple-inputs"></a>Voor de webservice zijn meerdere invoer gegevens vereist
Als de webservice meerdere invoer heeft, gebruikt u de eigenschap **webServiceInputs** in plaats van **webserviceinputactivity**te gebruiken. Gegevens sets waarnaar wordt verwezen door de **webServiceInputs** moeten ook worden opgenomen in de activiteiten **invoer**.

In uw Azure Machine Learning Studio-experiment hebben de invoer-en uitvoer poorten voor de web-service en de globale para meters standaard namen ("input1", "input2") die u kunt aanpassen. De namen die u gebruikt voor de instellingen webServiceInputs, webServiceOutputs en globalParameters moeten exact overeenkomen met de namen in de experimenten. U kunt de nettolading van de voorbeeld aanvraag bekijken op de Help-pagina voor het uitvoeren van batch uitvoering voor uw Azure Machine Learning Studio-eind punt om de verwachte toewijzing te controleren.

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>Voor de webservice is geen invoer vereist
Azure Machine Learning Studio-webservices voor batch uitvoering kunnen worden gebruikt voor het uitvoeren van werk stromen, zoals R-of python-scripts, waarvoor geen invoer vereist is. Het experiment kan ook worden geconfigureerd met een lezers module die geen GlobalParameters beschikbaar maakt. In dat geval wordt de AzureMLBatchExecution-activiteit als volgt geconfigureerd:

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>Voor de webservice is geen invoer/uitvoer vereist
Voor de Azure Machine Learning Studio batch Execution-webservice is mogelijk geen webservice-uitvoer geconfigureerd. In dit voor beeld is er geen webservice-invoer of-uitvoer, en evenmin zijn er GlobalParameters geconfigureerd. Er is nog steeds een uitvoer geconfigureerd voor de activiteit zelf, maar deze wordt niet gegeven als een webServiceOutput.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>De webservice gebruikt lezers en schrijvers en de activiteit wordt alleen uitgevoerd als andere activiteiten zijn geslaagd
De modules Azure Machine Learning Studio Web Service Reader en Writer kunnen worden geconfigureerd om met of zonder GlobalParameters te worden uitgevoerd. Het is echter mogelijk dat u service aanroepen wilt insluiten in een pijp lijn die gebruikmaakt van gegevensset-afhankelijkheden om de service alleen aan te roepen wanneer een upstream-verwerking is voltooid. U kunt ook een andere actie activeren nadat de batch uitvoering is voltooid met behulp van deze methode. In dat geval kunt u de afhankelijkheden uitdrukken met behulp van activiteit invoer en uitvoer, zonder dat u ze een naam geeft als invoer of uitvoer van de webservice.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

De **Takeaways** zijn:

* Als uw proef eindpunt gebruikmaakt van een Webserviceinputactivity: deze wordt vertegenwoordigd door een BLOB-gegevensset en is opgenomen in de invoer van de activiteit en de eigenschap Webserviceinputactivity. Anders wordt de eigenschap Webserviceinputactivity wegge laten.
* Als uw proef eindpunt gebruikmaakt van webServiceOutput (s): ze worden vertegenwoordigd door BLOB-gegevens sets en worden opgenomen in de uitvoer van de activiteit en in de eigenschap webServiceOutputs. De uitvoer van de activiteit en webServiceOutputs worden toegewezen door de naam van elke uitvoer in het experiment. Anders wordt de eigenschap webServiceOutputs wegge laten.
* Als uw experiment eind punt globalParameter (s) weergeeft, worden deze weer gegeven in de eigenschap activity globalParameters als sleutel, waardeparen. Anders wordt de eigenschap globalParameters wegge laten. De sleutels zijn hoofdletter gevoelig. [Azure Data Factory functies](data-factory-functions-variables.md) kunnen worden gebruikt in de waarden.
* Er kunnen extra gegevens sets worden opgenomen in de eigenschappen van de activiteit en uitvoer, zonder ernaar te verwijzen in de activiteit typeProperties. Deze gegevens sets bepalen de uitvoering met behulp van segment afhankelijkheden, maar worden anders genegeerd door de AzureMLBatchExecution-activiteit.


## <a name="updating-models-using-update-resource-activity"></a>Modellen bijwerken met de activiteit resource bijwerken
Wanneer u klaar bent met het opnieuw trainen, werkt u de Score-webservice (voorspellende experiment weer gegeven als een webservice) bij met het nieuwe getrainde model met behulp van de **activiteit Azure machine learning Studio update resource**. Zie [modellen bijwerken met update resource activiteit](data-factory-azure-ml-update-resource-activity.md) artikel voor meer informatie.

### <a name="reader-and-writer-modules"></a>Reader-en Writer-modules
Een veelvoorkomend scenario voor het gebruik van web service-para meters is het gebruik van Azure SQL-lezers en schrijvers. De module lezer wordt gebruikt om gegevens te laden in een experiment vanuit Data Management Services buiten Azure Machine Learning Studio. De schrijver-module is het opslaan van gegevens van uw experimenten in Data Management Services buiten Azure Machine Learning Studio.

Zie de onderwerpen over [lezer](https://msdn.microsoft.com/library/azure/dn905997.aspx) en [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) in de MSDN-bibliotheek voor meer informatie over Azure Blob/Azure SQL Reader/Writer. In het voor beeld in de vorige sectie is de Azure Blob Reader en Azure Blob Writer gebruikt. In deze sectie wordt beschreven hoe u Azure SQL Reader en Azure SQL Writer gebruikt.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
**V:** Ik heb meerdere bestanden die worden gegenereerd door mijn big data pijp lijnen. Kan ik de AzureMLBatchExecution-activiteit gebruiken om aan alle bestanden te werken?

**A:** Ja. Zie de sectie **een lees module gebruiken om gegevens te lezen uit meerdere bestanden in de Azure Blob** voor meer informatie.

## <a name="azure-machine-learning-studio-batch-scoring-activity"></a>Azure Machine Learning Studio-batch Score activiteit
Als u de **AzureMLBatchScoring** -activiteit gebruikt om te integreren met Azure machine learning, raden we u aan de nieuwste **AzureMLBatchExecution** -activiteit te gebruiken.

De AzureMLBatchExecution-activiteit wordt geïntroduceerd in de release van de Azure SDK en Azure PowerShell van de augustus 2015.

Als u wilt door gaan met het gebruik van de AzureMLBatchScoring-activiteit, gaat u door met het lezen van deze sectie.

### <a name="azure-machine-learning-studio-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Azure Machine Learning Studio-batch Score activiteit met behulp van Azure Storage voor invoer/uitvoer

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Webservice-para meters
Als u waarden voor de para meters van de webservice wilt opgeven, voegt u een sectie **typeProperties** toe aan de sectie **AZUREMLBATCHSCORINGACTIVITY** in de JSON van de pijp lijn, zoals wordt weer gegeven in het volgende voor beeld:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
U kunt ook [Data Factory-functies](data-factory-functions-variables.md) gebruiken bij het door geven van waarden voor de para meters van de webservice, zoals wordt weer gegeven in het volgende voor beeld:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> De para meters van de webservice zijn hoofdletter gevoelig, dus zorg ervoor dat de namen die u opgeeft in de JSON van de activiteit overeenkomen met die van de webservice.
>
>

## <a name="see-also"></a>Zie ook
* [Azure blog post: aan de slag met Azure Data Factory en Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/
