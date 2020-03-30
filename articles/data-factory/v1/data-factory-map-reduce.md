---
title: MapReduce-programma aanroepen vanuit Azure Data Factory
description: Meer informatie over het verwerken van gegevens door mapte uitvoerenVerlaag programma's op een Azure HDInsight-cluster vanuit een Azure-gegevensfabriek.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 598a16d25ba375b984a966cba190181edbda3d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703158"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>MapReduce-programma's van gegevensfabriek aanroepen
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
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de datafabriekservice gebruikt, raadpleegt u [gegevens transformeren met MapReduce-activiteit in Gegevensfabriek](../transform-data-using-hadoop-map-reduce.md).


De HDInsight MapReduce activiteit in een Data [Factory-pijplijn](data-factory-create-pipelines.md) voert MapReduce-programma's uit op [uw eigen](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) of [on-demand](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-gebaseerde HDInsight-cluster. Dit artikel bouwt voort op het artikel [over gegevenstransformatieactiviteiten,](data-factory-data-transformation-activities.md) dat een algemeen overzicht geeft van gegevenstransformatie en de ondersteunde transformatieactiviteiten.

> [!NOTE] 
> Als u nieuw bent in Azure Data Factory, leest u [Inleiding tot Azure Data Factory](data-factory-introduction.md) en doet u de zelfstudie: Bouw uw eerste [gegevenspijplijn](data-factory-build-your-first-pipeline.md) voordat u dit artikel leest.  

## <a name="introduction"></a>Inleiding
Een pijplijn in een Azure-gegevensfabriek verwerkt gegevens in gekoppelde opslagservices met behulp van gekoppelde compute-services. Het bevat een reeks activiteiten waarbij elke activiteit een specifieke verwerkingsbewerking uitvoert. In dit artikel wordt beschreven met behulp van de HDInsight MapReduce Activiteit.

Zie [Pig](data-factory-pig-activity.md) en [Hive](data-factory-hive-activity.md) voor meer informatie over het uitvoeren van Pig/Hive-scripts op een HDInsight-cluster op basis van Windows/Linux vanuit een pijplijn met HDInsight Pig- en Hive-activiteiten. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON voor HDInsight MapReduce Activiteit
In de JSON-definitie voor de HDInsight-activiteit: 

1. Stel het **type** **activiteit** in op **HDInsight**.
2. Geef de naam op van de klasse voor de eigenschap **className.**
3. Geef het pad op naar het JAR-bestand, inclusief de bestandsnaam voor de eigenschap **jarFilePath.**
4. Geef de gekoppelde service op die verwijst naar de Azure Blob Storage die het JAR-bestand voor de eigenschap **JarLinkedService** bevat.   
5. Geef argumenten op voor het programma MapReduce in de sectie **argumenten.** Bij runtime ziet u een paar extra argumenten (bijvoorbeeld: mapreduce.job.tags) uit het MapReduce-framework. Als u uw argumenten wilt onderscheiden met de argumenten MapReduce, u overwegen zowel de optie als de waarde te gebruiken als argumenten zoals weergegeven in het volgende voorbeeld (-s, --invoer, --uitvoer enz., zijn opties die onmiddellijk worden gevolgd door hun waarden).

    ```JSON   
    {
        "name": "MahoutMapReduceSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "-s",
                            "SIMILARITY_LOGLIKELIHOOD",
                            "--input",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                            "--output",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                            "--maxSimilaritiesPerItem",
                            "500",
                            "--tempDir",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                        ]
                    },
                    "inputs": [
                        {
                            "name": "MahoutInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "MahoutOutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MahoutActivity",
                    "description": "Custom Map Reduce to generate Mahout result",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-01-03T00:00:00Z",
            "end": "2017-01-04T00:00:00Z"
        }
    }
    ```
   U de HDInsight MapReduce Activity gebruiken om elk MapReduce-jarbestand op een HDInsight-cluster uit te voeren. In de volgende VOORBEELD-JSON-definitie van een pijplijn is de HDInsight-activiteit geconfigureerd om een Mahout JAR-bestand uit te voeren.

## <a name="sample-on-github"></a>Voorbeeld op GitHub
U een voorbeeld downloaden voor het gebruik van de HDInsight MapReduce Activity from: [Data Factory Samples on GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Het Word Count-programma uitvoeren
In de pijplijn in dit voorbeeld wordt het Word Count Map/Reduce-programma uitgevoerd op uw Azure HDInsight-cluster.   

### <a name="linked-services"></a>Gekoppelde services
Eerst maakt u een gekoppelde service om de Azure Storage die wordt gebruikt door het Azure HDInsight-cluster te koppelen aan de Azure-gegevensfabriek. Als u de volgende code kopieert/plakt, vergeet dan niet de **accountnaam** en **accountsleutel** te vervangen door de naam en sleutel van uw Azure Storage. 

#### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Gekoppelde Azure HDInsight-service
Vervolgens maakt u een gekoppelde service om uw Azure HDInsight-cluster te koppelen aan de Azure-gegevensfabriek. Als u de volgende code kopieert/plakt, vervangt u **de clusternaam HDInsight** door de naam van uw HDInsight-cluster en wijzigt u de gebruikersnaam en wachtwoordwaarden.   

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>Gegevenssets
#### <a name="output-dataset"></a>Uitvoergegevensset
De pijplijn in dit voorbeeld neemt geen ingangen. U geeft een uitvoergegevensset op voor de ACTIVITEIT HDInsight MapReduce. Deze gegevensset is slechts een dummy-gegevensset die nodig is om het pijplijnschema te besturen.  

```JSON
{
    "name": "MROutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "WordCountOutput1.txt",
            "folderPath": "example/data/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Pijplijn
De pijplijn in dit voorbeeld heeft slechts één activiteit die van type is: HDInsightMapReduce. Enkele van de belangrijke eigenschappen in de JSON zijn: 

| Eigenschap | Opmerkingen |
|:--- |:--- |
| type |Het type moet zijn ingesteld op **HDInsightMapReduce**. |
| Classname |Naam van de klasse is: **wordcount** |
| jarFilePath |Ga naar het potbestand met de klasse. Als u de volgende code kopieert/plakt, vergeet dan niet de naam van het cluster te wijzigen. |
| jarLinkedService |Gekoppelde Azure Storage-service die het jar-bestand bevat. Deze gekoppelde service verwijst naar de opslag die is gekoppeld aan het HDInsight-cluster. |
| Argumenten |Het wordcount-programma heeft twee argumenten nodig, een invoer en een uitvoer. Het invoerbestand is het davinci.txt-bestand. |
| frequency/interval |De waarden voor deze eigenschappen komen overeen met de uitvoergegevensset. |
| linkedServiceName |verwijst naar de HDInsight gekoppelde service die u eerder had gemaakt. |

```JSON
{
    "name": "MRSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run the Word Count Program",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "wordcount",
                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": [
                        "/example/data/gutenberg/davinci.txt",
                        "/example/data/WordCountOutput1"
                    ]
                },
                "outputs": [
                    {
                        "name": "MROutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "MRActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-03T00:00:00Z",
        "end": "2014-01-04T00:00:00Z"
    }
}
```

## <a name="run-spark-programs"></a>Spark-programma's uitvoeren
U kunt de MapReduce-activiteit gebruiken om Spark-programma's uit te voeren op uw HDInsight Spark-cluster. Zie [Spark-programma's aanroepen vanuit Azure Data Factory](data-factory-spark.md) voor meer informatie.  

[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: https://portal.azure.com

## <a name="see-also"></a>Zie ook
* [Hive Activiteit](data-factory-hive-activity.md)
* [Varkensactiviteit](data-factory-pig-activity.md)
* [Hadoop streaming activiteit](data-factory-hadoop-streaming-activity.md)
* [Spark-programma's aanroepen](data-factory-spark.md)
* [R-scripts aanroepen](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

