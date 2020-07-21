---
title: Azure Resource Manager sjabloon voor Log Analytics werk ruimte
description: U kunt Azure Resource Manager sjablonen gebruiken om Log Analytics-werk ruimten te maken en te configureren.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2020
ms.openlocfilehash: 240a261f8dd401f36ef763e4c1274a1c0760f2dd
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515645"
---
# <a name="manage-log-analytics-workspace-using-azure-resource-manager-templates"></a>Log Analytics-werk ruimte beheren met Azure Resource Manager sjablonen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

U kunt [Azure Resource Manager sjablonen](../../azure-resource-manager/templates/template-syntax.md) gebruiken om log Analytics-werk ruimten in azure monitor te maken en te configureren. Voor beelden van de taken die u kunt uitvoeren met sjablonen zijn:

* Een werk ruimte maken, inclusief het instellen van de prijs categorie en capaciteits reservering
* Een oplossing toevoegen
* Opgeslagen Zoek opdrachten maken
* Een computer groep maken
* Verzamelen van IIS-logboeken inschakelen op computers waarop de Windows-agent is geïnstalleerd
* Prestatie meter items verzamelen van Linux-en Windows-computers
* Gebeurtenissen verzamelen van syslog op Linux-computers 
* Gebeurtenissen verzamelen van Windows-gebeurtenis logboeken
* Aangepaste logboeken van Windows-computer verzamelen
* De log Analytics-agent toevoegen aan een virtuele machine van Azure
* Log Analytics configureren voor het indexeren van gegevens die zijn verzameld met Azure Diagnostics

In dit artikel vindt u voor beelden van sjablonen die een deel van de configuratie illustreren die u kunt uitvoeren met sjablonen.

## <a name="api-versions"></a>API-versies

De volgende tabel geeft een overzicht van de API-versie voor de resources die in dit voor beeld worden gebruikt.

| Resource | Resourcetype | API-versie |
|:---|:---|:---|
| Werkruimte   | workspaces    | 2017-03-15-preview |
| Search      | savedSearches | 2015-03-20 |
| Gegevensbron | gegevens bronnen   | 2015-11-01-preview |
| Oplossing    | oplossingen     | 2015-11-01-preview |

## <a name="create-a-log-analytics-workspace"></a>Een Log Analytics-werkruimte maken

In het volgende voor beeld wordt een werk ruimte gemaakt op basis van een sjabloon van uw lokale computer. De JSON-sjabloon is zo geconfigureerd dat alleen de naam en locatie van de nieuwe werk ruimte zijn vereist. Het gebruikt waarden die zijn opgegeven voor andere werkruimte parameters, zoals de [toegangs beheer modus](design-logs-deployment.md#access-control-mode), de prijs categorie, de retentie en het capaciteits reserverings niveau.

> [!WARNING]
> Met de volgende sjabloon maakt u een Log Analytics-werk ruimte en configureert u de gegevens verzameling. Hierdoor kunnen uw facturerings instellingen worden gewijzigd. Bekijk het beheren van het [gebruik en de kosten met Azure monitor logboeken](manage-cost-storage.md) om inzicht te krijgen in de facturering van gegevens die zijn verzameld in een log Analytics werk ruimte voordat u deze toepast in uw Azure-omgeving.

Voor de capaciteits reservering definieert u een geselecteerde capaciteits reservering voor het opnemen van gegevens door de SKU `CapacityReservation` en een waarde in GB voor de eigenschap op te geven `capacityReservationLevel` . De volgende lijst bevat de ondersteunde waarden en het gedrag bij het configureren ervan.

- Zodra u de reserverings limiet hebt ingesteld, kunt u niet binnen 31 dagen overschakelen op een andere SKU.

- Zodra u de reserverings waarde hebt ingesteld, kunt u deze alleen binnen 31 dagen verg Roten.

- U kunt alleen de waarde van `capacityReservationLevel` in veelvouden van 100 instellen, met een maximale waarde van 50000.

- Als u het reserverings niveau verhoogt, wordt de timer opnieuw ingesteld en kunt u deze niet meer 31 dagen uit deze update wijzigen.  

- Als u een andere eigenschap voor de werk ruimte wijzigt, maar de reserverings limiet op hetzelfde niveau behoudt, wordt de timer niet opnieuw ingesteld. 

### <a name="create-and-deploy-template"></a>Een sjabloon maken en implementeren

1. Kopieer en plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
      "sku": {
        "type": "string",
        "allowedValues": [
          "pergb2018",
          "Free",
          "Standalone",
          "PerNode",
          "Standard",
          "Premium"
          ],
        "defaultValue": "pergb2018",
        "metadata": {
        "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
        }
      },
      "location": {
        "type": "String",
        "allowedValues": [
        "australiacentral", 
        "australiaeast", 
        "australiasoutheast", 
        "brazilsouth",
        "canadacentral", 
        "centralindia", 
        "centralus", 
        "eastasia", 
        "eastus", 
        "eastus2", 
        "francecentral", 
        "japaneast", 
        "koreacentral", 
        "northcentralus", 
        "northeurope", 
        "southafricanorth", 
        "southcentralus", 
        "southeastasia", 
        "uksouth", 
        "ukwest", 
        "westcentralus", 
        "westeurope", 
        "westus", 
        "westus2" 
        ],
      "metadata": {
        "description": "Specifies the location in which to create the workspace."
        }
      }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]"
                },
                "retentionInDays": 120,
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            }
          }
       ]
    }
    ```

   >[!NOTE]
   >Voor instellingen voor capaciteits reservering gebruikt u deze eigenschappen onder SKU:
   >* "naam": "CapacityReservation",
   >* "capacityReservationLevel": 100

2. Bewerk de sjabloon om te voldoen aan uw vereisten. Overweeg om een [Resource Manager-parameter bestand](../../azure-resource-manager/templates/parameter-files.md) te maken in plaats van para meters door te geven als inline-waarden. Raadpleeg de naslag informatie over [micro soft. OperationalInsights/werkruimte sjablonen](/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) als u wilt weten welke eigenschappen en waarden worden ondersteund. 

3. Sla dit bestand als **deploylaworkspacetemplate.jsop in** een lokale map.

4. U kunt deze sjabloon nu implementeren. U kunt Power shell of de opdracht regel gebruiken om de werk ruimte te maken, waarbij u de naam en locatie van de werk ruimte opgeeft als onderdeel van de opdracht. De naam van de werk ruimte moet globaal uniek zijn in alle Azure-abonnementen.

   * Gebruik voor Power shell de volgende opdrachten uit de map met de sjabloon:
   
        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json -workspaceName <workspace-name> -location <location>
        ```

   * Voor de opdracht regel gebruikt u de volgende opdrachten uit de map met de sjabloon:

        ```cmd
        azure config mode arm
        azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json --workspaceName <workspace-name> --location <location>
        ```

De implementatie kan enkele minuten duren. Wanneer de bewerking is voltooid, ziet u een bericht dat lijkt op het volgende:<br><br> ![Voor beeld van resultaat wanneer de implementatie is voltooid](./media/template-workspace-configuration/template-output-01.png)

## <a name="configure-a-log-analytics-workspace"></a>Een Log Analytics-werk ruimte configureren

In het volgende sjabloon voorbeeld ziet u hoe u:

1. Oplossingen toevoegen aan de werk ruimte
2. Opgeslagen Zoek opdrachten maken. Om ervoor te zorgen dat implementaties niet per ongeluk opgeslagen Zoek opdrachten overschrijven, moet een eTag-eigenschap worden toegevoegd aan de resource ' savedSearches ' om de idempotentie van opgeslagen Zoek opdrachten te overschrijven en te onderhouden.
3. Opgeslagen functie maken. De eTag moet worden toegevoegd om de functie override te onderdrukken en idempotentie te onderhouden.
4. Een computer groep maken
5. Verzamelen van IIS-logboeken inschakelen op computers waarop de Windows-agent is geïnstalleerd
6. Prestatie meter items voor logische schijven verzamelen van Linux-computers (% used inodes; Beschik bare mega bytes; Percentage gebruikte ruimte; Schijf overdrachten per seconde; Schijf lezen per seconde; Schrijf bewerkingen per seconde)
7. Syslog-gebeurtenissen verzamelen van Linux-computers
8. Fout-en waarschuwings gebeurtenissen verzamelen uit het toepassings gebeurtenis logboek van Windows-computers
9. Het prestatie meter item geheugen beschik bare Mbytes verzamelen van Windows-computers
10. IIS-logboeken en Windows-gebeurtenis logboeken die door Azure Diagnostics zijn geschreven, verzamelen in een opslag account
11. Aangepaste logboeken van Windows-computer verzamelen

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Workspace name"
      }
    },
    "sku": {
      "type": "string",
      "allowedValues": [
        "PerGB2018",
        "Free",
        "Standalone",
        "PerNode",
        "Standard",
        "Premium"
      ],
      "defaultValue": "pergb2018",
      "metadata": {
        "description": "Pricing tier: pergb2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
      }
    },
    "dataRetention": {
      "type": "int",
      "defaultValue": 30,
      "minValue": 7,
      "maxValue": 730,
      "metadata": {
        "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
      }
    },
    "immediatePurgeDataOn30Days": {
      "type": "bool",
      "defaultValue": "[bool('false')]",
      "metadata": {
        "description": "If set to true, changing retention to 30 days will immediately delete older data. Use this with extreme caution. This only applies when retention is being set to 30 days."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "australiacentral",
        "australiaeast",
        "australiasoutheast",
        "brazilsouth",
        "canadacentral",
        "centralindia",
        "centralus",
        "eastasia",
        "eastus",
        "eastus2",
        "francecentral",
        "japaneast",
        "koreacentral",
        "northcentralus",
        "northeurope",
        "southafricanorth",
        "southcentralus",
        "southeastasia",
        "uksouth",
        "ukwest",
        "westcentralus",
        "westeurope",
        "westus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location in which to create the workspace."
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the storage account with Azure diagnostics output"
      }
    },
    "applicationDiagnosticsStorageAccountResourceGroup": {
      "type": "string",
      "metadata": {
        "description": "The resource group name containing the storage account with Azure diagnostics output"
      }
    },
    "customLogName": {
      "type": "string",
      "metadata": {
        "description": "The custom log name"
      }
    }
  },
  "variables": {
    "Updates": {
      "Name": "[Concat('Updates', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "Updates"
    },
    "AntiMalware": {
      "Name": "[concat('AntiMalware', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "AntiMalware"
    },
    "SQLAssessment": {
      "Name": "[Concat('SQLAssessment', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "SQLAssessment"
    },
    "diagnosticsStorageAccount": "[resourceId(parameters('applicationDiagnosticsStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName'))]"
  },
  "resources": [
    {
      "apiVersion": "2017-03-15-preview",
      "type": "Microsoft.OperationalInsights/workspaces",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "retentionInDays": "[parameters('dataRetention')]",
        "features": {
          "immediatePurgeDataOn30Days": "[parameters('immediatePurgeDataOn30Days')]"
        },
        "sku": {
          "name": "[parameters('sku')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-03-20",
          "name": "VMSS Queries2",
          "type": "savedSearches",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "eTag": "*",
            "category": "VMSS",
            "displayName": "VMSS Instance Count",
            "query": "Event | where Source == \"ServiceFabricNodeBootstrapAgent\" | summarize AggregatedValue = count() by Computer",
            "version": 1
          }
        },
        {
          "apiVersion": "2017-04-26-preview",
          "name": "Cross workspace function",
          "type": "savedSearches",
            "dependsOn": [
             "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "properties": {
              "etag": "*",
              "displayName": "failedLogOnEvents",
              "category": "Security",
              "FunctionAlias": "failedlogonsecurityevents",
              "query": "
                union withsource=SourceWorkspace
                workspace('workspace1').SecurityEvent,
                workspace('workspace2').SecurityEvent,
                workspace('workspace3').SecurityEvent,
                | where EventID == 4625"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsEvent1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "Application",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsPerfCounter1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsPerformanceCounter",
          "properties": {
            "objectName": "Memory",
            "instanceName": "*",
            "intervalSeconds": 10,
            "counterName": "Available MBytes"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleIISLog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "IISLogs",
          "properties": {
            "state": "OnPremiseEnabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslog",
          "properties": {
            "syslogName": "kern",
            "syslogSeverities": [
              {
                "severity": "emerg"
              },
              {
                "severity": "alert"
              },
              {
                "severity": "crit"
              },
              {
                "severity": "err"
              },
              {
                "severity": "warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslogCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslogCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerf1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceObject",
          "properties": {
            "performanceCounters": [
              {
                "counterName": "% Used Inodes"
              },
              {
                "counterName": "Free Megabytes"
              },
              {
                "counterName": "% Used Space"
              },
              {
                "counterName": "Disk Transfers/sec"
              },
              {
                "counterName": "Disk Reads/sec"
              },
              {
                "counterName": "Disk Writes/sec"
              }
            ],
            "objectName": "Logical Disk",
            "instanceName": "*",
            "intervalSeconds": 10
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "dataSources",
          "name": "[concat(parameters('workspaceName'), parameters('customLogName'))]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', '/', parameters('workspaceName'))]"
          ],
          "kind": "CustomLog",
          "properties": {
            "customLogName": "[parameters('customLogName')]",
            "description": "this is a description",
            "extractions": [
              {
                "extractionName": "TimeGenerated",
                "extractionProperties": {
                  "dateTimeExtraction": {
                    "regex": [
                      {
                        "matchIndex": 0,
                        "numberdGroup": null,
                        "pattern": "((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]"
                      }
                    ]
                  }
                },
                "extractionType": "DateTime"
              }
            ],
            "inputs": [
              {
                "location": {
                  "fileSystemLocations": {
                    "linuxFileTypeLogPaths": null,
                    "windowsFileTypeLogPaths": [
                      "[concat('c:\\Windows\\Logs\\',parameters('customLogName'))]"
                    ]
                  }
                },
                "recordDelimiter": {
                  "regexDelimiter": {
                    "matchIndex": 0,
                    "numberdGroup": null,
                    "pattern": "(^.*((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9].*$)"
                  }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerfCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-03-20",
          "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('workspaceName'))]",
          "type": "storageinsightconfigs",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "containers": [
              "wad-iis-logfiles"
            ],
            "tables": [
              "WADWindowsEventLogsTable"
            ],
            "storageAccount": {
              "id": "[variables('diagnosticsStorageAccount')]",
              "key": "[listKeys(variables('diagnosticsStorageAccount'),'2015-06-15').key1]"
            }
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('Updates').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('Updates').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('Updates').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('AntiMalware').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('AntiMalware').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('AntiMalware').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('AntiMalware').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('SQLAssessment').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('SQLAssessment').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('SQLAssessment').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('SQLAssessment').GalleryName)]",
            "promotionCode": ""
          }
        }
      ]
    }
  ],
  "outputs": {
    "workspaceName": {
      "type": "string",
      "value": "[parameters('workspaceName')]"
    },
    "provisioningState": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').provisioningState]"
    },
    "source": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').source]"
    },
    "customerId": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').customerId]"
    },
    "sku": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').sku.name]"
    },
    "retentionInDays": {
      "type": "int",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').retentionInDays]"
    },
    "immediatePurgeDataOn30Days": {
      "type": "bool",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').features.immediatePurgeDataOn30Days]"
    },
    "portalUrl": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').portalUrl]"
    }
  }
}
```

### <a name="deploying-the-sample-template"></a>De voorbeeld sjabloon implementeren

De voorbeeld sjabloon implementeren:

1. Sla het bijgevoegde voor beeld op in een bestand, bijvoorbeeld`azuredeploy.json` 
2. Bewerk de sjabloon voor de gewenste configuratie
3. Power shell of de opdracht regel gebruiken om de sjabloon te implementeren

#### <a name="powershell"></a>PowerShell

```powershell
New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile azuredeploy.json
```

#### <a name="command-line"></a>Opdrachtregel

```cmd
azure config mode arm
azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile azuredeploy.json
```

## <a name="example-resource-manager-templates"></a>Voor beelden van Resource Manager-sjablonen

De Azure Quick Start-sjabloon galerie bevat verschillende sjablonen voor Log Analytics, waaronder:

* [Een virtuele machine met Windows met de extensie Log Analytics VM implementeren](https://azure.microsoft.com/documentation/templates/201-oms-extension-windows-vm/)
* [Een virtuele machine met Linux implementeren met de extensie Log Analytics VM](https://azure.microsoft.com/documentation/templates/201-oms-extension-ubuntu-vm/)
* [Azure Site Recovery bewaken met behulp van een bestaande Log Analytics werk ruimte](https://azure.microsoft.com/documentation/templates/asr-oms-monitoring/)
* [Azure Web Apps bewaken met behulp van een bestaande Log Analytics-werk ruimte](https://azure.microsoft.com/documentation/templates/101-webappazure-oms-monitoring/)
* [Een bestaand opslag account toevoegen aan Log Analytics](https://azure.microsoft.com/resources/templates/oms-existing-storage-account/)

## <a name="next-steps"></a>Volgende stappen

* [Implementeer Windows-agent op Azure vm's met behulp van de Resource Manager-sjabloon](../../virtual-machines/extensions/oms-windows.md).

* [Implementeer een Linux-agent naar Azure vm's met behulp van de Resource Manager-sjabloon](../../virtual-machines/extensions/oms-linux.md).
