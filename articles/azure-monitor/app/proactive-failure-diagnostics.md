---
title: Smart Detection - failure anomalies, in Application Insights | Microsoft Docs
description: Alerts you to unusual changes in the rate of failed requests to your web app, and provides diagnostic analysis. No configuration is needed.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: c556f726cd63971abe1e9b6d8b87117bb3e378db
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2020
ms.locfileid: "75912842"
---
# <a name="smart-detection---failure-anomalies"></a>Smart Detection - Failure Anomalies
[Application Insights](../../azure-monitor/app/app-insights-overview.md) automatically alerts you in near real time if your web app experiences an abnormal rise in the rate of failed requests. It detects an unusual rise in the rate of HTTP requests or dependency calls that are reported as failed. For requests, failed requests usually have response codes of 400 or higher. To help you triage and diagnose the problem, an analysis of the characteristics of the failures and related application data is provided in the alert details. There are also links to the Application Insights portal for further diagnosis. The feature needs no set-up nor configuration, as it uses machine learning algorithms to predict the normal failure rate.

This feature works for any web app, hosted in the cloud or on your own servers, that generate application request or dependency data. For example, if you have a worker role that calls [TrackRequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) or [TrackDependency()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

After setting up [Application Insights for your project](../../azure-monitor/app/app-insights-overview.md), and if your app generates a certain minimum amount of data, Smart Detection of failure anomalies takes 24 hours to learn the normal behavior of your app, before it is switched on and can send alerts.

Here's a sample alert:

[![](./media/proactive-failure-diagnostics/013.png "Sample smart detection alert showing cluster analysis around failure")](./media/proactive-failure-diagnostics/013.png#lightbox)

The alert details will tell you:

* The failure rate compared to normal app behavior.
* How many users are affected - so you know how much to worry.
* A characteristic pattern associated with the failures. In this example, there's a particular response code, request name (operation), and application version. That immediately tells you where to start looking in your code. Other possibilities could be a specific browser or client operating system.
* The exception, log traces, and dependency failure (databases or other external components) that appear to be associated with the characterized failures.
* Links directly to relevant searches on the data in Application Insights.

## <a name="benefits-of-smart-detection"></a>Benefits of Smart Detection
Ordinary [metric alerts](../../azure-monitor/app/alerts.md) tell you there might be a problem. But Smart Detection starts the diagnostic work for you, performing much the analysis you would otherwise have to do yourself. You get the results neatly packaged, helping you to get quickly to the root of the problem.

## <a name="how-it-works"></a>Het werkt als volgt
Smart Detection monitors the data received from your app, and in particular the failure rates. This rule counts the number of requests for which the `Successful request` property is false, and the number of dependency calls for which the `Successful call` property is false. For requests, by default, `Successful request == (resultCode < 400)` (unless you have written custom code to [filter](../../azure-monitor/app/api-filtering-sampling.md#filtering) or generate your own [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) calls). 

Your app's performance has a typical pattern of behavior. Some requests or dependency calls will be more prone to failure than others; and the overall failure rate may go up as load increases. Smart Detection uses machine learning to find these anomalies.

As data comes into Application Insights from your web app, Smart Detection compares the current behavior with the patterns seen over the past few days. If an abnormal rise in failure rate is observed by comparison with previous performance, an analysis is triggered.

When an analysis is triggered, the service performs a cluster analysis on the failed request, to try to identify a pattern of values that characterize the failures. 

In the example above, the analysis has discovered that most failures are about a specific result code, request name, Server URL host, and role instance. 

When your service is instrumented with these calls, the analyzer looks for an exception and a dependency failure that are associated with requests in the cluster it has identified, together with an example of any trace logs associated with those requests.

The resulting analysis is sent to you as alert, unless you have configured it not to.

Like the [alerts you set manually](../../azure-monitor/app/alerts.md), you can inspect the state of the fired alert, which can be resolved if the issue is fixed. Configure the alert rules in the Alerts page of your Application Insights resource. But unlike other alerts, you don't need to set up or configure Smart Detection. If you want, you can disable it or change its target email addresses.

### <a name="alert-logic-details"></a>Alert logic details

The alerts are triggered by our proprietary machine learning algorithm so we can't share the exact implementation details. With that said, we understand that you sometimes need to know more about how the underlying logic works. De primaire factoren die worden geëvalueerd om te bepalen of een waarschuwing moet worden geactiveerd zijn: 

* Analyse van het percentage mislukte aanvragen/afhankelijkheden in een rollend venster van 20 minuten.
* Een vergelijking van het uitval percentage van de laatste 20 minuten tot het percentage in de afgelopen 40 minuten en de afgelopen zeven dagen en op zoek naar aanzienlijke afwijkingen die de X-tijden van de standaard deviatie overschrijden.
* Het gebruik van een adaptieve limiet voor het minimale uitval percentage, dat varieert op basis van het volume van de aanvragen/afhankelijkheden van de app.
* Er is een logica waarmee de geactiveerde waarschuwing voor waarschuwings monitor automatisch kan worden opgelost als het probleem gedurende 8-24 uur niet meer wordt gedetecteerd.

## <a name="configure-alerts"></a>Waarschuwingen configureren

U kunt de waarschuwings regel voor slimme detectie uitschakelen vanuit de portal of met behulp van Azure Resource Manager ([Zie voor beeld van sjabloon](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)).

Deze waarschuwings regel wordt gemaakt met een bijbehorende [actie groep](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) met de naam ' Application Insights Slimme detectie ' die e-mail en webhook-acties bevat, en kan worden uitgebreid om aanvullende acties te activeren wanneer de waarschuwing wordt geactiveerd.

> [!NOTE]
> E-mail meldingen die vanuit deze waarschuwings regel worden verzonden, worden nu standaard verzonden naar gebruikers die zijn gekoppeld aan de bewakings-en bewakings functies van het abonnement. Hier vindt u meer informatie over dit [onderwerp](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification).
> Meldingen die vanuit deze waarschuwings regel worden verzonden, volgen het [algemene waarschuwings schema](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema).
>

Open de pagina waarschuwingen. Waarschuwings regels voor fout afwijkingen zijn opgenomen in alle waarschuwingen die u hand matig hebt ingesteld en u kunt zien of deze zich momenteel in de status van de waarschuwing bevindt.

[![](./media/proactive-failure-diagnostics/021.png "On the Application Insights resource page, click 'Alerts' tile, then 'Manage alert rules'")](./media/proactive-failure-diagnostics/021.png#lightbox)

Klik op de waarschuwing om deze te configureren.

[![](./media/proactive-failure-diagnostics/032.png "Rule configuration screen")](./media/proactive-failure-diagnostics/032.png#lightbox)

U kunt een waarschuwings regel voor fout afwijkingen uitschakelen of verwijderen, maar niet op dezelfde Application Insights resource maken.

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>Voor beeld van fout afwijkingen waarschuwing webhook Payload

```json
{
    "properties": {
        "essentials": {
            "severity": "Sev3",
            "signalType": "Log",
            "alertState": "New",
            "monitorCondition": "Resolved",
            "monitorService": "Smart Detector",
            "targetResource": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule",
            "targetResourceName": "test-rule",
            "targetResourceGroup": "test-group",
            "targetResourceType": "microsoft.insights/components",
            "sourceCreatedId": "1a0a5b6436a9b2a13377f5c89a3477855276f8208982e0f167697a2b45fcbb3e",
            "alertRule": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.alertsmanagement/smartdetectoralertrules/failure anomalies - test-rule",
            "startDateTime": "2019-10-30T17:52:32.5802978Z",
            "lastModifiedDateTime": "2019-10-30T18:25:23.1072443Z",
            "monitorConditionResolvedDateTime": "2019-10-30T18:25:26.4440603Z",
            "lastModifiedUserName": "System",
            "actionStatus": {
                "isSuppressed": false
            },
            "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls."
        },
        "context": {
            "DetectionSummary": "An abnormal rise in failed request rate",
            "FormattedOccurenceTime": "2019-10-30T17:50:00Z",
            "DetectedFailureRate": "50.0% (200/400 requests)",
            "NormalFailureRate": "0.0% (over the last 30 minutes)",
            "FailureRateChart": [["2019-10-30T05:20:00Z",
            0],
            ["2019-10-30T05:40:00Z",
            100],
            ["2019-10-30T06:00:00Z",
            0],
            ["2019-10-30T06:20:00Z",
            0],
            ["2019-10-30T06:40:00Z",
            100],
            ["2019-10-30T07:00:00Z",
            0],
            ["2019-10-30T07:20:00Z",
            0],
            ["2019-10-30T07:40:00Z",
            100],
            ["2019-10-30T08:00:00Z",
            0],
            ["2019-10-30T08:20:00Z",
            0],
            ["2019-10-30T08:40:00Z",
            100],
            ["2019-10-30T17:00:00Z",
            0],
            ["2019-10-30T17:20:00Z",
            0],
            ["2019-10-30T09:00:00Z",
            0],
            ["2019-10-30T09:20:00Z",
            0],
            ["2019-10-30T09:40:00Z",
            100],
            ["2019-10-30T10:00:00Z",
            0],
            ["2019-10-30T10:20:00Z",
            0],
            ["2019-10-30T10:40:00Z",
            100],
            ["2019-10-30T11:00:00Z",
            0],
            ["2019-10-30T11:20:00Z",
            0],
            ["2019-10-30T11:40:00Z",
            100],
            ["2019-10-30T12:00:00Z",
            0],
            ["2019-10-30T12:20:00Z",
            0],
            ["2019-10-30T12:40:00Z",
            100],
            ["2019-10-30T13:00:00Z",
            0],
            ["2019-10-30T13:20:00Z",
            0],
            ["2019-10-30T13:40:00Z",
            100],
            ["2019-10-30T14:00:00Z",
            0],
            ["2019-10-30T14:20:00Z",
            0],
            ["2019-10-30T14:40:00Z",
            100],
            ["2019-10-30T15:00:00Z",
            0],
            ["2019-10-30T15:20:00Z",
            0],
            ["2019-10-30T15:40:00Z",
            100],
            ["2019-10-30T16:00:00Z",
            0],
            ["2019-10-30T16:20:00Z",
            0],
            ["2019-10-30T16:40:00Z",
            100],
            ["2019-10-30T17:30:00Z",
            50]],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [{
                "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
            }],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [{
                "rootJsonNode": null,
                "sectionName": null,
                "displayControls": [{
                    "property": "DetectionSummary",
                    "displayName": "What was detected?",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "FormattedOccurenceTime",
                    "displayName": "When did this occur?",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "DetectedFailureRate",
                    "displayName": "Detected failure rate",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "NormalFailureRate",
                    "displayName": "Normal failure rate",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "chartType": "Line",
                    "xAxisType": "Date",
                    "yAxisType": "Percentage",
                    "xAxisName": "",
                    "yAxisName": "",
                    "property": "FailureRateChart",
                    "displayName": "Failure rate over last 12 hours",
                    "type": "Chart",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "defaultLoad": true,
                    "displayConfig": [{
                        "rootJsonNode": null,
                        "sectionName": null,
                        "displayControls": [{
                            "showHeader": false,
                            "columns": [{
                                "property": "Name",
                                "displayName": "Name"
                            },
                            {
                                "property": "Value",
                                "displayName": "Value"
                            }],
                            "property": "tables[0].rows[0][0]",
                            "displayName": "All of the failed requests had these characteristics:",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": true
                        }]
                    }],
                    "property": "ArmSystemEventsRequest",
                    "displayName": "",
                    "type": "ARMRequest",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "showHeader": false,
                    "columns": [{
                        "property": "Link",
                        "displayName": "Link"
                    }],
                    "property": "LinksTable",
                    "displayName": "Links",
                    "type": "Table",
                    "isOptional": false,
                    "isPropertySerialized": false
                }]
            }]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>Een waarschuwing sorteren en vaststellen

Een waarschuwing geeft aan dat een abnormale toename van het aantal mislukte aanvragen is gedetecteerd. Waarschijnlijk is er sprake van een probleem met uw app of van de omgeving.

Als u verder wilt onderzoeken, klikt u op ' volledige details weer geven in Application Insights ' op de koppelingen op deze pagina gaat u rechtstreeks naar een [Zoek pagina](../../azure-monitor/app/diagnostic-search.md) die wordt gefilterd op de relevante aanvragen, uitzonde ringen, afhankelijkheid of traceringen. 

U kunt ook de [Azure Portal](https://portal.azure.com)openen, naar de Application Insights resource voor uw app gaan en de pagina fouten openen.

Als u op fouten opsporen klikt, krijgt u meer informatie en kunt u het probleem oplossen.

[![](./media/proactive-failure-diagnostics/051.png "Diagnostic search")](./media/proactive-failure-diagnostics/051.png#lightbox)

Op basis van het percentage aanvragen en het aantal betrokken gebruikers kunt u bepalen hoe urgent het probleem is. In het bovenstaande voor beeld wordt het uitval percentage van 78,5% vergeleken met een normaal percentage van 2,2%, wat erop wijst dat er iets mis gaat. Aan de andere kant werden slechts 46 gebruikers beïnvloed. Als dit uw app is, kunt u beoordelen hoe ernstig het is.

In veel gevallen kunt u het probleem snel vaststellen op basis van de gegevens van de aanvraag naam, uitzonde ring, fout bij het openen van een afhankelijkheid en traceer gegeven.

In dit voor beeld is er een uitzonde ring van SQL database omdat de aanvraag limiet is bereikt.

[![](./media/proactive-failure-diagnostics/052.png "Failed request details")](./media/proactive-failure-diagnostics/052.png#lightbox)

## <a name="review-recent-alerts"></a>Recente waarschuwingen controleren

Klik op **waarschuwingen** op de pagina Application Insights resource om de meest recente geactiveerde waarschuwingen weer te geven:

[![](./media/proactive-failure-diagnostics/070.png "Alerts summary")](./media/proactive-failure-diagnostics/070.png#lightbox)

## <a name="whats-the-difference-"></a>Wat is het verschil...
Slimme detectie van fout afwijkingen is een aanvulling op andere vergelijk bare functies van Application Insights.

* [Metrische waarschuwingen](../../azure-monitor/app/alerts.md) worden door u ingesteld en kunnen een breed scala aan metrische gegevens bewaken, zoals CPU-bezetting, aanvraag tarieven, pagina laad tijden, enzovoort. U kunt deze gebruiken om u te waarschuwen als u bijvoorbeeld meer resources wilt toevoegen. Slimme detectie van fout afwijkingen is daarentegen van toepassing op een klein aantal kritische metrische gegevens (momenteel alleen mislukte aanvragen), ontworpen om u vrijwel realtime te informeren wanneer de frequentie van de mislukte aanvragen van de web-app toeneemt in vergelijking met het normale gedrag van de web-app. In tegens telling tot metrische waarschuwingen worden door slimme detectie automatisch drempel waarden ingesteld en bijgewerkt als reactie wijzigingen in het gedrag optreden. Slimme detectie start ook het diagnostische werk voor u en bespaart u tijd bij het oplossen van problemen.

* [Slimme detectie van prestatie afwijkingen](proactive-performance-diagnostics.md) maakt ook gebruik van machine Intelligence om ongebruikelijke patronen in uw metrische gegevens te ontdekken. u hoeft hiervoor geen configuratie op te nemen. Maar in tegens telling tot Slimme detectie van fout afwijkingen, is het doel van de detectie van de prestatie afwijkingen te bepalen van de segmenten van uw gebruiks fragmenten die mogelijk niet goed worden aangeboden, bijvoorbeeld door specifieke pagina's van een specifiek type browser. De analyse wordt dagelijks uitgevoerd en als er een resultaat wordt gevonden, is het waarschijnlijk veel minder urgent dan een waarschuwing. Daarentegen wordt de analyse voor fout afwijkingen doorlopend uitgevoerd op binnenkomende toepassings gegevens en wordt u binnen enkele minuten gewaarschuwd als de snelheid van de server fouten groter is dan verwacht.

## <a name="if-you-receive-a-smart-detection-alert"></a>Als u een Smart detection-waarschuwing ontvangt
*Waarom heb ik deze waarschuwing ontvangen?*

* Er is een abnormale toename in de frequentie van mislukte aanvragen gedetecteerd vergeleken met de normale basis lijn van de voor gaande periode. Na de analyse van de fouten en de bijbehorende toepassings gegevens, denken we dat er een probleem is waarin u moet letten.

*Is er een probleem met de melding?*

* We proberen een waarschuwing te ontvangen over het onderbreken of degraderen van apps, maar alleen u kunt de semantiek en de impact van de app of gebruikers volledig begrijpen.

*En nu gaat u naar mijn toepassings gegevens?*

* Nee. De service is volledig automatisch. Alleen de meldingen worden weer geven. Uw gegevens zijn [privé](../../azure-monitor/app/data-retention-privacy.md).

*Moet ik zich abonneren op deze waarschuwing?*

* Nee. Elke toepassing die aanvraag gegevens verzendt, heeft de regel waarschuwing voor slimme detectie.

*Kan ik uw abonnement opzeggen of meldingen ontvangen die naar mijn collega's worden verzonden?*

* Ja, klik in waarschuwings regels op de regel voor slimme detectie om deze te configureren. U kunt de waarschuwing uitschakelen of ontvangers wijzigen voor de waarschuwing.

*Ik ben de e-mail kwijt geraakt. Waar vind ik de meldingen in de portal?*

* In de activiteiten Logboeken. Open in azure de Application Insights resource voor uw app en selecteer vervolgens activiteiten Logboeken.

*Sommige waarschuwingen zijn te vinden op bekende problemen en ik wil ze niet ontvangen.*

* U kunt de functie voor het onderdrukken van [waarschuwings regels](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-action-rules) gebruiken.

## <a name="next-steps"></a>Volgende stappen
Deze diagnostische hulpprogram ma's helpen u bij het controleren van de gegevens in uw app:

* [Metrische Explorer](../../azure-monitor/app/metrics-explorer.md)
* [Zoek Verkenner](../../azure-monitor/app/diagnostic-search.md)
* [Analyse-krachtige query taal](../../azure-monitor/log-query/get-started-portal.md)

Slimme detecties zijn automatisch. Maar misschien wilt u nog meer waarschuwingen instellen?

* [Hand matig geconfigureerde metrische waarschuwingen](../../azure-monitor/app/alerts.md)
* [Webtests voor Beschik baarheid](../../azure-monitor/app/monitor-web-app-availability.md)
