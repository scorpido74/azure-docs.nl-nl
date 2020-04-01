---
title: Metrische waarschuwingen maken voor logboeken in Azure-monitor
description: Zelfstudie over het maken van near-real-time metrische waarschuwingen voor populaire log-analysegegevens.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/17/2018
ms.subservice: alerts
ms.openlocfilehash: 6b21f228858954292e7a3bc5561d5e86fcfaaf41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055183"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Metrische waarschuwingen maken voor logboeken in Azure-monitor

## <a name="overview"></a>Overzicht

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor ondersteunt [het type metrische waarschuwing](../../azure-monitor/platform/alerts-metric-near-real-time.md) en voordelen ten opzichte van de klassieke [waarschuwingen.](../../azure-monitor/platform/alerts-classic-portal.md) Statistieken zijn beschikbaar voor [grote lijst met Azure-services.](../../azure-monitor/platform/metrics-supported.md) In dit artikel wordt uitgelegd dat een `Microsoft.OperationalInsights/workspaces`subset (dat wil zeggen) voor resource - .

U metrische waarschuwingen gebruiken op populaire Logboekanalyselogboeken die zijn geëxtraheerd als onderdeel van statistieken uit logboeken, waaronder resources in Azure of on-premises. De ondersteunde Log Analytics-oplossingen staan hieronder vermeld:

- [Prestatiemeteritems](../../azure-monitor/platform/data-sources-performance-counters.md) voor Windows & Linux-machines
- [Heartbeatrecords voor agentstatus](../../azure-monitor/insights/solution-agenthealth.md)
- [Beheerrecords bijwerken](../../automation/automation-update-management.md)
- [Logboeken van gebeurtenisgegevens](../../azure-monitor/platform/data-sources-windows-events.md)

Er zijn veel voordelen voor het gebruik **van metrische waarschuwingen voor logboeken** via op query's gebaseerde [logboekwaarschuwingen](../../azure-monitor/platform/alerts-log.md) in Azure; sommige van hen zijn hieronder vermeld:

- Metrische waarschuwingen bieden near-real-time monitoringmogelijkheden en metrische waarschuwingen voor logboeken vorken gegevens van logboekbron om hetzelfde te garanderen.
- Metrische waarschuwingen zijn stateful - slechts één keer op de hoogte wanneer de waarschuwing wordt geactiveerd en eenmaal wanneer de waarschuwing is opgelost; in tegenstelling tot Log-waarschuwingen, die stateloos zijn en bij elk interval blijven vuren als aan de waarschuwingsvoorwaarde is voldaan.
- Metrische waarschuwingen voor logboek bieden meerdere dimensies, waardoor filtering naar specifieke waarden zoals computers, OS-type, enz. zonder de noodzaak van penning query in analytics.

> [!NOTE]
> Specifieke metrische en/of dimensie wordt alleen weergegeven als er gegevens voor bestaan in de gekozen periode. Deze statistieken zijn beschikbaar voor klanten met Azure Log Analytics-werkruimten.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Statistieken en dimensies die worden ondersteund voor logboeken

 Metrische waarschuwingen ondersteunen waarschuwingen voor statistieken die dimensies gebruiken. U dimensies gebruiken om uw statistiek op het juiste niveau te filteren. De volledige lijst met statistieken die worden ondersteund voor logboeken van [Logboekanalyse-werkruimten](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) wordt weergegeven. ondersteunde oplossingen.

> [!NOTE]
> Als u een ondersteunde statistiek wilt weergeven die is geëxtraheerd uit een werkruimte Log Analytics via [Azure Monitor - Metrics,](../../azure-monitor/platform/metrics-charts.md)moet op die specifieke statistiek een metrische waarschuwing voor logboek worden gemaakt. De dimensies die in de metrische waarschuwing voor logboeken zijn gekozen - worden alleen weergegeven voor verkenning via Azure Monitor - Metrische gegevens.

## <a name="creating-metric-alert-for-log-analytics"></a>Metrische waarschuwing maken voor Logboekanalyse

Metrische gegevens uit populaire logboeken worden gepipeteerd voordat deze worden verwerkt in Log Analytics, in Azure Monitor - Metrics. Hierdoor kunnen gebruikers gebruikmaken van de mogelijkheden van het Metric-platform en metrische waarschuwing, inclusief waarschuwingen met een frequentie van maar liefst 1 minuut.
Hieronder staan de middelen om een metrische waarschuwing voor logboeken te maken.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Vereisten voor metrische waarschuwing voor logboeken

Voordat metrische gegevens voor logboeken die zijn verzameld over Log Analytics-gegevens werken, moet het volgende zijn ingesteld en beschikbaar zijn:

1. **Active Log Analytics Workspace**: Er moet een geldige en actieve Log Analytics-werkruimte aanwezig zijn. Zie [Een Logboekanalysewerkruimte maken in Azure-portal](../../azure-monitor/learn/quick-create-workspace.md)voor meer informatie.
2. **Agent is geconfigureerd voor Log Analytics Workspace:** agent moet worden geconfigureerd voor Azure VM's (en/of) on-premises VM's om gegevens naar de Log Analytics Workspace te verzenden die in een eerdere stap is gebruikt. Zie [Log Analytics - Agent-overzicht](../../azure-monitor/platform/agents-overview.md)voor meer informatie.
3. **Ondersteunde Log Analytics-oplossingen zijn geïnstalleerd:** log analytics-oplossing moet worden geconfigureerd en gegevens verzenden naar de werkruimte Log Analytics - ondersteunde oplossingen zijn [prestatiemeteritems voor Windows & Linux,](../../azure-monitor/platform/data-sources-performance-counters.md) [Heartbeat-records voor agentstatus,](../../azure-monitor/insights/solution-agenthealth.md) [updatebeheer](../../automation/automation-update-management.md)en [gebeurtenisgegevens](../../azure-monitor/platform/data-sources-windows-events.md).
4. **Log Analytics-oplossingen die zijn geconfigureerd om logboeken te verzenden:** log Analytics-oplossing moet de vereiste logboeken/gegevens bevatten die overeenkomen met [statistieken die worden ondersteund voor logboekanalysewerkruimten](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) ingeschakeld. Voor de teller *voor % beschikbaar geheugen* moet u bijvoorbeeld eerst zijn geconfigureerd in de oplossing [Prestatiemeteritems.](../../azure-monitor/platform/data-sources-performance-counters.md)

## <a name="configuring-metric-alert-for-logs"></a>Metrische waarschuwing configureren voor logboeken

 Metrische waarschuwingen kunnen worden gemaakt en beheerd met behulp van de Azure-portal, Resource Manager-sjablonen, REST API, PowerShell en Azure CLI. Aangezien metrische waarschuwingen voor logboeken een variant van metrische waarschuwingen zijn, kan metrische waarschuwing voor logboeken worden gemaakt voor de opgegeven werkruimte Log Analytics. Alle kenmerken en functionaliteiten van [metrische waarschuwingen](../../azure-monitor/platform/alerts-metric-near-real-time.md) zijn ook van toepassing op metrische waarschuwingen voor logboeken; inclusief laadschema, toepasselijke quotalimieten en gefactureerde prijs.

Voor stapsgewijze details en voorbeelden : zie [het maken en beheren van metrische waarschuwingen](https://aka.ms/createmetricalert). Volg met name voor metrische waarschuwingen voor logboeken de instructies voor het beheren van metrische waarschuwingen en zorg voor het volgende:

- Doel voor metrische waarschuwing is een geldige *Log Analytics-werkruimte*
- Signaal dat is gekozen voor metrische waarschuwing voor geselecteerde *Log Analytics-werkruimte* is van type **Metric**
- Filteren op specifieke voorwaarden of resource met dimensiefilters; statistieken voor logboeken zijn multidimensionaal
- Bij het configureren van *signaallogica*kan één waarschuwing worden gemaakt om meerdere waarden van dimensie (zoals computer) te omvatten
- Als **u geen** Azure-portal gebruikt voor het maken van metrische waarschuwingvoor geselecteerde *Logboekanalysewerkruimte*; vervolgens moet de gebruiker eerst handmatig een expliciete regel maken voor het converteren van logboekgegevens naar een statistiek met [Azure Monitor - Geplande queryregels](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules).

> [!NOTE]
> Bij het maken van metrische waarschuwing voor log Analytics-werkruimte via azure portal - overeenkomstige regel voor het converteren van loggegevens in metrische gegevens via [Azure Monitor - worden geplande queryregels](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) automatisch op de achtergrond gemaakt, zonder tussenkomst of actie van de *gebruiker*. Zie [Resourcesjabloon voor metrische waarschuwingen voor logboeken voor](#resource-template-for-metric-alerts-for-logs) metrische waarschuwingen voor voorbeeldmiddelen voor het maken van een op planningopgesteldlogboek voor metrische conversieregel voordat metrische waarschuwingsregels worden gemaakt, voordat metrische waarschuwingsregels worden gemaakt, worden gemaakt , zie Resourcesjabloon voor metrische waarschuwingen voor logboeken over voorbeeldmiddelen voor het maken van een op scheduledQueryrule gebaseerdlogboek tot metrische conversieregel voordat metrische waarschuwingen worden gemaakt - anders worden er geen gegevens voor de metrische waarschuwing voor logboeken gemaakt.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Resourcesjabloon voor metrische waarschuwingen voor logboeken

Zoals eerder vermeld, is het proces voor het maken van metrische waarschuwingen van logboeken twee ledig:

1. Een regel maken voor het extraheren van statistieken uit ondersteunde logboeken met de GeplandeQueryRule API
2. Een metrische waarschuwing maken voor metrische gegevens die zijn geëxtraheerd uit logboek (in stap1) en de werkruimte Log Analytics als doelbron

### <a name="metric-alerts-for-logs-with-static-threshold"></a>Metrische waarschuwingen voor logboeken met statische drempelwaarde

Om hetzelfde te bereiken, kan men het voorbeeld Azure Resource Manager Template hieronder gebruiken - waarbij het maken van een statische drempelmetrische waarschuwing afhankelijk is van het succesvol maken van de regel voor het extraheren van statistieken uit logboeken via scheduledQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Stel dat de bovenstaande JSON wordt opgeslagen als metricfromLogsAlertStatic.json - dan kan het worden gekoppeld aan een JSON-bestand voor parameterJSON voor het maken op basis van resourcesjablonen. Een voorbeeld parameter JSON bestand wordt hieronder vermeld:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule" 
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterThan"
        },
        "threshold":{
            "value": "1"
        },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Ervan uitgaande dat het bovenstaande parameterbestand wordt opgeslagen als metricfromLogsAlertStatic.parameters.json; vervolgens kan men metrische waarschuwing voor logboeken maken met behulp van [Resource template voor creatie in Azure portal](../../azure-resource-manager/templates/deploy-portal.md).

Als alternatief kan men de opdracht Azure Powershell hieronder ook gebruiken:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertStatic.json TemplateParameterFile metricfromLogsAlertStatic.parameters.json
```

Of gebruik deploy Resource Template met Azure CLI:

```azurecli
az group deployment create --resource-group myRG --template-file metricfromLogsAlertStatic.json --parameters @metricfromLogsAlertStatic.parameters.json
```

### <a name="metric-alerts-for-logs-with-dynamic-thresholds"></a>Metrische waarschuwingen voor logboeken met dynamische drempelwaarden

Om hetzelfde te bereiken, kan men het voorbeeld Azure Resource Manager Template hieronder gebruiken - waarbij het maken van een metrische waarschuwing dynamische drempelwaarden afhankelijk is van het succesvol maken van de regel voor het extraheren van statistieken uit logboeken via scheduledQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Stel dat de bovenstaande JSON wordt opgeslagen als metricfromLogsAlertDynamic.json - dan kan het worden gekoppeld aan een JSON-bestand voor parameterJSON voor het maken op basis van resourcesjablonen. Een voorbeeld parameter JSON bestand wordt hieronder vermeld:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule"
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterOrLessThan"
          },
          "alertSensitivity": {
              "value": "Medium"
          },
          "numberOfEvaluationPeriods": {
              "value": "4"
          },
          "minFailingPeriodsToAlert": {
              "value": "3"
          },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Ervan uitgaande dat het bovenstaande parameterbestand wordt opgeslagen als metricfromLogsAlertDynamic.parameters.json; vervolgens kan men metrische waarschuwing voor logboeken maken met behulp van [Resource template voor creatie in Azure portal](../../azure-resource-manager/templates/deploy-portal.md).

Als alternatief kan men de opdracht Azure Powershell hieronder ook gebruiken:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertDynamic.json TemplateParameterFile metricfromLogsAlertDynamic.parameters.json
```

Of gebruik deploy Resource Template met Azure CLI:

```azurecli
az group deployment create --resource-group myRG --template-file metricfromLogsAlertDynamic.json --parameters @metricfromLogsAlertDynamic.parameters.json
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [metrische waarschuwingen](alerts-metric.md).
- Meer informatie over [logboekwaarschuwingen in Azure](../../azure-monitor/platform/alerts-unified-log.md).
- Meer informatie over [waarschuwingen in Azure](alerts-overview.md).
