---
title: Voorspellende gegevenspijplijnen maken met Azure Data Factory
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
ms.openlocfilehash: eba5df587d6bd6dda6083314cfb94836c6669393
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73683149"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Voorspellende pijplijnen maken met Azure Machine Learning en Azure Data Factory

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

## <a name="introduction"></a>Inleiding
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de datafabriekservice gebruikt, [raadpleegt](../transform-data-using-machine-learning.md)u gegevens transformeren met behulp van machine learning in Data Factory .


### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) stelt u in staat om voorspellende analyseoplossingen te bouwen, te testen en te implementeren. Vanuit het oogpunt van hoog niveau gebeurt dit in drie stappen:

1. **Maak een trainingsexperiment**. Dit doe je stap met de Azure Machine Learning-studio. Azure Machine Learning-studio is een collaboratieve visuele ontwikkelingsomgeving die u gebruikt om een voorspellend analysemodel te trainen en te testen met behulp van trainingsgegevens.
2. **Converteer het naar een voorspellend experiment.** Zodra uw model is getraind met bestaande gegevens en u klaar bent om het te gebruiken om nieuwe gegevens te scoren, bereidt u uw experiment voor op het scoren.
3. **Implementeer het als een webservice.** U uw scoreexperiment publiceren als een Azure-webservice. U gegevens naar uw model sturen via dit eindpunt van de webservice en resultaatvoorspellingen ontvangen voor het model.

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory is een cloudgebaseerde data-integratieservice die de **beweging** en **transformatie** van data orkestreert en automatiseert. U oplossingen voor gegevensintegratie maken met Azure Data Factory die gegevens uit verschillende gegevensopslag kunnen opnemen, de gegevens kunnen transformeren/verwerken en de resultaatgegevens kunnen publiceren naar de gegevensopslag.

Met Data Factory-service kunt u gegevenspijplijnen maken die gegevens verplaatsen en transformeren, en de pijplijnen vervolgens uitvoeren volgens een opgegeven schema (per uur, dagelijks, wekelijks enz.). U vindt uitgebreide visualisaties om de afkomst en afhankelijkheden tussen uw gegevenspijplijnen weer te geven en al uw gegevenspijplijnen te controleren vanuit één centrale weergave zodat u eenvoudig problemen kunt detecteren en bewakingswaarschuwingen kunt instellen.

Zie [Inleiding tot Azure Data Factory](data-factory-introduction.md) en Bouw uw eerste [pijplijnartikelen](data-factory-build-your-first-pipeline.md) om snel aan de slag te gaan met de Azure Data Factory-service.

### <a name="data-factory-and-machine-learning-together"></a>Data Factory en Machine Learning samen
Met Azure Data Factory u eenvoudig pijplijnen maken die gebruikmaken van een gepubliceerde [Azure Machine Learning-webservice][azure-machine-learning] voor voorspellende analyses. Met behulp van de **batchuitvoeringsactiviteit** in een Azure Data Factory-pijplijn u een webservice van Azure Machine Learning studio aanroepen om voorspellingen te doen over de gegevens in batch. Zie Een beroep doen op een webservice van Azure Machine Learning studio met de sectie Batch Execution Activity voor meer informatie.

Na verloop van tijd moeten de voorspellende modellen in de Azure Machine Learning-studio scoringexperimenten worden omgeschoold met behulp van nieuwe invoergegevenssets. U een Azure Machine Learning-studiomodel opnieuw trainen vanuit een Data Factory-pijplijn door de volgende stappen uit te voeren:

1. Publiceer het trainingsexperiment (geen voorspellend experiment) als een webservice. U doet deze stap in de Azure Machine Learning-studio zoals u deed om voorspellend experiment als webservice in het vorige scenario bloot te leggen.
2. Gebruik de batchuitvoeringsactiviteit van azure machine learning-studio om de webservice voor het trainingsexperiment aan te roepen. In principe u de batchuitvoeringsactiviteit van de Azure Machine Learning-studio gebruiken om zowel de webservice voor training als de webservice voor het scoren aan te roepen.

Nadat u klaar bent met omscholing, werkt u de scoringswebservice (voorspellend experiment dat als webservice wordt weergegeven) bij met het nieuw getrainde model met behulp van de **Azure Machine Learning-studio Update Resource Activity**. Zie Modellen bijwerken met het artikel [Resourceactiviteit bijwerken](data-factory-azure-ml-update-resource-activity.md) voor meer informatie.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Een webservice aanroepen met batchuitvoeringsactiviteit
U gebruikt Azure Data Factory om gegevensverplaatsing en -verwerking te orkestreren en vervolgens batchuitvoering uit te voeren met Azure Machine Learning. Hier zijn de stappen op het hoogste niveau:

1. Maak een gekoppelde Azure Machine Learning-service. U hebt de volgende waarden nodig:

   1. **Vraag URI** aan voor de API batchuitvoering. U de URI aanvragen vinden door op de koppeling **BATCH-UITVOERING** op de webpagina te klikken.
   2. **API-sleutel** voor de gepubliceerde Azure Machine Learning-webservice. U de API-sleutel vinden door op de webservice te klikken die u hebt gepubliceerd.
   3. Gebruik de activiteit **AzureMLBatchExecution.**

      ![Machine Learning-dashboard](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Batch URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenario: Experimenten met webservice-ingangen/uitvoer die verwijzen naar gegevens in Azure Blob Storage
In dit scenario voert de Webservice Azure Machine Learning voorspellingen uit met behulp van gegevens uit een bestand in een Azure blob-opslag en slaat de voorspellingsresultaten op in de blob-opslag. De volgende JSON definieert een Data Factory-pijplijn met een AzureMLBatchExecution-activiteit. De activiteit heeft de gegevensset **DecisionTreeInputBlob** als invoer en **DecisionTreeResultBlob** als uitvoer. De **DecisionTreeInputBlob** wordt doorgegeven als invoer voor de webservice met behulp van de eigenschap **webServiceInput** JSON. De **DecisionTreeResultBlob** wordt doorgegeven als een uitvoer naar de webservice met behulp van de eigenschap JSON van **webServiceOutputs.**

> [!IMPORTANT]
> Als de webservice meerdere ingangen gebruikt, gebruikt u de eigenschap **webServiceInputs** in plaats van **webServiceInput**te gebruiken. Zie de [webservice vereist meerdere invoersectie](#web-service-requires-multiple-inputs) voor een voorbeeld van het gebruik van de eigenschap webServiceInputs.
>
> Gegevenssets waarnaar wordt verwezen door de eigenschappen **webServiceInput**/**webServiceInputs** en **webServiceOutputs** (in **typeProperties)** moeten ook worden opgenomen in de **activiteitsinvoer** en **-uitvoer**.
>
> In uw Azure Machine Learning-studio-experiment hebben invoer- en uitvoerpoorten van webservice en globale parameters standaardnamen ('input1', 'input2') die u aanpassen. De namen die u gebruikt voor webServiceInputs, webServiceOutputs en globalParameters-instellingen moeten exact overeenkomen met de namen in de experimenten. U de payload van de voorbeeldaanvraag bekijken op de help-pagina batchuitvoering voor het eindpunt van uw Azure Machine Learning-studio om de verwachte toewijzing te verifiëren.
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
> Alleen ingangen en uitvoer van de AzureMLBatchExecution-activiteit kunnen als parameters worden doorgegeven aan de webservice. In het bovenstaande JSON-fragment is DecisionTreeInputBlob bijvoorbeeld een invoer voor de AzureMLBatchExecution-activiteit, die wordt doorgegeven als invoer naar de webservice via de parameter webServiceInput.
>
>

### <a name="example"></a>Voorbeeld
In dit voorbeeld wordt Azure Storage gebruikt om zowel de invoer- als uitvoergegevens vast te houden.

We raden je aan om [je eerste pijplijn te doorlopen met de][adf-build-1st-pipeline] zelfstudie Data Factory voordat je dit voorbeeld doorloopt. Gebruik de Data Factory Editor om Data Factory-artefacten (gekoppelde services, gegevenssets, pijplijn) in dit voorbeeld te maken.

1. Maak een **gekoppelde service** voor uw **Azure Storage.** Als de invoer- en uitvoerbestanden zich in verschillende opslagaccounts bevinden, hebt u twee gekoppelde services nodig. Hier is een JSON voorbeeld:

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
2. Maak de **gegevensset**Azure Data Factory voor **invoer** . In tegenstelling tot sommige andere gegevenssets van Data Factory moeten deze gegevenssets zowel **mapPath-** als **bestandsnaamwaarden** bevatten. U partitionering gebruiken om ervoor te zorgen dat elke batch-uitvoering (elk gegevenssegment) unieke invoer- en uitvoerbestanden verwerkt of produceert. Mogelijk moet u een aantal upstream-activiteit opnemen om de invoer om te zetten in de CSV-bestandsindeling en deze in het opslagaccount voor elk segment te plaatsen. In dat geval neemt u de **externe** en **externeGegevensinstellingen** in het volgende voorbeeld niet op en is uw DecisionTreeInputBlob de uitvoergegevensset van een andere activiteit.

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

    Het csv-invoerbestand moet de kolomkoprij hebben. Als u de **kopieeractiviteit** gebruikt om de csv naar de blobopslag te maken/verplaatsen, moet u de **sink-eigenschap blobWriterAddHeader** op **true**instellen. Bijvoorbeeld:

    ```JSON
    sink:
    {
        "type": "BlobSink",
        "blobWriterAddHeader": true
    }
    ```

    Als het csv-bestand de koptekstrij niet heeft, ziet u mogelijk de volgende fout: **Fout in activiteit: leestekenreeks fout. Onverwacht token: StartObject. Pad '', lijn 1, positie 1**.
3. De **gegevensset uitvoer** van Azure Data Factory **maken**. In dit voorbeeld wordt partitionering gebruikt om een uniek uitvoerpad te maken voor elke segmentuitvoering. Zonder de partitionering zou de activiteit het bestand overschrijven.

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
4. Maak een **gekoppelde service** van het type: **AzureMLLinkedService**, die de API-sleutel en modelbatchuitvoering URL.

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
5. Maak ten slotte een pijplijn met een **AzureMLBatchExecution** Activity. Bij runtime voert pipeline de volgende stappen uit:

   1. Haalt de locatie van het invoerbestand uit uw invoergegevenssets.
   2. Roept de AZURE Machine Learning batch execution API in
   3. Hiermee kopieert u de uitvoer van batchuitvoering naar de blob in de uitvoergegevensset.

      > [!NOTE]
      > AzureMLBatchExecution-activiteit kan nul of meer ingangen en een of meer uitvoerhebben.
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

      Zowel **begin-** als **einddatummoeten** in [ISO-formaat](https://en.wikipedia.org/wiki/ISO_8601)zijn. Bijvoorbeeld: 2014-10-14T16:32:41Z. De **eindtijd** is optioneel. Als u geen waarde opgeeft voor de **eigenschap end,** wordt deze berekend als "**start + 48 uur."** Als u de pijplijn voor onbepaalde tijd wilt uitvoeren, geeft u **9999-09-09** op als waarde voor de eigenschap **end**. Zie de [naslaginformatie voor JSON-scriptverwerking](https://msdn.microsoft.com/library/dn835050.aspx) voor meer informatie over de JSON-eigenschappen.

      > [!NOTE]
      > Het opgeven van invoer voor de AzureMLBatchExecution-activiteit is optioneel.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenario: Experimenten met reader/writer modules om te verwijzen naar gegevens in verschillende opslagen
Een ander veelvoorkomend scenario bij het maken van Azure Machine Learning-studio-experimenten is het gebruik van Reader- en Writer-modules. De lezermodule wordt gebruikt om gegevens in een experiment te laden en de schrijvermodule moet gegevens van uw experimenten opslaan. Zie [Lees-](https://msdn.microsoft.com/library/azure/dn905997.aspx) en [schrijversmodules](https://msdn.microsoft.com/library/azure/dn905984.aspx) voor meer informatie over lees- en schrijvermodules over de MSDN-bibliotheek.

Bij het gebruik van de lezer en schrijver modules, is het een goede gewoonte om een web service parameter te gebruiken voor elke eigenschap van deze lezer / schrijver modules. Met deze webparameters u de waarden tijdens runtime configureren. U bijvoorbeeld een experiment maken met een lezermodule die een Azure SQL Database gebruikt: XXX.database.windows.net. Nadat de webservice is geïmplementeerd, wilt u de consumenten van de webservice inschakelen om een andere Azure SQL Server op te geven, YYY.database.windows.net. U een parameter Webservice gebruiken om deze waarde te configureren.

> [!NOTE]
> De invoer en uitvoer van webserviceverschillen van de parameters van webservice. In het eerste scenario hebt u gezien hoe een invoer en uitvoer kunnen worden opgegeven voor een Webservice azure Machine Learning studio. In dit scenario geeft u parameters door voor een webservice die overeenkomen met eigenschappen van lees-/schrijvermodules.
>
>

Laten we eens kijken naar een scenario voor het gebruik van webserviceparameters. U hebt een geïmplementeerde Azure Machine Learning-webservice die een leesmodule gebruikt om gegevens te lezen uit een van de gegevensbronnen die worden ondersteund door Azure Machine Learning (bijvoorbeeld: Azure SQL Database). Nadat de batch-uitvoering is uitgevoerd, worden de resultaten geschreven met behulp van een Writer-module (Azure SQL Database).  Er worden geen webservice-ingangen en uitvoer gedefinieerd in de experimenten. In dit geval raden we u aan relevante webserviceparameters voor de lezer- en schrijvermodules te configureren. Met deze configuratie kunnen de lees-/writer-modules worden geconfigureerd wanneer u de AzureMLBatchExecution-activiteit gebruikt. U geeft als volgt webserviceparameters op in de sectie **globaal Parameters** in de activiteit JSON.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

U [gegevensfabrieksfuncties](data-factory-functions-variables.md) ook gebruiken bij het doorgeven van waarden voor de parameters van de webservice, zoals in het volgende voorbeeld wordt weergegeven:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> De parameters van de webservice zijn hoofdlettergevoelig, dus zorg ervoor dat de namen die u opgeeft in de activiteit JSON overeenkomen met de namen die door de webservice worden weergegeven.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Een Reader-module gebruiken om gegevens uit meerdere bestanden in Azure Blob te lezen
Big data-pijplijnen met activiteiten zoals Pig en Hive kunnen een of meer uitvoerbestanden produceren zonder extensies. Wanneer u bijvoorbeeld een externe Hive-tabel opgeeft, kunnen de gegevens voor de externe Hive-tabel worden opgeslagen in Azure blob-opslag met de volgende naam 000000_0. U de lezermodule in een experiment gebruiken om meerdere bestanden te lezen en deze te gebruiken voor voorspellingen.

Wanneer u de lezermodule gebruikt in een Azure Machine Learning-experiment, u Azure Blob opgeven als invoer. De bestanden in de Azure blob-opslag kunnen de uitvoerbestanden zijn (voorbeeld: 000000_0) die worden geproduceerd door een Pig and Hive-script dat op HDInsight wordt uitgevoerd. Met de lezermodule u bestanden lezen (zonder extensies) door het **pad naar de container, de map/blob te**configureren. De **containerpad** wijst naar de container en **map/blob** wijst naar map die de bestanden bevat zoals weergegeven in de volgende afbeelding. Het sterretje dat is, \*) geeft aan dat alle bestanden in de **container/map (dat wil zeggen gegevens/aggregateddata/year=2014/month-6/\*)** worden gelezen als onderdeel van het experiment.

![Azure Blob-eigenschappen](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Voorbeeld
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Pijplijn met AzureMLBatchExecution-activiteit met WebService-parameters

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

In het bovenstaande JSON-voorbeeld:

* De geïmplementeerde Azure Machine Learning Web-service gebruikt een lezer en een writer-module om gegevens te lezen/schrijven van/naar een Azure SQL-database. Deze webservice legt de volgende vier parameters bloot: databaseservernaam, databasenaam, servergebruikersnaam en wachtwoord voor servergebruikersaccount.
* Zowel **begin-** als **einddatummoeten** in [ISO-formaat](https://en.wikipedia.org/wiki/ISO_8601)zijn. Bijvoorbeeld: 2014-10-14T16:32:41Z. De **eindtijd** is optioneel. Als u geen waarde opgeeft voor de **eigenschap end,** wordt deze berekend als "**start + 48 uur."** Als u de pijplijn voor onbepaalde tijd wilt uitvoeren, geeft u **9999-09-09** op als waarde voor de eigenschap **end**. Zie de [naslaginformatie voor JSON-scriptverwerking](https://msdn.microsoft.com/library/dn835050.aspx) voor meer informatie over de JSON-eigenschappen.

### <a name="other-scenarios"></a>Andere scenario's
#### <a name="web-service-requires-multiple-inputs"></a>Voor webservice zijn meerdere ingangen vereist
Als de webservice meerdere ingangen gebruikt, gebruikt u de eigenschap **webServiceInputs** in plaats van **webServiceInput**te gebruiken. Gegevenssets waarnaar wordt verwezen door de **webServiceInputs** moeten ook worden opgenomen in de **activiteitsinvoer**.

In uw Azure Machine Learning-studio-experiment hebben invoer- en uitvoerpoorten van webservice en globale parameters standaardnamen ('input1', 'input2') die u aanpassen. De namen die u gebruikt voor webServiceInputs, webServiceOutputs en globalParameters-instellingen moeten exact overeenkomen met de namen in de experimenten. U de payload van de voorbeeldaanvraag bekijken op de help-pagina batchuitvoering voor het eindpunt van uw Azure Machine Learning-studio om de verwachte toewijzing te verifiëren.

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

#### <a name="web-service-does-not-require-an-input"></a>Voor Web Service is geen invoer vereist
Azure Machine Learning studio batch execution web services kunnen worden gebruikt om werkstromen uit te voeren, bijvoorbeeld R of Python scripts, waarvoor mogelijk geen ingangen nodig zijn. Of het experiment kan worden geconfigureerd met een Reader-module die geen GlobalParameters blootlegt. In dat geval wordt de AzureMLBatchExecution-activiteit als volgt geconfigureerd:

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

#### <a name="web-service-does-not-require-an-inputoutput"></a>Webservice vereist geen invoer/uitvoer
De webservice azure Machine Learning studio batch execution heeft mogelijk geen webservice-uitvoer geconfigureerd. In dit voorbeeld is er geen webservice-invoer of -uitvoer en zijn er ook geen GlobalParameters geconfigureerd. Er is nog steeds een uitvoer geconfigureerd op de activiteit zelf, maar deze wordt niet gegeven als een webServiceOutput.

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

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Web Service maakt gebruik van lezers en schrijvers, en de activiteit wordt alleen uitgevoerd wanneer andere activiteiten zijn geslaagd
De webservicelezer en schrijvermodules van Azure Machine Learning studio kunnen zijn geconfigureerd om met of zonder GlobalParameters uit te voeren. U echter serviceaanroepen insluiten in een pijplijn die afhankelijkheden van gegevenssets gebruikt om de service alleen aan te roepen wanneer sommige upstream-verwerking is voltooid. U ook een andere actie activeren nadat de batch-uitvoering is voltooid met behulp van deze aanpak. In dat geval u de afhankelijkheden uitdrukken met behulp van activiteitsinvoer en -uitgangen, zonder ze te benoemen als webservice-invoer of -uitvoer.

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

De **afhaalmaaltijden** zijn:

* Als uw experimenteindpunt een webServiceInput gebruikt: het wordt vertegenwoordigd door een blobgegevensset en wordt opgenomen in de activiteitsinvoer en de eigenschap webServiceInput. Anders wordt de eigenschap webServiceInput weggelaten.
* Als uw experimenteindpunt webServiceoutput(s) gebruikt: ze worden vertegenwoordigd door blobgegevenssets en worden opgenomen in de activiteitsuitvoer en in de eigenschap webServiceOutputs. De activiteitsuitvoer en webServiceOutputs worden toegewezen aan de naam van elke uitvoer in het experiment. Anders wordt de eigenschap webServiceOutputs weggelaten.
* Als uw experimenteindpunt globalParameter(s) blootlegt, worden deze weergegeven in de eigenschap activity globalParameters als sleutel, waardeparen. Anders wordt de eigenschap globalParameters weggelaten. De sleutels zijn hoofdlettergevoelig. [Azure Data Factory-functies](data-factory-functions-variables.md) kunnen worden gebruikt in de waarden.
* Aanvullende gegevenssets kunnen worden opgenomen in de eigenschappen activiteitsinvoer en uitvoer, zonder dat er naar wordt verwezen in activiteitstypeEigenschappen. Deze gegevenssets zijn van toepassing op de uitvoering met behulp van segmentafhankelijkheden, maar worden verder genegeerd door de AzureMLBatchExecution Activity.


## <a name="updating-models-using-update-resource-activity"></a>Modellen bijwerken met Resourceactiviteit bijwerken
Nadat u klaar bent met omscholing, werkt u de scoringswebservice (voorspellend experiment dat als webservice wordt weergegeven) bij met het nieuw getrainde model met behulp van de **Azure Machine Learning-studio Update Resource Activity**. Zie Modellen bijwerken met het artikel [Resourceactiviteit bijwerken](data-factory-azure-ml-update-resource-activity.md) voor meer informatie.

### <a name="reader-and-writer-modules"></a>Lees- en schrijvermodules
Een veelvoorkomend scenario voor het gebruik van webserviceparameters is het gebruik van Azure SQL-lezers en -schrijvers. De lezermodule wordt gebruikt om gegevens te laden in een experiment van gegevensbeheerservices buiten Azure Machine Learning Studio. De writer module is om gegevens uit uw experimenten op te slaan in data management services buiten Azure Machine Learning Studio.

Zie [Lees-](https://msdn.microsoft.com/library/azure/dn905997.aspx) en schrijver voor meer informatie over Azure Blob/Azure SQL-lezer/schrijver voor lees- en [schrijver-onderwerpen](https://msdn.microsoft.com/library/azure/dn905984.aspx) over DE MSDN-bibliotheek. In het voorbeeld in de vorige sectie werd de Azure Blob-lezer en de Azure Blob-schrijver gebruikt. In deze sectie wordt gesproken over het gebruik van Azure SQL reader en Azure SQL writer.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
**V:** Ik heb meerdere bestanden die worden gegenereerd door mijn big data pipelines. Kan ik de AzureMLBatchExecution Activity gebruiken om aan alle bestanden te werken?

**A:** Ja. Zie de **module Een lezer gebruiken om gegevens uit meerdere bestanden in de** sectie Azure Blob te lezen voor meer informatie.

## <a name="azure-machine-learning-studio-batch-scoring-activity"></a>Azure Machine Learning-studio Batchscoringactiviteit
Als u de **AzureMLBatchScoring-activiteit** gebruikt om te integreren met Azure Machine Learning, raden we u aan de nieuwste **AzureMLBatchExecution-activiteit** te gebruiken.

De AzureMLBatchExecution-activiteit wordt geïntroduceerd in de release van Azure SDK en Azure PowerShell in augustus 2015.

Als u de AzureMLBatchScoring-activiteit wilt blijven gebruiken, leest u verder in deze sectie.

### <a name="azure-machine-learning-studio-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Azure Machine Learning-studio Batchscoringsactiviteit met Azure Storage voor invoer/uitvoer

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

### <a name="web-service-parameters"></a>Webserviceparameters
Als u waarden voor webserviceparameters wilt opgeven, voegt u een sectie **typeEigenschappen** toe aan de sectie **AzureMLBatchScoringActivity** in de pijplijnJSON zoals weergegeven in het volgende voorbeeld:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
U [gegevensfabrieksfuncties](data-factory-functions-variables.md) ook gebruiken bij het doorgeven van waarden voor de parameters van de webservice, zoals in het volgende voorbeeld wordt weergegeven:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> De parameters van de webservice zijn hoofdlettergevoelig, dus zorg ervoor dat de namen die u opgeeft in de activiteit JSON overeenkomen met de namen die door de webservice worden weergegeven.
>
>

## <a name="see-also"></a>Zie ook
* [Azure-blogbericht: Aan de slag met Azure Data Factory en Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/
