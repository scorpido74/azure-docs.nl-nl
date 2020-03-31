---
title: Problemen met logboekwaarschuwingen oplossen in Azure Monitor | Microsoft Documenten
description: Veelvoorkomende problemen, fouten en oplossingen voor logboekwaarschuwingsregels in Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: acb9784b745fa90fc9cd264162930020e6d64751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249033"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Problemen met logboekwaarschuwingen oplossen in Azure Monitor  

In dit artikel ziet u hoe u veelvoorkomende problemen oplossen die kunnen optreden wanneer u logboekwaarschuwingen in Azure Monitor instelt. Het biedt ook oplossingen voor veelvoorkomende problemen met functionaliteit of configuratie van logboekwaarschuwingen.

De term *log alerts* beschrijven regels die worden afgestoken op basis van een logboekquery in een Azure [Log Analytics-werkruimte](../learn/tutorial-viewdata.md) of in [Azure Application Insights](../../azure-monitor/app/analytics.md). Meer informatie over functionaliteit, terminologie en typen in [Logboekwaarschuwingen in Azure Monitor](../platform/alerts-unified-log.md).

> [!NOTE]
> In dit artikel worden geen gevallen weergegeven waarin de Azure-portal een waarschuwingsregel weergeeft die is geactiveerd en een melding niet wordt uitgevoerd door een gekoppelde actiegroep. Zie voor dergelijke gevallen de details in [Actiegroepen maken en beheren in de Azure-portal.](../platform/action-groups.md)

## <a name="log-alert-didnt-fire"></a>Log alert heeft niet brand

Hier volgen enkele veelvoorkomende redenen waarom de status voor een geconfigureerde [logboekwaarschuwingsregel in Azure Monitor](../platform/alerts-log.md) niet wordt weergegeven [zoals wanneer *verwacht* ](../platform/alerts-managing-alert-states.md).

### <a name="data-ingestion-time-for-logs"></a>Gegevensopnametijd voor logboeken

Met een logboekwaarschuwing wordt uw query periodiek uitgevoerd op basis [van Log Analytics](../learn/tutorial-viewdata.md) of Application [Insights.](../../azure-monitor/app/analytics.md) Omdat Azure Monitor veel terabytes aan gegevens van duizenden klanten uit verschillende bronnen over de hele wereld verwerkt, is de service gevoelig voor verschillende vertragingen. Zie [Gegevensopnametijd in Azure Monitor-logboeken](../platform/data-ingestion-time.md)voor meer informatie.

Om vertragingen te beperken, wacht het systeem en probeert het de waarschuwingsquery meerdere keren opnieuw als het de benodigde gegevens nog niet heeft ingenomen. Het systeem heeft een exponentieel toenemende wachttijd ingesteld. De logboekwaarschuwing wordt pas geactiveerd nadat de gegevens beschikbaar zijn, dus de vertraging kan te wijten zijn aan langzame opname van logboekgegevens.

### <a name="incorrect-time-period-configured"></a>Onjuiste tijdsperiode geconfigureerd

Zoals beschreven in het artikel over [terminologie voor logboekwaarschuwingen,](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)geeft de in de configuratie vermelde tijdsperiode het tijdsbereik voor de query aan. De query retourneert alleen records die binnen dit bereik zijn gemaakt.

De periode beperkt de gegevens die zijn opgehaald voor een logboekquery om misbruik te voorkomen en omzeilt elke opdracht (zoals **geleden)** die in een logboekquery wordt gebruikt. Als de periode bijvoorbeeld is ingesteld op 60 minuten en de query wordt uitgevoerd om 13:15 uur, worden alleen records die tussen 12:15 en 13:15 uur zijn gemaakt, gebruikt voor de logboekquery. Als de logboekquery een tijdopdracht gebruikt zoals **geleden (1d),** gebruikt de query nog steeds alleen gegevens tussen 12:15 uur en 1:15 uur omdat de periode is ingesteld op dat interval.

Controleer of de periode in de configuratie overeenkomt met uw query. Als de logboekquery in het eerder weergegeven voorbeeld de groene markering **gebruikt (1d),** moet de periode worden ingesteld op 24 uur of 1.440 minuten (rood aangegeven). Deze instelling zorgt ervoor dat de query wordt uitgevoerd zoals bedoeld.

![Periode](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>De optie Waarschuwingen onderdrukken is ingesteld

Zoals beschreven in stap 8 van het artikel over [het maken van een logalertregel in de Azure-portal,](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)bieden logboekwaarschuwingen een optie **Waarschuwingen onderdrukken** om triggering- en meldingsacties voor een geconfigureerde tijd te onderdrukken. Als gevolg daarvan zou je kunnen denken dat een alarm niet is afgegaan. In feite, het deed vuur, maar werd onderdrukt.  

![Waarschuwingen onderdrukken](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Metrische meetwaarschuwingsregel is onjuist

*Metrische waarschuwingen voor metingen* zijn een subtype logboekwaarschuwingen met speciale mogelijkheden en een syntaxis van beperkte waarschuwingen. Een regel voor een metrische meetlogboekwaarschuwing vereist dat de queryuitvoer een metrische tijdreeks is. Dat wil zeggen, de output is een tabel met verschillende, even grote perioden samen met de bijbehorende geaggregeerde waarden.

U ervoor kiezen om extra variabelen in de tabel naast **AggregatedValue te**hebben. Deze variabelen kunnen worden gebruikt om de tabel te sorteren.

Stel dat een regel voor een metrische metingslogboekwaarschuwing is geconfigureerd als:

- Query van`search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- Periode van 6 uur
- Drempel van 50
- Waarschuwingslogica van drie opeenvolgende inbreuken
- **Aggregaat Op** gekozen als **$table**

Omdat de opdracht omvat **samen te vatten ... door** en biedt twee variabelen **(tijdstempel** en **$table**), het systeem kiest **$table** voor **Aggregate Upon**. Het systeem sorteert de resultatentabel op het **$table** veld, zoals in de volgende schermafbeelding wordt weergegeven. Vervolgens wordt gekeken naar de meerdere **aggregatedvalue-exemplaren** voor elk tabeltype (zoals **availabilityResults)** om te zien of er drie of meer opeenvolgende inbreuken waren.

![Uitvoering van metrische metingsquery's met meerdere waarden](media/alert-log-troubleshoot/LogMMQuery.png)

Omdat **Aggregate Upon** is gedefinieerd op **$table,** worden de gegevens gesorteerd op een **$table** kolom (rood aangegeven). Vervolgens groeperen en zoeken we naar typen van het veld **Aggregaat op.**

Voor **$table**worden waarden voor **beschikbaarheidresultaten** bijvoorbeeld beschouwd als één plot/entiteit (oranje aangegeven). In dit waardeperceel/entiteit controleert de waarschuwingsservice op drie opeenvolgende inbreuken (aangegeven in het groen). De inbreuken leiden tot een waarschuwing voor de beschikbaarheid van de **tabelwaardeResultaten**.

Evenzo, als drie opeenvolgende inbreuken gebeuren voor een andere waarde van **$table,** een andere waarschuwing wordt geactiveerd voor hetzelfde. De waarschuwingsservice sorteert de waarden automatisch in één plot/entiteit (oranje aangegeven) op tijd.

Stel nu dat de regel voor de waarschuwing voor `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`het metrische meetlogboek is gewijzigd en dat de query is gewijzigd. De rest van de configuratie bleef hetzelfde als voorheen, inclusief de waarschuwingslogica voor drie opeenvolgende inbreuken. De optie **Aggregaat op** is in dit geval standaard **tijdstempel.** Slechts één waarde is opgenomen in de query voor **samen te vatten ... door** (dat wil **zeggen, tijdstempel).** Net als het eerdere voorbeeld zou de uitvoer aan het einde van de uitvoering als volgt worden geïllustreerd.

   ![Uitvoering van metrische metingsquery's met enkelvoudwaarde](media/alert-log-troubleshoot/LogMMtimestamp.png)

Omdat **Aggregate Upon** is gedefinieerd op **tijdstempel,** worden de gegevens gesorteerd op de **tijdstempelkolom** (aangegeven in rood). Dan groeperen we door **timestamp**. Waarden voor `2018-10-17T06:00:00Z` worden bijvoorbeeld beschouwd als één plot/entiteit (in oranje aangegeven). In deze waardeplot/entiteit vindt de waarschuwingsservice geen opeenvolgende inbreuken (omdat elke **tijdstempelwaarde** slechts één vermelding heeft). Dus de waarschuwing wordt nooit geactiveerd. In dat geval moet de gebruiker:

- Voeg een dummyvariabele of een bestaande variabele (zoals **$table)** toe om correct te sorteren met het veld **Aggregaat op.**
- Configureer de waarschuwingsregel opnieuw om waarschuwingslogica te gebruiken op basis van **totale inbreuk.**

## <a name="log-alert-fired-unnecessarily"></a>Log alert onnodig afgevuurd

Een geconfigureerde [logboekwaarschuwingsregel in Azure Monitor](../platform/alerts-log.md) kan onverwacht worden geactiveerd wanneer u deze bekijkt in [Azure Alerts](../platform/alerts-managing-alert-states.md). In de volgende secties worden enkele veelvoorkomende redenen beschreven.

### <a name="alert-triggered-by-partial-data"></a>Waarschuwing geactiveerd door gedeeltelijke gegevens

Log Analytics en Application Insights zijn onderhevig aan inname vertragingen en verwerking. Wanneer u een waarschuwingsquery voor logboeken uitvoert, wordt mogelijk ontdekt dat er geen gegevens beschikbaar zijn of dat er slechts gegevens beschikbaar zijn. Zie [Tijd voor het innemen van gegevens registreren in Azure Monitor](../platform/data-ingestion-time.md)voor meer informatie.

Afhankelijk van hoe u de waarschuwingsregel hebt geconfigureerd, kan er sprake zijn van foutoptreden als er geen gegevens of gedeeltelijke gegevens in logboeken staan op het moment van de uitvoering van de waarschuwing. In dergelijke gevallen raden wij u aan om de waarschuwingsquery of -configuratie te wijzigen.

Als u bijvoorbeeld de regel voor logboekwaarschuwingen configureert die moet worden geactiveerd wanneer het aantal resultaten van een analysequery kleiner is dan 5, wordt de waarschuwing geactiveerd wanneer er geen gegevens (nulrecord) of gedeeltelijke resultaten (één record) zijn. Maar na de vertraging van de gegevensopname kan dezelfde query met volledige gegevens een resultaat van 10 records opleveren.

### <a name="alert-query-output-is-misunderstood"></a>Waarschuwingsquery-uitvoer wordt verkeerd begrepen

U geeft de logica voor logboekwaarschuwingen in een analysequery. De analysequery kan verschillende big data en wiskundige functies gebruiken. De waarschuwingsservice voert uw query uit met intervallen die zijn opgegeven met gegevens voor een bepaalde periode. De waarschuwingsservice brengt subtiele wijzigingen aan in de query op basis van het waarschuwingstype. U deze wijziging weergeven in de sectie **Query die moet worden uitgevoerd** op het scherm **Signaallogica configureren:**

![Query die moet worden uitgevoerd](media/alert-log-troubleshoot/LogAlertPreview.png)

Het vak **Query dat moet worden uitgevoerd,** is wat de logboekwaarschuwingsservice uitvoert. Als u wilt weten wat de uitvoer van waarschuwingsquery's kan zijn voordat u de waarschuwing maakt, u de opgegeven query en de tijdspanne uitvoeren via de [Analytics-portal](../log-query/portals.md) of de [Analytics API.](https://docs.microsoft.com/rest/api/loganalytics/)

## <a name="log-alert-was-disabled"></a>Logboekwaarschuwing is uitgeschakeld

In de volgende secties worden enkele redenen vermeld waarom Azure Monitor de [regel voor logboekwaarschuwingen](../platform/alerts-log.md)kan uitschakelen.

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Bron waarin de waarschuwing is gemaakt, bestaat niet meer

Logwaarschuwingsregels die zijn gemaakt in Azure Monitor, zijn gericht op een specifieke bron, zoals een Azure Log Analytics-werkruimte, een Azure Application Insights-app en een Azure-bron. De logboekwaarschuwingsservice voert vervolgens een analysequery uit die is opgegeven in de regel voor het opgegeven doel. Maar na het maken van regels, gebruikers gaan vaak verwijderen uit Azure - of verplaatsen binnen Azure - het doel van de log alert regel. Omdat het doel van de waarschuwingsregel niet langer geldig is, mislukt de uitvoering van de regel.

In dergelijke gevallen schakelt Azure Monitor de logboekwaarschuwing uit en zorgt u ervoor dat u niet onnodig wordt gefactureerd wanneer de regel gedurende een aanzienlijke periode (zoals een week) niet continu kan worden uitgevoerd. U de exacte tijd vinden waarop Azure Monitor de logboekwaarschuwing heeft uitgeschakeld via [Azure Activity Log.](../../azure-resource-manager/management/view-activity-logs.md) In Azure Activity Log wordt een gebeurtenis toegevoegd wanneer Azure Monitor de regel voor logboekwaarschuwingen uitschakelt.

De volgende voorbeeldgebeurtenis in Azure Activity Log is voor een waarschuwingsregel die is uitgeschakeld vanwege een voortdurende fout.

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

### <a name="query-used-in-a-log-alert-is-not-valid"></a>Query die wordt gebruikt in een logboekwaarschuwing is niet geldig

Elke logboekwaarschuwingsregel die in Azure Monitor is gemaakt als onderdeel van de configuratie, moet een analysequery opgeven die de waarschuwingsservice periodiek zal uitvoeren. De analysequery heeft mogelijk de juiste syntaxis op het moment van het maken of bijwerken van de regel. Maar soms kan de query in de regel voor logboekwaarschuwingen gedurende een bepaalde periode syntaxisproblemen ontwikkelen en de regeluitvoering doen mislukken. Enkele veelvoorkomende redenen waarom een analysequery in een logboekwaarschuwingsregel fouten kan ontwikkelen, zijn:

- De query is geschreven om [meerdere resources te gebruiken.](../log-query/cross-workspace-query.md) En een of meer van de opgegeven resources bestaan niet meer.
- [Metrieke meettypelogboekwaarschuwing](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) geconfigureerd als geconfigureerd heeft een waarschuwingsquery die niet voldoet aan de syntaxisnormen
- Er is geen gegevensstroom naar het analyseplatform geweest. De [queryuitvoering geeft een fout](https://dev.loganalytics.io/documentation/Using-the-API/Errors) omdat er geen gegevens zijn voor de meegeleverde query.
- Wijzigingen in [querytaal](https://docs.microsoft.com/azure/kusto/query/) omvatten een herziene indeling voor opdrachten en functies. De eerder in een waarschuwingsregel verstrekte query is dus niet langer geldig.

[Azure Advisor](../../advisor/advisor-overview.md) waarschuwt u voor dit gedrag. Er wordt een aanbeveling toegevoegd voor de specifieke regel voor logboekwaarschuwingen op Azure Advisor, onder de categorie Hoge beschikbaarheid met gemiddelde impact en een beschrijving van 'Herstel uw logboekwaarschuwingsregel om controle te garanderen'. Als een waarschuwingsquery in de regel voor waarschuwingen voor logboekwaarschuwingen niet wordt verholpen nadat Azure Advisor zeven dagen lang een aanbeveling heeft gegeven, schakelt Azure Monitor de logboekwaarschuwing uit en zorgt ervoor dat u niet onnodig wordt gefactureerd wanneer de regel gedurende een omvangrijke periode niet continu kan worden uitgevoerd ( als een week).

U de exacte tijd vinden waarop Azure Monitor de regel voor logboekwaarschuwingen heeft uitgeschakeld door te zoeken naar een gebeurtenis in [Azure Activity Log](../../azure-resource-manager/management/view-activity-logs.md).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [logboekwaarschuwingen in Azure](../platform/alerts-unified-log.md).
- Meer informatie over [applicatie-inzichten](../../azure-monitor/app/analytics.md).
- Meer informatie over [logboekquery's](../log-query/log-query-overview.md).
