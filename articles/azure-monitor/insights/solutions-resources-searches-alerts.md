---
title: Opgeslagen Zoek opdrachten in beheer oplossingen | Microsoft Docs
description: Beheer oplossingen bevatten meestal opgeslagen Zoek opdrachten in Log Analytics voor het analyseren van gegevens die zijn verzameld door de oplossing. Ze kunnen ook waarschuwingen definiëren om de gebruiker op de hoogte te stellen of automatisch actie ondernemen als reactie op een kritiek probleem. In dit artikel wordt beschreven hoe u Log Analytics opgeslagen Zoek opdrachten kunt definiëren in een resource manager-sjabloon, zodat deze kunnen worden opgenomen in beheer oplossingen.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d5372ac8b31db91aaac018b203ee8868fa313fd8
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772992"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Log Analytics opgeslagen Zoek opdrachten en waarschuwingen toevoegen aan beheer oplossing (preview)

> [!IMPORTANT]
> Zoals [eerder is aangekondigd](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), kunnen log Analytics-werk ruimten die zijn gemaakt na *1 juni 2019* , waarschuwings regels beheren met **alleen** Azure ScheduledQueryRules [rest API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/), [Azure Resource Manager sjabloon](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) en [Power shell cmdlet](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell). Klanten kunnen eenvoudig [hun voor keuren voor waarschuwings regel beheer](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) voor oudere werk ruimten gebruiken om Azure monitor scheduledQueryRules als standaard te gebruiken en veel [nieuwe voor delen](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) te krijgen, zoals de mogelijkheid om systeem eigen Power shell-cmdlets uit te scha kelen, verg root lookback tijd in regels, het maken van regels in een afzonderlijke resource groep of abonnement en nog veel meer.

> [!NOTE]
> Dit is voorlopige documentatie voor het maken van beheer oplossingen die momenteel als preview-versie beschikbaar zijn. Elk schema dat hieronder wordt beschreven, kan worden gewijzigd.

[Beheer oplossingen](solutions.md) bevatten doorgaans [opgeslagen zoek opdrachten](../../azure-monitor/log-query/log-query-overview.md) in log Analytics voor het analyseren van gegevens die door de oplossing worden verzameld. Ze kunnen ook [waarschuwingen](../../azure-monitor/platform/alerts-overview.md) definiëren om de gebruiker op de hoogte te stellen of automatisch actie ondernemen als reactie op een kritiek probleem. In dit artikel wordt beschreven hoe u Log Analytics opgeslagen Zoek opdrachten en waarschuwingen definieert in een [sjabloon voor resource beheer](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) , zodat deze kunnen worden opgenomen in [beheer oplossingen](solutions-creating.md).

> [!NOTE]
> In de voor beelden in dit artikel worden para meters en variabelen gebruikt die vereist zijn of gemeen schappelijk zijn voor beheer oplossingen en die worden beschreven in [ontwerp en bouw een beheer oplossing in azure](solutions-creating.md)

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u al bekend bent met het [maken van een beheer oplossing](solutions-creating.md) en de structuur van een [Resource Manager-sjabloon](../../azure-resource-manager/resource-group-authoring-templates.md) en een oplossings bestand.


## <a name="log-analytics-workspace"></a>Log Analytics-werkruimte
Alle resources in Log Analytics zijn opgenomen in een [werk ruimte](../../azure-monitor/platform/manage-access.md). Zoals beschreven in [log Analytics werk ruimte en het Automation-account](solutions.md#log-analytics-workspace-and-automation-account), is de werk ruimte niet opgenomen in de beheer oplossing, maar moet deze bestaan voordat de oplossing wordt geïnstalleerd. Als deze niet beschikbaar is, mislukt de installatie van de oplossing.

De naam van de werk ruimte bevindt zich in de naam van elke Log Analytics resource. Dit gebeurt in de oplossing met de **werkruimte** parameter, zoals in het volgende voor beeld van een SavedSearch-resource.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Log Analytics API-versie
Alle Log Analytics resources die in een resource manager-sjabloon zijn gedefinieerd, hebben een eigenschap **apiVersion** die de versie definieert van de API die door de resource moet worden gebruikt.

De volgende tabel geeft een overzicht van de API-versie voor de resource die in dit voor beeld wordt gebruikt.

| Resourcetype | API-versie | Query’s uitvoeren |
|:---|:---|:---|
| savedSearches | 2017-03-15-preview | Gebeurtenis &#124; waarbij EventLevelName = = "Error"  |


## <a name="saved-searches"></a>Opgeslagen zoekopdrachten
Neem [opgeslagen Zoek opdrachten](../../azure-monitor/log-query/log-query-overview.md) op in een oplossing om gebruikers in staat te stellen gegevens op te vragen die door uw oplossing zijn verzameld. Opgeslagen Zoek opdrachten worden weer gegeven onder **opgeslagen Zoek opdrachten** in het Azure Portal. Er is ook een opgeslagen zoek opdracht vereist voor elke waarschuwing.

[Log Analytics opgeslagen Zoek](../../azure-monitor/log-query/log-query-overview.md) bronnen hebben een type van `Microsoft.OperationalInsights/workspaces/savedSearches` en hebben de volgende structuur. Dit omvat algemene variabelen en para meters, zodat u dit code fragment kunt kopiëren en plakken in het oplossings bestand en de parameter namen wijzigt.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

Elke eigenschap van een opgeslagen zoek opdracht wordt beschreven in de volgende tabel.

| Eigenschap | description |
|:--- |:--- |
| category | De categorie voor de opgeslagen zoek opdracht.  Alle opgeslagen Zoek opdrachten in dezelfde oplossing delen vaak één categorie zodat ze in de-console samen worden gegroepeerd. |
| displayName | Naam die moet worden weer gegeven voor de opgeslagen zoek opdracht in de portal. |
| query | De query die moet worden uitgevoerd. |

> [!NOTE]
> U moet mogelijk escape tekens in de query gebruiken als deze tekens bevat die als JSON kunnen worden geïnterpreteerd. Als uw query bijvoorbeeld **AzureActivity | Operationname: ' micro soft. Compute/informatie/write '** , moet worden geschreven in het oplossings bestand als **AzureActivity | Operationname\":/micro soft. Compute/informatie/\"write**.

## <a name="alerts"></a>Waarschuwingen
[Waarschuwingen voor Azure-logboeken](../../azure-monitor/platform/alerts-unified-log.md) worden gemaakt door Azure-waarschuwings regels die regel matig opgegeven logboek query's uitvoeren. Als de resultaten van de query overeenkomen met opgegeven criteria, wordt een waarschuwings record gemaakt en worden een of meer acties uitgevoerd met [actie groepen](../../azure-monitor/platform/action-groups.md).

Voor gebruikers die waarschuwingen naar Azure uitbreiden, worden de acties nu beheerd in Azure-Actiegroepen. Wanneer een werk ruimte en de bijbehorende waarschuwingen worden uitgebreid naar Azure, kunt u acties ophalen of toevoegen met behulp van de [actie groep-Azure Resource Manager sjabloon](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).
Waarschuwings regels in verouderde beheer oplossingen bestaan uit de volgende drie verschillende bronnen.

- **Opgeslagen zoek opdracht.** Hiermee wordt de logboek zoekactie gedefinieerd die wordt uitgevoerd. Meerdere waarschuwings regels kunnen één opgeslagen zoek opdracht delen.
- **Planning.** Hiermee definieert u hoe vaak de zoek opdracht in Logboeken wordt uitgevoerd. Elke waarschuwings regel heeft één en slechts één schema.
- **Waarschuwings actie.** Elke waarschuwings regel heeft één actie groeps resource of actie bron (verouderd) met een type **waarschuwing** waarmee de details van de waarschuwing worden gedefinieerd, zoals de criteria voor het maken van een waarschuwings record en de ernst van de waarschuwing. De resource van de [actie groep](../../azure-monitor/platform/action-groups.md) kan een lijst met geconfigureerde acties hebben die moeten worden uitgevoerd wanneer de waarschuwing wordt geactiveerd, zoals spraak oproep, SMS, e-mail, webhook, ITSM-hulp programma, Automation-runbook, logische app, enzovoort.

Opgeslagen Zoek bronnen worden hierboven beschreven. De overige bronnen worden hieronder beschreven.

### <a name="schedule-resource"></a>Resource plannen

Een opgeslagen zoek opdracht kan een of meer schema's bevatten met elk schema dat een afzonderlijke waarschuwings regel vertegenwoordigt. De planning bepaalt hoe vaak de zoek actie wordt uitgevoerd en het tijds interval waarover de gegevens worden opgehaald. Plannings resources hebben een type `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` van en hebben de volgende structuur. Dit omvat algemene variabelen en para meters, zodat u dit code fragment kunt kopiëren en plakken in het oplossings bestand en de parameter namen wijzigt.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }
De eigenschappen voor plannings resources worden beschreven in de volgende tabel.

| De naam van element | Vereist | description |
|:--|:--|:--|
| enabled       | Ja | Hiermee wordt aangegeven of de waarschuwing wordt ingeschakeld wanneer deze wordt gemaakt. |
| interval      | Ja | Hoe vaak de query wordt uitgevoerd in minuten. |
| queryTimeSpan | Ja | De tijds duur in minuten waarover de resultaten moeten worden geëvalueerd. |

De plannings bron moet afhankelijk zijn van de opgeslagen zoek opdracht, zodat deze wordt gemaakt vóór het schema.
> [!NOTE]
> De naam van de planning moet uniek zijn in een bepaalde werk ruimte; twee schema's kunnen niet dezelfde ID hebben, zelfs als ze zijn gekoppeld aan verschillende opgeslagen Zoek opdrachten. De naam van alle opgeslagen Zoek opdrachten, planningen en acties die zijn gemaakt met de Log Analytics-API moet een kleine letter zijn.

### <a name="actions"></a>Acties
Een planning kan meerdere acties hebben. Een actie kan definiëren een of meer processen om uit te voeren, zoals een e-mail wordt verzonden of een runbook starten, of het kan een drempelwaarde waarmee wordt bepaald wanneer de resultaten van een zoekopdracht voldoen aan bepaalde criteria definiëren. Sommige acties worden beide definiëren, zodat de processen worden uitgevoerd wanneer de drempelwaarde wordt voldaan.
Acties kunnen worden gedefinieerd met behulp van de resource of actie resource van [actie groep].

Er zijn twee typen actie resources die zijn opgegeven met de eigenschap **type** . Voor een planning is één **waarschuwings** actie vereist, waarmee de details van de waarschuwings regel worden gedefinieerd en welke acties worden uitgevoerd wanneer een waarschuwing wordt gemaakt. Actie resources hebben het type van `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.

Waarschuwings acties hebben de volgende structuur. Dit omvat algemene variabelen en para meters, zodat u dit code fragment kunt kopiëren en plakken in het oplossings bestand en de parameter namen wijzigt.

```json
{
    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
    "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
    ],
    "properties": {
        "etag": "*",
        "type": "Alert",
        "name": "[variables('Alert').Name]",
        "description": "[variables('Alert').Description]",
        "severity": "[variables('Alert').Severity]",
        "threshold": {
            "operator": "[variables('Alert').Threshold.Operator]",
            "value": "[variables('Alert').Threshold.Value]",
            "metricsTrigger": {
                "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                "operator": "[variables('Alert').Trigger.Operator]",
                "value": "[variables('Alert').Trigger.Value]"
            },
        },
        "AzNsNotification": {
            "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
            "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]",
            "CustomWebhookPayload": "[variables('MyAlert').AzNsNotification.CustomWebhookPayload]"
        }
    }
}
```

De eigenschappen van resources voor waarschuwings acties worden beschreven in de volgende tabellen.

| De naam van element | Vereist | Description |
|:--|:--|:--|
| `type` | Ja | Het type van de actie.  Dit is een **waarschuwing** voor waarschuwings acties. |
| `name` | Ja | Weergavenaam voor de waarschuwing.  Dit is de naam die wordt weer gegeven in de console voor de waarschuwings regel. |
| `description` | Nee | Optionele beschrijving van de waarschuwing. |
| `severity` | Ja | Ernst van de waarschuwings record van de volgende waarden:<br><br> **critical**<br>**waarschuwing**<br>**informatief**

#### <a name="threshold"></a>Drempelwaarde
Deze sectie is vereist. Hiermee worden de eigenschappen van de waarschuwings drempel gedefinieerd.

| De naam van element | Vereist | Description |
|:--|:--|:--|
| `Operator` | Ja | Operator voor de vergelijking van de volgende waarden:<br><br>**gt = groter dan<br>lt = kleiner dan** |
| `Value` | Ja | De waarde waarmee de resultaten worden vergeleken. |

##### <a name="metricstrigger"></a>MetricsTrigger
Deze sectie is optioneel. Neem deze op voor een waarschuwing voor metrische metingen.

| De naam van element | Vereist | Description |
|:--|:--|:--|
| `TriggerCondition` | Ja | Hiermee geeft u op of de drempel waarde is voor het totale aantal schendingen of opeenvolgende schendingen van de volgende waarden:<br><br>**Totaal<br>aantal opeenvolgende** |
| `Operator` | Ja | Operator voor de vergelijking van de volgende waarden:<br><br>**gt = groter dan<br>lt = kleiner dan** |
| `Value` | Ja | Aantal keer dat aan de criteria moet worden voldaan om de waarschuwing te activeren. |


#### <a name="throttling"></a>Beperken
Deze sectie is optioneel. Neem deze sectie op als u waarschuwingen van dezelfde regel wilt onderdrukken gedurende een bepaalde hoeveelheid tijd nadat een waarschuwing is gemaakt.

| De naam van element | Vereist | description |
|:--|:--|:--|
| DurationInMinutes | Ja als het bandbreedte-element is opgenomen | Aantal minuten dat waarschuwingen worden onderdrukt nadat een van dezelfde waarschuwings regel is gemaakt. |

#### <a name="azure-action-group"></a>Actie groep van Azure
Alle waarschuwingen in Azure, gebruik actiegroep als het standaardmechanisme voor het verwerken van acties. Met de actiegroep, kunt u uw acties één keer opgeven en koppel vervolgens de actie die u wilt meerdere waarschuwingen - binnen Azure. Zonder de noodzaak om te declareren dezelfde acties herhaaldelijk telkens opnieuw. Actiegroepen ondersteuning voor meerdere acties - inclusief e-mail, SMS, Spraakoproep, ITSM-verbinding, Automation-Runbook, Webhook URI en meer.

Voor gebruikers die hun waarschuwingen hebt uitgebreid naar Azure, hebt een planning nu actiegroep informatie doorgegeven, samen met de drempelwaarde, kunnen een waarschuwing wilt maken. E-mail gegevens, webhook-Url's, Details van Runbook-automatisering en andere acties moeten eerst worden gedefinieerd in een actie groep naast het maken van een waarschuwing. een kan [actie groep maken van Azure monitor](../../azure-monitor/platform/action-groups.md) in portal of [actie groep gebruiken-resource sjabloon](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

| De naam van element | Vereist | description |
|:--|:--|:--|
| AzNsNotification | Ja | De resource-ID van de actie groep van Azure die moet worden gekoppeld aan een waarschuwing voor het nemen van de benodigde acties wanneer aan waarschuwings criteria wordt voldaan. |
| CustomEmailSubject | Nee | Aangepaste onderwerpregel van het e-mail bericht dat wordt verzonden naar alle adressen die zijn opgegeven in de gekoppelde actie groep. |
| CustomWebhookPayload | Nee | Aangepaste Payload die moet worden verzonden naar alle webhook-eind punten die zijn gedefinieerd in de bijbehorende actie groep. De indeling is afhankelijk van wat de webhook verwacht en moet een geldige geserialiseerde JSON zijn. |

## <a name="sample"></a>Voorbeeld

Hier volgt een voor beeld van een oplossing die de volgende resources bevat:

- Opgeslagen zoekactie
- Planning
- Actiegroep

In het voor beeld worden de variabelen voor [standaard oplossings parameters]( solutions-solution-file.md#parameters) gebruikt die doorgaans in een oplossing zouden worden toegepast, in tegens telling tot hardcoding-waarden in de resource definities.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "Description": "Name of Log Analytics workspace"
            }
        },
        "workspaceregionId": {
            "type": "string",
            "metadata": {
                "Description": "Region of Log Analytics workspace"
            }
        },
        "actiongroup": {
            "type": "string",
            "metadata": {
                "Description": "List of action groups for alert actions separated by semicolon"
            }
        }
    },
    "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
        "LogAnalyticsApiVersion-Search": "2017-03-15-preview",
        "LogAnalyticsApiVersion-Solution": "2015-11-01-preview",
        "MySearch": {
            "displayName": "Error records by hour",
            "query": "MyRecord_CL | summarize AggregatedValue = avg(Rating_d) by Instance_s, bin(TimeGenerated, 60m)",
            "category": "Samples",
            "name": "Samples-Count of data"
        },
        "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert. Fires when 3 error records found over hour interval.",
            "Severity": "critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
                "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
                "Interval": 15,
                "TimeSpan": 60
            },
            "MetricsTrigger": {
                "TriggerCondition": "Consecutive",
                "Operator": "gt",
                "Value": 3
            },
            "ThrottleMinutes": 60,
            "AzNsNotification": {
                "GroupIds": [
                    "[parameters('actiongroup')]"
                ],
                "CustomEmailSubject": "Sample alert"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Solution')]",
            "dependsOn": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
            ],
            "properties": {
                "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
                "referencedResources": [
                ],
                "containedResources": [
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
                ]
            },
            "plan": {
                "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
                "Version": "[variables('SolutionVersion')]",
                "product": "[variables('ProductName')]",
                "publisher": "[variables('SolutionPublisher')]",
                "promotionCode": ""
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
                "etag": "*",
                "query": "[variables('MySearch').query]",
                "displayName": "[variables('MySearch').displayName]",
                "category": "[variables('MySearch').category]"
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
                "etag": "*",
                "interval": "[variables('MyAlert').Schedule.Interval]",
                "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
                "enabled": true
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
                "etag": "*",
                "Type": "Alert",
                "Name": "[variables('MyAlert').DisplayName]",
                "Description": "[variables('MyAlert').Description]",
                "Severity": "[variables('MyAlert').Severity]",
                "Threshold": {
                    "Operator": "[variables('MyAlert').ThresholdOperator]",
                    "Value": "[variables('MyAlert').ThresholdValue]",
                    "MetricsTrigger": {
                        "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                        "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                        "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                    }
                },
                "Throttling": {
                    "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
                },
                "AzNsNotification": {
                    "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
                    "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
                }
            }
        }
    ]
}
```

Het volgende parameter bestand bevat voorbeeld waarden voor deze oplossing.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspacename": {
            "value": "myWorkspace"
        },
        "accountName": {
            "value": "myAccount"
        },
        "workspaceregionId": {
            "value": "East US"
        },
        "regionId": {
            "value": "East US 2"
        },
        "pricingTier": {
            "value": "Free"
        },
        "actiongroup": {
            "value": "/subscriptions/3b540246-808d-4331-99aa-917b808a9166/resourcegroups/myTestGroup/providers/microsoft.insights/actiongroups/sample"
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen
* [Voeg weer gaven](solutions-resources-views.md) toe aan uw beheer oplossing.
* [Voeg Automation-runbooks en andere resources](solutions-resources-automation.md) toe aan uw beheer oplossing.
