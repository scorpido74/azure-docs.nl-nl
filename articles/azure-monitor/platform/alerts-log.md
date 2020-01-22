---
title: Logboek waarschuwingen maken, weer geven en beheren met behulp van Azure Monitor | Microsoft Docs
description: Gebruik de Azure Monitor voor het ontwerpen, bekijken en beheren van waarschuwings regels voor logboeken in Azure.
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 525e8d2ffcd673f47cf30699de2572b35fc3b314
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290272"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Logboek waarschuwingen maken, weer geven en beheren met behulp van Azure Monitor

## <a name="overview"></a>Overzicht
In dit artikel wordt beschreven hoe u logboek waarschuwingen instelt met behulp van de interface waarschuwingen in Azure Portal. De definitie van een waarschuwings regel bevindt zich in drie delen:
- Doel: specifieke Azure-resource, die moet worden bewaakt
- Criteria: specifieke voor waarde of logica die wordt weer gegeven als signaal, moet actie activeren
- Actie: er is een specifieke aanroep verzonden naar een ontvanger van een melding: e-mail, SMS, webhook, enzovoort.

De term **logboek waarschuwingen** om waarschuwingen te beschrijven waarbij een signaal wordt vastgelegd in een [log Analytics werk ruimte](../learn/tutorial-viewdata.md) of [Application Insights](../app/analytics.md). Meer informatie over de functionaliteit, terminologie en typen in [logboek waarschuwingen-overzicht](alerts-unified-log.md).

> [!NOTE]
> Populaire logboek gegevens uit [een log Analytics-werk ruimte](../../azure-monitor/learn/tutorial-viewdata.md) zijn nu ook beschikbaar op het meet platform in azure monitor. Voor detail weergave, [metrische waarschuwing voor logboeken](alerts-metric-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>Logboek waarschuwingen beheren vanuit de Azure Portal

Gedetailleerde volgende stapsgewijze instructies voor het gebruik van logboek waarschuwingen met behulp van de Azure Portal-interface.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Een waarschuwings regel voor het logboek maken met de Azure Portal

1. Selecteer in de [Portal](https://portal.azure.com/) **bewaken** en klik onder de sectie monitor op **waarschuwingen**.

    ![Controleren](media/alerts-log/AlertsPreviewMenu.png)

1. Selecteer de knop **nieuwe waarschuwings regel** om een nieuwe waarschuwing in azure te maken.

    ![Waarschuwing toevoegen](media/alerts-log/AlertsPreviewOption.png)

1. De sectie waarschuwing maken wordt weer gegeven met de drie onderdelen die bestaan uit: *waarschuwings voorwaarde definiëren*, *waarschuwings Details definiëren*en *actie groep definiëren*.

    ![Regel maken](media/alerts-log/AlertsPreviewAdd.png)

1. Definieer de waarschuwings voorwaarde met behulp van de **resource koppeling selecteren** en geef het doel op door een resource te selecteren. Filter door het _abonnement_, het _resource type_en de vereiste _resource_te kiezen.

   > [!NOTE]
   > Voor het maken van een logboek waarschuwing: Controleer of het **logboek** signaal beschikbaar is voor de geselecteerde resource voordat u doorgaat.
   >  resource](media/alerts-log/Alert-SelectResourceLog.png) ![selecteren

1. *Waarschuwingen voor logboeken*: Controleer of het **resource type** een analyse bron is, zoals *log Analytics* , *Application Insights* en signaal type als **logboek**, en klik vervolgens op *gereed*. Gebruik vervolgens de knop **criteria toevoegen** om een lijst weer te geven met signaal opties die beschikbaar zijn voor de resource en uit de lijst met **aangepaste logboeken** voor de geselecteerde logboek monitor service, zoals *log Analytics* of *Application Insights*.

   ![Selecteer een resource-aangepast zoeken naar Logboeken](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > In waarschuwings lijsten kunnen analyse query's worden geïmporteerd als signaal type- **logboek (opgeslagen query)** , zoals in de bovenstaande afbeelding wordt weer gegeven. Gebruikers kunnen uw query in de analyse verbeteren en deze vervolgens opslaan voor toekomstig gebruik in waarschuwingen-meer informatie over het gebruik van de query opslaan [in de logboek query in azure monitor](../log-query/log-query-overview.md) of een [gedeelde query in Application Insights Analytics](../app/app-insights-overview.md).

1. *Logboek waarschuwingen*: als u deze hebt geselecteerd, kan de query voor waarschuwingen worden opgegeven in het **Zoek query** veld; Als de query syntaxis onjuist is, wordt in het veld de fout rood weer gegeven. Als de query syntaxis juist is: voor referentie gegevens van de vermelde query wordt weer gegeven als een grafiek met de optie om het tijd venster van de laatste zes uur tot de vorige week te verfijnen.

    ![Waarschuwings regel configureren](media/alerts-log/AlertsPreviewAlertLog.png)

   > [!NOTE]
   > 
   > Historische gegevens visualisatie kan alleen worden weer gegeven als de query resultaten tijd details bevatten. Als uw query resulteert in samenvattings gegevens of specifieke kolom waarden, wordt deze weer gegeven als een enkelvoudige tekening.
   > Voor metrische meet typen van logboek waarschuwingen met Application Insights of [overschakelen naar een nieuwe API](alerts-log-api-switch.md), kunt u opgeven welke specifieke variabele de gegevens moet groeperen met behulp **van de optie aggregate on** . zoals hieronder wordt beschreven:
   > 
   > ![aggregatie op optie](media/alerts-log/aggregate-on.png)

1. *Logboek waarschuwingen*: als de visualisatie is geïmplementeerd, kunt u **waarschuwings logica** selecteren uit de weer gegeven opties voor voor waarde, aggregatie en finally-drempel waarde. Geef ten slotte in de logica de tijd op die moet worden geëvalueerd voor de opgegeven voor waarde, met behulp van de **periode** optie. En hoe vaak de waarschuwing moet worden uitgevoerd door **frequentie**te selecteren. **Logboek waarschuwingen** kunnen worden gebaseerd op:
    - [Aantal records](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules): er wordt een waarschuwing gemaakt als het aantal records dat door de query wordt geretourneerd, groter is dan of kleiner is dan de opgegeven waarde.
    - [Metrische meting](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules): er wordt een waarschuwing gemaakt als elke *cumulatieve waarde* in de resultaten de opgegeven drempel waarde overschrijdt en wordt *gegroepeerd op basis van* de gekozen waarde. Het aantal schendingen voor een waarschuwing is het aantal keren dat de drempel waarde wordt overschreden in de gekozen tijds periode. U kunt het totale aantal schendingen opgeven voor elke combi natie van inbreuken in de resultatenset of opeenvolgende inbreuken om te vereisen dat de schendingen in opeenvolgende steek proeven moeten plaatsvinden.


1. Als de tweede stap definieert u een naam voor uw waarschuwing in het veld **naam van waarschuwings regel** , samen met een **Beschrijving** van Details voor de waarschuwing en **Ernst** waarde van de opgegeven opties. Deze details worden opnieuw gebruikt in alle waarschuwings-e-mails, meldingen of pushen die door Azure Monitor worden uitgevoerd. Daarnaast kan de gebruiker ervoor kiezen om de waarschuwings regel direct te activeren bij het maken van de optie **regel inschakelen bij het maken** .

    Voor alleen **logboek waarschuwingen** is een aantal aanvullende functies beschikbaar in waarschuwings Details:

    - **Waarschuwingen onderdrukken**: wanneer u onderdrukking inschakelt voor de waarschuwings regel, worden acties voor de regel gedurende een gedefinieerde tijds duur uitgeschakeld nadat een nieuwe waarschuwing is gemaakt. De regel wordt nog uitgevoerd en er worden waarschuwings records gemaakt aan de criteria waaraan wordt voldaan. U de tijd om het probleem op te lossen zonder dat er dubbele acties worden uitgevoerd.

        ![Waarschuwingen onderdrukken voor waarschuwingen in Logboeken](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > Geef een waarschuwings waarde voor onderdrukking op die groter is dan de frequentie van de waarschuwing om ervoor te zorgen dat meldingen worden gestopt zonder overlap ping

1. Geef als derde en laatste stap op of een **actie groep** moet worden geactiveerd voor de waarschuwings regel wanneer aan de waarschuwings voorwaarde wordt voldaan. U kunt elke bestaande actie groep met een waarschuwing kiezen of een nieuwe actie groep maken. Op basis van de geselecteerde actie groep, wanneer de waarschuwing Azure wordt geactiveerd: e-mail (s) verzenden, SMS (s) verzenden, webhook (s) aanroepen, met behulp van Azure Runbooks, pushen naar uw ITSM-programma, enzovoort. Meer informatie over [actie groepen](action-groups.md).

    > [!NOTE]
    > Raadpleeg de [service limieten voor Azure-abonnementen](../../azure-resource-manager/management/azure-subscription-service-limits.md) voor limieten voor de nettolading van de Runbook die zijn geactiveerd voor logboek waarschuwingen via Azure-actie groepen

    Voor **logboek waarschuwingen** is extra functionaliteit beschikbaar voor het overschrijven van de standaard acties:

    - **E-mail melding**: onderdrukt het *onderwerp* van de e-mail in het e-mail bericht, verzonden via actie groep; Als er een of meer e-mail acties bestaan in de bedoelde actie groep. U kunt de hoofd tekst van de e-mail niet wijzigen en dit veld is **niet** voor e-mail adres.
    - **Aangepaste JSON-nettolading toevoegen**: vervangt de JSON van de webhook die wordt gebruikt door actie groepen; Als er een of meer webhook-acties bestaan in de bedoelde actie groep. De gebruiker kan de indeling van JSON opgeven die moet worden gebruikt voor alle webhooks die zijn geconfigureerd in de gekoppelde actie groep. Zie [webhook-actie voor logboek waarschuwingen](../../azure-monitor/platform/alerts-log-webhook.md)voor meer informatie over de indeling van webhooks. U kunt de optie webhook weer geven gebruiken om de indeling te controleren met behulp van JSON-voorbeeld gegevens.

        ![Actie onderdrukkingen voor waarschuwingen in Logboeken](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Als alle velden geldig zijn en met het groene deel streepje op de knop **waarschuwings regel maken** kan worden geklikt en er een waarschuwing wordt gemaakt in azure monitor-waarschuwingen. Alle waarschuwingen kunnen worden weer gegeven in het dash board waarschuwingen.

     ![Regel maken](media/alerts-log/AlertsPreviewCreate.png)

     Binnen een paar minuten is de waarschuwing actief en worden triggers zoals eerder beschreven.

Gebruikers kunnen ook hun analyse query in [log Analytics](../log-query/portals.md) volt ooien en vervolgens pushen om een waarschuwing te maken met de knop ' waarschuwing instellen ', gevolgd door de volgende instructies van stap 6 en hoger in de bovenstaande zelf studie.

 ![Log Analytics-ingestelde waarschuwing](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Waarschuwingen voor logboeken in Azure Portal weer geven & beheren

1. Selecteer in de [Portal](https://portal.azure.com/) **bewaken** en klik onder de sectie monitor op **waarschuwingen**.

1. Het **dash board waarschuwingen** wordt weer gegeven: alle Azure-waarschuwingen (inclusief logboek waarschuwingen) worden weer gegeven in een enkel bord. inclusief elk exemplaar van wanneer uw logboek waarschuwings regel is geactiveerd. Zie [Waarschuwingenbeheer](https://aka.ms/managealertinstances)voor meer informatie.
    > [!NOTE]
    > Waarschuwings regels voor logboeken bestaan uit aangepaste op query's gebaseerde logica die door gebruikers wordt verschaft en daarom zonder een opgeloste status. Omdat er telkens wordt voldaan aan de voor waarden die zijn opgegeven in de waarschuwings regel voor logboeken, wordt deze geactiveerd.

1. Selecteer de knop **regels beheren** op de bovenste balk om naar de sectie regel beheer te gaan, waarin alle gemaakte waarschuwings regels worden weer gegeven. inclusief waarschuwingen die zijn uitgeschakeld.
    waarschuwings regels ![ beheren](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Logboek waarschuwingen beheren met Azure-resource sjabloon

Waarschuwingen in het logboek in Azure Monitor zijn gekoppeld aan het resource type `Microsoft.Insights/scheduledQueryRules/`. Zie [Azure monitor-geplande query regels API-verwijzing](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)voor meer informatie over dit resource type. Logboek waarschuwingen voor Application Insights of Log Analytics kunnen worden gemaakt met behulp van [geplande query regel-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

> [!NOTE]
> Waarschuwingen voor logboeken voor Log Analytics kunnen ook worden beheerd met verouderde [log Analytics waarschuwings-API](api-alerts.md) en verouderde sjablonen van [log Analytics opgeslagen Zoek opdrachten en waarschuwingen](../insights/solutions-resources-searches-alerts.md) . Zie voor meer informatie over het gebruik van de nieuwe ScheduledQueryRules-API die hier wordt beschreven, [overschakelen naar nieuwe API voor log Analytics-waarschuwingen](alerts-log-api-switch.md).


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Voorbeeld logboek waarschuwing maken met behulp van Azure-resource sjabloon

Hieronder volgt de structuur voor [geplande query regels maken](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) op basis van een resource sjabloon met behulp van de standaard zoekopdracht zoek opdracht van het [aantal resultaten van het type logboek](alerts-unified-log.md#number-of-results-alert-rules), waarbij voorbeeld gegevens zijn ingesteld als variabelen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "southcentralus",
        "alertName": "samplelogalert",
        "alertDescription": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"requests",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4"
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescription')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "aznsAction":{
                    "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]"
                }
            }
        }
    } ]
}

```

De bovenstaande voor beeld-JSON kan worden opgeslagen als (zeg) sampleScheduledQueryRule. json voor het doel van deze procedure en kan worden geïmplementeerd met behulp van [Azure Resource Manager in azure Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>Logboek waarschuwing met een query tussen meerdere resources met behulp van een Azure-resource sjabloon

Hieronder ziet u de structuur voor het maken van een resource sjabloon op basis van een [geplande query regel](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) met behulp van een logboek registratie voor het vastleggen van gegevens in [Logboeken](../../azure-monitor/log-query/cross-workspace-query.md) van het [metrische meet type](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules), waarbij een voor beeld van gegevensset als variabelen is ingesteld.

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "Region Name for your Application Insights App or Log Analytics Workspace",
        "alertName": "sample log alert",
        "alertDescr": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"union workspace(\"servicews\").Update, app('serviceapp').requests | summarize AggregatedValue = count() by bin(TimeGenerated,1h), Classification",
            "Resource1": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Resource2": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceapp",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4",
            "SuppressTimeinMin": 20
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "metricMeasurement": {
            "thresholdOperator": "Equal",
            "threshold": "1",
            "metricTriggerType": "Consecutive",
            "metricColumn": "Classification"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescr')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "authorizedResources": "[concat(array(variables('alertSource').Resource1), array(variables('alertSource').Resource2))]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "throttlingInMin": "[variables('alertActions').SuppressTimeinMin]",
                "aznsAction":{
                    "actionGroup": "[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]",
                    "metricTrigger":{
                        "thresholdOperator": "[variables('metricMeasurement').thresholdOperator]",
                        "threshold": "[variables('metricMeasurement').threshold]",
                        "metricColumn": "[variables('metricMeasurement').metricColumn]",
                        "metricTriggerType": "[variables('metricMeasurement').metricTriggerType]"
                    }
                }
            }
        }
    } ]
}

```

> [!IMPORTANT]
> Wanneer u een query voor meerdere resources gebruikt in de logboek waarschuwing, is het gebruik van [authorizedResources](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate#source) verplicht en moet de gebruiker toegang hebben tot de vermelde lijst met resources

De bovenstaande voor beeld-JSON kan worden opgeslagen als (zeg) sampleScheduledQueryRule. json voor het doel van deze procedure en kan worden geïmplementeerd met behulp van [Azure Resource Manager in azure Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="managing-log-alerts-using-powershell"></a>Logboek waarschuwingen beheren met Power shell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor- [geplande query regels-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) is een rest API en volledig compatibel met Azure Resource Manager rest API. En Power shell-cmdlets die hieronder worden weer gegeven, zijn beschikbaar voor gebruik van de [geplande query regels API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

1. [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) : Power shell-cmdlet voor het maken van een nieuwe waarschuwings regel voor het logboek.
1. [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) : Power shell-cmdlet om een bestaande waarschuwings regel voor het logboek bij te werken.
1. [New-AzScheduledQueryRuleSource](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulesource) : Power shell-cmdlet voor het maken of bijwerken van een object dat de bron parameters voor een logboek waarschuwing opgeeft. Wordt gebruikt als invoer door de cmdlet [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) en [set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) .
1. [New-AzScheduledQueryRuleSchedule](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleSchedule): Power shell-cmdlet voor het maken of bijwerken van een object dat de plannings parameters voor een logboek waarschuwing opgeeft. Wordt gebruikt als invoer door de cmdlet [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) en [set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) .
1. [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) : Power shell-cmdlet voor het maken of bijwerken van een object waarmee actie parameters voor een logboek waarschuwing worden opgegeven. Wordt gebruikt als invoer door de cmdlet [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) en [set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) .
1. [New-AzScheduledQueryRuleAznsActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : Power shell-cmdlet voor het maken of bijwerken van een object waarmee actie groepen para meters voor een logboek waarschuwing worden opgegeven. Wordt gebruikt als invoer door de cmdlet [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) .
1. [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : Power shell-cmdlet voor het maken of bijwerken van een object waarmee trigger voorwaarde parameters voor de logboek waarschuwing worden opgegeven. Wordt gebruikt als invoer door de cmdlet [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) .
1. [New-AzScheduledQueryRuleLogMetricTrigger](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : Power shell-cmdlet voor het maken of bijwerken van een object waarvoor metrische trigger voorwaarde parameters worden opgegeven voor de waarschuwing voor het [meting type van metrische](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)gegevens. Wordt gebruikt als invoer door de cmdlet [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) .
1. [Get-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule) : Power shell-cmdlet voor het weer geven van de bestaande logboek waarschuwings regels of een specifieke waarschuwings regel voor het logboek
1. [Update-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/update-azscheduledqueryrule) : Power shell-cmdlet voor het in-of uitschakelen van de waarschuwings regel voor logboek registratie
1. [Remove-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/remove-azscheduledqueryrule): Power shell-cmdlet voor het verwijderen van een bestaande waarschuwings regel voor het logboek

> [!NOTE]
> ScheduledQueryRules Power shell-cmdlets kunnen alleen regels maken die zelf de cmdlet zelf hebben gemaakt of die gebruikmaken van Azure Monitor- [geplande query regels-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Waarschuwings regels in logboek registreren die zijn gemaakt met verouderde [log Analytics-waarschuwings-API](api-alerts.md) en verouderde sjablonen van [log Analytics opgeslagen Zoek opdrachten en waarschuwingen](../insights/solutions-resources-searches-alerts.md) kunnen worden beheerd met behulp van ScheduledQueryRules Power shell-cmdlets, nadat de gebruikers-API-voor [keur voor log Analytics](alerts-log-api-switch.md)

Hieronder ziet u de stappen voor het maken van een waarschuwings regel voor een voorbeeld logboek met behulp van de scheduledQueryRules Power shell-cmdlets.
```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>Logboek waarschuwingen beheren met CLI of API

Azure Monitor- [geplande query regels-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) is een rest API en volledig compatibel met Azure Resource Manager rest API. Dit kan daarom worden gebruikt via Power shell met behulp van Resource Manager-opdrachten voor Azure CLI.


> [!NOTE]
> Waarschuwingen voor logboeken voor Log Analytics kunnen ook worden beheerd met verouderde [log Analytics waarschuwings-API](api-alerts.md) en verouderde sjablonen van [log Analytics opgeslagen Zoek opdrachten en waarschuwingen](../insights/solutions-resources-searches-alerts.md) . Zie voor meer informatie over het gebruik van de nieuwe ScheduledQueryRules-API die hier wordt beschreven, [overschakelen naar nieuwe API voor log Analytics-waarschuwingen](alerts-log-api-switch.md).

Er zijn momenteel geen speciale CLI-opdrachten voor logboek waarschuwingen; maar zoals hieronder wordt beschreven, kunnen worden gebruikt via Azure Resource Manager CLI-opdracht voor een voor beeld van een resource sjabloon die eerder is weer gegeven (sampleScheduledQueryRule. json) in de sectie resource sjabloon:

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

Bij een geslaagde bewerking wordt 201 geretourneerd naar een status nieuwe waarschuwings regel maken of wordt 200 geretourneerd als een bestaande waarschuwings regel is gewijzigd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [logboek waarschuwingen in azure-waarschuwingen](../../azure-monitor/platform/alerts-unified-log.md)
* Informatie [over webhook-acties voor logboek waarschuwingen](../../azure-monitor/platform/alerts-log-webhook.md)
* Meer informatie over [Application Insights](../../azure-monitor/app/analytics.md)
* Meer informatie over [logboek query's](../log-query/log-query-overview.md).
