---
title: ForEach-activiteit in Azure Data Factory
description: Met De voor elke activiteit wordt een herhalende besturingselementstroom in uw pijplijn gedefinieerd. Het wordt gebruikt voor het herhalen van een verzameling en het uitvoeren van bepaalde activiteiten.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: 71528385563b29ce70edf396434be0174beac105
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804840"
---
# <a name="foreach-activity-in-azure-data-factory"></a>ForEach-activiteit in Azure Data Factory
Met De ForEach-activiteit wordt een herhalende besturingselementstroom in uw pijplijn gedefinieerd. Deze activiteit wordt gebruikt om een verzameling te herhalen en voert opgegeven activiteiten uit in een lus. De lusimplementatie van deze activiteit is vergelijkbaar met Foreach-lusstructuur in computertalen.

## <a name="syntax"></a>Syntaxis
De eigenschappen worden later in dit artikel beschreven. De eigenschap items is de verzameling en elk item `@item()` in de verzameling wordt aangeduid met behulp van de zoals weergegeven in de volgende syntaxis:  

```json
{  
   "name":"MyForEachActivityName",
   "type":"ForEach",
   "typeProperties":{  
      "isSequential":"true",
        "items": {
            "value": "@pipeline().parameters.mySinkDatasetFolderPathCollection",
            "type": "Expression"
        },
      "activities":[  
         {  
            "name":"MyCopyActivity",
            "type":"Copy",
            "typeProperties":{  
               ...
            },
            "inputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@pipeline().parameters.mySourceDatasetFolderPath"
                  }
               }
            ],
            "outputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@item()"
                  }
               }
            ]
         }
      ]
   }
}

```

## <a name="type-properties"></a>Eigenschappen typen

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
name | Naam van de voor-elke activiteit. | Tekenreeks | Ja
type | Moet worden ingesteld op **ForEach** | Tekenreeks | Ja
isSequentiële | Hiermee geeft u op of de lus achtereenvolgens of parallel moet worden uitgevoerd.  Maximaal 20 lusiteraties kunnen parallel tegelijk worden uitgevoerd). Als u bijvoorbeeld een ForEach-activiteit hebt die wordt gewijzigd boven een kopieeractiviteit met 10 verschillende bron- en sinkgegevenssets met **isSequential** ingesteld op False, worden alle kopieën tegelijk uitgevoerd. Standaard is False. <br/><br/> Als 'isSequential' is ingesteld op False, moet u ervoor zorgen dat er een juiste configuratie is om meerdere uitvoerbare bestanden uit te voeren. Anders moet deze eigenschap met de nodige voorzichtigheid worden gebruikt om te voorkomen dat er schrijfconflicten ontstaan. Zie Sectie [Parallelle uitvoering](#parallel-execution) voor meer informatie. | Booleaans | Nee. Standaard is False.
batchAantal | Batchaantal dat moet worden gebruikt voor het regelen van het aantal parallelle uitvoeringen (wanneer isSequential is ingesteld op false). Dit is de bovenste gelijktijdigheidslimiet, maar de activiteit voor elke activiteit wordt niet altijd uitgevoerd op dit nummer | Geheel getal (maximaal 50) | Nee. Standaard is 20.
Items | Een expressie die een JSON-array retourneert om te worden herhaald. | Expressie (waarmee een JSON-array wordt geretourneerd) | Ja
Activiteiten | De uit te voeren activiteiten. | Lijst met activiteiten | Ja

## <a name="parallel-execution"></a>Parallelle uitvoering
Als **isSequential** is ingesteld op false, wordt de activiteit parallel met maximaal 20 gelijktijdige iteraties. Deze instelling moet met de nodige voorzichtigheid worden gebruikt. Als de gelijktijdige iteraties naar dezelfde map schrijven, maar naar verschillende bestanden, is deze benadering prima. Als de gelijktijdige iteraties gelijktijdig naar exact hetzelfde bestand schrijven, veroorzaakt deze benadering waarschijnlijk een fout. 

## <a name="iteration-expression-language"></a>De uitdrukkingstaal van de iteratie
Geef in de ForEach-activiteit een array die moet worden herhaald voor de **vastgoeditems."** Gebruiken `@item()` om te herhalen over een enkele opsomming in ForEach activiteit. Als **items** bijvoorbeeld een array zijn: [1, `@item()` 2, 3], retourneert 1 in de eerste iteratie, 2 in de tweede iteratie en 3 in de derde iteratie.

## <a name="iterating-over-a-single-activity"></a>Herhalen over één activiteit
**Scenario:** Kopieer vanuit hetzelfde bronbestand in Azure Blob naar meerdere doelbestanden in Azure Blob.

### <a name="pipeline-definition"></a>Pijplijndefinitie

```json
{
    "name": "<MyForEachPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyForEachActivity>",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "true",
                    "items": {
                        "value": "@pipeline().parameters.mySinkDatasetFolderPath",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "MyCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": "false"
                                },
                                "sink": {
                                    "type": "BlobSink",
                                    "copyBehavior": "PreserveHierarchy"
                                }
                            },
                            "inputs": [
                                {
                                    "referenceName": "<MyDataset>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@pipeline().parameters.mySourceDatasetFolderPath"
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "MyDataset",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@item()"
                                    }
                                }
                            ]
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "mySourceDatasetFolderPath": {
                "type": "String"
            },
            "mySinkDatasetFolderPath": {
                "type": "String"
            }
        }
    }
}

```

### <a name="blob-dataset-definition"></a>Blob-gegevenssetdefinitie

```json
{  
   "name":"<MyDataset>",
   "properties":{  
      "type":"AzureBlob",
      "typeProperties":{  
         "folderPath":{  
            "value":"@dataset().MyFolderPath",
            "type":"Expression"
         }
      },
      "linkedServiceName":{  
         "referenceName":"StorageLinkedService",
         "type":"LinkedServiceReference"
      },
      "parameters":{  
         "MyFolderPath":{  
            "type":"String"
         }
      }
   }
}

```

### <a name="run-parameter-values"></a>Parameterwaarden uitvoeren

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>Herhalen over meerdere activiteiten
Het is mogelijk om meerdere activiteiten (bijvoorbeeld kopieer- en webactiviteiten) in een ForEach-activiteit te herhalen. In dit scenario raden we u aan meerdere activiteiten in een afzonderlijke pijplijn te abstraheren. Vervolgens u de [activiteit ExecutePipeline](control-flow-execute-pipeline-activity.md) in de pijplijn gebruiken met ForEach-activiteit om de afzonderlijke pijplijn met meerdere activiteiten aan te roepen. 


### <a name="syntax"></a>Syntaxis

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "<MyForEachMultipleActivities>"
        "typeProperties": {
          "isSequential": true,
          "items": {
            ...
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "name": "<MyInnerPipeline>"
              "typeProperties": {
                "pipeline": {
                  "referenceName": "<copyHttpPipeline>",
                  "type": "PipelineReference"
                },
                "parameters": {
                  ...
                },
                "waitOnCompletion": true
              }
            }
          ]
        }
      }
    ],
    "parameters": {
      ...
    }
  }
}

```

### <a name="example"></a>Voorbeeld
**Scenario:** Herhalen over een InnerPipeline binnen een ForEach-activiteit met activiteit Pijplijn uitvoeren. De binnenste pijplijn wordt kopieën met schemadefinities geparameteriseerd.

#### <a name="master-pipeline-definition"></a>Hoofdpijplijndefinitie

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "MyForEachActivity",
        "typeProperties": {
          "isSequential": true,
          "items": {
            "value": "@pipeline().parameters.inputtables",
            "type": "Expression"
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "typeProperties": {
                "pipeline": {
                  "referenceName": "InnerCopyPipeline",
                  "type": "PipelineReference"
                },
                "parameters": {
                  "sourceTableName": {
                    "value": "@item().SourceTable",
                    "type": "Expression"
                  },
                  "sourceTableStructure": {
                    "value": "@item().SourceTableStructure",
                    "type": "Expression"
                  },
                  "sinkTableName": {
                    "value": "@item().DestTable",
                    "type": "Expression"
                  },
                  "sinkTableStructure": {
                    "value": "@item().DestTableStructure",
                    "type": "Expression"
                  }
                },
                "waitOnCompletion": true
              },
              "name": "ExecuteCopyPipeline"
            }
          ]
        }
      }
    ],
    "parameters": {
      "inputtables": {
        "type": "Array"
      }
    }
  }
}

```

#### <a name="inner-pipeline-definition"></a>Definitie van binnenpijplijn

```json
{
  "name": "InnerCopyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            }
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "name": "CopyActivity",
        "inputs": [
          {
            "referenceName": "sqlSourceDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sourceTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sourceTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sqlSinkDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sinkTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sinkTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceTableName": {
        "type": "String"
      },
      "sourceTableStructure": {
        "type": "String"
      },
      "sinkTableName": {
        "type": "String"
      },
      "sinkTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="source-dataset-definition"></a>Definitie van brongegevensset

```json
{
  "name": "sqlSourceDataset",
  "properties": {
    "type": "SqlServerTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "sqlserverLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="sink-dataset-definition"></a>Definitie van sinkgegevensset

```json
{
  "name": "sqlSinkDataSet",
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "azureSqlLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="master-pipeline-parameters"></a>Parameters van hoofdpijplijn
```json
{
    "inputtables": [
        {
            "SourceTable": "department",
            "SourceTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ],
            "DestTable": "department2",
            "DestTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ]
        }
    ]
    
}
```

## <a name="aggregating-outputs"></a>Uitvoer samenvoegen

Als u de uitvoer van __voorelke__ activiteit wilt samenvoegen, u _variabelen_ en variabele activiteit _toevoegen._

Declareer eerst `array` een _variabele_ in de pijplijn. Roep vervolgens variabele activiteit _toevoegen_ aan in elke __voorelke__ lus. Vervolgens u de aggregatie ophalen van uw array.

## <a name="limitations-and-workarounds"></a>Beperkingen en tijdelijke oplossingen

Hier volgen enkele beperkingen van de ForEach-activiteit en voorgestelde tijdelijke oplossingen.

| Beperking | Tijdelijke oplossing |
|---|---|
| U een ForEach-lus niet nesten in een andere ForEach-lus (of een lus Tot). | Ontwerp een pijplijn op twee niveaus waarbij de buitenste pijplijn met de buitenste ForEach-lus over een binnenleiding met de geneste lus heen loopt. |
| De ForEach-activiteit `batchCount` heeft een maximum van 50 voor parallelle verwerking en maximaal 100.000 artikelen. | Ontwerp een pijplijn op twee niveaus waarbij de buitenste pijplijn met de ForEach-activiteit over een binnenpijplijn wordt gehesen. |
| | |

## <a name="next-steps"></a>Volgende stappen
Bekijk andere controlestroomactiviteiten die worden ondersteund door Data Factory: 

- [Execute Pipeline Activity](control-flow-execute-pipeline-activity.md)
- [Get Metadata Activity](control-flow-get-metadata-activity.md)
- [Lookup Activity](control-flow-lookup-activity.md)
- [Webactiviteit](control-flow-web-activity.md)
