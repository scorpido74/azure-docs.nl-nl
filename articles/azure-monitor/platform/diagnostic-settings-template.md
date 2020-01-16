---
title: Een diagnostische instelling maken in azure met behulp van de Resource Manager-sjabloon
description: Diagnostische instellingen maken met een resource manager-sjabloon voor het door sturen van Azure-platform logboeken naar Azure Monitor-logboeken, Azure-opslag of Azure Event Hubs.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 2a171ae89e8314684eddf29f78b9b09bc52f9c9b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977555"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Diagnostische instelling maken in azure met behulp van een resource manager-sjabloon
[Diagnostische instellingen](diagnostic-settings.md) in azure monitor opgeven waar u de [platform logboeken](platform-logs-overview.md) wilt verzenden die worden verzameld door Azure-resources en het Azure-platform waarvan ze afhankelijk zijn. In dit artikel vindt u details en voor beelden voor het gebruik van een [Azure Resource Manager sjabloon](../../azure-resource-manager/templates/template-syntax.md) voor het maken en configureren van diagnostische instellingen voor het verzamelen van platform logboeken op verschillende locaties.

> [!NOTE]
> Omdat u geen [Diagnostische instelling](diagnostic-settings.md) voor het Azure-activiteiten logboek kunt maken met behulp van Power shell of cli zoals Diagnostische instellingen voor andere Azure-resources, maakt u een resource manager-sjabloon voor het activiteiten logboek met behulp van de informatie in dit artikel en implementeert u de sjabloon met Power shell of cli.

## <a name="deployment-methods"></a>Implementatie methoden
U kunt Resource Manager-sjablonen implementeren met een wille keurige geldige methode, waaronder Power shell en CLI. Diagnostische instellingen voor het activiteiten logboek moeten worden geïmplementeerd op een abonnement met `az deployment create` voor CLI of `New-AzDeployment` voor Power shell. Diagnostische instellingen voor resource logboeken moeten worden geïmplementeerd op een resource groep met `az group deployment create` voor CLI of `New-AzResourceGroupDeployment` voor Power shell.

Zie [resources implementeren met Resource Manager-sjablonen en resources Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) en [implementeren met Resource Manager-sjablonen en Azure cli](../../azure-resource-manager/templates/deploy-cli.md) voor meer informatie. 





## <a name="resource-logs"></a>Resourcelogboeken
Voor resource logboeken voegt u een resource van het type `<resource namespace>/providers/diagnosticSettings` toe aan de sjabloon. De sectie Properties is gebaseerd op de indeling die wordt beschreven in [Diagnostische instellingen-maken of bijwerken](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). Geef een `category` op in de sectie `logs` voor elk van de categorieën die geldig zijn voor de resource die u wilt verzamelen. Voeg de eigenschap `metrics` toe voor het verzamelen van metrische gegevens voor resources aan dezelfde doelen als de [resource metrische gegevens ondersteunt](metrics-supported.md).

Hieronder volgt een sjabloon waarmee een resource logboek categorie voor een bepaalde resource wordt verzameld voor een Log Analytics-werk ruimte, een opslag account en Event Hub.

```json
"resources": [
  {
    "type": "/<resource namespace>/providers/diagnosticSettings",
    "name": "[concat(parameters('resourceName'),'/microsoft.insights/', parameters('settingName'))]",
    "dependsOn": [
      "[<resource Id for which resource logs will be enabled>]"
    ],
    "apiVersion": "2017-05-01-preview",
    "properties": {
      "name": "[parameters('settingName')]",
      "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
      "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
      "eventHubName": "[parameters('eventHubName')]",
      "workspaceId": "[parameters('workspaceId')]",
      "logs": [
        {
          "category": "<category name>",
          "enabled": true
        }
      ],
      "metrics": [
        {
          "category": "AllMetrics",
          "enabled": true
        }
      ]
    }
  }
]
```



### <a name="example"></a>Voorbeeld
Hier volgt een voor beeld van het maken van een diagnostische instelling voor een instelling voor automatisch schalen waarmee streaming van bron logboeken kan worden gestreamd naar een Event Hub, een opslag account en een Log Analytics-werk ruimte.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "autoscaleSettingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the autoscale setting"
            }
        },
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceIDl of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
    {
      "type": "microsoft.insights/autoscalesettings/providers/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[concat(parameters('autoscaleSettingName'),'/microsoft.insights/', parameters('settingName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/autoscalesettings', parameters('autoscaleSettingName'))]"
      ],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "storageAccountId": "[parameters('storageAccountId')]",
        "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
        "eventHubName": "[parameters('eventHubName')]",
        "logs": [
          {
            "category": "AutoscaleScaleActions",
            "enabled": true
          },
          {
            "category": "AutoscaleEvaluations",
            "enabled": true
          }
        ]
      }
    }
  ]
}
```

## <a name="activity-log"></a>Activiteitenlogboek
Voeg voor het Azure-activiteiten logboek een resource van het type `Microsoft.Insights/diagnosticSettings`toe. De beschik bare categorieën worden weer gegeven in [Categorieën in het activiteiten logboek](activity-log-view.md#categories-in-the-activity-log). Hieronder volgt een sjabloon waarmee alle activiteiten logboek categorieën worden verzameld voor een Log Analytics-werk ruimte, een opslag account en Event Hub.


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceID of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
            "location": "global",
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "logs": [
                    {
                        "category": "Administrative",
                        "enabled": true
                    },
                    {
                        "category": "Security",
                        "enabled": true
                    },
                    {
                        "category": "ServiceHealth",
                        "enabled": true
                    },
                    {
                        "category": "Alert",
                        "enabled": true
                    },
                    {
                        "category": "Recommendation",
                        "enabled": true
                    },
                    {
                        "category": "Policy",
                        "enabled": true
                    },
                    {
                        "category": "Autoscale",
                        "enabled": true
                    },
                    {
                        "category": "ResourceHealth",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [platform Logboeken vindt u in azure](platform-logs-overview.md).
* Meer informatie over [Diagnostische instellingen](diagnostic-settings.md).
