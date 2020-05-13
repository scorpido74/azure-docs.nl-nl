---
title: Query's uitvoeren op resources met Azure Monitor | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een query kunt uitvoeren op resources uit meerdere werk ruimten en app Insights-app in uw abonnement.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2020
ms.openlocfilehash: 83c33e6935de7c9ed9f1b2c9f97aa18dd6b10f01
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199915"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Query's tussen bronnen en logboeken uitvoeren in Azure Monitor  

> [!IMPORTANT]
> Als u een [Application Insights resource-](../app/create-workspace-resource.md) telemetrie op basis van een werk ruimte gebruikt, wordt deze opgeslagen in een log Analytics werk ruimte met alle andere logboek gegevens. Gebruik de log ()-expressie voor het schrijven van een query die toepassing bevat in meerdere werk ruimten. Voor meerdere toepassingen in dezelfde werk ruimte hebt u geen query op meerdere werk ruimten nodig.

Azure Monitor voorheen kon u alleen gegevens uit de huidige werk ruimte analyseren, maar u kunt ook een query uitvoeren op meerdere werk ruimten die in uw abonnement zijn gedefinieerd.  Daarnaast kunt u alleen vanuit uw webtoepassing verzamelde telemetriegegevens doorzoeken met Application Insights rechtstreeks in Application Insights of vanuit Visual Studio. Hierdoor is het ook een uitdaging om operationele en toepassings gegevens samen te analyseren.

U kunt nu niet alleen een query uitvoeren op meerdere Log Analytics-werk ruimten, maar ook gegevens uit een specifieke Application Insights-app in dezelfde resource groep, een andere resource groep of een ander abonnement. Dit geeft u een overzicht van uw gegevens op het hele systeem. U kunt deze typen query's alleen uitvoeren in [log Analytics](portals.md).

## <a name="cross-resource-query-limits"></a>Limieten voor meerdere bron query's 

* Het aantal Application Insights resources en Log Analytics werk ruimten die u in één query kunt toevoegen, is beperkt tot 100.
* Query op meerdere resources wordt niet ondersteund in de ontwerp functie voor weer gaven. U kunt een query in Log Analytics ontwerpen en deze vastmaken aan Azure dash board om [een logboek query te visualiseren](../learn/tutorial-logs-dashboards.md). 
* Er wordt een query voor meerdere resources in logboek waarschuwingen ondersteund in de nieuwe [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Azure Monitor maakt standaard gebruik van de [verouderde log Analytics waarschuwings-API](../platform/api-alerts.md) voor het maken van nieuwe logboek waarschuwings regels van Azure Portal, tenzij u overschakelt van [VERouderde API voor logboek waarschuwingen](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Na de switch wordt de nieuwe API de standaard instelling voor nieuwe waarschuwings regels in Azure Portal en kunt u regels voor het maken van query logboek waarschuwingen voor meerdere resources. U kunt waarschuwings regels voor het query logboek voor meerdere resources maken zonder de switch te maken met behulp van de [Azure Resource Manager sjabloon voor scheduledQueryRules-API](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) , maar deze waarschuwings regel kan wel worden beheerd met [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) en niet vanuit Azure Portal.


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Query's uitvoeren voor Log Analytics-werk ruimten en van Application Insights
Als u wilt verwijzen naar een andere werk ruimte in uw query, gebruikt u de [*werk ruimte*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) -id en gebruikt u voor een app uit Application Insights de [*app*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression) -id.  

### <a name="identifying-workspace-resources"></a>Werkruimte resources identificeren
In de volgende voor beelden ziet u query's over Log Analytics werk ruimten voor het retour neren van het aantal logboeken dat is opgegeven in de update tabel van een werk ruimte met de naam *contosoretail*. 

Het identificeren van een werk ruimte kan op een van de volgende manieren worden uitgevoerd:

* Resource naam: is een door een mens lees bare naam van de werk ruimte, ook wel *onderdeel naam*genoemd. 

    `workspace("contosoretail-it").Update | count`

* Gekwalificeerde naam: is de volledige naam van de werk ruimte, die bestaat uit de naam van het abonnement, de resource groep en de naam van het onderdeel in deze indeling, zoals *subscriptionname/resourceGroup/* naam van onderdeel. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Omdat namen van Azure-abonnementen niet uniek zijn, is deze id mogelijk dubbel zinnig. 
    >

* Werk ruimte-ID: een werk ruimte-ID is de unieke, onveranderlijke id die is toegewezen aan elke werk ruimte die wordt weer gegeven als een Globally Unique Identifier (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure-Resource-ID: de door Azure gedefinieerde unieke identiteit van de werk ruimte. U gebruikt de resource-ID als de resource naam dubbel zinnig is.  Voor werk ruimten is de indeling: */Subscriptions/subscriptionId/ResourceGroups/resourceGroup/providers/Microsoft. OperationalInsights/werk ruimten/onderdeelnaam*.  

    Bijvoorbeeld:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Een toepassing identificeren
De volgende voor beelden geven een samen vatting van het aantal aanvragen dat is gemaakt voor een app met de naam *fabrikamapp* in Application Insights. 

Het identificeren van een toepassing in Application Insights kan worden uitgevoerd met de *app-expressie (id)* .  Met het argument *id* geeft u de app op met behulp van een van de volgende opties:

* Resource naam: is een door de mens lees bare naam van de app, ook wel de naam van het *onderdeel*genoemd.  

    `app("fabrikamapp")`

    >[!NOTE]
    >Als u de naam van een toepassing identificeert, wordt ervan uitgegaan dat deze uniek is voor alle toegankelijke abonnementen. Als u meerdere toepassingen met de opgegeven naam hebt, mislukt de query vanwege de dubbel zinnigheid. In dit geval moet u een van de andere id's gebruiken.

* Gekwalificeerde naam: is de volledige naam van de app, die bestaat uit de naam van het abonnement, de resource groep en de naam van het onderdeel in deze indeling, zoals *subscriptionname/resourceGroup/* naam van onderdeel. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Omdat namen van Azure-abonnementen niet uniek zijn, is deze id mogelijk dubbel zinnig. 
    >

* ID: de app-GUID van de toepassing.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure-Resource-ID: de door Azure gedefinieerde unieke identiteit van de app. U gebruikt de resource-ID als de resource naam dubbel zinnig is. De indeling is: */Subscriptions/subscriptionId/ResourceGroups/resourceGroup/providers/Microsoft. OperationalInsights/onderdelen/onderdeelnaam*.  

    Bijvoorbeeld:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Een query uitvoeren op meerdere resources
U kunt een query uitvoeren op meerdere resources uit een van uw resource-exemplaren. dit kunnen werk ruimten en apps zijn.
    
Voor beeld voor query's in twee werk ruimten:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Query's voor meerdere bronnen gebruiken voor meer resources
Bij het gebruik van query's tussen meerdere bronnen voor het correleren van gegevens uit verschillende Log Analytics werk ruimten en Application Insights resources, kan de query complex en lastig worden onderhouden. U moet gebruikmaken van [functies in azure monitor-logboek query's](functions.md) om de query logica te scheiden van het bereik van de query resources, waardoor de query structuur wordt vereenvoudigd. In het volgende voor beeld ziet u hoe u meerdere Application Insights resources kunt bewaken en het aantal mislukte aanvragen op toepassings naam visualiseren. 

Maak een query zoals de volgende waarmee wordt verwezen naar het bereik van Application Insights-resources. De `withsource= SourceApp` opdracht voegt een kolom toe die de naam van de toepassing aanduidt die het logboek heeft verzonden. [Sla de query op als functie](functions.md#create-a-function) met de alias _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



U kunt [deze functie nu gebruiken](../../azure-monitor/log-query/functions.md#use-a-function) in een query voor meerdere resources, zoals in de volgende. De functie alias _applicationsScoping_ retourneert de samen voeging van de tabel aanvragen van alle gedefinieerde toepassingen. De query filtert vervolgens op mislukte aanvragen en visualiseert de trends op basis van de toepassing. In dit voor beeld is de operator _parse_ optioneel. De naam van de toepassing wordt geëxtraheerd uit de eigenschap _SourceApp_ .

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
>Deze methode kan niet worden gebruikt met logboek waarschuwingen omdat de toegangs validatie van de resources van de waarschuwings regel, inclusief werk ruimten en toepassingen, wordt uitgevoerd tijdens het maken van de waarschuwing. Het toevoegen van nieuwe resources aan de functie nadat het maken van de waarschuwing niet wordt ondersteund. Als u de functie voor het bereik van resources in logboek waarschuwingen wilt gebruiken, moet u de waarschuwings regel in de portal of met een resource manager-sjabloon bewerken om de resources binnen het bereik bij te werken. U kunt ook de lijst met resources opnemen in de waarschuwings query voor Logboeken.


![Timechart](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Volgende stappen

- Bekijk [logboek gegevens analyseren in azure monitor](log-query-overview.md) voor een overzicht van logboek query's en hoe Azure monitor logboek gegevens zijn gestructureerd.
- Bekijk [Azure monitor-logboek query's](query-language.md) om alle resources voor Azure monitor-logboek query's weer te geven.