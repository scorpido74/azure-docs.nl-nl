---
title: Diagnostische instelling maken in Azure met behulp van resourcebeheersjabloon
description: Diagnostische instellingen maken met behulp van een Resource Manager-sjabloon om Azure-platformlogboeken door te sturen naar Azure Monitor-logboeken, Azure-opslag of Azure Event Hubs.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: a2569ca3f998030680bd7dbd872d71ccd372a25d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672426"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Diagnostische instelling maken in Azure met behulp van een resourcemanagersjabloon
[Diagnostische instellingen](diagnostic-settings.md) in Azure Monitor geven aan waar [platformlogboeken](platform-logs-overview.md) moeten worden verzonden die worden verzameld door Azure-bronnen en het Azure-platform waarvan ze afhankelijk zijn. In dit artikel vindt u details en voorbeelden voor het gebruik van een [Azure Resource Manager-sjabloon](../../azure-resource-manager/templates/template-syntax.md) om diagnostische instellingen te maken en te configureren om platformlogboeken naar verschillende bestemmingen te verzamelen.

> [!NOTE]
> Aangezien u geen [diagnostische instelling](diagnostic-settings.md) voor het Azure Activity-logboek maken met PowerShell- of CLI-achtige diagnostische instellingen voor andere Azure-resources, maakt u een resourcebeheersjabloon voor het activiteitenlogboek met behulp van de informatie in dit artikel en implementeert u de sjabloon met PowerShell of CLI.

## <a name="deployment-methods"></a>Implementatiemethoden
U Resource Manager-sjablonen implementeren met behulp van elke geldige methode, waaronder PowerShell en CLI. Diagnostische instellingen voor het activiteitslogboek `az deployment create` moeten `New-AzDeployment` worden geïmplementeerd op een abonnement met cli of powershell. Diagnostische instellingen voor resourcelogboeken moeten `az group deployment create` worden geïmplementeerd `New-AzResourceGroupDeployment` in een resourcegroep die wordt gebruikt voor CLI of voor PowerShell.

Zie [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell-](../../azure-resource-manager/templates/deploy-powershell.md) en [Implementeerresources met Resource Manager-sjablonen en Azure CLI](../../azure-resource-manager/templates/deploy-cli.md) voor meer informatie. 





## <a name="resource-logs"></a>Resourcelogboeken
Voeg voor bronlogboeken een `<resource namespace>/providers/diagnosticSettings` bron van type toe aan de sjabloon. De sectie Eigenschappen volgt de indeling die is beschreven in [Diagnostische instellingen - Maken of bijwerken](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). Geef `category` een `logs` in de sectie op voor elk van de categorieën die geldig zijn voor de resource die u wilt verzamelen. Voeg `metrics` de eigenschap toe om resourcestatistieken aan dezelfde bestemmingen te verzamelen als de [resource statistieken ondersteunt](metrics-supported.md).

Hieronder volgt een sjabloon die een resourcelogboekcategorie voor een bepaalde resource verzamelt voor een werkruimte, opslagaccount en gebeurtenishub van Log Analytics.

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
Hieronder volgt een voorbeeld dat een diagnostische instelling maakt voor een instelling voor automatisch schalen waarmee bronlogboeken naar een gebeurtenishub, een opslagaccount en een werkruimte Log Analytics kunnen worden gestreamd.

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
Voeg voor het azure-activiteitenlogboek `Microsoft.Insights/diagnosticSettings`een bron van het type toe . De beschikbare rubrieken worden weergegeven in [Rubrieken in het activiteitenlogboek](activity-log-view.md#categories-in-the-activity-log). Hieronder volgt een sjabloon die alle categorieën van activiteitenlogboeken verzamelt voor een log-inhoudswerkruimte, opslagaccount en gebeurtenishub.


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
* Lees meer over [platformlogboeken in Azure.](platform-logs-overview.md)
* Meer informatie over [diagnostische instellingen](diagnostic-settings.md).
