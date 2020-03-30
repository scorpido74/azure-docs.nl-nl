---
title: Pijplijnactiviteit uitvoeren in Azure Data Factory
description: Meer informatie over hoe u de pijplijnactiviteit uitvoeren gebruiken om een datafabriekpijplijn uit een andere Data Factory-pijplijn aan te roepen.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 26dd7c4f33360030b13ddbfc1516396436724c40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440446"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Pijplijnactiviteit uitvoeren in Azure Data Factory
De activiteit Execute Pipeline stelt een Data Factory-pijplijn in staat om een andere pijplijn aan te roepen.

## <a name="syntax"></a>Syntaxis

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>Eigenschappen typen

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
name | Naam van de uitvoerpijplijnactiviteit. | Tekenreeks | Ja
type | Moet zijn ingesteld op: **ExecutePipeline**. | Tekenreeks | Ja
Pijpleiding | Pijplijnverwijzing naar de afhankelijke pijplijn die deze pijplijn aanroept. Een pijplijnverwijzingsobject heeft twee eigenschappen: **referenceName** en **type**. De eigenschap referenceName geeft de naam van de referentiepijplijn op. De eigenschap type moet zijn ingesteld op PipelineReference. | PipelineReference | Ja
parameters | Parameters die moeten worden doorgegeven aan de aangeroepen pijplijn | Een JSON-object dat parameternamen inkaart aan argumentwaarden | Nee
waitOnCompletion waitOnCompletion waitOnCompletion waitOn | Hiermee bepaalt u of de uitvoering van de activiteit wacht tot de afhankelijke pijplijnuitvoering is voltooid. De standaardinstelling is onwaar. | Booleaans | Nee

## <a name="sample"></a>Voorbeeld
Dit scenario heeft twee pijplijnen:

- **Hoofdpijplijn** - Deze pijplijn heeft één uitvoerpijplijnactiviteit die de aangeroepen pijplijn aanroept. De hoofdpijplijn heeft `masterSourceBlobContainer`twee `masterSinkBlobContainer`parameters: , .
- **Aangeroepen pijplijn** - Deze pijplijn heeft één kopieeractiviteit die gegevens kopieert van een Azure Blob-bron naar Azure Blob-sink. De aangeroepen pijplijn heeft `sourceBlobContainer` `sinkBlobContainer`twee parameters: , .

### <a name="master-pipeline-definition"></a>Hoofdpijplijndefinitie

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobContainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>Ingeroepen pijplijndefinitie

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

**Gekoppelde service**

```json
{
    "name": "BlobStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=*****;AccountKey=*****"
    }
  }
}
```

**Brongegevensset**
```json
{
    "name": "SourceBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sourceBlobContainer",
        "type": "Expression"
      },
      "fileName": "salesforce.txt"
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

**Sink-gegevensset**
```json
{
    "name": "sinkBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sinkBlobContainer",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

### <a name="running-the-pipeline"></a>De pijplijn uitvoeren

Als u de hoofdpijplijn in dit voorbeeld wilt uitvoeren, worden de volgende waarden doorgegeven voor de parameters masterSourceBlobContainer en masterSinkBlobContainer: 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

De hoofdpijplijn stuurt deze waarden door naar de aangeroepen pijplijn, zoals in het volgende voorbeeld wordt weergegeven: 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobContainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>Volgende stappen
Bekijk andere controlestroomactiviteiten die worden ondersteund door Data Factory: 

- [Voor elke activiteit](control-flow-for-each-activity.md)
- [Get Metadata Activity](control-flow-get-metadata-activity.md)
- [Lookup Activity](control-flow-lookup-activity.md)
- [Webactiviteit](control-flow-web-activity.md)
