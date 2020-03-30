---
title: Logboekwaarschuwingen in Azure-monitor
description: E-mails, meldingen, url's van oproepwebsites (webhooks) of automatisering activeren wanneer aan de door u opgegeven analytische queryvoorwaarden is voldaan voor Azure Alerts.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 5/31/2019
ms.subservice: alerts
ms.openlocfilehash: a6abf4665c27771497037da35f85bb540e6e904e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665218"
---
# <a name="log-alerts-in-azure-monitor"></a>Logboekwaarschuwingen in Azure-monitor

In dit artikel vindt u details van logboekwaarschuwingen die worden ondersteund in de [Azure Alerts](../../azure-monitor/platform/alerts-overview.md) en kunnen gebruikers het analyseplatform van Azure gebruiken als basis voor waarschuwingen.

Logboekwaarschuwing bestaat uit logsearchregels die zijn gemaakt voor [Azure Monitor Logs](../../azure-monitor/learn/tutorial-viewdata.md) of Application [Insights.](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events) Zie [Logboekwaarschuwingen maken in Azure](../../azure-monitor/platform/alerts-log.md) voor meer informatie over het gebruik ervan

> [!NOTE]
> Populaire logboekgegevens van [Azure Monitor Logs](../../azure-monitor/learn/tutorial-viewdata.md) zijn nu ook beschikbaar op het metrische platform in Azure Monitor. Voor meer informatie, [metrische waarschuwing voor logboeken](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Waarschuwingsregel voor zoeken in logboeken - definitie en typen

Er worden door Azure Alerts regels gemaakt voor het zoeken in logboeken om met regelmatige intervallen automatisch opgegeven logboekzoekopdrachten uit te voeren.  Als de resultaten van de logboekzoekopdracht aan bepaalde criteria voldoen, wordt een waarschuwingsrecord gemaakt. De regel kan vervolgens automatisch een of meer acties uitvoeren met behulp van [actiegroepen](../../azure-monitor/platform/action-groups.md). [Azure Monitoring Inzenderrol](../../azure-monitor/platform/roles-permissions-security.md) voor het maken, wijzigen en bijwerken van logboekwaarschuwingen kan nodig zijn; samen met toegang & queryuitvoeringsrechten voor de analysedoel(en) in waarschuwingsregel of waarschuwingsquery. Als de gebruiker geen toegang heeft tot alle analytics-doel(en) in waarschuwingsregel of waarschuwingsquery, kan het maken van regels mislukken of wordt de log-waarschuwingsregel uitgevoerd met gedeeltelijke resultaten.

Log search regels worden gedefinieerd door de volgende details:

- **Logboekquery**.  de query die wordt uitgevoerd telkens wanneer de waarschuwingsregel wordt geactiveerd.  De records die door deze query worden geretourneerd, worden gebruikt om te bepalen of een waarschuwing moet worden geactiveerd. Analytics-query kan zijn voor een specifieke Log Analytics-werkruimte of Application Insights-app en zelfs over [meerdere bronnen voor Log Analytics en Application Insights,](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) mits de gebruiker toegang heeft tot alle bronnen. 
    > [!IMPORTANT]
    > [cross-resource queryondersteuning](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) in logboekwaarschuwingen voor Toepassingsinzichten en logboekwaarschuwingen voor Log Analytics die alleen [zijn geconfigureerd met de API van ScheduledQueryRules.](../../azure-monitor/platform/alerts-log-api-switch.md)

    Sommige analytische opdrachten en combinaties zijn onverenigbaar met het gebruik in logboekwaarschuwingen; Voor meer details weergave, [Log waarschuwingsquery's in Azure Monitor](../../azure-monitor/platform/alerts-log-query.md).

- **Periode .**  Hiermee geeft u het tijdsbereik voor de query op. De query retourneert alleen de records die zijn gemaakt binnen dit tijdsbereik. De periode beperkt de gegevens die zijn opgehaald voor logboekquery om misbruik te voorkomen en omzeilt elke opdracht (zoals geleden) die wordt gebruikt in logboekquery. <br>*Als de periode bijvoorbeeld is ingesteld op 60 minuten en de query wordt uitgevoerd om 13:15 uur, worden alleen records die tussen 12:15 uur en 13:15 uur zijn gemaakt, geretourneerd om logboekquery's uit te voeren. Als de logboekquery de opdracht tijd gebruikt zoals geleden (7d), wordt de logboekquery alleen uitgevoerd voor gegevens tussen 12:15 uur en 13:15 uur - alsof er alleen gegevens bestaan voor de afgelopen 60 minuten. En niet voor zeven dagen aan gegevens zoals gespecificeerd in logquery's.*

- **Frequentie**.  Hiermee geeft u op hoe vaak de query moet worden uitgevoerd. Kan elke waarde tussen 5 minuten en 24 uur. Moet gelijk zijn aan of minder dan de periode.  Als de waarde groter is dan de periode, loopt u het risico dat records worden gemist.<br>*Denk bijvoorbeeld aan een periode van 30 minuten en een frequentie van 60 minuten.  Als de query om 13.00 uur wordt uitgevoerd, worden records tussen 12:30 en 13:00 uur geretourneerd.  De volgende keer dat de query wordt uitgevoerd is 2:00 wanneer het records tussen 1:30 en 2:00 zou retourneren.  Alle records die tussen 1:00 en 1:30 zijn gemaakt, worden nooit geëvalueerd.*

- **Drempel .**  De resultaten van de logboekzoekopdracht worden geëvalueerd om te bepalen of er een waarschuwing moet worden gemaakt.  De drempelwaarde is anders voor de verschillende typen waarschuwingsregels voor logboekzoekopdrachten.

Log search rules of het nu gaat om [Azure Monitor Logs](../../azure-monitor/learn/tutorial-viewdata.md) of Application [Insights,](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events)kan van twee soorten zijn. Elk van deze typen wordt in detail beschreven in de volgende secties.

- **[Aantal resultaten](#number-of-results-alert-rules)**. Eén waarschuwing die is gemaakt wanneer de nummerrecords die door de logboekzoekopdracht worden geretourneerd, een opgegeven getal overschrijden.
- **[Metrische meting](#metric-measurement-alert-rules)**.  Waarschuwing die voor elk object is gemaakt in de resultaten van de logboekzoekopdracht met waarden die de opgegeven drempelwaarde overschrijden.

De verschillen tussen waarschuwingsregeltypen zijn als volgt.

- *Als het aantal waarschuwingsregels voor resultaten* wordt altijd één waarschuwing gemaakt, terwijl *de waarschuwingsregel met metrische meting* een waarschuwing maakt voor elk object dat de drempelwaarde overschrijdt.
- *Het aantal waarschuwingsregels voor resultaten* maakt een waarschuwing wanneer de drempelwaarde één keer wordt overschreden. *Metrische waarschuwingsregels* voor meting kunnen een waarschuwing maken wanneer de drempelwaarde een bepaald aantal keren wordt overschreden gedurende een bepaald tijdsinterval.

### <a name="number-of-results-alert-rules"></a>Aantal waarschuwingsregels voor resultaten

**Het aantal waarschuwingsregels voor resultaten** maakt één waarschuwing wanneer het aantal records dat door de zoekopdracht wordt geretourneerd, de opgegeven drempelwaarde overschrijdt. Dit type waarschuwingsregel is ideaal voor het werken met gebeurtenissen zoals Windows-gebeurtenislogboeken, Syslog, WebApp Response en Aangepaste logboeken.  U een waarschuwing maken wanneer een bepaalde foutgebeurtenis wordt gemaakt of wanneer binnen een bepaalde periode meerdere foutgebeurtenissen worden gemaakt.

**Drempelwaarde**: De drempel waarde voor een aantal waarschuwingsregels voor resultaten is groter dan of lager dan een bepaalde waarde.  Als het aantal records dat door de logboekzoekopdracht wordt geretourneerd, overeenkomt met deze criteria, wordt een waarschuwing gemaakt.

Als u wilt waarschuwen voor één gebeurtenis, stelt u het aantal resultaten in op meer dan 0 en controleert u op het optreden van één gebeurtenis die is gemaakt sinds de laatste keer dat de query is uitgevoerd. Sommige toepassingen kunnen af en toe een fout inloggen die niet noodzakelijkerwijs een waarschuwing moet verhogen.  De toepassing kan bijvoorbeeld het proces dat de foutgebeurtenis heeft gemaakt opnieuw proberen en vervolgens de volgende keer slagen.  In dit geval wilt u de waarschuwing mogelijk niet maken, tenzij er meerdere gebeurtenissen worden gemaakt binnen een bepaalde periode.  

In sommige gevallen u een waarschuwing maken als er geen gebeurtenis is.  Een proces kan bijvoorbeeld regelmatige gebeurtenissen registreren om aan te geven dat het goed werkt.  Als een van deze gebeurtenissen niet binnen een bepaalde periode wordt logboeken, moet een waarschuwing worden gemaakt.  In dit geval stelt u de drempelwaarde in op **minder dan 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Voorbeeld van logboekwaarschuwing van het type aantal records

Overweeg een scenario waarin u wilt weten wanneer uw webgebaseerde app een antwoord geeft aan gebruikers met code 500 (dat wil zeggen) interne serverfout. U maakt een waarschuwingsregel met de volgende details:  

- **Vraag:** aanvragen | waar resultaatCode == "500"<br>
- **Periode:** 30 minuten<br>
- **Waarschuwingsfrequentie:** vijf minuten<br>
- **Drempelwaarde:** Groter dan 0<br>

Dan alert zou de query elke 5 minuten, met 30 minuten van de gegevens - om te zoeken naar een record waar de resultaatcode was 500. Als er zelfs een dergelijke record wordt gevonden, wordt de waarschuwing geactiveerd en wordt de geconfigureerde actie geactiveerd.

### <a name="metric-measurement-alert-rules"></a>Waarschuwingsregels voor metrische meting

**Metrische waarschuwingsregels** maken een waarschuwing voor elk object in een query met een waarde die een opgegeven drempelwaarde en opgegeven triggerconditie overschrijdt. In tegenstelling tot **het aantal waarschuwingsregels voor resultaten** werken metrische **waarschuwingsregels** wanneer het resultaat van analyses een tijdreeks biedt. Ze hebben de volgende duidelijke verschillen **met aantal resultaten** waarschuwingsregels.

- **Aggregaatfunctie:** hiermee bepaalt u de berekening die wordt uitgevoerd en mogelijk een numeriek veld dat moet worden samengevoegd.  Als u bijvoorbeeld **count()** het aantal records in de query retourneert, geeft **avg(CounterValue)** het gemiddelde van het veld CounterValue over het interval. De samengevoegde functie in query moet de naam/aangeroepen hebben: AggregatedValue en een numerieke waarde opgeven. 

- **Groepsveld:** voor elk exemplaar van dit veld wordt een record met een geaggregeerde waarde gemaakt en voor elk veld kan een waarschuwing worden gegenereerd.  Als u bijvoorbeeld voor elke computer een waarschuwing wilt genereren, gebruikt u **per computer**. In het geval dat er meerdere groepsvelden zijn opgegeven in waarschuwingsquery, kan de gebruiker opgeven welk veld moet worden gebruikt om resultaten te sorteren met de parameter **Aggregate On** (metricColumn)

    > [!NOTE]
    > *De* optie Aggregate On (metricColumn) is alleen beschikbaar voor waarschuwingen voor het type metrische meting voor toepassingsinzichten en logboekwaarschuwingen voor Logboekanalyse die zijn geconfigureerd met alleen [de API van ScheduledQueryRules.](../../azure-monitor/platform/alerts-log-api-switch.md)

- **Interval:** hiermee definieert u het tijdsinterval waarover de gegevens worden samengevoegd.  Als u bijvoorbeeld **vijf minuten**hebt opgegeven, wordt er een record gemaakt voor elk exemplaar van het groepsveld, samengevoegd met intervallen van 5 minuten gedurende de voor de waarschuwing opgegeven periode.

    > [!NOTE]
    > De functie Opslaglocatie moet worden gebruikt in query om interval op te geven. Als bin() kan resulteren in ongelijke tijdsintervallen - Alert converteert automatisch de opdracht Bin naar bin_at opdracht met de juiste tijd bij runtime, om resultaten met een vast punt te garanderen. Het type metrische meting van logboekwaarschuwing is ontworpen om te werken met query's met een opdracht maximaal drie exemplaren van de opdracht Bin()
    
- **Drempelwaarde**: De drempelwaarde voor metrische meetwaarschuwingsregels wordt gedefinieerd door een geaggregeerde waarde en een aantal inbreuken.  Als een gegevenspunt in de logboekzoekopdracht deze waarde overschrijdt, wordt het beschouwd als een inbreuk.  Als het aantal inbreuken op een object in de resultaten de opgegeven waarde overschrijdt, wordt er een waarschuwing voor dat object gemaakt.

Verkeerde configuratie van de optie *Aggregaat op* of *metrische kolom* kan ervoor zorgen dat waarschuwingsregels verkeerd worden inbrandt. Zie [probleemoplossing wanneer de waarschuwingsregel voor metrische metingen onjuist is](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect)voor meer informatie.

#### <a name="example-of-metric-measurement-type-log-alert"></a>Voorbeeld van waarschuwing voor het type metrische meting

Overweeg een scenario waarin u een waarschuwing wilde als een computer het processorgebruik van 90% drie keer in 30 minuten heeft overschreden.  U maakt een waarschuwingsregel met de volgende details:  

- **Query:** Perf | waar ObjectName == "Processor" en CounterName == "% Processortijd" | samenvatten Van aggregatedValue = avg(CounterValue) per opslaglocatie (TimeGenerated, 5m), Computer<br>
- **Periode:** 30 minuten<br>
- **Waarschuwingsfrequentie:** vijf minuten<br>
- **Waarschuwingslogica - Voorwaarde & Drempelwaarde:** Groter dan 90<br>
- **Groepsveld (aggregaat):** Computer
- **Triggerwaarschuwing op basis van:** Totale inbreuken groter dan 2<br>

De query maakt een gemiddelde waarde voor elke computer met intervallen van 5 minuten.  Deze query wordt elke 5 minuten uitgevoerd voor gegevens die in de afgelopen 30 minuten zijn verzameld. Aangezien het gekozen groepsveld (Aggregaat) columnar 'Computer' is - wordt de aggregatedvalue gesplitst voor verschillende waarden van 'Computer' en wordt het gemiddelde processorgebruik voor elke computer bepaald voor een tijdsopslag van 5 minuten.  Voorbeeld query resultaat voor (laten we zeggen) drie computers, zou worden als hieronder.


|TimeGenerated [UTC] |Computer  |Geaggregeerde waarde  |
|---------|---------|---------|
|20xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Als het queryresultaat moet worden uitgezet, wordt het weergegeven als.

![Voorbeeldqueryresultaten](media/alerts-unified-log/metrics-measurement-sample-graph.png)

In dit voorbeeld zien we in bakken van 5 minuten voor elk van de drie computers - gemiddeld processorgebruik zoals berekend gedurende 5 minuten. Drempel van 90 wordt doorsrv01 slechts eenmaal om 1:25 bak overschreden. Ter vergelijking: srv02 overschrijdt de drempelwaarde van 90 bij 1:10, 1:15 en 1:25 bakken; terwijl srv03 de drempel waarde 90 overschrijdt om 1:10, 1:15, 1:20 en 1:30.
Aangezien waarschuwing is geconfigureerd om te activeren op basis van totale inbreuken zijn meer dan twee, zien we dat srv02 en srv03 alleen voldoen aan de criteria. Vandaar aparte waarschuwingen zou worden gemaakt voor srv02 en srv03, omdat ze de 90% drempel twee keer overschreden over meerdere tijd bakken.  Als de *Trigger-waarschuwing op basis van:* parameter in plaats daarvan is geconfigureerd voor *continue inbreuken* optie, dan is een waarschuwing alleen worden **afgevuurd** voor srv03, omdat het de drempel voor drie opeenvolgende tijd bakken van 1:10 tot 1:20 overschreden. En **niet** voor srv02, want het overschreed de drempel voor twee opeenvolgende tijd bakken van 1:10 tot 1:15.

## <a name="log-search-alert-rule---firing-and-state"></a>Waarschuwingsregel voor zoeken in logboek - afvuren en status

Waarschuwingsregels voor logboekzoekopdrachten werken alleen op de logica die u in de query inbouwt. Het waarschuwingssysteem heeft geen andere context van de status van het systeem, uw intentie of de hoofdoorzaak die door de query wordt geïmpliceerd. Logwaarschuwingen worden daarom stateloos genoemd. De voorwaarden worden beoordeeld als "WAAR" of "FALSE" elke keer dat ze worden uitgevoerd.  Een waarschuwing wordt geactiveerd telkens wanneer de evaluatie van de waarschuwingstoestand "WAAR" is, ongeacht of deze eerder is geactiveerd.    

Laten we eens kijken naar dit gedrag in actie met een praktisch voorbeeld. Stel dat we een logalertregel hebben met de naam *Contoso-Log-Alert*, die is geconfigureerd zoals weergegeven in het [voorbeeld dat wordt opgegeven voor waarschuwing voor het waarschuwingslogboek van het type aantal resultaten](#example-of-number-of-records-type-log-alert). De voorwaarde is een aangepaste waarschuwingsquery die is ontworpen om te zoeken naar 500-resultaatcode in logboeken. Als er nog een smeerdant 500 resultaatcodes worden gevonden in logboeken, is de voorwaarde van de waarschuwing waar. 

Bij elk interval hieronder evalueert het Azure-waarschuwingssysteem de voorwaarde voor de *Contoso-Log-Alert.*


| Time    | Aantal records dat wordt geretourneerd door een query in logboek | Log voorwaarde evalutie | Resultaat 
| ------- | ----------| ----------| ------- 
| 13:05 | 0 records | 0 is niet > 0 dus ONWAAR |  Alarm schiet niet af. Geen acties geroepen.
| 13:10 uur | 2 records | 2 > 0 dus WAAR  | Alerte branden en actiegroepen gebeld. Waarschuwingsstatus ACTIEF.
| 13:15 uur | 5 records | 5 > 0 dus WAAR  | Alerte branden en actiegroepen gebeld. Waarschuwingsstatus ACTIEF.
| 13:20 uur | 0 records | 0 is niet > 0 dus ONWAAR |  Alarm schiet niet af. Geen acties geroepen. Waarschuwingsstatus actief verlaten.

Het vorige geval als voorbeeld gebruiken:

Om 13:15 uur kunnen Azure-waarschuwingen niet bepalen of de onderliggende problemen die om 1:10 uur worden gezien, blijven bestaan en of de records netto nieuwe fouten of herhalingen van oudere fouten zijn om 13:10 uur. De door de gebruiker verstrekte query kan al dan niet rekening houden met eerdere records en het systeem weet het niet. Het Azure-waarschuwingssysteem is gebouwd om zich te vergissen aan de kant van de voorzichtigheid en vuurt de waarschuwing en bijbehorende acties opnieuw af om 13:15 uur. 

Om 13:20 uur wanneer nul records worden gezien met 500-resultaatcode, kunnen Azure-waarschuwingen er niet zeker van zijn dat de oorzaak van 500-doelcode om 13:10 uur en 13:15 uur nu is opgelost. Het weet niet of de 500 fout problemen zal gebeuren om dezelfde redenen weer. Daarom wordt *Contoso-Log-Alert* niet gewijzigd in Opgelost in **azure** alert-dashboard en/of worden er geen meldingen verzonden waarin staat dat de waarschuwing is opgelost. Alleen u, die de exacte voorwaarde of reden voor de logica begrijpt die is ingesloten in de analysequery, [de waarschuwing zo gesloten markeren](alerts-managing-alert-states.md) als dat nodig is.

## <a name="pricing-and-billing-of-log-alerts"></a>Prijzen en facturering van logboekwaarschuwingen

Prijzen die van toepassing zijn op logboekwaarschuwingen worden vermeld op de pagina [Azure Monitor Pricing.](https://azure.microsoft.com/pricing/details/monitor/) In Azure-facturen worden logboekwaarschuwingen `microsoft.insights/scheduledqueryrules` weergegeven als type met:

- Logboekwaarschuwingen op toepassingsstatistieken die worden weergegeven met de exacte waarschuwingsnaam, samen met resourcegroep- en waarschuwingseigenschappen
- Log waarschuwingen op Log Analytics weergegeven met exacte waarschuwingsnaam, samen met resourcegroep en waarschuwingseigenschappen; wanneer gemaakt met [de API van ScheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

De [verouderde Log Analytics API](../../azure-monitor/platform/api-alerts.md) heeft waarschuwingsacties en -schema's als onderdeel van Logboekanalyse opgeslagen zoekopdracht en niet de juiste Azure [Resources.](../../azure-resource-manager/management/overview.md) Om facturering voor dergelijke verouderde logboekwaarschuwingen die zijn gemaakt voor Log Analytics mogelijk te maken met behulp `microsoft.insights/scheduledqueryrules` van Azure-portal **zonder** over te schakelen naar een nieuwe [API](../../azure-monitor/platform/alerts-log-api-switch.md) of via een verouderde Log [Analytics-API](../../azure-monitor/platform/api-alerts.md) - worden verborgen pseudo-waarschuwingsregels gemaakt voor facturering op Azure. De verborgen pseudo-waarschuwingsregels `microsoft.insights/scheduledqueryrules` die zijn `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` gemaakt voor facturering op dezelfde weergegeven functie als samen met resourcegroep- en waarschuwingseigenschappen.

> [!NOTE]
> Als ongeldige `<, >, %, &, \, ?, /` tekens zoals aanwezig zijn, `_` worden ze vervangen door in de naam van de verborgen pseudo-waarschuwingsregel en dus ook in de Azure-factuur.

Als u de verborgen resources van ScheduleQueryRules wilt verwijderen die zijn gemaakt voor facturering van waarschuwingsregels met behulp van [de verouderde Log Analytics API,](api-alerts.md)kan de gebruiker een van de volgende handelingen uitvoeren:

- Beide gebruikers kunnen [api-voorkeur overschakelen voor de waarschuwingsregels op de Log Analytics-werkruimte](../../azure-monitor/platform/alerts-log-api-switch.md) en zonder verlies van hun waarschuwingsregels of het bewaken van de overstap naar de [geplande geplande Api](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)van Azure Resource Manager. Hierdoor elimineren de noodzaak om pseudo verborgen waarschuwingsregels voor facturering te maken.
- Of als de gebruiker de API-voorkeur niet wil overschakelen, moet de gebruiker de oorspronkelijke planning en waarschuwingsactie **verwijderen** met behulp van [de verouderde Log Analytics API](api-alerts.md) of de oorspronkelijke logboekwaarschuwingsregel in [Azure-portal](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal) verwijderen

Bovendien voor de verborgen planningQueryRules-resources die zijn gemaakt voor facturering van waarschuwingsregels met behulp van [de verouderde Log Analytics API,](api-alerts.md)mislukt elke wijzigingsbewerking zoals PUT. Aangezien `microsoft.insights/scheduledqueryrules` het type pseudoregels bedoeld zijn voor facturering, worden de waarschuwingsregels die zijn gemaakt met behulp van [de verouderde Log Analytics API](api-alerts.md). Elke wijziging van de waarschuwingsregel moet worden uitgevoerd met behulp van [de verouderde Log Analytics API](api-alerts.md) (of) gebruiker kan [api-voorkeur voor de waarschuwingsregels schakelen](../../azure-monitor/platform/alerts-log-api-switch.md) om in plaats daarvan de [GeplandeQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) te gebruiken.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het maken van inlogwaarschuwingen in Azure](../../azure-monitor/platform/alerts-log.md).
* Webhooks [in logwaarschuwingen in Azure begrijpen.](alerts-log-webhook.md)
* Meer informatie over [Azure Alerts](../../azure-monitor/platform/alerts-overview.md).
* Meer informatie over [applicatie-inzichten](../../azure-monitor/app/analytics.md).
* Meer informatie over [Log Analytics](../../azure-monitor/log-query/log-query-overview.md).
