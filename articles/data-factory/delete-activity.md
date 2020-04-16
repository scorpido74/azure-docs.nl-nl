---
title: Verwijderactiviteit in Azure Data Factory
description: Meer informatie over het verwijderen van bestanden in verschillende bestandsstores met de activiteit Verwijderen in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: d90f38f83bd4d2d5311f277fcc928e442d7ea793
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416382"
---
# <a name="delete-activity-in-azure-data-factory"></a>Verwijderactiviteit in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


U de activiteit verwijderen in Azure Data Factory gebruiken om bestanden of mappen te verwijderen uit on-premises opslagopslag of cloudopslag. Gebruik deze activiteit om bestanden op te schonen of te archiveren wanneer ze niet meer nodig zijn.

> [!WARNING]
> Verwijderde bestanden of mappen kunnen niet worden hersteld (tenzij de opslag is ingeschakeld voor soft-delete). Wees voorzichtig wanneer u de activiteit Verwijderen gebruikt voor het verwijderen van bestanden of mappen.

## <a name="best-practices"></a>Aanbevolen procedures

Hier volgen enkele aanbevelingen voor het gebruik van de activiteit Verwijderen:

-   Maak een back-up van uw bestanden voordat u ze verwijdert met de activiteit Verwijderen voor het geval u ze in de toekomst moet herstellen.

-   Zorg ervoor dat Data Factory schrijfmachtigingen heeft om mappen of bestanden uit het opslagarchief te verwijderen.

-   Zorg ervoor dat u geen bestanden verwijderd die tegelijkertijd worden geschreven. 

-   Als u bestanden of mappen wilt verwijderen uit een on-premises systeem, moet u ervoor zorgen dat u een zelf gehoste internetverbinding gebruikt met een versie groter dan 3.14.

## <a name="supported-data-stores"></a>Ondersteunde gegevensarchieven

-   [Azure Blob-opslag](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)

### <a name="file-system-data-stores"></a>Bestandssysteemgegevensopslag

-   [Bestandssysteem](connector-file-system.md)
-   [FTP](connector-ftp.md)
-   [SFTP](connector-sftp.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [Google Cloud Storage](connector-google-cloud-storage.md)

## <a name="syntax"></a>Syntaxis

```json
{
    "name": "DeleteActivity",
    "type": "Delete",
    "typeProperties": {
        "dataset": {
            "referenceName": "<dataset name>",
            "type": "DatasetReference"
        },
        "recursive": true/false,
        "maxConcurrentConnections": <number>,
        "enableLogging": true/false,
        "logStorageSettings": {
            "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference"
            },
            "path": "<path to save log file>"
        }
    }
}
```

## <a name="type-properties"></a>Eigenschappen typen

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| Dataset | Hiermee wordt de gegevenssetverwijzing weergegeven om te bepalen welke bestanden of mappen moeten worden verwijderd | Ja |
| Recursieve | Hiermee geeft u aan of de bestanden opnieuw worden verwijderd uit de submappen of alleen uit de opgegeven map.  | Nee. De standaardwaarde is `false`. |
| maxConcurrentVerbindingen | Het aantal verbindingen dat tegelijkertijd verbinding maakt met het opslagarchief voor het verwijderen van mappen of bestanden.   |  Nee. De standaardwaarde is `1`. |
| inschakelen | Hiermee geeft u aan of u de map- of bestandsnamen die zijn verwijderd, moet opnemen. Als dit waar is, moet u verder een opslagaccount verstrekken om het logboekbestand op te slaan, zodat u het gedrag van de activiteit Verwijderen volgen door het logboekbestand te lezen. | Nee |
| logStorageInstellingen | Alleen van toepassing wanneer enablelogging = true.<br/><br/>Een groep opslageigenschappen die kan worden opgegeven waar u het logboekbestand wilt opslaan met de map of bestandsnamen die zijn verwijderd door de activiteit Verwijderen. | Nee |
| linkedServiceName | Alleen van toepassing wanneer enablelogging = true.<br/><br/>De gekoppelde service van [Azure Storage](connector-azure-blob-storage.md#linked-service-properties), Azure Data Lake [Storage Gen1](connector-azure-data-lake-store.md#linked-service-properties)of [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) om het logboekbestand op te slaan dat de map- of bestandsnamen bevat die zijn verwijderd door de activiteit Verwijderen. Houd er rekening mee dat het moet zijn geconfigureerd met hetzelfde type integratieruntime van de activiteit die wordt gebruikt door activiteit te verwijderen om bestanden te verwijderen. | Nee |
| path | Alleen van toepassing wanneer enablelogging = true.<br/><br/>Het pad om het logboekbestand op te slaan in uw opslagaccount. Als u geen pad opgeeft, maakt de service een container voor u. | Nee |

## <a name="monitoring"></a>Bewaking

Er zijn twee plaatsen waar u de resultaten van de activiteit Verwijderen zien en controleren: 
-   Van de uitvoer van de activiteit Verwijderen.
-   Uit het logboekbestand.

### <a name="sample-output-of-the-delete-activity"></a>Voorbeelduitvoer van de activiteit Verwijderen

```json
{ 
  "datasetName": "AmazonS3",
  "type": "AmazonS3Object",
  "prefix": "test",
  "bucketName": "adf",
  "recursive": true,
  "isWildcardUsed": false,
  "maxConcurrentConnections": 2,  
  "filesDeleted": 4,
  "logPath": "https://sample.blob.core.windows.net/mycontainer/5c698705-a6e2-40bf-911e-e0a927de3f07",
  "effectiveIntegrationRuntime": "MyAzureIR (West Central US)",
  "executionDuration": 650
}
```

### <a name="sample-log-file-of-the-delete-activity"></a>Voorbeeldlogboekbestand van de activiteit Verwijderen

| Naam | Categorie | Status | Fout |
|:--- |:--- |:--- |:--- |
| test1/yyy.json | File | Verwijderen |  |
| test2/hello789.txt | File | Verwijderen |  |
| test2/test3/hello000.txt | File | Verwijderen |  |
| test2/test3/zzz.json | File | Verwijderen |  |

## <a name="examples-of-using-the-delete-activity"></a>Voorbeelden van het gebruik van de activiteit Verwijderen

### <a name="delete-specific-folders-or-files"></a>Specifieke mappen of bestanden verwijderen

Het archief heeft de volgende mapstructuur:

Wortel/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2.<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1.<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2.<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt 8.txt

Nu gebruikt u de activiteit Verwijderen om map of bestanden te verwijderen door de combinatie van verschillende eigenschapswaarde uit de gegevensset en de activiteit Verwijderen:

| folderPath (uit gegevensset) | fileName (uit de gegevensset) | recursieve (van de activiteit Verwijderen) | Uitvoer |
|:--- |:--- |:--- |:--- |
| Wortel/ Folder_A_2 | NULL | False | Wortel/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2.<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1.<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2.<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt 8.txt |
| Wortel/ Folder_A_2 | NULL | True | Wortel/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_A_2.</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_1.</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>7.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_2.</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt 8.txt</strike> |
| Wortel/ Folder_A_2 | *.txt | False | Wortel/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2.<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1.<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2.<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt 8.txt |
| Wortel/ Folder_A_2 | *.txt | True | Wortel/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2.<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1.<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2.<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt 8.txt</strike> |

### <a name="periodically-clean-up-the-time-partitioned-folder-or-files"></a>De tijdpartitiemap of -bestanden periodiek opschonen

U een pijplijn maken om de tijdpartitiemap of bestanden periodiek op te schonen.  De mapstructuur is bijvoorbeeld vergelijkbaar `/mycontainer/2018/12/14/*.csv`met: .  U adf-systeemvariabele gebruiken vanaf de trigger van de planning om te bepalen welke map of bestanden in elke pijplijnmoeten worden verwijderd. 

#### <a name="sample-pipeline"></a>Voorbeeldpijplijn

```json
{
    "name": "cleanup_time_partitioned_folder",
    "properties": {
        "activities": [
            {
                "name": "DeleteOneFolder",
                "type": "Delete",
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "typeProperties": {
                    "dataset": {
                        "referenceName": "PartitionedFolder",
                        "type": "DatasetReference",
                        "parameters": {
                            "TriggerTime": {
                                "value": "@formatDateTime(pipeline().parameters.TriggerTime, 'yyyy/MM/dd')",
                                "type": "Expression"
                            }
                        }
                    },
                    "recursive": true,
                    "logStorageSettings": {
                        "linkedServiceName": {
                            "referenceName": "BloblinkedService",
                            "type": "LinkedServiceReference"
                        },
                        "path": "mycontainer/log"
                    },
                    "enableLogging": true
                }
            }
        ],
        "parameters": {
            "TriggerTime": {
                "type": "String"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

#### <a name="sample-dataset"></a>Voorbeeldgegevensset

```json
{
    "name": "PartitionedFolder",
    "properties": {
        "linkedServiceName": {
            "referenceName": "BloblinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "TriggerTime": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@concat('mycontainer/',dataset().TriggerTime)",
                "type": "Expression"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}
```

#### <a name="sample-trigger"></a>Voorbeeldtrigger

```json
{
    "name": "DailyTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "cleanup_time_partitioned_folder",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "TriggerTime": "@trigger().scheduledTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Day",
                "interval": 1,
                "startTime": "2018-12-13T00:00:00.000Z",
                "timeZone": "UTC",
                "schedule": {
                    "minutes": [
                        59
                    ],
                    "hours": [
                        23
                    ]
                }
            }
        }
    }
}
```

### <a name="clean-up-the-expired-files-that-were-last-modified-before-201811"></a>De verlopen bestanden opschonen die voor het laatst zijn gewijzigd vóór 2018.1.1

U een pijplijn maken om de oude of verlopen bestanden op te schonen door gebruik te maken van het filter van bestandskenmerk: 'LastModified' in de gegevensset.  

#### <a name="sample-pipeline"></a>Voorbeeldpijplijn

```json
{
    "name": "CleanupExpiredFiles",
    "properties": {
        "activities": [
            {
                "name": "DeleteFilebyLastModified",
                "type": "Delete",
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "typeProperties": {
                    "dataset": {
                        "referenceName": "BlobFilesLastModifiedBefore201811",
                        "type": "DatasetReference"
                    },
                    "recursive": true,
                    "logStorageSettings": {
                        "linkedServiceName": {
                            "referenceName": "BloblinkedService",
                            "type": "LinkedServiceReference"
                        },
                        "path": "mycontainer/log"
                    },
                    "enableLogging": true
                }
            }
        ]
    }
}
```

#### <a name="sample-dataset"></a>Voorbeeldgegevensset

```json
{
    "name": "BlobFilesLastModifiedBefore201811",
    "properties": {
        "linkedServiceName": {
            "referenceName": "BloblinkedService",
            "type": "LinkedServiceReference"
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": "*",
            "folderPath": "mycontainer",
            "modifiedDatetimeEnd": "2018-01-01T00:00:00.000Z"
        }
    }
}
```

### <a name="move-files-by-chaining-the-copy-activity-and-the-delete-activity"></a>Bestanden verplaatsen door de activiteit Kopiëren en de activiteit Verwijderen te ketenen

U een bestand verplaatsen met behulp van een kopieeractiviteit om een bestand te kopiëren en vervolgens een verwijderactiviteit om een bestand in een pijplijn te verwijderen.  Wanneer u meerdere bestanden wilt verplaatsen, u de activiteit GetMetadata + Filteractiviteit + Activiteit voor elke activiteit + Kopieeractiviteit + Activiteit verwijderen gebruiken zoals in het volgende voorbeeld:

> [!NOTE]
> Als u de hele map wilt verplaatsen door een gegevensset te definiëren die alleen een mappad bevat, en vervolgens een kopieeractiviteit en een activiteit Verwijderen wilt gebruiken om te verwijzen naar dezelfde gegevensset die een map vertegenwoordigt, moet u heel voorzichtig zijn. Het is omdat je ervoor moet zorgen dat er geen nieuwe bestanden in de map komen tussen kopieerbewerking en verwijderen.  Als er nieuwe bestanden in de map aankomen op het moment dat uw kopieeractiviteit net de kopieertaak heeft voltooid, maar de activiteit Verwijderen niet is aangestaard, is het mogelijk dat de activiteit Verwijderen dit nieuwe binnenkomende bestand verwijdert dat nog niet naar de bestemming is gekopieerd door de hele map te verwijderen. 

#### <a name="sample-pipeline"></a>Voorbeeldpijplijn

```json
{
    "name": "MoveFiles",
    "properties": {
        "activities": [
            {
                "name": "GetFileList",
                "type": "GetMetadata",
                "typeProperties": {
                    "dataset": {
                        "referenceName": "OneSourceFolder",
                        "type": "DatasetReference"
                    },
                    "fieldList": [
                        "childItems"
                    ]
                }
            },
            {
                "name": "FilterFiles",
                "type": "Filter",
                "dependsOn": [
                    {
                        "activity": "GetFileList",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "typeProperties": {
                    "items": {
                        "value": "@activity('GetFileList').output.childItems",
                        "type": "Expression"
                    },
                    "condition": {
                        "value": "@equals(item().type, 'File')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "ForEachFile",
                "type": "ForEach",
                "dependsOn": [
                    {
                        "activity": "FilterFiles",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "typeProperties": {
                    "items": {
                        "value": "@activity('FilterFiles').output.value",
                        "type": "Expression"
                    },
                    "batchCount": 20,
                    "activities": [
                        {
                            "name": "CopyAFile",
                            "type": "Copy",
                            "policy": {
                                "timeout": "7.00:00:00",
                                "retry": 0,
                                "retryIntervalInSeconds": 30,
                                "secureOutput": false,
                                "secureInput": false
                            },
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": false
                                },
                                "sink": {
                                    "type": "BlobSink"
                                },
                                "enableStaging": false,
                                "dataIntegrationUnits": 0
                            },
                            "inputs": [
                                {
                                    "referenceName": "OneSourceFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "path": "myFolder",
                                        "filename": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "OneDestinationFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "DestinationFileName": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                }
                            ]
                        },
                        {
                            "name": "DeleteAFile",
                            "type": "Delete",
                            "dependsOn": [
                                {
                                    "activity": "CopyAFile",
                                    "dependencyConditions": [
                                        "Succeeded"
                                    ]
                                }
                            ],
                            "policy": {
                                "timeout": "7.00:00:00",
                                "retry": 0,
                                "retryIntervalInSeconds": 30,
                                "secureOutput": false,
                                "secureInput": false
                            },
                            "typeProperties": {
                                "dataset": {
                                    "referenceName": "OneSourceFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "path": "myFolder",
                                        "filename": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                },
                                "logStorageSettings": {
                                    "linkedServiceName": {
                                        "referenceName": "BloblinkedService",
                                        "type": "LinkedServiceReference"
                                    },
                                    "path": "Container/log"
                                },
                                "enableLogging": true
                            }
                        }
                    ]
                }
            }
        ]
    }
}
```

#### <a name="sample-datasets"></a>Voorbeeldgegevenssets

Gegevensset die wordt gebruikt door getMetadata-activiteit om de bestandslijst op te sommen.

```json
{
    "name": "OneSourceFolder",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": "",
            "folderPath": "myFolder"
        }
    }
}
```

Gegevensset voor gegevensbron die wordt gebruikt door kopieeractiviteit en de activiteit Verwijderen.

```json
{
    "name": "OneSourceFile",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            },
            "filename": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": {
                "value": "@dataset().filename",
                "type": "Expression"
            },
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        }
    }
}
```

Gegevensset voor gegevensbestemming die wordt gebruikt door kopieeractiviteit.

```json
{
    "name": "OneDestinationFile",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "DestinationFileName": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": {
                "value": "@dataset().DestinationFileName",
                "type": "Expression"
            },
            "folderPath": "mycontainer/dest"
        }
    }
}
```

U ook de sjabloon om bestanden te verplaatsen vanaf [hier](solution-template-move-files.md).

## <a name="known-limitation"></a>Bekende beperking

-   Activiteit verwijderen biedt geen ondersteuning voor het verwijderen van een lijst met mappen die door een wildcard zijn beschreven.

-   Wanneer u het filter van bestandskenmerk gebruikt: gewijzigdDatumtimeStart en gewijzigdDatetimeEnd om bestanden te selecteren die moeten worden verwijderd, moet u 'fileName': "*" instellen in de gegevensset.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het verplaatsen van bestanden in Azure Data Factory.

-   [Hulpprogramma voor gegevens kopiëren in Azure Data Factory](copy-data-tool.md)
