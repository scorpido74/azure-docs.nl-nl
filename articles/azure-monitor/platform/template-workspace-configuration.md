---
title: Azure Resource Manager-sjabloon voor de werkruimte Log Analytics
description: U Azure Resource Manager-sjablonen gebruiken om Logboekanalysewerkruimten te maken en te configureren.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2020
ms.openlocfilehash: 60f85a30815bc1bace409b50af6332bb6622d7ca
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477977"
---
# <a name="manage-log-analytics-workspace-using-azure-resource-manager-templates"></a>Logboekanalysewerkruimte beheren met Azure Resource Manager-sjablonen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

U [Azure Resource Manager-sjablonen](../../azure-resource-manager/templates/template-syntax.md) gebruiken om Logboekanalysewerkruimten te maken en te configureren in Azure Monitor. Voorbeelden van de taken die u met sjablonen uitvoeren, zijn:

* Een werkruimte maken, inclusief het instellen van de prijsniveau en capaciteitsreservering
* Een oplossing toevoegen
* Opgeslagen zoekopdrachten maken
* Een computergroep maken
* Verzameling van IIS-logboeken inschakelen vanaf computers waarop de Windows-agent is geïnstalleerd
* Prestatiemeteritems verzamelen van Linux- en Windows-computers
* Gebeurtenissen verzamelen van syslog op Linux-computers 
* Gebeurtenissen verzamelen uit Logboeken van Windows-gebeurtenissen
* Aangepaste logboeken van Windows-computer verzamelen
* De logboekanalyseagent toevoegen aan een virtuele Azure-machine
* Logboekanalyses configureren om gegevens te indexeren die zijn verzameld met Azure-diagnose

In dit artikel worden sjabloonvoorbeelden gegeven die een aantal van de configuratie illustreren die u met sjablonen uitvoeren.

## <a name="api-versions"></a>API-versies

In de volgende tabel vindt u de API-versie voor de resources die in dit voorbeeld worden gebruikt.

| Resource | Resourcetype | API-versie |
|:---|:---|:---|
| Werkruimte   | werkruimten    | 2017-03-15-preview |
| Search      | opgeslagenzoekopdrachten | 2015-03-20 |
| Gegevensbron | Datasources   | 2015-11-01-preview |
| Oplossing    | oplossingen     | 2015-11-01-preview |

## <a name="create-a-log-analytics-workspace"></a>Een Log Analytics-werkruimte maken

In het volgende voorbeeld wordt een werkruimte gemaakt met behulp van een sjabloon van uw lokale machine. De JSON-sjabloon is zo geconfigureerd dat alleen de naam en locatie van de nieuwe werkruimte vereist zijn. Het gebruikt waarden die zijn opgegeven voor andere werkruimteparameters, zoals [de toegangscontrolemodus,](design-logs-deployment.md#access-control-mode)de prijscategorie, de retentie en het capaciteitsreserveringsniveau.

> [!WARNING]
> Met de volgende sjabloon wordt een werkruimte Log Analytics gemaakt en wordt het verzamelen van gegevens geconfigureerd. Dit kan uw factureringsinstellingen wijzigen. Controleer [Gebruik en kosten beheren met Azure Monitor-logboeken](manage-cost-storage.md) om inzicht te krijgen in facturering voor gegevens die zijn verzameld in een werkruimte log Analytics voordat u deze toepast in uw Azure-omgeving.

Voor capaciteitsreservering definieert u een geselecteerde capaciteitsreservering voor het `CapacityReservation` opnemen van gegevens door `capacityReservationLevel`de SKU en een waarde in GB voor de eigenschap op te geven. In de volgende lijst worden de ondersteunde waarden en het gedrag beschreven bij het configureren ervan.

- Zodra u de reserveringslimiet hebt ingesteld, u niet binnen 31 dagen overschakelen naar een andere SKU.

- Zodra u de reserveringswaarde instelt, u deze alleen binnen 31 dagen verhogen.

- U de waarde `capacityReservationLevel` van alleen in veelvouden van 100 instellen, met een maximale waarde van 50000.

- Als u het reserveringsniveau verhoogt, wordt de timer opnieuw ingesteld en u deze niet nog 31 dagen wijzigen vanaf deze update.  

- Als u een andere eigenschap voor de werkruimte wijzigt, maar de reserveringslimiet op hetzelfde niveau behoudt, wordt de timer niet opnieuw ingesteld. 

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
   >Gebruik deze eigenschappen voor capaciteitsreserveringsinstellingen onder 'sku':
   >* "naam": "CapacityReservation",
   >* "capacityReservationLevel": 100

2. Bewerk de sjabloon om aan uw vereisten te voldoen. Overweeg een [resourcebeheerbestand te](../../azure-resource-manager/templates/parameter-files.md) maken in plaats van parameters door te geven als inlinewaarden. Bekijk [de sjabloonverwijzing microsoft.operationalInsights/workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) om te zien welke eigenschappen en waarden worden ondersteund. 

3. Sla dit bestand op als **deploylaworkspacetemplate.json** in een lokale map.

4. U kunt deze sjabloon nu implementeren. U gebruikt PowerShell of de opdrachtregel om de werkruimte te maken en de naam en locatie van de werkruimte op te geven als onderdeel van de opdracht. De naam van de werkruimte moet wereldwijd uniek zijn voor alle Azure-abonnementen.

   * Voor PowerShell gebruiken de volgende opdrachten uit de map met de sjabloon:
   
        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json -workspaceName <workspace-name> -location <location>
        ```

   * Gebruik voor opdrachtregel de volgende opdrachten uit de map met de sjabloon:

        ```cmd
        azure config mode arm
        azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json --workspaceName <workspace-name> --location <location>
        ```

De implementatie kan enkele minuten duren. Wanneer het is voltooid, ziet u een bericht dat vergelijkbaar is met het volgende dat het resultaat bevat:<br><br> ![Voorbeeldresultaat wanneer de implementatie is voltooid](./media/template-workspace-configuration/template-output-01.png)

## <a name="configure-a-log-analytics-workspace"></a>Een werkruimte Log Analytics configureren

In het volgende voorbeeld van een sjabloon ziet u hoe u:

1. Oplossingen toevoegen aan de werkruimte
2. Opgeslagen zoekopdrachten maken. Om ervoor te zorgen dat implementaties opgeslagen zoekopdrachten niet per ongeluk overschrijven, moet een eTag-eigenschap worden toegevoegd in de bron 'savedSearches' om de idempotentie van opgeslagen zoekopdrachten te overschrijven en te behouden.
3. Een computergroep maken
4. Verzameling van IIS-logboeken inschakelen vanaf computers waarop de Windows-agent is geïnstalleerd
5. Logische schijfperftellers verzamelen van Linux-computers (% Gebruikte Inodes; Gratis Megabytes; % gebruikte ruimte; Schijfoverdrachten per seconde; Schijfleest/sec; Schijfschrijft/sec)
6. Syslog-gebeurtenissen verzamelen vanaf Linux-computers
7. Fout- en waarschuwingsgebeurtenissen verzamelen in het logboek van toepassingsgebeurtenis van Windows-computers
8. Beschikbare Mbytes-prestatiemeter items verzamelen vanaf Windows-computers
9. IIS-logboeken en Windows-gebeurtenislogboeken verzamelen die zijn geschreven door Azure-diagnose smaken naar een opslagaccount
10. Aangepaste logboeken van Windows-computer verzamelen

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
        "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
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
            "category": "VMSS",
            "eTag": "*",
            "displayName": "VMSS Instance Count",
            "query": "Event | where Source == \"ServiceFabricNodeBootstrapAgent\" | summarize AggregatedValue = count() by Computer",
            "version": 1
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

### <a name="deploying-the-sample-template"></a>De voorbeeldsjabloon implementeren

Ga als volgende over de volgende keer dat u de voorbeeldsjabloon wilt implementeren:

1. Het bijgevoegde voorbeeld opslaan in een bestand, bijvoorbeeld`azuredeploy.json` 
2. De sjabloon bewerken om de gewenste configuratie te hebben
3. PowerShell of de opdrachtregel gebruiken om de sjabloon te implementeren

#### <a name="powershell"></a>PowerShell

```powershell
New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile azuredeploy.json
```

#### <a name="command-line"></a>Opdrachtregel

```cmd
azure config mode arm
azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile azuredeploy.json
```

## <a name="example-resource-manager-templates"></a>Voorbeeld van Resource Manager-sjablonen

De azure quickstart-sjabloongalerie bevat verschillende sjablonen voor Logboekanalyse, waaronder:

* [Een virtuele machine met Windows implementeren met de VM-extensie Log Analytics](https://azure.microsoft.com/documentation/templates/201-oms-extension-windows-vm/)
* [Een virtuele machine met Linux implementeren met de LOG Analytics VM-extensie](https://azure.microsoft.com/documentation/templates/201-oms-extension-ubuntu-vm/)
* [Azure-siteherstel bewaken met behulp van een bestaande Logboekanalysewerkruimte](https://azure.microsoft.com/documentation/templates/asr-oms-monitoring/)
* [Azure Web Apps bewaken met behulp van een bestaande Log Analytics-werkruimte](https://azure.microsoft.com/documentation/templates/101-webappazure-oms-monitoring/)
* [Een bestaand opslagaccount toevoegen aan Log Analytics](https://azure.microsoft.com/resources/templates/oms-existing-storage-account/)

## <a name="next-steps"></a>Volgende stappen

* [Windows-agent implementeren in Azure VM's met behulp van resourcebeheersjabloon](../../virtual-machines/extensions/oms-windows.md).

* [Linux-agent implementeren in Azure VM's met behulp van resourcebeheersjabloon](../../virtual-machines/extensions/oms-linux.md).
