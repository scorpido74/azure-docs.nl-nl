---
title: Meerdere Azure Monitor Application Insights resources samen voegen | Microsoft Docs
description: In dit artikel vindt u informatie over het gebruik van een functie in Azure Monitor logboeken voor het opvragen van meerdere Application Insights resources en het visualiseren van die gegevens.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 07dd4c96ba51b1ac1e0cb2807c9e26df87a6daa7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75364965"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Meerdere Azure Monitor Application Insights resources samen voegen 
In dit artikel wordt beschreven hoe u al uw Application Insights logboek gegevens op één plek kunt opvragen en weer geven, zelfs wanneer ze zich in verschillende Azure-abonnementen bevinden, als vervanging voor de afschaffing van de Application Insights-connector. Het aantal Application Insights resources dat u in één query kunt toevoegen, is beperkt tot 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Aanbevolen benadering voor het opvragen van meerdere Application Insights resources 
Het weer geven van meerdere Application Insights resources in een query kan lastig en moeilijk te onderhouden zijn. In plaats daarvan kunt u gebruikmaken van de functie voor het scheiden van de query logica van de scopes van de toepassing.  

In dit voor beeld ziet u hoe u meerdere Application Insights resources kunt bewaken en het aantal mislukte aanvragen op toepassings naam visualiseren. Voordat u begint, voert u deze query uit in de werk ruimte die is verbonden met Application Insights resources om de lijst met verbonden toepassingen te verkrijgen: 

```
ApplicationInsights
| summarize by ApplicationName
```

Maak een functie met behulp van de operator Union met de lijst met toepassingen en sla de query vervolgens op in uw werk ruimte als functie met de alias *applicationsScoping*. 

U kunt de vermelde toepassingen op elk gewenst moment in de portal wijzigen door te navigeren naar query Explorer in uw werk ruimte en de functie te selecteren die u wilt bewerken en vervolgens op te slaan, of door de `SavedSearch` Power shell-cmdlet te gebruiken. 

>[!NOTE]
>Deze methode kan niet worden gebruikt met logboek waarschuwingen omdat de toegangs validatie van de resources van de waarschuwings regel, inclusief werk ruimten en toepassingen, wordt uitgevoerd tijdens het maken van de waarschuwing. Het toevoegen van nieuwe resources aan de functie nadat het maken van de waarschuwing niet wordt ondersteund. Als u de functie voor het bereik van resources in logboek waarschuwingen wilt gebruiken, moet u de waarschuwings regel in de portal of met een resource manager-sjabloon bewerken om de resources binnen het bereik bij te werken. U kunt ook de lijst met resources opnemen in de waarschuwings query voor Logboeken.

De `withsource= SourceApp` opdracht voegt een kolom toe aan de resultaten die de toepassing aanduidt die het logboek heeft verzonden. De operator parse is optioneel in dit voor beeld en wordt gebruikt om de toepassings naam op te halen uit de eigenschap SourceApp. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

U bent nu klaar om de functie applicationsScoping in de cross-resource query te gebruiken:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

De query maakt gebruik van Application Insights schema, hoewel de query wordt uitgevoerd in de werk ruimte, omdat de functie applicationsScoping de Application Insights gegevens structuur retourneert. De functie alias retourneert de samen voeging van de aanvragen van alle gedefinieerde toepassingen. De query filtert vervolgens op mislukte aanvragen en visualiseert de trends op basis van de toepassing.

![Voor beeld van resultaten van cross-query](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Query's uitvoeren op Application Insights resources en werkruimte gegevens 
Wanneer u de connector stopt en query's moet uitvoeren over een tijds bereik dat is bijgesneden door Application Insights gegevens retentie (90 dagen), moet u [query's voor meerdere resources](../../azure-monitor/log-query/cross-workspace-query.md) op de werk ruimte uitvoeren en Application Insights resources voor een tussenliggende periode. Dit is tot de gegevens van uw toepassingen worden verzameld op basis van de nieuwe Application Insights voor het bewaren van gegevens. Voor de query zijn enkele bewerkingen vereist, aangezien de schema's in Application Insights en de werk ruimte verschillend zijn. Zie de tabel verderop in deze sectie om de schema verschillen te markeren. 

>[!NOTE]
>Er wordt een [query voor meerdere resources](../log-query/cross-workspace-query.md) in logboek waarschuwingen ondersteund in de nieuwe [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Azure Monitor maakt standaard gebruik van de [verouderde log Analytics waarschuwings-API](../platform/api-alerts.md) voor het maken van nieuwe logboek waarschuwings regels van Azure Portal, tenzij u overschakelt van [VERouderde API voor logboek waarschuwingen](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Na de switch wordt de nieuwe API de standaard instelling voor nieuwe waarschuwings regels in Azure Portal en kunt u regels voor het maken van query logboek waarschuwingen voor meerdere resources. U kunt waarschuwings regels voor het query logboek voor [meerdere resources](../log-query/cross-workspace-query.md) maken zonder de switch te maken met behulp van de [arm-sjabloon voor de scheduledQueryRules-API](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) , maar deze waarschuwings regel kan wel worden beheerd met [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) en niet vanuit Azure Portal.

Als de connector bijvoorbeeld niet meer werkt op 2018-11-01, wordt uw query, zoals in het volgende voor beeld, geconstrueerd als u Logboeken doorzoekt tussen Application Insights resources en toepassings gegevens in de werk ruimte:

```
applicationsScoping //this brings data from Application Insights resources 
| where timestamp between (datetime("2018-11-01") .. now()) 
| where success == 'False' 
| where duration > 1000 
| union ( 
    ApplicationInsights //this is Application Insights data in Log Analytics workspace 
    | where TimeGenerated < (datetime("2018-12-01") 
    | where RequestSuccess == 'False' 
    | where RequestDuration > 1000 
    | extend duration = RequestDuration //align to Application Insights schema 
    | extend timestamp = TimeGenerated //align to Application Insights schema 
    | extend name = RequestName //align to Application Insights schema 
    | extend resultCode = ResponseCode //align to Application Insights schema 
    | project-away RequestDuration , RequestName , ResponseCode , TimeGenerated 
) 
| project timestamp , duration , name , resultCode 
```

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Verschillen in schema Application Insights en Log Analytics van werk ruimten
In de volgende tabel ziet u de schema verschillen tussen Log Analytics en Application Insights.  

| Eigenschappen van Log Analytics werk ruimte| Eigenschappen van Application Insights-bron|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | Toepassings|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | tijdstempel |
| Browser | client_browser |
| Plaats | client_city |
| ClientIP | client_IP |
| Computer | cloud_RoleInstance | 
| Land/regio | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | type |
| Bewerkings-id | operation_id |
| OperationName | operation_Name | 
| Besturingssysteem | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| Aanvraag-id | id | 
| RequestName | name | 
| RequestSuccess | voltooid | 
| ResponseCode | ResultCode | 
| Rol | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| sessie-id | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Volgende stappen

Gebruik [zoeken in logboeken](../../azure-monitor/log-query/log-query-overview.md) om gedetailleerde informatie voor uw Application Insights-apps weer te geven.
