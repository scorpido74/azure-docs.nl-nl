---
title: Problemen met logboek waarschuwingen in Azure Monitor oplossen | Microsoft Docs
description: Veelvoorkomende problemen, fouten en oplossingen voor logboek waarschuwings regels in Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: b8edbbc397a56f4fcf5b3ae070f04ca61659d98d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87045351"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Problemen met logboek waarschuwingen in Azure Monitor oplossen  

In dit artikel wordt beschreven hoe u veelvoorkomende problemen met logboek waarschuwingen in Azure Monitor kunt oplossen. Het bevat ook oplossingen voor veelvoorkomende problemen met de functionaliteit en configuratie van logboek waarschuwingen.

In de term *logboek waarschuwingen* worden regels beschreven die worden geactiveerd op basis van een logboek query in een [Azure log Analytics-werk ruimte](../log-query/get-started-portal.md) of in [Azure-toepassing Insights](../log-query/log-query-overview.md). Meer informatie over de functionaliteit, terminologie en typen in [logboek waarschuwingen in azure monitor](../platform/alerts-unified-log.md).

> [!NOTE]
> In dit artikel wordt niet in overweging genomen gevallen waarin de Azure Portal een waarschuwings regel weergeeft en er geen melding wordt uitgevoerd door een gekoppelde actie groep. Zie voor dergelijke gevallen de details in [actie groepen maken en beheren in de Azure Portal](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>De logboek waarschuwing is niet geactiveerd

Hier volgen enkele veelvoorkomende redenen waarom de status voor een geconfigureerde [logboek waarschuwings regel in azure monitor](../platform/alerts-log.md) niet wordt weer gegeven [als verwacht *fired* ](../platform/alerts-managing-alert-states.md).

### <a name="data-ingestion-time-for-logs"></a>Gegevens opname tijd voor logboeken

Met een waarschuwing van een logboek wordt uw query periodiek uitgevoerd op basis van [log Analytics](../log-query/get-started-portal.md) of [Application Insights](../log-query/log-query-overview.md). Omdat Azure Monitor veel terabytes aan gegevens van duizenden klanten in verschillende bronnen wereld wijd verwerkt, is de service vatbaar voor verschillende vertragingen. Zie [gegevens opname tijd in azure monitor logboeken](../platform/data-ingestion-time.md)voor meer informatie.

Om vertragingen te verhelpen, wacht het systeem en wordt de waarschuwings query meermaals herhaald als de benodigde gegevens nog niet zijn opgenomen. Het systeem heeft een exponentieel toenemende wacht tijd ingesteld. De logboek waarschuwing wordt pas geactiveerd nadat de gegevens beschikbaar zijn, waardoor de vertraging kan worden veroorzaakt door trage opname van logboek gegevens.

### <a name="incorrect-time-period-configured"></a>Onjuiste tijds periode geconfigureerd

Zoals beschreven in het artikel over [terminologie voor logboek waarschuwingen](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), geeft de tijds periode die in de configuratie wordt vermeld, het tijds bereik voor de query op. De query retourneert alleen records die binnen dit bereik zijn gemaakt.

De tijds periode beperkt de gegevens die zijn opgehaald voor een logboek query om misbruik te voor komen. het omzeilen van een tijd opdracht ( **zoals voorheen**) in een logboek query wordt omzeild. Als de tijds periode bijvoorbeeld is ingesteld op 60 minuten en de query wordt uitgevoerd om 1:15 uur, worden alleen records die zijn gemaakt tussen 12:15 uur en 1:15 uur, gebruikt voor de logboek query. Als de logboek query een tijd opdracht gebruikt, zoals **geleden (1d)**, gebruikt de query nog steeds gegevens tussen 12:15 en 1:15 uur, omdat de periode is ingesteld op dat interval.

Controleer of de tijds periode in de configuratie overeenkomt met de query. Voor het voor beeld dat eerder wordt weer gegeven, moet de periode worden ingesteld op 24 uur of 1.440 minuten (rood aangegeven) als de logboek query **geleden (1d)** met de groene markering gebruikt. Deze instelling zorgt ervoor dat de query wordt uitgevoerd zoals bedoeld.

![Periode](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>De optie waarschuwingen onderdrukken is ingesteld

Zoals beschreven in stap 8 van het artikel over [het maken van een waarschuwings regel voor het logboek in de Azure Portal](../platform/alerts-log.md#create-a-log-alert-rule-with-the-azure-portal), bieden logboek waarschuwingen een optie **waarschuwingen onderdrukken** om activerings-en meldings acties voor een ingestelde periode te onderdrukken. Als gevolg hiervan is het mogelijk dat een waarschuwing niet is geactiveerd. Eigenlijk werd het geactiveerd, maar werd dit onderdrukt.  

![Waarschuwingen onderdrukken](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Waarschuwings regel voor metrische meting is onjuist

*Waarschuwingen voor metrische meet logboeken* zijn een subtype van logboek waarschuwingen met speciale mogelijkheden en een beperkte waarschuwings query syntaxis. Voor een regel voor een waarschuwing voor metrische maat logboeken moet de query-uitvoer een metrische tijd reeks zijn. Dat wil zeggen dat de uitvoer een tabel is met een unieke, even grote tijd periode, samen met de bijbehorende geaggregeerde waarden.

U kunt ervoor kiezen om extra variabelen te hebben in de tabel naast **AggregatedValue**. Deze variabelen kunnen worden gebruikt om de tabel te sorteren.

Stel bijvoorbeeld dat een regel voor een waarschuwing voor metrische meting logboek is geconfigureerd als:

- Query van`search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- Tijds duur van 6 uur
- Drempel waarde van 50
- Waarschuwings logica van drie opeenvolgende inbreuken
- **Aggregatie bij** het kiezen van **$Table**

Omdat de opdracht **samen vatting bevat... door** en biedt twee variabelen (**tijds tempel** en **$Table**), het systeem kiest **$Table** voor **aggregatie op**. Het systeem sorteert de resultaten tabel op het **$Table** veld, zoals wordt weer gegeven in de volgende scherm afbeelding. Vervolgens wordt gekeken naar de meerdere **AggregatedValue** -instanties voor elk tabel type (zoals **availabilityResults**) om te zien of er drie of meer opeenvolgende schendingen zijn.

![Meting van metrische meet query's met meerdere waarden](media/alert-log-troubleshoot/LogMMQuery.png)

Omdat **aggregatie** op is gedefinieerd voor **$Table**, worden de gegevens gesorteerd op een **$Table** kolom (rood aangegeven). Vervolgens groeperen en zoeken naar typen van het veld **aggregate bij** .

Voor **$Table**worden bijvoorbeeld waarden voor **availabilityResults** beschouwd als één plot/entiteit (oranje aangegeven). In dit teken/deze entiteit controleert de waarschuwings service op drie opeenvolgende inbreuken (groen aangegeven). De schendingen activeren een waarschuwing voor de tabel waarde **availabilityResults**.

Als er drie opeenvolgende inbreuken optreden voor een andere waarde van **$Table**, wordt er ook een andere waarschuwings melding voor hetzelfde item geactiveerd. De waarschuwings service sorteert automatisch de waarden in één tekening/entiteit (oranje aangegeven) op tijd.

Stel nu dat de regel voor de waarschuwing voor metrische maat Logboeken is gewijzigd en dat de query was `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` . De rest van de configuratie bleef hetzelfde als voorheen, met inbegrip van de waarschuwings logica voor drie opeenvolgende inbreuken. De optie **aggregate op** in dit geval is standaard **Time Stamp** . Er wordt slechts één waarde gegeven in de query voor **samenvatten... door** (dat wil zeggen, **tijds tempel**). Net als in het vorige voor beeld zou de uitvoer aan het eind van de uitvoering er als volgt uitzien.

   ![Meting van metrische meet query's met enkelvouds waarde](media/alert-log-troubleshoot/LogMMtimestamp.png)

Omdat **aggregatie** op is gedefinieerd op **tijds tempel**, worden de gegevens gesorteerd op de **Time Stamp** -kolom (rood aangegeven). Vervolgens groeperen op **tijds tempel**. Waarden voor `2018-10-17T06:00:00Z` worden bijvoorbeeld beschouwd als één teken/entiteit (oranje aangegeven). In deze waarde is teken/entiteit geen opeenvolgende schendingen gevonden (omdat elke **Time Stamp** -waarde slechts één vermelding heeft). De waarschuwing wordt dus nooit geactiveerd. In dat geval moet de gebruiker:

- Voeg een dummy-variabele of een bestaande variabele toe (bijvoorbeeld **$Table**) om op de juiste manier te sorteren met behulp van het veld **aggregate bij** .
- Configureer de waarschuwings regel opnieuw om waarschuwings logica te gebruiken op basis van het **totale aantal schendingen** .

## <a name="log-alert-fired-unnecessarily"></a>De logboek waarschuwing is onnodig geactiveerd

Een geconfigureerde [logboek waarschuwings regel in azure monitor](../platform/alerts-log.md) kan onverwacht worden geactiveerd wanneer u deze in azure- [waarschuwingen](../platform/alerts-managing-alert-states.md)bekijkt. In de volgende secties worden enkele veelvoorkomende redenen beschreven.

### <a name="alert-triggered-by-partial-data"></a>Waarschuwing geactiveerd door gedeeltelijke gegevens

Log Analytics en Application Insights zijn onderhevig aan opname vertragingen en-verwerking. Wanneer u een logboek waarschuwings query uitvoert, merkt u mogelijk dat er geen gegevens beschikbaar zijn of dat er slechts enkele gegevens beschikbaar zijn. Zie [gegevens opname tijd vastleggen in azure monitor](../platform/data-ingestion-time.md)voor meer informatie.

Afhankelijk van hoe u de waarschuwings regel hebt geconfigureerd, kan het uitvoeren van de waarschuwing mislukken als er geen gegevens of gedeeltelijke gegevens in Logboeken worden weer gegeven op het moment van de uitvoering van de waarschuwing. In dergelijke gevallen raden wij u aan om de waarschuwings query of configuratie te wijzigen.

Als u bijvoorbeeld de regel voor logboek waarschuwing configureert die moet worden geactiveerd wanneer het aantal resultaten van een analyse query kleiner is dan 5, wordt de waarschuwing geactiveerd wanneer er geen gegevens (nul record) of gedeeltelijke resultaten (één record) zijn. Maar na de vertraging van de gegevens opname kan dezelfde query met volledige gegevens een resultaat van 10 records opleveren.

### <a name="alert-query-output-is-misunderstood"></a>Uitvoer van waarschuwings query is onduidelijk

U geeft de logica op voor logboek waarschuwingen in een Analytics-query. De analyse query kan verschillende big data en wiskundige functies gebruiken. De waarschuwings service voert uw query uit op intervallen die zijn opgegeven voor gegevens gedurende een opgegeven periode. Met de waarschuwings service worden subtiele wijzigingen in de query aangebracht op basis van het waarschuwings type. U kunt deze wijziging bekijken in de **query die moet worden uitgevoerd** op het scherm **signaal logica configureren** :

![De query die moet worden uitgevoerd](media/alert-log-troubleshoot/LogAlertPreview.png)

In het vak **query die moet worden uitgevoerd** , wordt de service logboek waarschuwing uitgevoerd. Als u wilt weten wat de uitvoer van waarschuwings query's kan zijn voordat u de waarschuwing maakt, kunt u de vermelde query en de time span uitvoeren via de [analyse Portal](../log-query/log-query-overview.md) of de [analyse-API](/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>De logboek waarschuwing is uitgeschakeld

De volgende secties bevatten een aantal redenen waarom Azure Monitor de [waarschuwings regel](../platform/alerts-log.md)voor het logboek kan uitschakelen.

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>De resource waar de waarschuwing is gemaakt, bestaat niet meer

Waarschuwings regels in logboek registreren die zijn gemaakt in Azure Monitor gericht op een specifieke resource, zoals een Azure Log Analytics-werk ruimte, een Azure-toepassing Insights-app en een Azure-resource. De service logboek waarschuwing voert vervolgens een analyse query uit die is opgegeven in de regel voor het opgegeven doel. Maar na het maken van de regel gaat gebruikers vaak naar verwijderen uit Azure, of verplaatsen binnen Azure, het doel van de waarschuwings regel voor het logboek. Omdat het doel van de waarschuwings regel niet meer geldig is, mislukt de uitvoering van de regel.

In dergelijke gevallen Azure Monitor de logboek waarschuwing uitgeschakeld en zorgt u ervoor dat u niet onnodig wordt gefactureerd wanneer de regel niet continu kan worden uitgevoerd voor de verkleinde periode (bijvoorbeeld een week). U kunt de exacte tijd ontdekken wanneer Azure Monitor de logboek waarschuwing hebt uitgeschakeld via [Azure-activiteiten logboek](../../azure-resource-manager/management/view-activity-logs.md). In azure-activiteiten logboek wordt een gebeurtenis toegevoegd wanneer Azure Monitor de waarschuwings regel voor het logboek uitschakelt.

De volgende voorbeeld gebeurtenis in het Azure-activiteiten logboek is voor een waarschuwings regel die is uitgeschakeld vanwege een continue fout.

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>De query die wordt gebruikt in een logboek waarschuwing, is ongeldig

Elke waarschuwings regel voor logboeken die in Azure Monitor is gemaakt als onderdeel van de configuratie, moet een analyse query opgeven die regel matig door de waarschuwings service wordt uitgevoerd. De analyse query heeft mogelijk de juiste syntaxis op het moment van het maken of bijwerken van de regel. Soms kan de query in de waarschuwings regel voor Logboeken echter in een bepaalde tijd syntaxis problemen ontwikkelen en kan de regel worden uitgevoerd. Enkele veelvoorkomende redenen waarom een Analytics-query in een waarschuwings regel voor logboeken fouten kan ontwikkelen:

- De query is geschreven om te worden [uitgevoerd op meerdere resources](../log-query/cross-workspace-query.md). En een of meer van de opgegeven resources bestaan niet meer.
- De waarschuwing die is geconfigureerd voor het [metings type van metrische gegevens](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) heeft een waarschuwings query die niet voldoet aan de syntaxis norm
- Er is geen gegevens stroom naar het analyse platform. De [uitvoering van de query levert een fout](https://dev.loganalytics.io/documentation/Using-the-API/Errors) op omdat er geen gegevens voor de gegeven query zijn.
- Wijzigingen in de [query taal](/azure/kusto/query/) bevatten een gewijzigde indeling voor opdrachten en functies. De query die eerder in een waarschuwings regel is gegeven, is dus niet meer geldig.

[Azure Advisor](../../advisor/advisor-overview.md) wordt u gewaarschuwd over dit gedrag. Er wordt een aanbeveling toegevoegd voor de specifieke waarschuwings regel voor het logboek op Azure Advisor, onder de categorie hoge Beschik baarheid met gemiddelde impact en een beschrijving van de regel voor logboek waarschuwing herstellen om te controleren of deze is ingeschakeld.

> [!NOTE]
> Als een waarschuwings query in de waarschuwings regel van het logboek niet is opgelost nadat Azure Advisor zeven dagen een aanbeveling heeft gegeven, schakelt Azure Monitor de logboek waarschuwing uit en zorgt u ervoor dat er geen kosten in rekening worden gebracht wanneer de regel niet continu kan worden uitgevoerd voor een verkleinde periode (7 dagen). U kunt de exacte tijd voor het Azure Monitor uitschakelen van de waarschuwings regel voor het logboek vinden door te zoeken naar een gebeurtenis in het [activiteiten logboek van Azure](../../azure-resource-manager/management/view-activity-logs.md).

## <a name="alert-rule-quota-was-reached"></a>Het quotum voor waarschuwings regels is bereikt

Het aantal waarschuwings regels voor logboek zoeken per abonnement en resource zijn afhankelijk van de quotum limieten die [hier](https://docs.microsoft.com/azure/azure-monitor/service-limits)worden beschreven.

### <a name="recommended-steps"></a>Aanbevolen stappen
    
Als u de quotum limiet hebt bereikt, kunnen de volgende stappen u helpen om het probleem op te lossen.

1. Probeer de waarschuwings regels voor logboek zoeken te verwijderen of uit te scha kelen die niet meer worden gebruikt.
2. Als de quotumlimiet moet worden verhoogd, gaat u door met het openen van een ondersteuningsaanvraag en geeft u de volgende informatie op:

    - Abonnement-id('s) waarvan de quotumlimiet moet(en) worden verhoogd
    - Reden voor toename van quotum
    - Het resource type voor de quota verhoging: **log Analytics**, **Application Insights** .
    - Aangevraagde quotumlimiet


### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>Het huidige gebruik van nieuwe waarschuwings regels voor logboeken controleren
    
#### <a name="from-the-azure-portal"></a>Vanuit Azure Portal

1. Open het scherm *Waarschuwingen* en klik op *Waarschuwingsregels beheren*
2. Filteren op het relevante abonnement met behulp van de vervolgkeuzelijst *Abonnement*
3. Zorg ervoor dat u NIET filtert op een specifieke resourcegroep, resourcetype of resource
4. Selecteer in de vervolg keuzelijst *type signaal* de optie zoeken naar Logboeken
5. Controleer of de vervolgkeuzelijst voor besturingselementen *Status* is ingesteld op ‘ingeschakeld’
6. Het totale aantal waarschuwings regels voor logboek zoeken wordt weer gegeven boven de lijst met regels

#### <a name="from-api"></a>Van API

- Power shell- [Get-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule?view=azps-3.7.0)
- REST API - [Lijst per abonnement](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [logboek waarschuwingen in azure](../platform/alerts-unified-log.md).
- Meer informatie over [Application Insights](../log-query/log-query-overview.md).
- Meer informatie over [logboek query's](../log-query/log-query-overview.md).
