---
title: Een Azure-data factory maken met Resource Manager-sjabloon
description: In deze zelfstudie maakt u een Azure Data Factory-voorbeeldpijplijn op basis van een Azure Resource Manager-sjabloon.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 02/20/2019
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 7ad0367a89730c3aba37c5f75158cb42ae4ae668
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440061"
---
# <a name="tutorial-create-an-azure-data-factory-using-azure-resource-manager-template"></a>Zelfstudie: een Azure Data Factory maken op basis van een Azure Resource Manager-sjabloon

> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Huidige versie](quickstart-create-data-factory-resource-manager-template.md)

In deze QuickStart wordt beschreven hoe u een Azure Resource Manager-sjabloon gebruikt om een Azure Data Factory te maken. Met de pijplijn die u in deze data factory maakt, worden gegevens **gekopieerd** van één map naar een andere map in een Azure Blob Storage. Zie [Zelfstudie: Gegevens transformeren met Spark](transform-data-using-spark.md) voor meer informatie over het **transformeren** van gegevens met Azure Data Factory.

> [!NOTE]
> Dit artikel is geen gedetailleerde introductie tot de Data Factory-service. Zie [Inleiding tot Azure Data Factory](introduction.md) voor een inleiding tot Azure Data Factory-service.

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)]

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Installeer de nieuwste Azure PowerShell-modules met de instructies in [Azure PowerShell installeren en configureren](/powershell/azure/install-Az-ps).

## <a name="resource-manager-templates"></a>Resource Manager-sjablonen

Zie [Azure Resource Manager-sjablonen maken](../azure-resource-manager/templates/template-syntax.md) voor meer informatie over Azure Resource Manager-sjablonen.

In de volgende sectie vindt u de volledige Resource Manager-sjabloon voor het definiëren van Data Factory-entiteiten zodat u de zelfstudie snel kunt doorlopen en de sjabloon kunt testen. Raadpleeg de sectie [Data Factory-entiteiten in de sjabloon](#data-factory-entities-in-the-template) om te lezen hoe elke Data Factory-entiteit wordt gedefinieerd.

Zie [Microsoft.DataFactory resource types](/azure/templates/microsoft.datafactory/allversions) (Microsoft.DataFactory-resourcetypen) voor informatie over de JSON-syntaxis en de eigenschappen voor Data Factory-resources in een sjabloon.

## <a name="data-factory-json"></a>JSON van data factory

Maak een JSON-bestand met de naam **ADFTutorialARM. json** in de map **C:\ADFTutorial** (Maak de map ADFTutorial als deze nog niet bestaat) met de volgende inhoud:

```json
{  
    "$schema":"http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion":"1.0.0.0",
    "parameters":{  
        "dataFactoryName":{  
            "type":"string",
            "metadata":"Data Factory Name"
        },
        "dataFactoryLocation":{  
            "type":"string",
            "defaultValue":"East US",
            "metadata":{  
                "description":"Location of the data factory. Currently, only East US, East US 2, and West Europe are supported. "
            }
        },
        "storageAccountName":{  
            "type":"string",
            "metadata":{  
                "description":"Name of the Azure storage account that contains the input/output data."
            }
        },
        "storageAccountKey":{  
            "type":"securestring",
            "metadata":{  
                "description":"Key for the Azure storage account."
            }
        },
        "triggerStartTime": {
            "type": "string",
            "metadata": {
                "description": "Start time for the trigger."
            }
        },
        "triggerEndTime": {
            "type": "string",
            "metadata": {
                "description": "End time for the trigger."
            }
        }
    },      
    "variables":{  
        "factoryId":"[concat('Microsoft.DataFactory/factories/', parameters('dataFactoryName'))]"
    },
    "resources":[  
        {  
            "name":"[parameters('dataFactoryName')]",
            "apiVersion":"2018-06-01",
            "type":"Microsoft.DataFactory/factories",
            "location":"[parameters('dataFactoryLocation')]",
            "identity":{  
                "type":"SystemAssigned"
            },
            "resources":[  
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateStorageLinkedService')]",
                    "type":"Microsoft.DataFactory/factories/linkedServices",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "annotations":[  

                        ],
                        "type":"AzureBlobStorage",
                        "typeProperties":{  
                            "connectionString":"[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetIn')]",
                    "type":"Microsoft.DataFactory/factories/datasets",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "linkedServiceName":{  
                            "referenceName":"ArmtemplateStorageLinkedService",
                            "type":"LinkedServiceReference"
                        },
                        "annotations":[  

                        ],
                        "type":"Binary",
                        "typeProperties":{  
                            "location":{  
                                "type":"AzureBlobStorageLocation",
                                "fileName":"emp.txt",
                                "folderPath":"input",
                                "container":"adftutorial"
                            }
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetOut')]",
                    "type":"Microsoft.DataFactory/factories/datasets",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "linkedServiceName":{  
                            "referenceName":"ArmtemplateStorageLinkedService",
                            "type":"LinkedServiceReference"
                        },
                        "annotations":[  

                        ],
                        "type":"Binary",
                        "typeProperties":{  
                            "location":{  
                                "type":"AzureBlobStorageLocation",
                                "folderPath":"output",
                                "container":"adftutorial"
                            }
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateSampleCopyPipeline')]",
                    "type":"Microsoft.DataFactory/factories/pipelines",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "activities":[  
                            {  
                                "name":"MyCopyActivity",
                                "type":"Copy",
                                "dependsOn":[  

                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  

                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"BinarySource",
                                        "storeSettings":{  
                                            "type":"AzureBlobStorageReadSettings",
                                            "recursive":true
                                        }
                                    },
                                    "sink":{  
                                        "type":"BinarySink",
                                        "storeSettings":{  
                                            "type":"AzureBlobStorageWriteSettings"
                                        }
                                    },
                                    "enableStaging":false
                                },
                                "inputs":[  
                                    {  
                                        "referenceName":"ArmtemplateTestDatasetIn",
                                        "type":"DatasetReference",
                                        "parameters":{  

                                        }
                                    }
                                ],
                                "outputs":[  
                                    {  
                                        "referenceName":"ArmtemplateTestDatasetOut",
                                        "type":"DatasetReference",
                                        "parameters":{  

                                        }
                                    }
                                ]
                            }
                        ],
                        "annotations":[  

                        ]
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetIn')]",
                        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetOut')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmTemplateTestTrigger')]",
                    "type":"Microsoft.DataFactory/factories/triggers",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "annotations":[  

                        ],
                        "runtimeState":"Started",
                        "pipelines":[  
                            {  
                                "pipelineReference":{  
                                    "referenceName":"ArmtemplateSampleCopyPipeline",
                                    "type":"PipelineReference"
                                },
                                "parameters":{  

                                }
                            }
                        ],
                        "type":"ScheduleTrigger",
                        "typeProperties":{  
                            "recurrence":{  
                                "frequency":"Hour",
                                "interval":1,
                                "startTime":"[parameters('triggerStartTime')]",
                                "endTime":"[parameters('triggerEndTime')]",
                                "timeZone":"UTC"
                            }
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/pipelines/ArmtemplateSampleCopyPipeline')]"
                    ]
                }
            ]
        }
    ]
}
```
## <a name="parameters-json"></a>JSON-bestand met parameters

Maak een JSON-bestand met de naam **ADFTutorialARM-Parameters.json** dat parameters voor de Azure Resource Manager-sjabloon bevat.

> [!IMPORTANT]
> - Geef de naam en sleutel van uw Azure Storage-account op voor de parameters **storageAccountName** en **storageAccountKey** in dit parameterbestand. U hebt de container adftutorial gemaakt en het voorbeeldbestand (emp.txt) geüpload naar de invoermap in deze Azure-blobopslag.
> - Geef een wereldwijd unieke naam voor de data factory op voor de parameter **dataFactoryName**. Bijvoorbeeld: ARMTutorialFactoryJohnDoe11282017.
> - Geef voor **triggerStartTime** de huidige dag op in de notatie: `2019-09-08T00:00:00`.
> - Geef voor **triggerEndTime** de volgende dag op in de notatie: `2019-09-09T00:00:00`. U kunt ook de huidige UTC-tijd controleren en de volgende een of twee uur opgeven als eindtijd. Als de huidige UTC-tijd bijvoorbeeld 1:32 AM is, geeft u `2019-09-09:03:00:00` op als eindtijd. In dit geval voert de trigger de pijplijn tweemaal uit (om 2 uur en om 3 uur).

```json
{  
    "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":"1.0.0.0",
    "parameters":{  
        "dataFactoryName":{  
            "value":"<datafactoryname>"
        },
        "dataFactoryLocation":{  
            "value":"East US"
        },
        "storageAccountName":{  
            "value":"<yourstorageaccountname>"
        },
        "storageAccountKey":{  
            "value":"<yourstorageaccountkey>"
        },
        "triggerStartTime":{  
            "value":"2019-09-08T11:00:00"
        },
        "triggerEndTime":{  
            "value":"2019-09-08T14:00:00"
        }
    }
}
```

> [!IMPORTANT]
> Mogelijk beschikt u over afzonderlijke JSON-bestanden met parameters voor ontwikkel-, test- en productieomgevingen die u met dezelfde JSON-sjabloon voor Data Factory kunt gebruiken. U kunt met behulp van een Power Shell-script het implementeren van Data Factory-entiteiten in deze omgevingen automatiseren.

## <a name="deploy-data-factory-entities"></a>Data Factory-entiteiten implementeren

Voer in Power shell de volgende opdracht uit om Data Factory entiteiten in uw resource groep te implementeren (in dit geval moet u ADFTutorialResourceGroup als voor beeld nemen) met behulp van de Resource Manager-sjabloon die u eerder in deze Quick Start hebt gemaakt.

```powershell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFTutorial\ADFTutorialARM.json -TemplateParameterFile C:\ADFTutorial\ADFTutorialARM-Parameters.json
```

De uitvoer ziet eruit als in het volgende voorbeeld:

```console
DeploymentName          : MyARMDeployment
ResourceGroupName       : ADFTutorialResourceGroup
ProvisioningState       : Succeeded
Timestamp               : 9/8/2019 10:52:29 AM
Mode                    : Incremental
TemplateLink            : 
Parameters              : 
                          Name                   Type                       Value     
                          =====================  =========================  ==========
                          dataFactoryName        String                     <data factory name>
                          dataFactoryLocation    String                     East US   
                          storageAccountName     String                     <storage account name>
                          storageAccountKey      SecureString                         
                          triggerStartTime       String                     9/8/2019 11:00:00 AM
                          triggerEndTime         String                     9/8/2019 2:00:00 PM
                          
Outputs                 : 
DeploymentDebugLogLevel : 
```

## <a name="start-the-trigger"></a>De trigger starten

De sjabloon implementeert de volgende Data Factory-entiteiten:

- Een gekoppelde Azure Storage-service
- Binaire gegevens sets (invoer en uitvoer)
- Pijplijn met een kopieeractiviteit
- Trigger om de pijplijn te activeren

De geïmplementeerde trigger is gestopt. Een van de manieren om de trigger te starten, is met de Power shell **-cmdlet start-AzDataFactoryV2Trigger** . De volgende procedure bevat gedetailleerde stappen:

1. Maak in het PowerShell-venster een variabele voor de naam van de resourcegroep. Kopieer de volgende opdracht naar het PowerShell-venster en druk op ENTER. Als u een andere naam voor de resource groep hebt opgegeven voor de opdracht New-AzResourceGroupDeployment, werkt u de waarde hier bij.

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```
2. Maak een variabele voor de naam van de data factory. Geef dezelfde naam op die u hebt opgegeven in het bestand ADFTutorialARM-Parameters.json.

    ```powershell
    $dataFactoryName = "<yourdatafactoryname>"
    ```
3. Stel een variabele in voor de naam van de trigger. De naam van de trigger is via code vastgelegd in het Resource Manager-sjabloonbestand (ADFTutorialARM.json).

    ```powershell
    $triggerName = "ArmTemplateTestTrigger"
    ```
4. Haal de **status van de trigger** op door de volgende PowerShell-opdracht uit te voeren nadat u de naam van uw data factory en trigger hebt opgegeven:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

    Hier volgt een voorbeeld van uitvoer:

    ```json

    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFQuickstartsDataFactory0905
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Stopped
    ```
    
    Zoals u ziet, is de runtimestatus van de trigger **Gestopt**.
5. **Start de trigger**. De trigger voert de pijplijn die in de sjabloon is gedefinieerd, uit op het hele uur. Dat wil zeggen dat als u deze opdracht uitvoert om 14:25 uur, de trigger de pijplijn voor het eerst uitvoert om 15:00 uur. Vervolgens wordt de pijplijn elk uur uitgevoerd totdat de opgegeven eindtijd voor de trigger is bereikt.

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Hier volgt een voorbeeld van uitvoer:
    
    ```console
    Confirm
    Are you sure you want to start trigger 'ArmTemplateTestTrigger' in data factory 'ARMFactory1128'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
    True
    ```
6. Controleer of de trigger is gestart door de opdracht Get-AzDataFactoryV2Trigger opnieuw uit te voeren.

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Hier volgt een voorbeeld van uitvoer:
    
    ```console
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFQuickstartsDataFactory0905
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Started
    ```

## <a name="monitor-the-pipeline"></a>De pijplijn bewaken

1. Nadat u zich hebt aangemeld bij [Azure Portal](https://portal.azure.com/), klikt u op **Alle services**, zoekt u met een trefwoord zoals **gegeve**, en selecteert u **Gegevensfabrieken**.

2. Klik op de pagina **Data factory's** op de data factory die u hebt gemaakt. Filter de lijst zo nodig op de naam van uw data factory.

3. Klik op de pagina Data Factory op de tegel **&-monitor** .

4. Op de pagina **aan de slag** selecteert u het **tabblad Monitor**.  pipeline-](media/doc-common-process/get-started-page-monitor-button.png) ![controleren

    > [!IMPORTANT]
    > Zoals u ziet, wordt de pijplijn alleen uitgevoerd op het hele uur (bijvoorbeeld om 4:00 uur, 5:00 uur, 6:00 uur enz.). Klik op **Vernieuwen** op de werkbalk om de lijst te vernieuwen wanneer het volgende uur wordt bereikt.

5. Klik op de koppeling **uitvoeringen van activiteit weer geven** in de kolom **acties** .

    ![Koppeling voor pijplijnacties](media/quickstart-create-data-factory-resource-manager-template/pipeline-actions-link.png)

6. U ziet de uitvoering van de activiteiten die zijn gekoppeld aan de pijplijnuitvoering. In deze QuickStart heeft de pijplijn slechts één activiteit en wel van het type Kopiëren. Daarom ziet u een uitvoering voor die activiteit.

    ![Uitvoering van activiteiten](media/quickstart-create-data-factory-resource-manager-template/activity-runs.png)
7. Klik op de koppeling **uitvoer** onder acties kolom. U ziet de uitvoer van de kopieerbewerking in het venster **Uitvoer**. Klik op de knop Maximaliseren om de volledige uitvoer te bekijken. U kunt het gemaximaliseerde uitvoervenster sluiten.

8. Stop de trigger zodra u ziet dat de uitvoering is geslaagd/mislukt. De trigger voert de pijplijn eenmaal per uur uit. Bij elke uitvoering kopieert de pijplijn hetzelfde bestand uit de invoermap naar de uitvoermap. Als u de trigger wilt stoppen, voert u de volgende opdracht uit in het PowerShell-venster.
    
    ```powershell
    Stop-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)]

## <a name="data-factory-entities-in-the-template"></a> JSON-definities voor entiteiten

De volgende Data Factory-entiteiten worden in de JSON-sjabloon gedefinieerd:

- [Een gekoppelde Azure Storage-service](#azure-storage-linked-service)
- [Gegevensset voor binaire invoer](#binary-input-dataset)
- [Gegevensset voor binaire uitvoer](#binary-output-dataset)
- [De gegevenspijplijn met een kopieerbewerking](#data-pipeline)
- [Trigger](#trigger)

#### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service

De AzureStorageLinkedService koppelt uw Azure-opslagaccount aan de gegevensfactory. U hebt een container gemaakt en gegevens naar dit opslagaccount geüpload als onderdeel van de vereisten. In deze sectie geeft u de naam en sleutel van uw Azure Storage-account op. Zie [Een gekoppelde Azure Storage-service](connector-azure-blob-storage.md#linked-service-properties) voor meer informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van een gekoppelde Azure Storage-service.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateStorageLinkedService')]",
    "type":"Microsoft.DataFactory/factories/linkedServices",
    "apiVersion":"2018-06-01",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]"
    ]
}
```

De tekenreeks connectionString maakt gebruik van de parameters storageAccountName en storageAccountKey. De waarden voor deze parameters worden doorgegeven met behulp van een configuratiebestand. De definitie maakt ook gebruik van de variabelen azureStorageLinkedService en dataFactoryName die zijn gedefinieerd in de sjabloon.

#### <a name="binary-input-dataset"></a>Gegevensset voor binaire invoer

De gekoppelde Azure Storage-service geeft de verbindingsreeks op die de Data Factory-service tijdens runtime gebruikt om verbinding te maken met uw Azure-opslagaccount. In definitie van een binaire gegevensset geeft u de namen van de BLOB-container, map en het bestand op dat de invoer gegevens bevat. Zie [Eigenschappen van binaire gegevensset](format-binary.md#dataset-properties) voor meer informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van een binaire gegevensset.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetIn')]",
    "type":"Microsoft.DataFactory/factories/datasets",
    "apiVersion":"2018-06-01",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"ArmtemplateStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "fileName":"emp.txt",
                "folderPath":"input",
                "container":"adftutorial"
            }
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
    ]
}
```

#### <a name="binary-output-dataset"></a>Gegevensset voor binaire uitvoer

U geeft de naam op van de map in de Azure-blobopslag die de gekopieerde gegevens uit de invoermap bevat. Zie [Eigenschappen van binaire gegevensset](format-binary.md#dataset-properties) voor meer informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van een binaire gegevensset.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetOut')]",
    "type":"Microsoft.DataFactory/factories/datasets",
    "apiVersion":"2018-06-01",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"ArmtemplateStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "folderPath":"output",
                "container":"adftutorial"
            }
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
    ]
}
```

#### <a name="data-pipeline"></a>Gegevenspijplijn

U definieert een pijp lijn die gegevens van de ene binaire gegevensset naar een andere binaire gegevensset kopieert. Zie [JSON-bestand voor een pijplijn](concepts-pipelines-activities.md#pipeline-json) voor beschrijvingen van JSON-elementen die worden gebruikt voor het definiëren van een pijplijn in dit voorbeeld.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateSampleCopyPipeline')]",
    "type":"Microsoft.DataFactory/factories/pipelines",
    "apiVersion":"2018-06-01",
    "properties":{  
        "activities":[  
            {  
                "name":"MyCopyActivity",
                "type":"Copy",
                "dependsOn":[  

                ],
                "policy":{  
                    "timeout":"7.00:00:00",
                    "retry":0,
                    "retryIntervalInSeconds":30,
                    "secureOutput":false,
                    "secureInput":false
                },
                "userProperties":[  

                ],
                "typeProperties":{  
                    "source":{  
                        "type":"BinarySource",
                        "storeSettings":{  
                            "type":"AzureBlobStorageReadSettings",
                            "recursive":true
                        }
                    },
                    "sink":{  
                        "type":"BinarySink",
                        "storeSettings":{  
                            "type":"AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging":false
                },
                "inputs":[  
                    {  
                        "referenceName":"ArmtemplateTestDatasetIn",
                        "type":"DatasetReference",
                        "parameters":{  

                        }
                    }
                ],
                "outputs":[  
                    {  
                        "referenceName":"ArmtemplateTestDatasetOut",
                        "type":"DatasetReference",
                        "parameters":{  

                        }
                    }
                ]
            }
        ],
        "annotations":[  

        ]
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetIn')]",
        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetOut')]"
    ]
}
```

#### <a name="trigger"></a>Trigger

U definieert een trigger die de pijplijn eenmaal per uur uitvoert. De geïmplementeerde trigger is gestopt. Start de trigger met behulp van de cmdlet **Start-AzDataFactoryV2Trigger** . Zie het artikel [Pijplijnen uitvoeren en triggers](concepts-pipeline-execution-triggers.md#triggers) voor meer informatie over triggers.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmTemplateTestTrigger')]",
    "type":"Microsoft.DataFactory/factories/triggers",
    "apiVersion":"2018-06-01",
    "properties":{  
        "annotations":[  

        ],
        "runtimeState":"Started",
        "pipelines":[  
            {  
                "pipelineReference":{  
                    "referenceName":"ArmtemplateSampleCopyPipeline",
                    "type":"PipelineReference"
                },
                "parameters":{  

                }
            }
        ],
        "type":"ScheduleTrigger",
        "typeProperties":{  
            "recurrence":{  
                "frequency":"Hour",
                "interval":1,
                "startTime":"[parameters('triggerStartTime')]",
                "endTime":"[parameters('triggerEndTime')]",
                "timeZone":"UTC"
            }
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/pipelines/ArmtemplateSampleCopyPipeline')]"
    ]
}
```

## <a name="reuse-the-template"></a>De sjabloon hergebruiken

U hebt in de zelfstudie een sjabloon voor het definiëren van Data Factory-entiteiten en een sjabloon voor het doorgeven van waarden voor parameters gemaakt. Als u dezelfde sjabloon wilt gebruiken voor het implementeren van Data Factory-entiteiten in verschillende omgevingen, maakt u een parameterbestand voor elke omgeving en gebruikt u dit bij het implementeren in die omgeving.

Voorbeeld:

```powershell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```

De eerste opdracht maakt gebruik van het parameterbestand voor de ontwikkelomgeving, de tweede voor de testomgeving en de derde voor de productieomgeving.

U kunt de sjabloon ook hergebruiken om herhaalde taken uit te voeren. U kunt bijvoorbeeld veel data factory's maken met een of meer pijplijnen die dezelfde logica implementeren, waarbij elke data factory echter gebruikmaakt van andere Azure Storage-accounts. In dit scenario gebruikt u dezelfde sjabloon in dezelfde omgeving (voor het ontwikkelen, testen of de productie) met andere parameterbestanden om de gegevensfactory's te maken.

## <a name="next-steps"></a>Volgende stappen

Met de pijplijn in dit voorbeeld worden gegevens gekopieerd van de ene naar de andere locatie in Azure Blob Storage. Doorloop de [zelfstudies](tutorial-copy-data-dot-net.md) voor meer informatie over het gebruiken van Data Factory in andere scenario's.
