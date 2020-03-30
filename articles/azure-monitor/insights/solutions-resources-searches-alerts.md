---
title: Opgeslagen zoekopdrachten in managementoplossingen | Microsoft Documenten
description: Beheeroplossingen omvatten doorgaans opgeslagen logboekquery's om gegevens te analyseren die door de oplossing zijn verzameld. In dit artikel wordt beschreven hoe u opgeslagen zoekopdrachten in Logboekanalyse definiëren in een resourcemanagersjabloon.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 61fc64e140af091b5ff3f631398daf901557791b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663025"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Opgeslagen zoekopdrachten en waarschuwingen voor Logboekanalyse toevoegen aan beheeroplossing (Voorbeeld)

> [!IMPORTANT]
> Zoals [eerder aangekondigd](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), kunnen log analytics workspace(s) die na 1 juni *2019* zijn gemaakt - waarschuwingsregels beheren met **alleen** Azure scheduledQueryRules [REST API,](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)Azure Resource Manager [Template](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) en [PowerShell cmdlet](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell). Klanten kunnen eenvoudig [hun favoriete manier van waarschuwingsregelbeheer](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) voor oudere werkruimten om Azure Monitor scheduledQueryRules standaard te gebruiken en veel nieuwe [voordelen](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) te behalen, zoals de mogelijkheid om native PowerShell-cmdlets te gebruiken, langere kijktijdin regels, het maken van regels in afzonderlijke resourcegroep of -abonnement en nog veel meer.

> [!NOTE]
> Dit is voorlopige documentatie voor het maken van managementoplossingen die momenteel in preview zijn. Elk schema dat hieronder wordt beschreven, kan worden gewijzigd.

[Beheeroplossingen](solutions.md) omvatten doorgaans [opgeslagen zoekopdrachten](../../azure-monitor/log-query/log-query-overview.md) in Log Analytics om gegevens te analyseren die door de oplossing worden verzameld. Ze kunnen ook [waarschuwingen](../../azure-monitor/platform/alerts-overview.md) definiëren om de gebruiker op de hoogte te stellen of automatisch actie te ondernemen als reactie op een kritiek probleem. In dit artikel wordt beschreven hoe u door Logboekanalyse opgeslagen zoekopdrachten en waarschuwingen in een [resourcebeheersjabloon](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) definiëren, zodat ze kunnen worden opgenomen in [beheeroplossingen.](solutions-creating.md)

> [!NOTE]
> De voorbeelden in dit artikel gebruiken parameters en variabelen die vereist of gemeenschappelijk zijn voor beheeroplossingen en worden beschreven in [Ontwerp en een beheeroplossing bouwen in Azure](solutions-creating.md)

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u al weet hoe u [een beheeroplossing](solutions-creating.md) en de structuur van een [resourcemanager-sjabloon](../../azure-resource-manager/templates/template-syntax.md) en oplossingsbestand maken.


## <a name="log-analytics-workspace"></a>Log Analytics-werkruimte
Alle bronnen in Log Analytics bevinden zich in een [werkruimte.](../../azure-monitor/platform/manage-access.md) Zoals beschreven in [het log analytics-werkruimte- en automatiseringsaccount,](solutions.md#log-analytics-workspace-and-automation-account)is de werkruimte niet opgenomen in de beheeroplossing, maar moet deze bestaan voordat de oplossing wordt geïnstalleerd. Als deze niet beschikbaar is, mislukt de installatie van de oplossing.

De naam van de werkruimte staat op naam van elke Log Analytics-bron. Dit gebeurt in de oplossing met de **parameter werkruimte,** zoals in het volgende voorbeeld van een SavedSearch-bron.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Log Analytics API-versie
Alle Log Analytics-resources die zijn gedefinieerd in een Resource Manager-sjabloon hebben een **eigenschapsapiVersie** die de versie van de API definieert die de bron moet gebruiken.

In de volgende tabel vindt u de API-versie voor de resource die in dit voorbeeld wordt gebruikt.

| Resourcetype | API-versie | Query’s uitvoeren |
|:---|:---|:---|
| opgeslagenzoekopdrachten | 2017-03-15-preview | Gebeurtenis &#124; waar EventLevelName == "Fout"  |


## <a name="saved-searches"></a>Opgeslagen zoekopdrachten
Voeg [opgeslagen zoekopdrachten](../../azure-monitor/log-query/log-query-overview.md) toe aan een oplossing waarmee gebruikers gegevens kunnen opvragen die door uw oplossing zijn verzameld. Opgeslagen zoekopdrachten worden weergegeven onder **Opgeslagen zoekopdrachten** in de Azure-portal. Voor elke waarschuwing is ook een opgeslagen zoekopdracht vereist.

[Door Log Analytics opgeslagen zoekbronnen](../../azure-monitor/log-query/log-query-overview.md) hebben een type `Microsoft.OperationalInsights/workspaces/savedSearches` en hebben de volgende structuur. Dit omvat veelvoorkomende variabelen en parameters, zodat u dit codefragment kopiëren en plakken in uw oplossingsbestand en de parameternamen wijzigen.

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

Elke eigenschap van een opgeslagen zoekopdracht wordt beschreven in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| category | De categorie voor de opgeslagen zoekopdracht.  Opgeslagen zoekopdrachten in dezelfde oplossing delen vaak één categorie, zodat ze zijn gegroepeerd in de console. |
| displayname | Naam om weer te geven voor de opgeslagen zoekopdracht in de portal. |
| query | Query uit te voeren. |

> [!NOTE]
> Mogelijk moet u escape-tekens in de query gebruiken als deze tekens bevat die kunnen worden geïnterpreteerd als JSON. Als uw query bijvoorbeeld AzureActivity was **| OperationName:"Microsoft.Compute/virtualMachines/write"**, het moet worden geschreven in het oplossingsbestand als **AzureActivity | OperationName:/\"Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Waarschuwingen
[Azure Log-waarschuwingen](../../azure-monitor/platform/alerts-unified-log.md) worden gemaakt door Azure Alert-regels die op gezette tijden opgegeven logboekquery's uitvoeren. Als de resultaten van de query overeenkomen met opgegeven criteria, wordt een waarschuwingsrecord gemaakt en worden een of meer acties uitgevoerd met [actiegroepen](../../azure-monitor/platform/action-groups.md).

Voor gebruikers die waarschuwingen uitbreiden naar Azure, worden acties nu beheerd in Azure-actiegroepen. Wanneer een werkruimte en de bijbehorende waarschuwingen worden uitgebreid naar Azure, u acties ophalen of toevoegen met behulp van de [sjabloon Actiegroep - Azure Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).
Waarschuwingsregels in legacy management oplossing bestaan uit de volgende drie verschillende resources.

- **Opgeslagen zoekopdracht.** Hiermee definieert u de logboekzoekopdracht die wordt uitgevoerd. Meerdere waarschuwingsregels kunnen één opgeslagen zoekopdracht delen.
- **Schema.** Hiermee bepaalt u hoe vaak de logboekzoekopdracht wordt uitgevoerd. Elke waarschuwingsregel heeft één en slechts één schema.
- **Waarschuwingsactie.** Elke waarschuwingsregel heeft één actiegroepresource of actiebron (legacy) met een type **waarschuwing** dat de details van de waarschuwing definieert, zoals de criteria voor het maken van een waarschuwingsrecord en de ernst van de waarschuwing. [Actiegroepbron](../../azure-monitor/platform/action-groups.md) kan een lijst met geconfigureerde acties hebben die moeten worden uitgevoerd wanneer een waarschuwing wordt geactiveerd, zoals spraakoproepen, sms, e-mail, webhook, ITSM-tool, automatiseringsrunbook, logische app, enz.

Opgeslagen zoekbronnen worden hierboven beschreven. De andere bronnen worden hieronder beschreven.

### <a name="schedule-resource"></a>Resource plannen

Een opgeslagen zoekopdracht kan een of meer schema's hebben met elk schema dat een afzonderlijke waarschuwingsregel vertegenwoordigt. Het schema bepaalt hoe vaak de zoekopdracht wordt uitgevoerd en het tijdsinterval waarop de gegevens worden opgehaald. Resources plannen hebben `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` een type en hebben de volgende structuur. Dit omvat veelvoorkomende variabelen en parameters, zodat u dit codefragment kopiëren en plakken in uw oplossingsbestand en de parameternamen wijzigen.

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
De eigenschappen voor planningsresources worden beschreven in de volgende tabel.

| Elementnaam | Vereist | Beschrijving |
|:--|:--|:--|
| enabled       | Ja | Hiermee geeft u op of de waarschuwing is ingeschakeld wanneer deze wordt gemaakt. |
| interval      | Ja | Hoe vaak de query binnen enkele minuten wordt uitgevoerd. |
| queryTimeSpan | Ja | Tijdsduur in minuten om de resultaten te evalueren. |

De planningsbron moet afhankelijk zijn van de opgeslagen zoekopdracht, zodat deze vóór de planning is gemaakt.
> [!NOTE]
> De naam van het schema moet uniek zijn in een bepaalde werkruimte; twee schema's kunnen niet dezelfde ID hebben, zelfs als ze zijn gekoppeld aan verschillende opgeslagen zoekopdrachten. Ook de naam voor alle opgeslagen zoekopdrachten, schema's en acties die zijn gemaakt met de Log Analytics API moet in kleine letters.

### <a name="actions"></a>Acties
Een planning kan meerdere acties hebben. Een actie kan een of meer processen definiëren die moeten worden uitgevoerd, zoals het verzenden van een e-mail of het starten van een runbook, of het kan een drempelwaarde definiëren die bepaalt wanneer de resultaten van een zoekopdracht overeenkomen met bepaalde criteria. Sommige acties definiëren beide, zodat de processen worden uitgevoerd wanneer de drempel wordt bereikt.
Acties kunnen worden gedefinieerd met behulp van [actiegroep] resource of actieresource.

Er zijn twee typen actiebronnen die zijn opgegeven door de eigenschap **Type.** Een planning vereist één **waarschuwingsactie,** die de details van de waarschuwingsregel definieert en welke acties worden uitgevoerd wanneer een waarschuwing wordt gemaakt. Actiebronnen hebben een `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`soort .

Waarschuwingsacties hebben de volgende structuur. Dit omvat veelvoorkomende variabelen en parameters, zodat u dit codefragment kopiëren en plakken in uw oplossingsbestand en de parameternamen wijzigen.

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

De eigenschappen voor actiebronnen voor waarschuwingen worden beschreven in de volgende tabellen.

| Elementnaam | Vereist | Beschrijving |
|:--|:--|:--|
| `type` | Ja | Type van de actie.  Dit is **Alert** voor waarschuwingsacties. |
| `name` | Ja | Geef de naam voor de waarschuwing weer.  Dit is de naam die in de console wordt weergegeven voor de waarschuwingsregel. |
| `description` | Nee | Optionele beschrijving van de waarschuwing. |
| `severity` | Ja | Ernst van de waarschuwingsrecord van de volgende waarden:<br><br> **Kritische**<br>**Waarschuwing**<br>**Informatieve**

#### <a name="threshold"></a>Drempelwaarde
Deze sectie is vereist. Het definieert de eigenschappen voor de waarschuwingsdrempel.

| Elementnaam | Vereist | Beschrijving |
|:--|:--|:--|
| `Operator` | Ja | Operator voor de vergelijking van de volgende waarden:<br><br>**gt =<br>groter dan lt = minder dan** |
| `Value` | Ja | De waarde om de resultaten te vergelijken. |

##### <a name="metricstrigger"></a>StatistiekenTrigger
Deze sectie is optioneel. Voeg het toe voor een metrische metingswaarschuwing.

| Elementnaam | Vereist | Beschrijving |
|:--|:--|:--|
| `TriggerCondition` | Ja | Hiermee geeft u op of de drempel waarde is voor het totale aantal inbreuken of opeenvolgende inbreuken op de volgende waarden:<br><br>**Totaal<br>opeenvolgende** |
| `Operator` | Ja | Operator voor de vergelijking van de volgende waarden:<br><br>**gt =<br>groter dan lt = minder dan** |
| `Value` | Ja | Aantal keren dat aan de criteria moet worden voldaan om de waarschuwing te activeren. |


#### <a name="throttling"></a>Beperking
Deze sectie is optioneel. Neem deze sectie op als u waarschuwingen van dezelfde regel gedurende enige tijd wilt onderdrukken nadat een waarschuwing is gemaakt.

| Elementnaam | Vereist | Beschrijving |
|:--|:--|:--|
| DuurInMinuten | Ja als element Beperking is opgenomen | Het aantal minuten dat waarschuwingen moet worden onderdrukt nadat één van dezelfde waarschuwingsregel is gemaakt. |

#### <a name="azure-action-group"></a>Azure-actiegroep
Alle waarschuwingen in Azure gebruiken actiegroepen als het standaardmechanisme voor het afhandelen van acties. Met Actiegroep u uw acties één keer opgeven en de actiegroep vervolgens koppelen aan meerdere waarschuwingen in Azure. Zonder de noodzaak, herhaaldelijk dezelfde acties te verklaren over en weer. Actiegroepen ondersteunen meerdere acties - waaronder e-mail, SMS, Voice Call, ITSM Connection, Automation Runbook, Webhook URI en meer.

Voor gebruikers die hun waarschuwingen hebben uitgebreid naar Azure - een planning moet nu de details van de actiegroep hebben doorgegeven aan drempelwaarde, om een waarschuwing te kunnen maken. E-mailgegevens, Webhook-URL's, details over runbook automation en andere acties moeten eerst in een groep naast elkaar worden gedefinieerd voordat een waarschuwing wordt gegenereerd; Men kan [actiegroepen](../../azure-monitor/platform/action-groups.md) maken vanuit Azure Monitor in Portal of [Actiegroep gebruiken - Resourcesjabloon](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

| Elementnaam | Vereist | Beschrijving |
|:--|:--|:--|
| AznsNotification | Ja | De resource-id van de Azure-actiegroep die moet worden gekoppeld aan waarschuwing voor het uitvoeren van noodzakelijke acties wanneer aan waarschuwingscriteria is voldaan. |
| AangepastEEEEEEOnderwerp | Nee | Aangepaste onderwerpregel van de e-mail die wordt verzonden naar alle adressen die zijn opgegeven in de bijbehorende actiegroep. |
| CustomWebhookPayload | Nee | Aangepaste payload die moet worden verzonden naar alle webhook-eindpunten die zijn gedefinieerd in de bijbehorende actiegroep. Het formaat is afhankelijk van wat de webhook verwacht en moet een geldige geserialiseerde JSON. |

## <a name="sample"></a>Voorbeeld

Hieronder volgt een voorbeeld van een oplossing die de volgende bronnen bevat:

- Opgeslagen zoekopdracht
- Planning
- Actiegroep

Het voorbeeld maakt gebruik van [standaardparametersparametersvariabelen]( solutions-solution-file.md#parameters) die vaak in een oplossing worden gebruikt in tegenstelling tot hardcoderingswaarden in de brondefinities.

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

Het volgende parameterbestand bevat sampleswaarden voor deze oplossing.

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
* [Weergaven toevoegen](solutions-resources-views.md) aan uw beheeroplossing.
* [Voeg automation runbooks en andere resources toe](solutions-resources-automation.md) aan uw beheeroplossing.
