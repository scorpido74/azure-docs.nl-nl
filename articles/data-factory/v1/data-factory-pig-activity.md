---
title: Gegevens transformeren met varkensactiviteit in Azure Data Factory
description: Ontdek hoe u de varkensactiviteit in een Azure-gegevensfabriek gebruiken om Pig-scripts uit te voeren op een on-demand/uw eigen HDInsight-cluster.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 5b8e7201a6239ef1fe83fb89d4b361995e305bbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703197"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Gegevens transformeren met varkensactiviteit in Azure Data Factory
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
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de datafabriekservice gebruikt, raadpleegt u [gegevens transformeren met varkensactiviteit in Gegevensfabriek](../transform-data-using-hadoop-pig.md).


De HDInsight Pig-activiteit in een Data [Factory-pijplijn](data-factory-create-pipelines.md) voert Pig-query's uit op [uw eigen](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) of [on-demand](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-gebaseerde HDInsight-cluster. Dit artikel bouwt voort op het artikel [over gegevenstransformatieactiviteiten,](data-factory-data-transformation-activities.md) dat een algemeen overzicht geeft van gegevenstransformatie en de ondersteunde transformatieactiviteiten.

> [!NOTE] 
> Als u nieuw bent in Azure Data Factory, leest u [Inleiding tot Azure Data Factory](data-factory-introduction.md) en doet u de zelfstudie: Bouw uw eerste [gegevenspijplijn](data-factory-build-your-first-pipeline.md) voordat u dit artikel leest. 

## <a name="syntax"></a>Syntaxis

```JSON
{
  "name": "HiveActivitySamplePipeline",
  "properties": {
    "activities": [
      {
        "name": "Pig Activity",
        "description": "description",
        "type": "HDInsightPig",
        "inputs": [
          {
            "name": "input tables"
          }
        ],
        "outputs": [
          {
            "name": "output tables"
          }
        ],
        "linkedServiceName": "MyHDInsightLinkedService",
        "typeProperties": {
          "script": "Pig script",
          "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
      }
    ]
  }
}
```

## <a name="syntax-details"></a>Syntaxisdetails

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| name |Naam van de activiteit |Ja |
| description |Tekst waarin wordt beschreven waarvoor de activiteit wordt gebruikt |Nee |
| type |HDinsightPig HDinsightPig |Ja |
| Ingangen |Een of meer inputs verbruikt door de Pig-activiteit |Nee |
| Uitgangen |Een of meer outputs geproduceerd door de varkensactiviteit |Ja |
| linkedServiceName |Verwijzing naar het HDInsight-cluster dat is geregistreerd als gekoppelde service in Data Factory |Ja |
| uit |De inline van het varkensscript opgeven |Nee |
| scriptPath |Sla het Pig-script op in een Azure blob-opslag en geef het pad naar het bestand op. Gebruik de eigenschap 'script' of 'scriptPath'. Beide kunnen niet samen worden gebruikt. De bestandsnaam is hoofdlettergevoelig. |Nee |
| Definieert |Parameters opgeven als sleutel-/waardeparen voor verwijzingen in het script Varken |Nee |

## <a name="example"></a>Voorbeeld
Laten we eens kijken naar een voorbeeld van analytics voor gamelogs waarbij u de tijd wilt identificeren die wordt besteed aan spelers die games spelen die door uw bedrijf zijn gelanceerd.

Het volgende voorbeeldspellogboek is een door komma (,) gescheiden bestand. Het bevat de volgende velden : ProfileID, SessionStart, Duur, SrcIPAddress en GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

Het **script Varken** om deze gegevens te verwerken:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Ga als volgt te werk om dit Varkensscript uit te voeren in een pijplijn gegevensfabriek:

1. Maak een gekoppelde service om [uw eigen HDInsight-computecluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) te registreren of [on-demand HDInsight-computecluster te](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)configureren. Laten we deze gekoppelde dienst **HDInsightLinkedService**noemen.
2. Maak een [gekoppelde service](data-factory-azure-blob-connector.md) om de verbinding met Azure Blob-opslag met de gegevens te configureren. Laten we deze gekoppelde service **StorageLinkedService**noemen.
3. Maak [gegevenssets](data-factory-create-datasets.md) die verwijzen naar de invoer en de uitvoergegevens. Laten we de invoergegevensset **PigSampleIn** en de uitvoergegevensset **PigSampleOut**aanroepen.
4. Kopieer de varkensquery in een bestand dat de Azure Blob Storage in stap #2 heeft geconfigureerd. Als de Azure-opslag die de gegevens host, verschilt van de opslag die het querybestand host, maakt u een afzonderlijke azure storage-gekoppelde service. Raadpleeg de gekoppelde service in de activiteitsconfiguratie. Gebruik **scriptPath** om het pad naar het varkensscriptbestand en **scriptLinkedService**op te geven . 
   
   > [!NOTE]
   > U de inline van het varkensscript ook in de activiteitsdefinitie opnemen met behulp van de eigenschap **script.** We raden deze aanpak echter niet aan, omdat alle speciale tekens in het script moeten worden ontsnapt en problemen met foutopsporing kunnen veroorzaken. De beste praktijk is om stap #4 te volgen.
   >
   >
5. Maak de pijplijn met de HDInsightPig-activiteit. Deze activiteit verwerkt de invoergegevens door het pig-script uit te voeren op het HDInsight-cluster.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
              "scriptLinkedService": "StorageLinkedService"
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
6. Implementeer de pijplijn. Zie Het artikel [Pijplijnen maken](data-factory-create-pipelines.md) voor meer informatie. 
7. Controleer de pijplijn met behulp van de monitoring- en beheerweergaven van de gegevensfabriek. Zie Het artikel [over gegevensfabrieken controleren en beheren](data-factory-monitor-manage-pipelines.md) voor meer informatie.

## <a name="specifying-parameters-for-a-pig-script"></a>Parameters opgeven voor een Varkensscript
Neem het volgende voorbeeld: gamelogboeken worden dagelijks ingenomen in Azure Blob Storage en opgeslagen in een map die is verdeeld op basis van datum en tijd. U wilt het Pig-script parameteriseren en de locatie van de invoermap dynamisch doorgeven tijdens runtime en ook de uitvoer produceren die is verdeeld met datum en tijd.

Ga als volgt te werk om het met parameteren de seinsom te gebruiken:

* Definieer de parameters in **definieert**.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
              "scriptLinkedService": "StorageLinkedService",
              "defines": {
                "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0:MM}/dayno={0: dd}/',SliceStart)",
                "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
              }
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
* Raadpleeg in het Varkensscript de parameters met **'$parameterName'** zoals weergegeven in het volgende voorbeeld:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Zie ook
* [Hive Activiteit](data-factory-hive-activity.md)
* [Activiteit kaart verminderen](data-factory-map-reduce.md)
* [Hadoop streaming activiteit](data-factory-hadoop-streaming-activity.md)
* [Spark-programma's aanroepen](data-factory-spark.md)
* [R-scripts aanroepen](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)
