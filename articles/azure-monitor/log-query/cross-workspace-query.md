---
title: Query's tussen resources met Azure Monitor | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u opvragen tegen bronnen uit meerdere werkruimten en de App Insights-app in uw abonnement.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/05/2019
ms.openlocfilehash: 4740034bd970f42833125fa43bfdf72f710ac147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249605"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Query's voor logboeken met verschillende resources uitvoeren in Azure Monitor  

Voorheen met Azure Monitor kon u alleen gegevens analyseren vanuit de huidige werkruimte en beperkt het de mogelijkheid om te vragen over meerdere werkruimten die in uw abonnement zijn gedefinieerd.  Bovendien u alleen telemetrie-items zoeken die zijn verzameld uit uw webtoepassing met Application Insights rechtstreeks in Application Insights of bij Visual Studio. Dit maakte het ook een uitdaging om operationele en applicatiegegevens samen native te analyseren.

U nu niet alleen query's uitvoeren in meerdere Log Analytics-werkruimten, maar ook gegevens uit een specifieke Application Insights-app in dezelfde resourcegroep, een andere brongroep of een ander abonnement. Dit biedt u een systeembreed overzicht van uw gegevens. U dit soort query's alleen uitvoeren in [Log Analytics.](portals.md)

## <a name="cross-resource-query-limits"></a>Querylimieten voor verschillende resources 

* Het aantal Resources Voor Application Insights en Logboekanalyse-werkruimten dat u in één query opnemen, is beperkt tot 100.
* Query met kruisresources wordt niet ondersteund in View Designer. U een query maken in Log Analytics en deze vastmaken aan het Azure-dashboard om [een logboekquery](../learn/tutorial-logs-dashboards.md)te visualiseren. 
* Logboekwaarschuwingen voor cross-resourcequery's worden ondersteund in de nieuwe [api voor geplandeQueryregels](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Azure Monitor gebruikt standaard de [verouderde Log Analytics Alert API](../platform/api-alerts.md) voor het maken van nieuwe logboekwaarschuwingsregels vanuit Azure portal, tenzij u overstapt van de verouderde Api voor [logboekwaarschuwingen.](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) Na de switch wordt de nieuwe API de standaardvoor nieuwe waarschuwingsregels in azure-portal en u regels voor waarschuwingen voor querylogboeken met meerdere resources maken. U waarschuwingsregels voor querylogboeken met meerdere resources maken zonder de overstap te maken met de [Azure Resource Manager-sjabloon voor de GeplandeQueryRules API,](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) maar deze waarschuwingsregel is beheerbaar via [de GeplandeQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) en niet vanuit Azure-portal.


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Query's voor logboekanalyse-werkruimten en vanuit toepassingsinzichten
Als u wilt verwijzen naar een andere werkruimte in uw query, gebruikt u de [*werkruimte-id*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) en gebruikt u voor een app uit Application Insights de [*app-id.*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression)  

### <a name="identifying-workspace-resources"></a>Werkruimtebronnen identificeren
In de volgende voorbeelden worden query's in log analytics-werkruimten getoond om samengevatte tellingen van logboeken terug te sturen uit de tabel Bijwerken op een werkruimte met de naam *contosoretail-it.* 

Het identificeren van een werkruimte kan op een van de vele manieren worden uitgevoerd:

* Resourcenaam - is een door de mens leesbare naam van de werkruimte, soms aangeduid als *componentnaam*. 

    `workspace("contosoretail-it").Update | count`

* Gekwalificeerde naam - is de "volledige naam" van de werkruimte, samengesteld uit de abonnementsnaam, resourcegroep en componentnaam in deze indeling: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Omdat Azure-abonnementsnamen niet uniek zijn, kan deze id dubbelzinnig zijn. 
    >

* Werkruimte-id - Een werkruimte-id is de unieke, onveranderlijke id die is toegewezen aan elke werkruimte die wordt weergegeven als een GUID (Globally Unique Identifier).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure Resource ID – de door Azure gedefinieerde unieke identiteit van de werkruimte. U gebruikt de resource-id wanneer de naam van de resource dubbelzinnig is.  Voor werkruimten is de indeling: */abonnementen/abonnementenId/resourcegroepen/resourceGroup/providers/microsoft. OperationalInsights/workspaces/componentName*.  

    Bijvoorbeeld:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Een toepassing identificeren
In de volgende voorbeelden wordt een samengevataantal aanvragen voor een app met de naam *fabrikamapp* in Application Insights retourneren. 

Het identificeren van een toepassing in Application Insights kan worden uitgevoerd met de *expressie app(Identifier).*  Het argument *Id* geeft de app op met een van de volgende opties:

* Resourcenaam - is een door de mens leesbare naam van de app, soms aangeduid als de *naam van*de component .  

    `app("fabrikamapp")`

    >[!NOTE]
    >Het identificeren van een toepassing op naam veronderstelt uniciteit voor alle toegankelijke abonnementen. Als u meerdere toepassingen met de opgegeven naam hebt, mislukt de query vanwege de ambiguïteit. In dit geval moet u een van de andere id's gebruiken.

* Gekwalificeerde naam - is de "volledige naam" van de app, samengesteld uit de abonnementsnaam, resourcegroep en componentnaam in deze indeling: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Omdat Azure-abonnementsnamen niet uniek zijn, kan deze id dubbelzinnig zijn. 
    >

* ID - de app GUID van de applicatie.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure Resource ID - de door Azure gedefinieerde unieke identiteit van de app. U gebruikt de resource-id wanneer de naam van de resource dubbelzinnig is. De indeling is: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/components/componentName*.  

    Bijvoorbeeld:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Een query uitvoeren in meerdere bronnen
U meerdere bronnen uit een van uw broninstanties opvragen, dit kunnen werkruimten en apps zijn die worden gecombineerd.
    
Voorbeeld voor query's in twee werkruimten:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Kruisbronquery gebruiken voor meerdere resources
Wanneer u query's met meerdere resources gebruikt om gegevens uit meerdere Log Analytics-werkruimten en toepassingsinzichten te correleren, kan de query complex en moeilijk te onderhouden zijn. U moet [functies in Azure Monitor-logboekquery's](functions.md) gebruiken om de querylogica te scheiden van de scoping van de querybronnen, wat de querystructuur vereenvoudigt. In het volgende voorbeeld wordt uitgelegd hoe u meerdere resources voor Application Insights controleren en het aantal mislukte aanvragen visualiseren op naam van de toepassing. 

Maak een query zoals de volgende die verwijst naar het bereik van de resources Application Insights. De `withsource= SourceApp` opdracht voegt een kolom toe die de toepassingsnaam aanwijst die het logboek heeft verzonden. [Sla de query op als functie](functions.md#create-a-function) met de _aliasapplicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



U [deze functie](../../azure-monitor/log-query/functions.md#use-a-function) nu gebruiken in een cross-resource query zoals de volgende. De functiealias _applicationsScoping_ retourneert de unie van de tabel aanvragen van alle gedefinieerde toepassingen. De query filtert vervolgens op mislukte aanvragen en visualiseert de trends per toepassing. De _parse_ operator is optioneel in dit voorbeeld. Het haalt de naam van de toepassing uit de eigenschap _SourceApp._

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
>Deze methode kan niet worden gebruikt met logboekwaarschuwingen omdat de toegangsvalidatie van de bronnen met de waarschuwingsregel, inclusief werkruimten en toepassingen, wordt uitgevoerd op het maken van waarschuwingen. Het toevoegen van nieuwe resources aan de functie na het maken van de waarschuwing wordt niet ondersteund. Als u de functie liever gebruikt voor resourcescoping in logboekwaarschuwingen, moet u de waarschuwingsregel in de portal of met een resourcemanagersjabloon bewerken om de scoped resources bij te werken. U ook de lijst met bronnen opnemen in de waarschuwingsquery voor logboeken.


![Tijddiagram](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Volgende stappen

- [Controleer Loggegevens analyseren in Azure Monitor](log-query-overview.md) voor een overzicht van logboekquery's en hoe Azure Monitor-logboekgegevens zijn gestructureerd.
- Controleer [azure monitor-logboekquery's](query-language.md) om alle resources voor azure monitor-logboekquery's weer te geven.
