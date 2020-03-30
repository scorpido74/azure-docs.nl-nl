---
title: Slimme detectie - prestatieafwijkingen | Microsoft Documenten
description: Application Insights voert slimme analyse van uw app telemetrie uit en waarschuwt u voor mogelijke problemen. Deze functie behoeft geen installatie.
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.openlocfilehash: 3d8de08605d3dd693eb74a84a29c2efa6cad669a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671729"
---
# <a name="smart-detection---performance-anomalies"></a>Slimme detectie - Prestatieafwijkingen

[Application Insights](../../azure-monitor/app/app-insights-overview.md) analyseert automatisch de prestaties van uw webapplicatie en kan u waarschuwen voor mogelijke problemen. Mogelijk leest u dit omdat u een van onze slimme detectiemeldingen hebt ontvangen.

Deze functie vereist geen speciale installatie, anders dan het configureren van uw app voor Application Insights (op [ASP.NET,](../../azure-monitor/app/asp-net.md) [Java](../../azure-monitor/app/java-get-started.md)of [Node.js](../../azure-monitor/app/nodejs.md), en in [webpagina code).](../../azure-monitor/app/javascript.md) Het is actief wanneer uw app voldoende telemetrie genereert.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Wanneer krijg ik een slimme detectiemelding?

Application Insights heeft ontdekt dat de prestaties van uw toepassing op een van de volgende manieren zijn verslechterd:

* **Verslechtering van de reactietijd** - Uw app reageert langzamer op aanvragen dan voorheen. De wijziging kan snel zijn geweest, bijvoorbeeld omdat er een regressie was in uw laatste implementatie. Of het is misschien geleidelijk geweest, misschien veroorzaakt door een geheugenlek. 
* **Afhankelijkheidsduur degradatie** - Uw app voert aan naar een REST API, database of andere afhankelijkheid. De afhankelijkheid reageert langzamer dan vroeger.
* **Traag prestatiepatroon** : uw app lijkt een prestatieprobleem te hebben dat slechts van invloed is op sommige aanvragen. Pagina's worden bijvoorbeeld langzamer geladen op het ene type browser dan op andere; of verzoeken worden langzamer bediend vanaf een bepaalde server. Momenteel bekijken onze algoritmen de laadtijden van pagina's, reactietijden voor aanvragen en reactietijden van afhankelijkheid.  

Smart Detection vereist ten minste 8 dagen telemetrie op een werkbaar volume om een basislijn van normale prestaties vast te stellen. Dus, nadat uw aanvraag is uitgevoerd voor die periode, een significant probleem zal resulteren in een melding.


## <a name="does-my-app-definitely-have-a-problem"></a>Heeft mijn app zeker een probleem?

Nee, een melding betekent niet dat uw app zeker een probleem heeft. Het is slechts een suggestie voor iets dat u misschien nauwkeuriger moet bekijken.

## <a name="how-do-i-fix-it"></a>Hoe kan ik dit probleem oplossen?

De meldingen bevatten diagnostische informatie. Hier volgt een voorbeeld:


![Hier is een voorbeeld van serverresponstijddegradatiedetectie](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Triage.** In de melding ziet u hoeveel gebruikers of hoeveel bewerkingen worden beïnvloed. Dit kan u helpen een prioriteit toe te wijzen aan het probleem.
2. **Toepassingsgebied**. Is het probleem van invloed op al het verkeer, of slechts enkele pagina's? Is het beperkt tot bepaalde browsers of locaties? Deze informatie kan worden verkregen uit de kennisgeving.
3. **Diagnose stellen**. Vaak zal de diagnostische informatie in de melding de aard van het probleem suggereren. Als de reactietijd bijvoorbeeld vertraagt wanneer de aanvraagsnelheid hoog is, suggereert dit dat uw server of afhankelijkheden overbelast zijn. 

    Open anders het prestatieblad in Application Insights. Daar vindt u [Profiler](profiler.md) Profiler-gegevens. Als er uitzonderingen worden gemaakt, u ook de [momentopnamefoutopsporing proberen.](../../azure-monitor/app/snapshot-debugger.md)



## <a name="configure-email-notifications"></a>E-mailmeldingen configureren

Smart Detection-meldingen worden standaard ingeschakeld en verzonden naar degenen die toegang hebben tot [monitoringlezer](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) en [monitoringbijdrager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) toegang tot het abonnement waarin de Application Insights-bron zich bevindt. Als u dit wilt wijzigen, klikt u op **Configureren** in de e-mailmelding of opent u Instellingen voor slimme detectie in Toepassingsinzichten. 
  
  ![Slimme detectie-instellingen](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * U de **koppeling Afmelden** in de e-mail Voor slimme detectie gebruiken om de e-mailmeldingen niet meer te ontvangen.

E-mails over prestatieafwijkingen voor slimme detecties zijn beperkt tot één e-mail per dag per Application Insights-bron. De e-mail wordt alleen verzonden als er ten minste één nieuw probleem is dat op die dag is gedetecteerd. Je krijgt geen herhalingen van een bericht. 

## <a name="faq"></a>Veelgestelde vragen

* *Dus, Microsoft personeel kijken naar mijn gegevens?*
  * Nee. De service is volledig automatisch. Alleen jij krijgt de meldingen. Uw gegevens zijn [privé.](../../azure-monitor/app/data-retention-privacy.md)
* *Analyseert u alle gegevens verzameld door Application Insights?*
  * Op dit moment niet. Momenteel analyseren we de responstijd van aanvragen, de reactietijd van afhankelijkheid en de laadtijd van de pagina. Analyse van aanvullende statistieken is op onze achterstand vooruit te kijken.

* Voor welke soorten toepassingen werkt dit?
  * Deze degradaties worden gedetecteerd in elke toepassing die de juiste telemetrie genereert. Als u Application Insights in uw web-app hebt geïnstalleerd, worden aanvragen en afhankelijkheden automatisch bijgehouden. Maar in backendservices of andere apps werkt Smart Detection op dezelfde manier als u oproepen hebt geplaatst naar [TrackRequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) of [TrackDependency.](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)

* *Kan ik mijn eigen regels voor anomaliedetectie maken of bestaande regels aanpassen?*

  * Nog niet, maar je kunt:
    * [Stel waarschuwingen in](../../azure-monitor/app/alerts.md) die u vertellen wanneer een statistiek een drempelwaarde overschrijdt.
    * [Exporteer telemetrie](../../azure-monitor/app/export-telemetry.md) naar een [database](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) of [naar PowerBI,](../../azure-monitor/app/export-power-bi.md )waar u het zelf analyseren.
* *Hoe vaak wordt de analyse uitgevoerd?*

  * We voeren de analyse dagelijks uit op de telemetrie van de vorige dag (volledige dag in UTC-tijdzone).
* *Dus is dit in de plaats [van metrische waarschuwingen?](../../azure-monitor/app/alerts.md)*
  * Nee.  We verplichten ons niet om elk gedrag te detecteren dat je abnormaal zou kunnen vinden.


* *Als ik niets doe naar aanleiding van een melding, ontvang ik dan een herinnering?*
  * Nee, u krijgt slechts één keer een bericht over elk probleem. Als het probleem blijft bestaan, wordt het bijgewerkt in het voedingsblad voor slimme detectie.
* *Ik ben de e-mail kwijt. Waar vind ik de meldingen in de portal?*
  * Klik in het overzicht Applicatie-inzichten van uw app op de tegel **Slimme detectie.** Daar kun je alle meldingen tot 90 dagen terug vinden.

## <a name="how-can-i-improve-performance"></a>Hoe kan ik de prestaties verbeteren?
Trage en mislukte reacties zijn een van de grootste frustraties voor websitegebruikers, zoals u weet uit uw eigen ervaring. Het is dus belangrijk om de problemen aan te pakken.

### <a name="triage"></a>Triage
Ten eerste, maakt het uit? Als een pagina altijd traag is om te laden, maar slechts 1% van de gebruikers van uw site er ooit naar hoeft te kijken, hebt u misschien belangrijkere dingen om over na te denken. Aan de andere kant, als slechts 1% van de gebruikers te openen, maar het gooit uitzonderingen elke keer, dat zou kunnen worden onderzocht.

Gebruik de impactinstructie (getroffen gebruikers of % van het verkeer) als algemene handleiding, maar houd er rekening mee dat het niet het hele verhaal is. Verzamel ander bewijs om het te bevestigen.

Houd rekening met de parameters van het probleem. Als het afhankelijk is van geografie, stelt u [beschikbaarheidstests](../../azure-monitor/app/monitor-web-app-availability.md) in, inclusief die regio: er kunnen alleen netwerkproblemen zijn in dat gebied.

### <a name="diagnose-slow-page-loads"></a>Diagnose van langzame paginabelastingen
Waar is het probleem? Is de server traag om te reageren, is de pagina erg lang, of moet de browser veel werk doen om het weer te geven?

Open het metriekblad Browsers. De gesegmenteerde weergave van de laadtijd van de browserpagina geeft aan waar de tijd naartoe gaat. 

* Als **de aanvraagtijd verzenden** hoog is, reageert de server langzaam of is het verzoek een bericht met veel gegevens. Bekijk de prestatiestatistieken om de [responstijden](../../azure-monitor/app/web-monitor-performance.md#metrics) te onderzoeken.
* Stel [afhankelijkheidstracking](../../azure-monitor/app/asp-net-dependencies.md) in om te zien of de traagheid te wijten is aan externe services of uw database.
* Als **ontvangstrespons** overheersend is, zijn uw pagina en de afhankelijke onderdelen ervan - JavaScript, CSS, afbeeldingen enzovoort (maar niet asynchrone geladen gegevens) lang. Stel een [beschikbaarheidstest](../../azure-monitor/app/monitor-web-app-availability.md)in en stel de optie in om afhankelijke onderdelen te laden. Wanneer u resultaten krijgt, opent u de details van een resultaat en vouwt u het uit om de laadtijden van verschillende bestanden te zien.
* Hoge **verwerkingstijd van** de client suggereert dat scripts langzaam worden uitgevoerd. Als de reden niet duidelijk is, u overwegen om een bepaalde timingcode toe te voegen en de tijden in trackMetric-oproepen te verzenden.

### <a name="improve-slow-pages"></a>Langzame pagina's verbeteren
Er is een web vol met advies over het verbeteren van uw server reacties en pagina laadtijden, dus we zullen niet proberen om het allemaal hier te herhalen. Hier zijn een paar tips die je waarschijnlijk al weet, gewoon om je aan het denken:

* Langzaam laden vanwege grote bestanden: laad de scripts en andere onderdelen asynchroon. Gebruik scriptbundeling. Breek de hoofdpagina op in widgets die hun gegevens afzonderlijk laden. Stuur geen gewone oude HTML voor lange tabellen: gebruik een script om de gegevens op te vragen als JSON of een ander compact formaat en vul de tabel op zijn plaats. Er zijn grote kaders om te helpen met dit alles. (Ze brengen ook grote scripts, natuurlijk.)
* Langzame serverafhankelijkheden: houd rekening met de geografische locaties van uw componenten. Als u bijvoorbeeld Azure gebruikt, controleert u of de webserver en de database zich in dezelfde regio bevinden. Halen query's meer informatie op dan ze nodig hebben? Zou caching of batching helpen?
* Capaciteitsproblemen: bekijk de serverstatistieken van responstijden en het aantal aanvragen. Als de responstijden onevenredig pieken met pieken in het aantal aanvragen, is het waarschijnlijk dat uw servers worden uitgerekt.


## <a name="server-response-time-degradation"></a>Verslechtering van de reactietijd van de server

De melding van de degradatievan de reactietijd vertelt u:

* De reactietijd in vergelijking met de normale reactietijd voor deze bewerking.
* Hoeveel gebruikers worden beïnvloed.
* Gemiddelde responstijd en 90e percentiel responstijd voor deze bewerking op de dag van de detectie en 7 dagen vóór. 
* Tel van deze bewerkingsaanvragen op de dag van de detectie en 7 dagen ervoor.
* Correlatie tussen degradatie in deze operatie en degradaties in gerelateerde afhankelijkheden. 
* Links om u te helpen het probleem te diagnosticeren.
  * Profiel van profilerom u te helpen te zien waar de bewerkingstijd wordt besteed (de koppeling is beschikbaar als voorbeelden van Profiler-traces voor deze bewerking zijn verzameld tijdens de detectieperiode). 
  * Prestatierapporten in Metric Explorer, waar u het tijdbereik/filters voor deze bewerking segmenteren en inblokjesen.
  * Zoek naar deze oproep om specifieke oproepeigenschappen weer te geven.
  * Foutrapporten - Als het aantal > 1 betekent dit dat er fouten in deze bewerking zijn die kunnen hebben bijgedragen aan prestatiedegradatie.

## <a name="dependency-duration-degradation"></a>Afhankelijkheidsduur degradatie

Moderne toepassing meer en meer gebruik maken van micro-diensten ontwerp aanpak, die in veel gevallen leidt tot een hoge betrouwbaarheid op externe diensten. Bijvoorbeeld, als uw toepassing is gebaseerd op een gegevensplatform of zelfs als u uw eigen bot service die u zal waarschijnlijk relais op een aantal cognitieve dienstverlener om uw bots in staat stellen om te communiceren in meer menselijke manieren en sommige data store service voor bot om de antwoorden te trekken Van.  

Melding van afhankelijkheidsdegradatie:

![Hier is een voorbeeld van afhankelijkheidsduration degradatiedetectie](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

Merk op dat het je vertelt:

* De duur in vergelijking met de normale reactietijd voor deze bewerking
* Hoeveel gebruikers worden beïnvloed
* Gemiddelde duur en 90e percentielduur voor deze afhankelijkheid op de dag van de detectie en 7 dagen vóór
* Aantal afhankelijkheidsoproepen op de dag van de detectie en 7 dagen vóór
* Koppelingen om u te helpen het probleem te diagnosticeren
  * Prestatierapporten in Metric Explorer voor deze afhankelijkheid
  * Zoeken naar deze afhankelijkheidsoproepen om aanroepeneigenschappen weer te geven
  * Foutrapporten - Als het aantal > 1 betekent dit dat er mislukte afhankelijkheidsoproepen zijn uitgevoerd tijdens de detectieperiode die mogelijk hebben bijgedragen aan de degradatie van de duur. 
  * Analytics openen met query's die deze afhankelijkheidsduur berekenen en tellen  

## <a name="smart-detection-of-slow-performing-patterns"></a>Slimme detectie van traag presterende patronen 

Application Insights vindt prestatieproblemen die slechts van invloed kunnen zijn op een deel van uw gebruikers of die in sommige gevallen alleen van invloed zijn op gebruikers. De melding over het laden van pagina's is bijvoorbeeld trager in het ene type browser dan in andere typen browsers, of als aanvragen langzamer worden weergegeven vanaf een bepaalde server. Het kan ook problemen ontdekken die verband houden met combinaties van eigenschappen, zoals langzame paginabelastingen in één geografisch gebied voor clients die een bepaald besturingssysteem gebruiken.  

Afwijkingen zoals deze zijn zeer moeilijk te detecteren alleen door het inspecteren van de gegevens, maar komen vaker voor dan je zou denken. Vaak komen ze alleen naar boven als uw klanten klagen. Tegen die tijd is het te laat: de getroffen gebruikers zijn al over te schakelen naar uw concurrenten!

Momenteel bekijken onze algoritmen de laadtijden van pagina's, de responstijden van aanvragen op de server en de reactietijden van afhankelijkheid.  

U hoeft geen drempelwaarden in te stellen of regels te configureren. Machine learning en data mining algoritmen worden gebruikt om abnormale patronen te detecteren.

![Klik in de e-mailwaarschuwing op de koppeling om het diagnostische rapport in Azure te openen](./media/proactive-performance-diagnostics/03.png)

* **Wanneer** wordt weergegeven hoe laat het probleem is gedetecteerd.
* **Wat** beschrijft:

  * Het probleem dat is gedetecteerd;
  * De kenmerken van de reeks gebeurtenissen die we vonden, toonden het probleemgedrag.
* De tabel vergelijkt de slecht presterende set met het gemiddelde gedrag van alle andere gebeurtenissen.

Klik op de koppelingen om Metric Explorer te openen en zoeken op relevante rapporten, gefilterd op de tijd en eigenschappen van de langzaam presterende set.

Wijzig het tijdsbereik en de filters om de telemetrie te verkennen.

## <a name="next-steps"></a>Volgende stappen
Met deze diagnostische hulpprogramma's u de telemetrie vanuit uw app inspecteren:

* [Profiler](profiler.md) 
* [Snapshot Debugger](../../azure-monitor/app/snapshot-debugger.md)
* [Analytics](../../azure-monitor/log-query/get-started-portal.md)
* [Analytics slimme diagnostiek](../../azure-monitor/app/analytics.md)

Slimme detecties zijn volledig automatisch. Maar misschien wilt u nog meer waarschuwingen instellen?

* [Handmatig geconfigureerde metrische waarschuwingen](../../azure-monitor/app/alerts.md)
* [Webtests voor beschikbaarheid](../../azure-monitor/app/monitor-web-app-availability.md)
