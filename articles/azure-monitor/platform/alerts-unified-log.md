---
title: Waarschuwingen registreren in Azure Monitor
description: Activeer e-mails, meldingen, Url's voor websites (webhooks) of automatisering wanneer de door u opgegeven analyse query voorwaarden worden vervuld voor Azure-waarschuwingen.
author: yanivlavi
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 5/31/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: b8cae9f7c43098b713d0d5d8f74e46cb0386600c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75396483"
---
# <a name="log-alerts-in-azure-monitor"></a>Waarschuwingen registreren in Azure Monitor

In dit artikel vindt u informatie over logboek waarschuwingen, een van de typen waarschuwingen die worden ondersteund in de [Azure-waarschuwingen](../../azure-monitor/platform/alerts-overview.md) en waarmee gebruikers het Analytics platform van Azure kunnen gebruiken als basis voor waarschuwingen.

De logboek waarschuwing bestaat uit logboek zoek regels die zijn gemaakt voor [Azure monitor logboeken](../../azure-monitor/learn/tutorial-viewdata.md) of [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events). Zie [logboek waarschuwingen maken in azure](../../azure-monitor/platform/alerts-log.md) voor meer informatie over het gebruik ervan.

> [!NOTE]
> Populaire logboek gegevens uit [Azure monitor-logboeken](../../azure-monitor/learn/tutorial-viewdata.md) zijn nu ook beschikbaar op het meet platform in azure monitor. Voor detail weergave, [metrische waarschuwing voor logboeken](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Waarschuwings regel voor zoeken in Logboeken-definitie en typen

Er worden door Azure Alerts regels gemaakt voor het zoeken in logboeken om met regelmatige intervallen automatisch opgegeven logboekzoekopdrachten uit te voeren.  Als de resultaten van de logboekzoekopdracht aan bepaalde criteria voldoen, wordt een waarschuwingsrecord gemaakt. De regel kan vervolgens automatisch een of meer acties uitvoeren met behulp van [actiegroepen](../../azure-monitor/platform/action-groups.md). De rol van [Azure monitoring-bijdrager](../../azure-monitor/platform/roles-permissions-security.md) voor het maken, wijzigen en bijwerken van logboek waarschuwingen is mogelijk vereist. samen met Access & uitvoerings rechten voor query's voor de analyse doel (en) in de waarschuwings regel of waarschuwings query. Als het maken van de gebruiker geen toegang heeft tot alle analyse doel (en) in een waarschuwings regel of waarschuwings query, kan het maken van de regel mislukken of kan de waarschuwings regel voor het logboek worden uitgevoerd met gedeeltelijke resultaten.

Zoek regels voor logboeken worden gedefinieerd door de volgende details:

- **Query registreren**.  de query die wordt uitgevoerd telkens wanneer de waarschuwingsregel wordt geactiveerd.  De records die door deze query worden geretourneerd, worden gebruikt om te bepalen of een waarschuwing moet worden geactiveerd. Analytics-query's kunnen voor een specifieke Log Analytics werk ruimte of Application Insights app en zelfs over [meerdere log Analytics en Application Insights resources](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) beschikken die de gebruiker toegang heeft, evenals de query rechten op alle resources. 
    > [!IMPORTANT]
    > ondersteuning voor [meerdere bronnen query's](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) in logboek waarschuwingen voor Application Insights en logboek waarschuwingen voor [log Analytics geconfigureerd met behulp](../../azure-monitor/platform/alerts-log-api-switch.md) van de scheduledQueryRules-API.

    Sommige analyse opdrachten en combi Naties zijn incompatibel met gebruik in logboek waarschuwingen. voor meer informatie kunt u [waarschuwings Query's vastleggen in azure monitor](../../azure-monitor/platform/alerts-log-query.md).

- **Tijds periode**.  Hiermee geeft u het tijds bereik voor de query op. De query retourneert alleen de records die zijn gemaakt binnen dit tijdsbereik. De tijds duur beperkt de gegevens die zijn opgehaald voor de logboek query om misbruik te voor komen en elke keer dat de opdracht (zoals voorheen) wordt gebruikt in de logboek query te omzeilen. <br>*Als de tijds periode bijvoorbeeld is ingesteld op 60 minuten en de query wordt uitgevoerd om 1:15 uur, worden alleen records die zijn gemaakt tussen 12:15 uur en 1:15 PM geretourneerd om de logboek query uit te voeren. Als de query van het logboek gebruik maakt van de tijd opdracht zoals voorheen (7d), wordt de logboek query alleen uitgevoerd voor gegevens tussen 12:15 uur en 1:15 uur, alsof gegevens alleen voor de afgelopen 60 minuten bestaan. En niet voor zeven dagen aan gegevens zoals opgegeven in de logboek query.*

- **Frequentie**.  Hiermee geeft u op hoe vaak de query moet worden uitgevoerd. Dit kan een waarde zijn tussen 5 minuten en 24 uur. Moet gelijk zijn aan of korter zijn dan de tijds periode.  Als de waarde groter is dan de tijds periode, worden de risico records gemist.<br>*Denk bijvoorbeeld aan een tijds periode van 30 minuten en een frequentie van 60 minuten.  Als de query wordt uitgevoerd om 1:00, worden records tussen 12:30 en 1:00 uur geretourneerd.  De volgende keer dat de query wordt uitgevoerd, is 2:00 wanneer deze records retourneert tussen 1:30 en 2:00.  Records die zijn gemaakt tussen 1:00 en 1:30, worden nooit geëvalueerd.*

- **Drempel waarde**.  De resultaten van de zoek opdracht in het logboek worden geëvalueerd om te bepalen of er een waarschuwing moet worden gemaakt.  De drempel waarde verschilt voor de verschillende typen waarschuwings regels voor logboek zoeken.

De logboeken voor het zoeken naar [Azure monitor logboeken](../../azure-monitor/learn/tutorial-viewdata.md) of [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events)kunnen van twee typen zijn. Elk van deze typen wordt gedetailleerd beschreven in de volgende secties.

- **[Aantal resultaten](#number-of-results-alert-rules)** . Er wordt één waarschuwing gemaakt wanneer de aantal records die worden geretourneerd door de logboek zoekactie een opgegeven aantal overschrijdt.
- **[Meting van metrische gegevens](#metric-measurement-alert-rules)** .  Er is een waarschuwing gemaakt voor elk object in de resultaten van het zoeken naar Logboeken met waarden die de opgegeven drempel waarde overschrijden.

De verschillen tussen typen waarschuwings regels zijn als volgt.

- *Met het aantal resultaten regels voor* waarschuwingen wordt altijd één waarschuwing gemaakt, terwijl de regel waarschuwing voor *metrische metingen* een waarschuwing maakt voor elk object dat de drempel waarde overschrijdt.
- *Aantal waarschuwings regels voor resultaten* er wordt een waarschuwing gemaakt wanneer de drempel waarde één keer wordt overschreden. Waarschuwings regels voor *metrische waarderingen* kunnen een waarschuwing maken wanneer de drempel een bepaald aantal keer voor een bepaald tijds interval wordt overschreden.

### <a name="number-of-results-alert-rules"></a>Aantal waarschuwings regels voor resultaten

**Met het aantal resultaten** waarschuwings regels wordt één waarschuwing gemaakt wanneer het aantal records dat wordt geretourneerd door de zoek query de opgegeven drempel waarde overschrijdt. Dit type waarschuwings regel is ideaal voor het werken met gebeurtenissen, zoals Windows-gebeurtenis logboeken, syslog-, WebApp-reacties en aangepaste Logboeken.  U kunt een waarschuwing maken wanneer een bepaalde fout gebeurtenis wordt gemaakt, of wanneer er meerdere fout gebeurtenissen binnen een bepaalde tijds periode worden gemaakt.

**Drempel**waarde: de drempel waarde voor een aantal waarschuwings regels voor resultaten is groter dan of kleiner dan een bepaalde waarden.  Als het aantal records dat door de zoek opdracht in Logboeken wordt geretourneerd, overeenkomen met deze criteria, wordt er een waarschuwing gemaakt.

Als u een waarschuwing wilt ontvangen voor één gebeurtenis, stelt u het aantal resultaten in op groter dan 0 en controleert u of er één gebeurtenis is gemaakt sinds de laatste keer dat de query is uitgevoerd. Sommige toepassingen kunnen een incidentele fout registreren die niet noodzakelijkerwijs een waarschuwing moet veroorzaken.  De toepassing kan bijvoorbeeld het proces voor het maken van de fout gebeurtenis opnieuw proberen en de volgende keer daarna slagen.  In dit geval is het wellicht niet verstandig om de waarschuwing te maken, tenzij er meerdere gebeurtenissen binnen een bepaalde periode zijn gemaakt.  

In sommige gevallen wilt u mogelijk een waarschuwing maken als er geen gebeurtenis is.  Zo kan een proces regel matige gebeurtenissen registreren om aan te geven dat het goed werkt.  Als een van deze gebeurtenissen niet binnen een bepaalde tijds periode wordt geregistreerd, moet er een waarschuwing worden gemaakt.  In dit geval stelt u de drempel waarde in op **minder dan 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Voor beeld van een logboek waarschuwing voor het type records

Overweeg een scenario waarin u wilt weten wanneer uw webtoepassing een reactie geeft op gebruikers met code 500 (dat wil zeggen) interne server fout. U maakt een waarschuwings regel met de volgende details:  

- **Query:** aanvragen | Where resultCode = = "500"<br>
- **Tijds periode:** 30 minuten<br>
- **Waarschuwings frequentie:** vijf minuten<br>
- **Drempel waarde:** Groter dan 0<br>

Vervolgens wordt de query elke vijf minuten uitgevoerd, met 30 minuten aan gegevens-om te zoeken naar een record met een resultaat code van 500. Als er nog een record wordt gevonden, wordt de waarschuwing geactiveerd en wordt de geconfigureerde actie geactiveerd.

### <a name="metric-measurement-alert-rules"></a>Waarschuwings regels voor metrische maat eenheden

Met regels voor **metrische waarderings** waarschuwingen wordt een waarschuwing voor elk object in een query gemaakt met een waarde die de opgegeven drempel waarden en de opgegeven trigger voorwaarde overschrijdt. In tegens telling tot het aantal waarschuwings regels **voor resultaten** , werken waarschuwings regels voor **metrische maat** eenheden wanneer het analyse resultaat een tijd reeks bevat. Ze hebben de volgende verschillende verschillen ten opzichte van het aantal waarschuwings regels **voor resultaten** .

- **Statistische functie**: bepaalt de berekening die wordt uitgevoerd en mogelijk een numeriek veld om samen te voegen.  **Aantal ()** retourneert bijvoorbeeld het aantal records in de query, **AVG (CounterValue)** berekent het gemiddelde van het veld CounterValue voor het interval. De statistische functie in de query moet een naam hebben of worden genoemd: AggregatedValue en een numerieke waarde opgeven. 

- **Groeps veld**: er wordt een record met een geaggregeerde waarde voor elk exemplaar van dit veld gemaakt en er kan voor elke instantie een waarschuwing worden gegenereerd.  Als u bijvoorbeeld een waarschuwing wilt genereren voor elke computer, gebruikt u **per computer**. Als er meerdere groeps velden zijn opgegeven in de waarschuwings query, kan de gebruiker opgeven welk veld moet worden gebruikt voor het sorteren van de resultaten met behulp van de para meter **aggregate on** (metricColumn)

    > [!NOTE]
    > De optie *aggregate op* (metricColumn) is beschikbaar voor metrische meet logboek waarschuwingen voor Application Insights en logboek waarschuwingen voor [log Analytics geconfigureerd met scheduledQueryRules-API](../../azure-monitor/platform/alerts-log-api-switch.md) .

- **Interval**: definieert het tijds interval waarover de gegevens worden geaggregeerd.  Als u bijvoorbeeld **vijf minuten**hebt opgegeven, wordt er een record gemaakt voor elke instantie van het groeps veld geaggregeerd met een interval van 5 minuten voor de periode die is opgegeven voor de waarschuwing.

    > [!NOTE]
    > De functie bin moet worden gebruikt in een query om een interval op te geven. Als bin () kan leiden tot ongelijke tijds intervallen. bij een waarschuwing wordt de bin-opdracht automatisch geconverteerd naar bin_at opdracht met de juiste tijd tijdens runtime, om ervoor te zorgen dat de resultaten met een vast punt worden uitgevoerd. Het meet type voor metrische gegevens van de logboek waarschuwing is ontworpen om te werken met query's met Maxi maal drie exemplaren van de opslag locatie ()-opdracht
    
- **Drempel waarde**: de drempel waarde voor waarschuwings regels voor metrische metingen wordt gedefinieerd door een aggregatie-en een aantal schendingen.  Als een gegevens punt in de zoek opdracht voor logboeken deze waarde overschrijdt, wordt het beschouwd als een schending.  Als het aantal schendingen in voor een object in de resultaten de opgegeven waarde overschrijdt, wordt er een waarschuwing voor dat object gemaakt.

Onjuiste configuratie van de optie *aggregatie op* of *metricColumn* kan waarschuwings regels veroorzaken die niet kunnen worden gestart. Zie [problemen oplossen als waarschuwings regel voor metrische meting onjuist is](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect)voor meer informatie.

#### <a name="example-of-metric-measurement-type-log-alert"></a>Voor beeld van een waarschuwing voor het type metrische maat eenheid

Overweeg een scenario waarin u een waarschuwing wilt ontvangen als een computer het processor gebruik van 90% drie keer meer dan 30 minuten heeft overschreden.  U maakt een waarschuwings regel met de volgende details:  

- **Query:** Prestaties | waarbij ObjectName = = "processor" en CounterName = = "% processor tijd" | samenvatten AggregatedValue = AVG (CounterValue) by bin (TimeGenerated, 5 min.), computer<br>
- **Tijds periode:** 30 minuten<br>
- **Waarschuwings frequentie:** vijf minuten<br>
- **Waarschuwings logica-drempel waarde voor &:** Groter dan 90<br>
- **Groeps veld (samen voegen):** Computer
- **Waarschuwing activeren op basis van:** Totaal aantal inbreuken groter dan 2<br>

De query maakt een gemiddelde waarde voor elke computer met een interval van vijf minuten.  Deze query wordt elke 5 minuten uitgevoerd voor gegevens die in de afgelopen 30 minuten zijn verzameld. Omdat het groeps veld (samen voegen) in kolom ' computer ' is gekozen, wordt de AggregatedValue voor de verschillende waarden van ' computer ' gesplitst en wordt het gemiddelde processor gebruik voor elke computer bepaald door een tijds lade van vijf minuten.  Een voor beeld van een query resultaat voor drie computers, zoals hieronder.


|TimeGenerated [UTC] |Computer  |AggregatedValue  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Als het query resultaat moet worden uitgezet, wordt dit weer gegeven als.

![Voorbeeld query resultaten](media/alerts-unified-log/metrics-measurement-sample-graph.png)

In dit voor beeld zien we in opslag locaties van 5 minuten voor elk van de drie computers-gemiddeld processor gebruik, zoals berekend gedurende 5 minuten. De drempel van 90 wordt slechts één keer op 1:25 bin geschonden door Srv01. Ten opzichte van SRV02 overschrijdt de drempel waarde van 90 op 1:10, 1:15 en 1:25 bakken; Hoewel srv03 groter is dan 90 drempel op 1:10, 1:15, 1:20 en 1:30.
Omdat de waarschuwing is geconfigureerd om te activeren op basis van het totale aantal schendingen zijn er meer dan twee, zien we dat SRV02 en srv03 alleen voldoen aan de criteria. Daarom worden afzonderlijke waarschuwingen gemaakt voor SRV02 en srv03, omdat ze de drempel waarde van 90% twee keer hebben geschonden over meerdere tijd bakken.  Als de *trigger waarschuwing gebaseerd op:* para meter is geconfigureerd voor de optie *voortdurende overtredingen* , wordt een waarschuwing **alleen** geactiveerd voor srv03 omdat deze de drempel waarde voor drie opeenvolgende tijd bakken van 1:10 tot 1:20 heeft geschonden. En **niet** voor SRV02, omdat deze de drempel waarde voor twee opeenvolgende tijd bakken van 1:10 tot 1:15 heeft geschonden.

## <a name="log-search-alert-rule---firing-and-state"></a>Waarschuwings regel voor zoeken in Logboeken-activeren en status

Waarschuwings regels voor zoeken in Logboeken werken alleen voor de logica die u in de query hebt gemaakt. Het waarschuwings systeem heeft geen andere context van de status van het systeem, uw intentie of de hoofd oorzaak geïmpliceerd door de query. Daarom worden logboek waarschuwingen aangeduid als status-less. De voor waarden worden geëvalueerd als ' waar ' of ' onwaar ' telkens wanneer ze worden uitgevoerd.  Telkens wanneer de evaluatie van de waarschuwings voorwaarde is ingesteld op ' TRUE ', wordt een waarschuwing geactiveerd, ongeacht of deze eerder is geactiveerd.    

Laten we dit gedrag zien in actie met een praktisch voor beeld. Stel dat er een waarschuwings regel voor logboeken met de naam *Contoso-log-alert*is geconfigureerd, zoals wordt weer gegeven in het [voor beeld dat is opgegeven voor het aantal logboek waarschuwingen van het type resultaten](#example-of-number-of-records-type-log-alert). De voor waarde is een aangepaste waarschuwings query die is ontworpen om te zoeken naar 500 resultaat code in Logboeken. Als er nog meer 500 resultaten worden gevonden in Logboeken, is de voor waarde van de waarschuwing waar. 

Op elk interval hieronder evalueert het Azure Alerts-systeem de voor waarde voor de *aanmelding contoso-log-alert*.


| Tijd    | Aantal records dat wordt geretourneerd door de zoek query van het logboek | Logboek voorwaarde evalution | Resultaat 
| ------- | ----------| ----------| ------- 
| 13:05 | 0 records | 0 is niet > 0 dus onwaar |  De waarschuwing wordt niet geactiveerd. Er zijn geen acties aangeroepen.
| 1:10 UUR | 2 records | 2 > 0 waar  | Waarschuwingen worden geactiveerd en actie groepen worden aangeroepen. Waarschuwings status actief.
| 13:15 uur | 5 records | 5 > 0 zo ja  | Waarschuwingen worden geactiveerd en actie groepen worden aangeroepen. Waarschuwings status actief.
| 1:20 UUR | 0 records | 0 is niet > 0 dus onwaar |  De waarschuwing wordt niet geactiveerd. Er zijn geen acties aangeroepen. Waarschuwings status is links actief.

Als voor beeld gebruikt u het vorige geval:

Om 1:15 uur kan Azure-waarschuwingen niet bepalen of de onderliggende problemen die op 1:10 aanwezig zijn, behouden blijven en als de records net nieuwe fouten of herhaalde storingen op 1:10PM zijn. De query van de gebruiker kan al dan niet rekening houden met eerdere records en het systeem is niet bekend. Het systeem van Azure Alerts is aan de kant van de waarschuwing gebouwd en de waarschuwing en de bijbehorende acties worden opnieuw gestart om 1:15 uur. 

Om 1:20 uur als er geen nul records worden weer gegeven met de 500-resultaat code, kan Azure-waarschuwingen niet zeker zijn dat de oorzaak van 500 resultaat code op 1:10 uur en 1:15 PM nu is opgelost. Het is niet bekend als de 500-fout problemen zich opnieuw voordoen. *Contoso-log-alert* wordt niet gewijzigd in **opgelost** in het Azure-waarschuwings dashboard en/of meldingen worden niet verzonden met de mede deling dat de waarschuwing is opgelost. Alleen u, die de exacte voor waarde of reden voor de logica die is inge sloten in de analyse query begrijpt, kan [de waarschuwing als gesloten markeren](alerts-managing-alert-states.md) .

## <a name="pricing-and-billing-of-log-alerts"></a>Prijzen en facturering van logboek waarschuwingen

De prijzen die van toepassing zijn op logboek waarschuwingen worden vermeld op de pagina [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/) . In azure-facturen worden logboek waarschuwingen weer gegeven als type `microsoft.insights/scheduledqueryrules` met:

- Waarschuwingen registreren op Application Insights die worden weer gegeven met de naam van een exacte waarschuwing samen met de eigenschappen van de resource groep en de waarschuwing
- Waarschuwingen in het logboek vastleggen op Log Analytics die worden weer gegeven met de naam van de resource groep en van de waarschuwing. Wanneer gemaakt met behulp van de [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

De [verouderde log Analytics-API](../../azure-monitor/platform/api-alerts.md) heeft waarschuwings acties en schema's als onderdeel van log Analytics opgeslagen zoek opdracht en niet de juiste [Azure-resources](../../azure-resource-manager/management/overview.md). Daarom is het mogelijk om facturering in te scha kelen voor dergelijke verouderde logboek waarschuwingen die zijn gemaakt voor Log Analytics met behulp van Azure Portal **zonder** [over te scha kelen naar een nieuwe API](../../azure-monitor/platform/alerts-log-api-switch.md) of via [verouderde log Analytics API](../../azure-monitor/platform/api-alerts.md) -verborgen pseudo-waarschuwings regels worden gemaakt op `microsoft.insights/scheduledqueryrules` voor facturering op Azure De verborgen pseudo-waarschuwings regels die zijn gemaakt voor facturering op `microsoft.insights/scheduledqueryrules`, zoals wordt weer gegeven als `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` samen met de eigenschappen van de resource groep en de waarschuwing.

> [!NOTE]
> Als er ongeldige tekens zoals `<, >, %, &, \, ?, /` aanwezig zijn, worden deze vervangen door `_` in de naam van de verborgen pseudo-waarschuwings regel en dus ook in de Azure-factuur.

Voor het verwijderen van de verborgen scheduleQueryRules-resources die zijn gemaakt voor het factureren van waarschuwings regels met behulp van [verouderde log Analytics-API](api-alerts.md), kan de gebruiker het volgende doen:

- Beide gebruikers kunnen [de API-voor keuren voor de waarschuwings regels in de werk ruimte log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) en zonder verlies van hun waarschuwings regels of bewaking verplaatsen naar Azure Resource Manager compatibele [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Hierdoor hoeft u geen pseudo-regels te maken om te worden gewaarschuwd voor facturering.
- Of als de gebruiker de voor keur van de API niet wil wijzigen, moet de gebruiker de oorspronkelijke planning en waarschuwings actie **verwijderen** met behulp van [verouderde log Analytics-API](api-alerts.md) of verwijderen in [Azure Portal de oorspronkelijke waarschuwings regel](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal) voor het logboek

Daarnaast worden voor de verborgen scheduleQueryRules-resources die zijn gemaakt voor het factureren van waarschuwings regels met behulp van [verouderde log Analytics-API](api-alerts.md), een wijzigings bewerking zoals put mislukt. Als de `microsoft.insights/scheduledqueryrules` type pseudo-regels zijn bedoeld om de waarschuwings regels te factureren die zijn gemaakt met [verouderde log Analytics API](api-alerts.md). Het wijzigen van waarschuwings regels moet worden uitgevoerd met behulp van een [verouderde log Analytics API](api-alerts.md) (of) gebruiker kan [de API-voor keur voor de waarschuwings regels voor het gebruik van](../../azure-monitor/platform/alerts-log-api-switch.md) [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) wijzigen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het maken van waarschuwingen in Logboeken in azure](../../azure-monitor/platform/alerts-log.md).
* Informatie [over webhooks in logboek waarschuwingen in azure](alerts-log-webhook.md).
* Meer informatie over [Azure-waarschuwingen](../../azure-monitor/platform/alerts-overview.md).
* Meer informatie over [Application Insights](../../azure-monitor/app/analytics.md).
* Meer informatie over [log Analytics](../../azure-monitor/log-query/log-query-overview.md).
