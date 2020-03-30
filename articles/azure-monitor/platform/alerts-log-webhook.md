---
title: Webhook-acties voor logboekwaarschuwingen in Azure-waarschuwingen
description: In dit artikel wordt beschreven hoe u een logboekwaarschuwingsregel maakt met behulp van de werkruimte Log Analytics of Application Insights, hoe de waarschuwing gegevens als een HTTP-webhook pusht en de details van de verschillende aanpassingen die mogelijk zijn.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 7b1956ad2bf9bf38ba9edc4c7234078557564071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667700"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhook-acties voor logboekwaarschuwingsregels
Wanneer een [logboekwaarschuwing wordt gemaakt in Azure,](alerts-log.md)u [deze configureren door actiegroepen](action-groups.md) te gebruiken om een of meer acties uit te voeren. In dit artikel worden de verschillende webhook-acties beschreven die beschikbaar zijn en wordt weergegeven hoe u een aangepaste JSON-gebaseerde webhook configureren.

> [!NOTE]
> U ook het [algemene waarschuwingsschema](https://aka.ms/commonAlertSchemaDocs) gebruiken voor uw webhook-integraties. Het algemene waarschuwingsschema biedt het voordeel dat er één uitbreidbare en uniforme waarschuwingspayload is voor alle waarschuwingsservices in Azure Monitor.Houd er rekening mee dat het algemene waarschuwingsschema niet voldoet aan de aangepaste JSON-optie voor logboekwaarschuwingen. Het stelt uit naar de algemene waarschuwing schema payload als dat is geselecteerd, ongeacht de aanpassing die u zou kunnen hebben gedaan op het niveau van de waarschuwingsregel. [Meer informatie over de algemene definities van waarschuwingsschema's.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Webhookacties

Met webhook-acties u een extern proces aanroepen via één HTTP POST-verzoek. De service die wordt aangeroepen moet webhooks ondersteunen en bepalen hoe het laadvermogen wordt gebruikt.

Webhook-acties vereisen de eigenschappen in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| **Webhook-URL** |De URL van de webhook. |
| **Aangepaste JSON-payload** |De aangepaste payload te sturen met de webhook wanneer deze optie wordt gekozen tijdens het maken van waarschuwingen. Zie [Logboekwaarschuwingen beheren](alerts-log.md)voor meer informatie .|

> [!NOTE]
> Met de knop **Webhook weergeven** naast de **optie Aangepaste JSON-payload opnemen voor webhook** voor de logboekwaarschuwing, wordt het voorbeeld webhook-payload weergegeven voor de geleverde aanpassing. Het bevat geen werkelijke gegevens, maar is representatief voor het JSON-schema dat wordt gebruikt voor logboekwaarschuwingen. 

Webhooks bevatten een URL en een payload geformatteerd in JSON dat de gegevens verzonden naar de externe dienst. Standaard bevat de payload de waarden in de volgende tabel. U ervoor kiezen om deze payload te vervangen door een aangepaste een van uw eigen. Gebruik in dat geval de variabelen in de tabel voor elk van de parameters om hun waarden op te nemen in uw aangepaste payload.


| Parameter | Variabele | Beschrijving |
|:--- |:--- |:--- |
| *WaarschuwingsregelNaam* |#alertrulename |Naam van de waarschuwingsregel. |
| *Ernst* |#severity |Ernst ingesteld voor de ontslagen log alert. |
| *AlertThresholdOperator* |#thresholdoperator |Drempeloperator voor de waarschuwingsregel, die groter dan of lager gebruikt dan. |
| *AlertThresholdValue AlertThresholdValue AlertThresholdValue AlertThreshold* |#thresholdvalue |Drempelwaarde voor de waarschuwingsregel. |
| *Koppeling naar zoekresultaten* |#linktosearchresults |Koppeling naar de Analytics-portal die de records retourneert van de query die de waarschuwing heeft gemaakt. |
| *Aantal resultaten* |#searchresultcount |Aantal records in de zoekresultaten. |
| *Eindtijd zoekinterval* |#searchintervalendtimeutc |Eindtijd voor de query in UTC, met het formaat mm/dd/yyyy HH:mm:ss AM/PM. |
| *Zoekinterval* |#searchinterval |Tijdvenster voor de waarschuwingsregel, met het formaat HH:mm:ss. |
| *Starttijd zoekinterval* |#searchintervalstarttimeutc |Begintijd voor de query in UTC, met het formaat mm/dd/yyyy HH:mm:ss AM/PM. 
| *ZoekenQuery* |#searchquery |Logboekzoekopdracht die wordt gebruikt door de waarschuwingsregel. |
| *Zoekresultaten* |"IncludeSearchResults": true|Records die door de query worden geretourneerd als een JSON-tabel, beperkt tot de eerste 1.000 records, als 'IncludeSearchResults': true wordt toegevoegd in een aangepaste JSON-webhook-definitie als eigenschap op het hoogste niveau. |
| *Waarschuwingstype*| #alerttype | Het type logboekwaarschuwingsregel dat is geconfigureerd als [metrische meting](alerts-unified-log.md#metric-measurement-alert-rules) of [aantal resultaten](alerts-unified-log.md#number-of-results-alert-rules).|
| *Werkruimte-id* |#workspaceid |ID van uw Log Analytics-werkruimte. |
| *Toepassings-id* |#applicationid |ID van uw Application Insights-app. |
| *Subscription ID* |#subscriptionid |ID van uw Azure-abonnement gebruikt. 

> [!NOTE]
> *LinkToSearchResults* geeft parameters zoals *SearchQuery,* *Search Interval StartTime*en *Einddatum zoekinterval* in de URL door aan de Azure-portal voor weergave in de sectie Analytics. De Azure-portal heeft een URI-groottelimiet van ongeveer 2.000 tekens. De portal opent *geen* koppelingen in waarschuwingen als de parameterwaarden de limiet overschrijden. U handmatig gegevens invoeren om resultaten weer te geven in de Analytics-portal. U ook de [Application Insights Analytics REST API](https://dev.applicationinsights.io/documentation/Using-the-API) of de Log Analytics REST [API](/rest/api/loganalytics/) gebruiken om resultaten programmatisch op te halen. 

U bijvoorbeeld de volgende aangepaste payload opgeven die één parameter bevat die tekst wordt *genoemd.* De service die deze webhook aanroept, verwacht deze parameter.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Dit voorbeeld payload verdwijnt naar iets als de volgende wanneer het wordt verzonden naar de webhook:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Omdat alle variabelen in een aangepaste webhook moeten worden opgegeven in een JSON-behuizing, zoals '#searchinterval', heeft de resulterende webhook ook variabele gegevens in behuizingen, zoals '00:05:00'.

Als u zoekresultaten wilt opnemen in een aangepaste payload, moet u ervoor zorgen dat **IncludeSearchResults** is ingesteld als een eigenschap op het hoogste niveau in de JSON-payload. 

## <a name="sample-payloads"></a>Monsterpayloads
In deze sectie worden voorbeeldpayloads voor webhooks voor logboekwaarschuwingen weergegeven. De sample payloads bevatten voorbeelden wanneer de payload standaard is en wanneer het op maat is.

### <a name="standard-webhook-for-log-alerts"></a>Standaard webhook voor logboekwaarschuwingen 
Beide voorbeelden hebben een dummy payload met slechts twee kolommen en twee rijen.

#### <a name="log-alert-for-log-analytics"></a>Logboekwaarschuwing voor Logboekanalyse
De volgende voorbeeldpayload is voor een standaard webhook-actie *zonder een aangepaste JSON-optie* die wordt gebruikt voor waarschuwingen op basis van Log Analytics:

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
> De veldwaarde 'Ernst' kan veranderen als u [uw API-voorkeur](alerts-log-api-switch.md) hebt ingeschakeld voor logboekwaarschuwingen op Log Analytics.


#### <a name="log-alert-for-application-insights"></a>Logboekwaarschuwing voor toepassingsinzichten
De volgende voorbeeldpayload is voor een standaard webhook *zonder een aangepaste JSON-optie* wanneer deze wordt gebruikt voor logboekwaarschuwingen op basis van Application Insights:
    
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

#### <a name="log-alert-with-custom-json-payload"></a>Logboekwaarschuwing met aangepaste JSON-payload
Als u bijvoorbeeld een aangepaste payload wilt maken die alleen de waarschuwingsnaam en de zoekresultaten bevat, u het volgende gebruiken: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

De volgende voorbeeldpayload is voor een aangepaste webhook-actie voor elke logboekwaarschuwing:
    
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
- Meer informatie over [logboekwaarschuwingen in Azure-waarschuwingen](alerts-unified-log.md).
- Meer informatie over het [beheren van logboekwaarschuwingen in Azure.](alerts-log.md)
- Actiegroepen maken en beheren [in Azure](action-groups.md).
- Meer informatie over [applicatie-inzichten](../../azure-monitor/app/analytics.md).
- Meer informatie over [logboekquery's](../log-query/log-query-overview.md). 

