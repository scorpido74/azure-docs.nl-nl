---
title: Gegevens transformeren met Hadoop-streamingactiviteit - Azure
description: Ontdek hoe u de Hadoop-streamingactiviteit in een Azure-gegevensfabriek gebruiken om gegevens te transformeren door Hadoop-streamingprogramma's uit te voeren op een on-demand/uw eigen HDInsight-cluster.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: a7f07365da699a40f5b51917104a68a62affa3d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703373"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Gegevens transformeren met Hadoop-streamingactiviteit in Azure Data Factory
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
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [gegevens transformeren met Hadoop-streamingactiviteit in Data Factory](../transform-data-using-hadoop-streaming.md).


U de HDInsightStreamingActivity Activity een Hadoop Streaming-taak aanroepen vanuit een Azure Data Factory-pijplijn. In het volgende JSON-fragment wordt de syntaxis weergegeven voor het gebruik van het JSON-bestand HDInsightStreaming in een json-bestand van een pijplijn. 

De HDInsight Streaming Activity in een Data [Factory-pijplijn](data-factory-create-pipelines.md) voert Hadoop Streaming-programma's uit op [uw eigen](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) of [on-demand](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-gebaseerde HDInsight-cluster. Dit artikel bouwt voort op het artikel [over gegevenstransformatieactiviteiten,](data-factory-data-transformation-activities.md) dat een algemeen overzicht geeft van gegevenstransformatie en de ondersteunde transformatieactiviteiten.

> [!NOTE] 
> Als u nieuw bent in Azure Data Factory, leest u [Inleiding tot Azure Data Factory](data-factory-introduction.md) en doet u de zelfstudie: Bouw uw eerste [gegevenspijplijn](data-factory-build-your-first-pipeline.md) voordat u dit artikel leest. 

## <a name="json-sample"></a>JSON-monster
Het CLUSTER HDInsight wordt automatisch gevuld met voorbeeldprogramma's (wc.exe en cat.exe) en gegevens (davinci.txt). Standaard is de naam van de container die wordt gebruikt door het HDInsight-cluster de naam van het cluster zelf. Als uw clusternaam bijvoorbeeld myhdicluster is, is de naam van de gekoppelde blobcontainer myhdicluster. 

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<nameofthecluster>/example/apps/wc.exe",
                        "<nameofthecluster>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "AzureStorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00Z",
        "end": "2014-01-05T00:00:00Z"
    }
}
```

Houd rekening met de volgende punten:

1. Stel de **linkedServiceName** in op de naam van de gekoppelde service die verwijst naar uw HDInsight-cluster waarop de taak streamingmapreduce wordt uitgevoerd.
2. Stel het type activiteit in op **HDInsightStreaming**.
3. Geef voor de eigenschap **mapper** de naam van mapper uitvoerbaar op. In het voorbeeld is cat.exe de mapper uitvoerbaar.
4. Geef voor de eigenschap **reducer** de naam op van reducer uitvoerbaar. In het voorbeeld is wc.exe de reducer uitvoerbaar.
5. Geef voor de eigenschap **invoertype** het invoerbestand (inclusief de locatie) voor de mapper op. In het `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt`voorbeeld: : adfsample is de blobcontainer, voorbeeld/gegevens/Gutenberg is de map en davinci.txt is de blob.
6. Geef voor de eigenschap **uitvoertype** het uitvoerbestand (inclusief de locatie) voor de reducer op. De uitvoer van de taak Hadoop Streaming wordt geschreven naar de locatie die voor deze eigenschap is opgegeven.
7. Geef in de sectie **filePaths** de paden op voor de mapper en reducer executables. In het voorbeeld: "adfsample/example/apps/wc.exe", adfsample is de blobcontainer, voorbeeld/apps is de map en wc.exe is de uitvoerbare.
8. Geef voor de eigenschap **fileLinkedService** de gekoppelde Azure Storage-service op die de Azure-opslag vertegenwoordigt die de bestanden bevat die zijn opgegeven in de sectie filePaths.
9. Geef voor de eigenschap **arguments** de argumenten op voor de streamingtaak.
10. De eigenschap **getDebugInfo** is een optioneel element. Wanneer deze is ingesteld op Mislukken, worden de logboeken alleen gedownload bij een storing. Wanneer het is ingesteld op Altijd, worden logboeken altijd gedownload, ongeacht de uitvoeringsstatus.

> [!NOTE]
> Zoals in het voorbeeld wordt weergegeven, geeft u een uitvoergegevensset op voor de hadoopstreamingactiviteit voor de eigenschap **uitvoer.** Deze gegevensset is slechts een dummy-gegevensset die nodig is om het pijplijnschema te besturen. U hoeft geen invoergegevensset op te geven voor de activiteit voor de eigenschap **inputs.**  
> 
> 

## <a name="example"></a>Voorbeeld
De pijplijn in deze walkthrough voert het Word Count-streamingmap/Reduce-programma uit op uw Azure HDInsight-cluster. 

### <a name="linked-services"></a>Gekoppelde services
#### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service
Eerst maakt u een gekoppelde service om de Azure Storage die wordt gebruikt door het Azure HDInsight-cluster te koppelen aan de Azure-gegevensfabriek. Als u de volgende code kopieert/plakt, vergeet dan niet de accountnaam en accountsleutel te vervangen door de naam en sleutel van uw Azure Storage. 

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
Vervolgens maakt u een gekoppelde service om uw Azure HDInsight-cluster te koppelen aan de Azure-gegevensfabriek. Als u de volgende code kopieert/plakt, vervangt u de clusternaam HDInsight door de naam van uw HDInsight-cluster en wijzigt u de gebruikersnaam en wachtwoordwaarden. 

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
De pijplijn in dit voorbeeld neemt geen ingangen. U geeft een uitvoergegevensset op voor de HDInsight-streamingactiviteit. Deze gegevensset is slechts een dummy-gegevensset die nodig is om het pijplijnschema te besturen. 

```JSON
{
    "name": "StreamingOutputDataset",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "adftutorial/streamingdata/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Pijplijn
De pijplijn in dit voorbeeld heeft slechts één activiteit die van type is: **HDInsightStreaming**. 

Het CLUSTER HDInsight wordt automatisch gevuld met voorbeeldprogramma's (wc.exe en cat.exe) en gegevens (davinci.txt). Standaard is de naam van de container die wordt gebruikt door het HDInsight-cluster de naam van het cluster zelf. Als uw clusternaam bijvoorbeeld myhdicluster is, is de naam van de gekoppelde blobcontainer myhdicluster.  

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<blobcontainer>/example/apps/wc.exe",
                        "<blobcontainer>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "StorageLinkedService"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00Z",
        "end": "2017-01-04T00:00:00Z"
    }
}
```
## <a name="see-also"></a>Zie ook
* [Hive Activiteit](data-factory-hive-activity.md)
* [Varkensactiviteit](data-factory-pig-activity.md)
* [Activiteit kaart verminderen](data-factory-map-reduce.md)
* [Spark-programma's aanroepen](data-factory-spark.md)
* [R-scripts aanroepen](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

