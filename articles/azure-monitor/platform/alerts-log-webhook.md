---
title: Webhook-acties voor logboek waarschuwingen in azure-waarschuwingen
description: In dit artikel wordt beschreven hoe u een waarschuwings regel voor het logboek maakt met behulp van de Log Analytics-werk ruimte of de Application Insights, hoe de waarschuwing gegevens pusht als een HTTP-webhook en de details van de verschillende aanpassingen die mogelijk zijn.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 7b1956ad2bf9bf38ba9edc4c7234078557564071
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77667700"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhook-acties voor waarschuwings regels voor logboeken
Wanneer er een [logboek waarschuwing wordt gemaakt in azure](alerts-log.md), kunt u [deze configureren met behulp van actie groepen](action-groups.md) om een of meer acties uit te voeren. In dit artikel worden de verschillende webhook-acties beschreven die beschikbaar zijn en wordt getoond hoe u een aangepaste JSON-gebaseerde webhook configureert.

> [!NOTE]
> U kunt ook het [algemene waarschuwings schema](https://aka.ms/commonAlertSchemaDocs) voor uw webhook-integraties gebruiken. Het algemene waarschuwings schema biedt het voor deel dat er één uitbreid bare en Unified alert Payload is voor alle waarschuwings Services in Azure Monitor. Houd er rekening mee dat het algemene waarschuwings schema de aangepaste JSON-optie voor logboek waarschuwingen niet in de hand heeft. De waarschuwing wordt uitgesteld naar de nettolading van het algemene waarschuwings schema als dat is geselecteerd, ongeacht de aanpassing die u mogelijk hebt gedaan op het niveau van de waarschuwings regel. [Meer informatie over de algemene schema definities voor waarschuwingen.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Webhookacties

Met de acties voor webhook kunt u een extern proces aanroepen via één HTTP POST-aanvraag. De aangeroepen service moet webhooks ondersteunen en bepalen hoe de ontvangen nettoladingen worden gebruikt.

Webhook-acties vereisen de eigenschappen in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| **Webhook-URL** |De URL van de webhook. |
| **Aangepaste JSON-nettolading** |De aangepaste nettolading die met de webhook moet worden verzonden wanneer deze optie wordt gekozen tijdens het maken van de waarschuwing. Zie [logboek waarschuwingen beheren](alerts-log.md)voor meer informatie.|

> [!NOTE]
> Op de knop **webhook weer geven** naast de optie **aangepaste JSON-nettolading voor webhook toevoegen** voor de logboek waarschuwing wordt de voor beeld-webhook Payload weer gegeven voor de aanpassing die is geleverd. Het bevat geen werkelijke gegevens, maar is representatief voor het JSON-schema dat wordt gebruikt voor logboek waarschuwingen. 

Webhooks bevatten een URL en een nettolading die in JSON is ingedeeld en die de gegevens die naar de externe service worden verzonden. De payload bevat standaard de waarden in de volgende tabel. U kunt ervoor kiezen om deze Payload te vervangen door een aangepaste versie. In dat geval gebruikt u de variabelen in de tabel voor elk van de para meters om hun waarden in uw aangepaste nettolading op te laten staan.


| Parameter | Variabele | Beschrijving |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |De naam van de waarschuwings regel. |
| *Ernst* |#severity |Ernst ingesteld voor de waarschuwing voor het geactiveerde logboek. |
| *AlertThresholdOperator* |#thresholdoperator |De drempel operator voor de waarschuwings regel, die groter dan of kleiner dan is. |
| *AlertThresholdValue* |#thresholdvalue |Drempel waarde voor de waarschuwings regel. |
| *LinkToSearchResults* |#linktosearchresults |Koppeling naar de analyse portal die de records retourneert uit de query die de waarschuwing heeft gemaakt. |
| *ResultCount* |#searchresultcount |Het aantal records in de zoek resultaten. |
| *Eind tijd van zoek interval* |#searchintervalendtimeutc |De eind tijd voor de query in UTC, met de notatie mm/dd/jjjj uu: mm: SS AM/PM. |
| *Zoek interval* |#searchinterval |Tijd venster voor de waarschuwings regel, met de indeling uu: mm: SS. |
| *Tijd in zoek interval* |#searchintervalstarttimeutc |Begin tijd voor de query in UTC met de notatie mm/dd/jjjj uu: mm: SS AM/PM. 
| *Search query* |#searchquery |Zoek query in logboek die wordt gebruikt door de waarschuwings regel. |
| *SearchResults* |"IncludeSearchResults": True|Records die worden geretourneerd door de query als een JSON-tabel, beperkt tot de eerste 1.000 records, als "IncludeSearchResults": True, wordt toegevoegd aan een aangepaste JSON-webhook-definitie als eigenschap op het hoogste niveau. |
| *Waarschuwings type*| #alerttype | Het type waarschuwings regel voor het logboek dat is geconfigureerd als [metrische meet](alerts-unified-log.md#metric-measurement-alert-rules) waarde of [aantal resultaten](alerts-unified-log.md#number-of-results-alert-rules).|
| *WorkspaceID* |#workspaceid |ID van uw Log Analytics-werk ruimte. |
| *Toepassings-ID* |#applicationid |ID van uw Application Insights-app. |
| *Subscription ID* |#subscriptionid |De ID van uw Azure-abonnement dat wordt gebruikt. 

> [!NOTE]
> *LinkToSearchResults* geeft para meters zoals *search query*, *Zoek*tijd en zoek *interval eind tijd* in de URL naar de Azure portal voor weer gave in de sectie Analytics. Het Azure Portal heeft een maximale URI-grootte van ongeveer 2.000 tekens. In de portal worden *geen* koppelingen geopend die in waarschuwingen zijn opgenomen als de parameter waarden de limiet overschrijden. U kunt de details hand matig invoeren om de resultaten in de analyse portal weer te geven. U kunt ook de [Application Insights Analytics rest API](https://dev.applicationinsights.io/documentation/Using-the-API) of de [log Analytics rest API](/rest/api/loganalytics/) gebruiken om de resultaten op te halen via een programma. 

U kunt bijvoorbeeld de volgende aangepaste nettolading opgeven die een enkele para meter met de naam *Text*bevat. Deze para meter wordt verwacht door de service die deze webhook aanroept.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
In dit voor beeld wordt het volgende omgezet naar de webhook:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Omdat alle variabelen in een aangepaste webhook moeten worden opgegeven binnen een JSON-behuizing, zoals ' #searchinterval ', heeft de resulterende webhook ook variabele gegevens in bijlagen, zoals ' 00:05:00 '.

Als u zoek resultaten in een aangepaste nettolading wilt toevoegen, moet u ervoor zorgen dat **IncludeSearchResults** is ingesteld als eigenschap op het hoogste niveau in de JSON-nettolading. 

## <a name="sample-payloads"></a>Voor beeld van nettoladingen
In deze sectie vindt u voor beelden van nettoladingen voor webhooks voor logboek waarschuwingen. De nettolading van de steek proeven bevatten voor beelden wanneer de payload standaard is en wanneer deze aangepast is.

### <a name="standard-webhook-for-log-alerts"></a>Standaard webhook voor logboek waarschuwingen 
Beide voor beelden hebben een dummy Payload met slechts twee kolommen en twee rijen.

#### <a name="log-alert-for-log-analytics"></a>Logboek waarschuwing voor Log Analytics
De volgende voor beeld-nettolading is voor een standaard-webhook-actie *zonder een aangepaste JSON-optie* die wordt gebruikt voor waarschuwingen op basis van log Analytics:

```json
{
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": "log alert rule",
    "Severity": "Warning",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
 }
 ```

> [!NOTE]
> De waarde van het veld ' Ernst ' kan worden gewijzigd als u [uw API-voor keur hebt overgeschakeld](alerts-log-api-switch.md) voor logboek waarschuwingen op Log Analytics.


#### <a name="log-alert-for-application-insights"></a>Logboek waarschuwing voor Application Insights
De volgende voor beeld-nettolading is voor een standaard-webhook *zonder aangepaste JSON-optie* als deze wordt gebruikt voor logboek waarschuwingen op basis van Application Insights:
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"requests | where resultCode == \"500\"",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
    "AlertType": "Number of results"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Waarschuwing vastleggen met aangepaste JSON-nettolading
Als u bijvoorbeeld een aangepaste nettolading wilt maken die alleen de naam van de waarschuwing en de zoek resultaten bevat, kunt u het volgende gebruiken: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

De volgende voor beeld-nettolading is voor een aangepaste webhook-actie voor een logboek waarschuwing:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [logboek waarschuwingen in azure-waarschuwingen](alerts-unified-log.md).
- Meer informatie over het [beheren van logboek waarschuwingen in azure](alerts-log.md).
- Actie groepen maken en beheren [in azure](action-groups.md).
- Meer informatie over [Application Insights](../../azure-monitor/app/analytics.md).
- Meer informatie over [logboek query's](../log-query/log-query-overview.md). 

