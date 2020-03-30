---
title: Tot activiteit in Azure Data Factory
description: De activiteit Tot voert een reeks activiteiten in een lus uit totdat de voorwaarde die aan de activiteit is gekoppeld, wordt geëvalueerd op waar of wanneer deze een tijd heeft.
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
ms.openlocfilehash: b4786b612dedb065239f57e0286bafb688180dff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440375"
---
# <a name="until-activity-in-azure-data-factory"></a>Tot activiteit in Azure Data Factory
De activiteit Until biedt dezelfde functionaliteit als de lusstructuur do-until in een programmeertaal. Er wordt een reeks activiteiten uitgevoerd totdat de voorwaarde die aan de activiteit is gekoppeld, resulteert in waar. U kunt in Data Factory een time-outwaarde voor de Until-activiteit opgeven. 

## <a name="syntax"></a>Syntaxis

```json
{
    "type": "Until",
    "typeProperties": {
        "expression":  {
            "value":  "<expression that evaluates to true or false>", 
            "type": "Expression"
        },
        "timeout": "<time out for the loop. for example: 00:01:00 (1 minute)>",
        "activities": [
            {
                "<Activity 1 definition>"
            },
            {
                "<Activity 2 definition>"
            },
            {
                "<Activity N definition>"
            }
        ]
    },
    "name": "MyUntilActivity"
}

```

## <a name="type-properties"></a>Eigenschappen typen

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
name | Naam van `Until` de activiteit. | Tekenreeks | Ja
type | Moet ingesteld worden op **Until.** | Tekenreeks | Ja
expressie | Expressie die moet worden geëvalueerd op waar of onwaar | Expressie.  | Ja
timeout | De do-until lus time-out na de opgegeven tijd hier. | Tekenreeks. `d.hh:mm:ss`(of) `hh:mm:ss`. De standaardwaarde is 7 dagen. Maximale waarde is: 90 dagen. | Nee
Activiteiten | Set activiteiten die worden uitgevoerd totdat `true`expressie is geëvalueerd in . | Scala aan activiteiten. |  Ja

## <a name="example-1"></a>Voorbeeld 1

> [!NOTE]
> In deze sectie worden JSON-definities en voorbeeld-PowerShell-opdrachten voor het uitvoeren van de pijplijn opgenomen. Zie [zelfstudie: maak een gegevensfabriek met Azure PowerShell](quickstart-create-data-factory-powershell.md)voor een stapsgewijze handleiding voor het maken van een Data Factory-pijplijn met azure PowerShell- en JSON-definities.

### <a name="pipeline-with-until-activity"></a>Pijplijn met activiteit Tot
In dit voorbeeld heeft de pijplijn twee activiteiten: **Tot** en **wacht.** De activiteit Wachten wacht op de opgegeven periode voordat de webactiviteit in de lus wordt uitgevoerd. Zie [Taal en functies expressie](control-flow-expression-language-functions.md)voor meer informatie over expressies en functies. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="example-2"></a>Voorbeeld 2 
De pijplijn in dit voorbeeld kopieert gegevens uit een invoermap naar een uitvoermap in een lus. De lus eindigt wanneer de waarde voor de herhalingsparameter is ingesteld op false of na één minuut een tijd.   

### <a name="pipeline-with-until-activity-adfv2quickstartpipelinejson"></a>Pijplijn met activiteit Tot (Adfv2QuickStartPipeline.json)

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression":  {
                        "value":  "@equals('false', pipeline().parameters.repeat)", 
                        "type": "Expression"
                    },
                    "timeout": "00:01:00",
                    "activities": [
                        {
                            "name": "CopyFromBlobToBlob",
                            "type": "Copy",
                            "inputs": [
                                {
                                    "referenceName": "BlobDataset",
                                    "parameters": {
                                        "path": "@pipeline().parameters.inputPath"
                                    },
                                    "type": "DatasetReference"
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "BlobDataset",
                                    "parameters": {
                                        "path": "@pipeline().parameters.outputPath"
                                    },
                                    "type": "DatasetReference"
                                }
                            ],
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource"
                                },
                                "sink": {
                                    "type": "BlobSink"
                                }
                            },
                            "policy": {
                                "retry": 1,
                                "timeout": "00:10:00",
                                "retryIntervalInSeconds": 60
                            }
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            },
            "repeat": {
                "type": "String"
            }                        
        }        
    }
}

```


### <a name="azure-storage-linked-service-azurestoragelinkedservicejson"></a>Gekoppelde Azure Storage-service (AzureStorageLinkedService.json)

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<Azure Storage account name>;AccountKey=<Azure Storage account key>"
        }
    }
}
```

### <a name="parameterized-azure-blob-dataset-blobdatasetjson"></a>Geparameteriseerde Azure Blob-gegevensset (BlobDataset.json)
De pijplijn stelt de **mapPath** in op de waarde van de parameter **outputPath1** of **outputPath2** van de pijplijn. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="pipeline-parameter-json-pipelineparametersjson"></a>Pipeline parameter JSON (PipelineParameters.json)

```json
{
    "inputPath": "adftutorial/input",
    "outputPath": "adftutorial/outputUntil",
    "repeat": "true"
}
```

### <a name="powershell-commands"></a>PowerShell-opdrachten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Deze opdrachten gaan ervan uit dat u de JSON-bestanden in de map hebt opgeslagen: C:\ADF. 

```powershell
Connect-AzAccount
Select-AzSubscription "<Your subscription name>"

$resourceGroupName = "<Resource Group Name>"
$dataFactoryName = "<Data Factory Name. Must be globally unique>";
Remove-AzDataFactoryV2 $dataFactoryName -ResourceGroupName $resourceGroupName -force


Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile "C:\ADF\AzureStorageLinkedService.json"
Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile "C:\ADF\BlobDataset.json"
Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile "C:\ADF\Adfv2QuickStartPipeline.json"
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile C:\ADF\PipelineParameters.json

while ($True) {
    $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

    if ($run) {
        if ($run.Status -ne 'InProgress') {
            Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
            $run
            break
        }
        Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        Write-Host "Activity run details:" -foregroundcolor "Yellow"
        $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
        $result

        Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
        $result.Output -join "`r`n"
    }

    Start-Sleep -Seconds 15
}
```

## <a name="next-steps"></a>Volgende stappen
Bekijk andere controlestroomactiviteiten die worden ondersteund door Data Factory: 

- [If Condition Activity](control-flow-if-condition-activity.md)
- [Execute Pipeline Activity](control-flow-execute-pipeline-activity.md)
- [Voor elke activiteit](control-flow-for-each-activity.md)
- [Get Metadata Activity](control-flow-get-metadata-activity.md)
- [Lookup Activity](control-flow-lookup-activity.md)
- [Webactiviteit](control-flow-web-activity.md)
