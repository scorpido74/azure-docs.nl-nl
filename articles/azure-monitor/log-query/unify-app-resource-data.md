---
title: Meerdere Azure Monitor Application Insights-bronnen verenigen | Microsoft Documenten
description: In dit artikel vindt u informatie over het gebruik van een functie in Azure Monitor Logs om meerdere Toepassingsinzichtenbronnen op te vragen en die gegevens te visualiseren.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: ce58aae3b1db1f0f338d353025d4f277aeb6944f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137502"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Meerdere Azure Monitor Application Insights-bronnen verenigen 
In dit artikel wordt beschreven hoe u al uw loggegevens van Application Insights op één plaats opvragen en weergeven, zelfs wanneer ze zich in verschillende Azure-abonnementen bevinden, als vervanging voor de afschrijving van de Application Insights Connector. Het aantal toepassingsinzichten dat u in één query opnemen, is beperkt tot 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Aanbevolen aanpak voor het opvragen van meerdere Resources voor Application Insights 
Het aanbieden van meerdere Toepassingsinsights-bronnen in een query kan omslachtig en moeilijk te onderhouden zijn. In plaats daarvan u de functie gebruiken om de querylogica te scheiden van de scoping van toepassingen.  

In dit voorbeeld wordt uitgelegd hoe u meerdere resources voor Application Insights controleren en het aantal mislukte aanvragen visualiseren op naam van de toepassing.

Maak een functie met de union operator met de lijst met toepassingen en sla de query op in uw werkruimte als functie met de alias *applicationsScoping*. 

U de vermelde toepassingen op elk gewenst moment in de portal wijzigen door naar Query explorer in uw `SavedSearch` werkruimte te navigeren en de functie te selecteren voor het bewerken en vervolgens opslaan of met de PowerShell-cmdlet. 

>[!NOTE]
>Deze methode kan niet worden gebruikt met logboekwaarschuwingen omdat de toegangsvalidatie van de bronnen met de waarschuwingsregel, inclusief werkruimten en toepassingen, wordt uitgevoerd op het maken van waarschuwingen. Het toevoegen van nieuwe resources aan de functie na het maken van de waarschuwing wordt niet ondersteund. Als u de functie liever gebruikt voor resourcescoping in logboekwaarschuwingen, moet u de waarschuwingsregel in de portal of met een resourcemanagersjabloon bewerken om de scoped resources bij te werken. U ook de lijst met bronnen opnemen in de waarschuwingsquery voor logboeken.

De `withsource= SourceApp` opdracht voegt een kolom toe aan de resultaten die de toepassing aanduidt die het logboek heeft verzonden. De parse operator is optioneel in dit voorbeeld en gebruikt om de naam van de toepassing uit de eigenschap SourceApp te extraheren. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

U bent nu klaar om de functie ApplicationsScoping te gebruiken in de cross-resource query:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

De query maakt gebruik van het Application Insights-schema, hoewel de query wordt uitgevoerd in de werkruimte omdat de functie applicationsScoping de gegevensstructuur Application Insights retourneert. De functiealias retourneert de samenvoeging van de aanvragen van alle gedefinieerde toepassingen. De query filtert vervolgens op mislukte aanvragen en visualiseert de trends per toepassing.

![Voorbeeld van resultaten voor kruisquery's](media/unify-app-resource-data/app-insights-query-results.png)

>[!NOTE]
>Logboekwaarschuwingen voor [cross-resourcequery's](../log-query/cross-workspace-query.md) worden ondersteund in de nieuwe [api voor geplandeQueryregels](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Azure Monitor gebruikt standaard de [verouderde Log Analytics Alert API](../platform/api-alerts.md) voor het maken van nieuwe logboekwaarschuwingsregels vanuit Azure portal, tenzij u overstapt van de verouderde Api voor [logboekwaarschuwingen.](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) Na de switch wordt de nieuwe API de standaardvoor nieuwe waarschuwingsregels in azure-portal en u regels voor waarschuwingen voor querylogboeken met meerdere resources maken. U [waarschuwingsregels voor querylogboeken met meerdere bronnen](../log-query/cross-workspace-query.md) maken zonder de overstap te maken met de [ARM-sjabloon voor de geplande QueryRules-API,](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) maar deze waarschuwingsregel is beheersbaar via [de GeplandeQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) en niet vanuit azure-portal.

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Schemaverschillen in toepassingsinzichten en logboekanalysewerkruimte
In de volgende tabel worden de schemaverschillen tussen Log Analytics en Application Insights weergegeven.  

| Eigenschappen van log Analytics-werkruimte| Resourceeigenschappen van Application Insights|
|------------|------------| 
| AnonUserId (AnonUserId) | user_id|
| ApplicationId | appId|
| ApplicationName | Appname|
| Toepassingstypeversie | application_Version |
| BeschikbaarheidAantal | ItemAantal |
| BeschikbaarheidDuur | duur |
| BeschikbaarheidMessage | message |
| BeschikbaarheidRunLocatie | location |
| BeschikbaarheidTestId | id |
| BeschikbaarheidTestName | name |
| BeschikbaarheidTimestamp | tijdstempel |
| Browser | client_browser |
| Plaats | client_city |
| ClientIP | client_IP |
| Computer | cloud_RoleInstance | 
| Land | client_CountryOrRegion | 
| AangepastEventAantal | ItemAantal | 
| AangepasteAfmetingen voor gebeurtenissen | aangepaste afmetingen |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| Aantal uitzonderingen | ItemAantal | 
| Exceptionhandledat | behandeldOp |
| ExceptionMessage | message | 
| ExceptionType | type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| OS | client_OS | 
| PageViewCount PageViewCount PageViewCount PageView | ItemAantal |
| PageViewDuur | duur | 
| PageViewName PageViewName | name | 
| ParentOperationID | operation_Id | 
| Aantal aanvragen | ItemAantal | 
| AanvraagDuur | duur | 
| RequestID | id | 
| RequestName | name | 
| VerzoekSucces | voltooid | 
| ResponseCode | resultaatCode | 
| Rol | cloud_RoleName |
| Rolinstantie | cloud_RoleInstance |
| Sessionid | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetrieTYpe | type |
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Volgende stappen

Gebruik [Logboekzoeken](../../azure-monitor/log-query/log-query-overview.md) om gedetailleerde informatie voor uw Application Insights-apps weer te geven.
