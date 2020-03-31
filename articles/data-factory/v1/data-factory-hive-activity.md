---
title: Gegevens transformeren met Hive-activiteit - Azure
description: Ontdek hoe u de Hive-activiteit in een Azure-gegevensfabriek gebruiken om Hive-query's uit te voeren op een on-demand/uw eigen HDInsight-cluster.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: d153f8c316cbb76e063f07f7f823c8d9c4a21f87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703358"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Gegevens transformeren met Hive-activiteit in Azure Data Factory 
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
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de datafabriekservice gebruikt, raadpleegt u [gegevens transformeren met behulp van Hive-activiteit in Gegevensfabriek](../transform-data-using-hadoop-hive.md).

De HDInsight Hive-activiteit in een Data [Factory-pijplijn](data-factory-create-pipelines.md) voert Hive-query's uit op [uw eigen](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) of [on-demand](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-cluster op basis van Windows/Linux. Dit artikel bouwt voort op het artikel [over gegevenstransformatieactiviteiten,](data-factory-data-transformation-activities.md) dat een algemeen overzicht geeft van gegevenstransformatie en de ondersteunde transformatieactiviteiten.

> [!NOTE] 
> Als u nieuw bent in Azure Data Factory, leest u [Inleiding tot Azure Data Factory](data-factory-introduction.md) en doet u de zelfstudie: Bouw uw eerste [gegevenspijplijn](data-factory-build-your-first-pipeline.md) voordat u dit artikel leest. 

## <a name="syntax"></a>Syntaxis

```JSON
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
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
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```
## <a name="syntax-details"></a>Syntaxisdetails
| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| name |Naam van de activiteit |Ja |
| description |Tekst waarin wordt beschreven waarvoor de activiteit wordt gebruikt |Nee |
| type |HDinsightHive HDinsightHive |Ja |
| Ingangen |Inputs verbruikt door de Hive-activiteit |Nee |
| Uitgangen |Outputs geproduceerd door de Hive-activiteit |Ja |
| linkedServiceName |Verwijzing naar het HDInsight-cluster dat is geregistreerd als gekoppelde service in Data Factory |Ja |
| uit |De inline van het Hive-script opgeven |Nee |
| scriptPath |Sla het Hive-script op in een Azure blob-opslag en geef het pad naar het bestand op. Gebruik de eigenschap 'script' of 'scriptPath'. Beide kunnen niet samen worden gebruikt. De bestandsnaam is hoofdlettergevoelig. |Nee |
| Definieert |Parameters opgeven als sleutel-/waardeparen voor verwijzing in het Hive-script met behulp van 'hiveconf' |Nee |

## <a name="example"></a>Voorbeeld
Laten we eens kijken naar een voorbeeld van analyse van gamelogboeken waarbij u de tijd wilt identificeren die wordt besteed aan gebruikers die games spelen die door uw bedrijf zijn gelanceerd. 

Het volgende logboek is een voorbeeld spel`,`log, dat is komma ( ) gescheiden en bevat de volgende velden - ProfileID, SessionStart, Duur, SrcIPAddress, en GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

Het **Hive-script** om deze gegevens te verwerken:

```
DROP TABLE IF EXISTS HiveSampleIn; 
CREATE EXTERNAL TABLE HiveSampleIn 
(
    ProfileID        string, 
    SessionStart     string, 
    Duration         int, 
    SrcIPAddress     string, 
    GameType         string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 

DROP TABLE IF EXISTS HiveSampleOut; 
CREATE EXTERNAL TABLE HiveSampleOut 
(    
    ProfileID     string, 
    Duration     int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';

INSERT OVERWRITE TABLE HiveSampleOut
Select 
    ProfileID,
    SUM(Duration)
FROM HiveSampleIn Group by ProfileID
```

Als u dit Hive-script wilt uitvoeren in een Data Factory-pijplijn, moet u het volgende doen

1. Maak een gekoppelde service om [uw eigen HDInsight-computecluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) te registreren of [on-demand HDInsight-computecluster te](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)configureren. Laten we deze gekoppelde dienst "HDInsightLinkedService" noemen.
2. Maak een [gekoppelde service](data-factory-azure-blob-connector.md) om de verbinding met Azure Blob-opslag met de gegevens te configureren. Laten we deze gekoppelde service "StorageLinkedService" noemen
3. Maak [gegevenssets](data-factory-create-datasets.md) die verwijzen naar de invoer en de uitvoergegevens. Laten we de invoergegevensset 'HiveSampleIn' en de uitvoergegevensset 'HiveSampleOut' noemen
4. Kopieer de Hive-query als bestand naar Azure Blob Storage, geconfigureerd in stap #2. Als de opslag voor het hosten van de gegevens verschilt van de opslag die dit querybestand host, maakt u een afzonderlijke gekoppelde Azure Storage-service en verwijst u ernaar in de activiteit. Gebruik **scriptPath** om het pad naar het querybestand en **scriptLinkedService** op te geven om de Azure-opslag op te geven die het scriptbestand bevat. 
   
   > [!NOTE]
   > U de Hive-scriptinline ook in de activiteitsdefinitie opnemen met behulp van de eigenschap **script.** We raden deze aanpak niet aan, omdat alle speciale tekens in het script in het JSON-document moeten worden ontsnapt en problemen met foutopsporing kunnen veroorzaken. De beste praktijk is om stap #4 te volgen.
   > 
   > 
5. Maak een pijplijn met de HDInsightHive-activiteit. De activiteit verwerkt/transformeert de gegevens.

    ```JSON   
    {   
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                {
                    "name": "HiveSampleIn"
                }
                ],
                "outputs": [
                {
                    "name": "HiveSampleOut"
                }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
            ]
        }
    }
    ```
6. Implementeer de pijplijn. Zie Het artikel [Pijplijnen maken](data-factory-create-pipelines.md) voor meer informatie. 
7. Controleer de pijplijn met behulp van de monitoring- en beheerweergaven van de gegevensfabriek. Zie Het artikel [over gegevensfabrieken controleren en beheren](data-factory-monitor-manage-pipelines.md) voor meer informatie. 

## <a name="specifying-parameters-for-a-hive-script"></a>Parameters opgeven voor een Hive-script
In dit voorbeeld worden gamelogboeken dagelijks opgenomen in Azure Blob Storage en worden ze opgeslagen in een map met datum en tijd. U wilt het Hive-script parameteriseren en de locatie van de invoermap dynamisch doorgeven tijdens runtime en ook de uitvoer produceren die is verdeeld met datum en tijd.

Ga als volgt te werk om een geparameteriseerd Hive-script te gebruiken:

* Definieer de parameters in **definieert**.

    ```JSON  
    {
        "name": "HiveActivitySamplePipeline",
          "properties": {
        "activities": [
             {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                      {
                        "name": "HiveSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "HiveSampleOut"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      },
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    }
                }
              }
        ]
      }
    }
    ```
* Raadpleeg in het Hive Script de parameter met **${hiveconf:parameterName}**. 
  
    ```
    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID     string, 
        SessionStart     string, 
        Duration     int, 
        SrcIPAddress     string, 
        GameType     string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 

    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (
        ProfileID     string, 
        Duration     int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID
    ```
  ## <a name="see-also"></a>Zie ook
* [Varkensactiviteit](data-factory-pig-activity.md)
* [Activiteit kaart verminderen](data-factory-map-reduce.md)
* [Hadoop streaming activiteit](data-factory-hadoop-streaming-activity.md)
* [Spark-programma's aanroepen](data-factory-spark.md)
* [R-scripts aanroepen](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

