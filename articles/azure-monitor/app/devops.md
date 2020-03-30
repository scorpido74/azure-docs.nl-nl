---
title: Prestatiebewaking van webapps - Azure Application Insights
description: Hoe Application Insights past in de devOps-cyclus
ms.topic: conceptual
ms.date: 12/21/2018
ms.openlocfilehash: 24095aade80022d1e1ebb38357971512bfc873c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669689"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Diepe diagnostische gegevens voor web-apps en services met Application Insights
## <a name="why-do-i-need-application-insights"></a>Waarom heb ik Application Insights nodig?
Application Insights bewaakt uw hardloopweb-app. Het vertelt u over storingen en prestatieproblemen en helpt u te analyseren hoe klanten uw app gebruiken. Het werkt voor apps die op veel platforms (ASP.NET, Java EE, Node.js, ...) worden uitgevoerd en wordt gehost in de cloud of on-premises. 

![Aspecten van de complexiteit van het leveren van web-apps](./media/devops/010.png)

Het is essentieel om een moderne toepassing te controleren terwijl het wordt uitgevoerd. Het belangrijkste is dat u fouten wilt detecteren voordat de meeste van uw klanten dat doen. U wilt ook prestatieproblemen ontdekken en oplossen die, hoewel niet catastrofaal, misschien dingen vertragen of enig ongemak veroorzaken voor uw gebruikers. En wanneer het systeem naar tevredenheid presteert, wilt u weten wat de gebruikers ermee doen: Gebruiken ze de nieuwste functie? Slagen ze ermee?

Moderne webapplicaties worden ontwikkeld in een cyclus van continue levering: release een nieuwe functie of verbetering; observeren hoe goed het werkt voor de gebruikers; plan de volgende toename van de ontwikkeling op basis van die kennis. Een belangrijk onderdeel van deze cyclus is de observatiefase. Application Insights biedt de tools om een webapplicatie te controleren op prestaties en gebruik.

Het belangrijkste aspect van dit proces is diagnostiek en diagnose. Als de toepassing mislukt, gaat het bedrijf verloren. De belangrijkste rol van een monitoringframework is daarom om storingen betrouwbaar op te sporen, u onmiddellijk op de hoogte te stellen en u de informatie te geven die nodig is om het probleem te diagnosticeren. Dit is precies wat Application Insights doet.

### <a name="where-do-bugs-come-from"></a>Waar komen insecten vandaan?
Storingen in websystemen zijn meestal het gevolg van configuratieproblemen of slechte interacties tussen de vele componenten. De eerste taak bij het aanpakken van een live site incident is dus het identificeren van de plaats van het probleem: welke component of relatie is de oorzaak?

Sommigen van ons, die met grijs haar, kan zich herinneren een eenvoudiger tijdperk waarin een computerprogramma liep in een computer. De ontwikkelaars zouden het grondig testen alvorens het te verschepen; en hebben verzonden, zou zelden zien of denken over het weer. De gebruikers zouden hebben opgemaakt met de resterende bugs voor vele jaren. 

De dingen zijn nu zo heel anders. Uw app heeft een overvloed aan verschillende apparaten om op te draaien, en het kan moeilijk zijn om exact hetzelfde gedrag op elk apparaat te garanderen. Hosting apps in de cloud betekent dat bugs snel kunnen worden opgelost, maar het betekent ook voortdurende concurrentie en de verwachting van nieuwe functies op frequente intervallen. 

In deze omstandigheden, de enige manier om een stevige controle op de bug tellen te houden is geautomatiseerde eenheid testen. Het zou onmogelijk zijn om handmatig opnieuw te testen alles op elke levering. Unit test is nu een alledaags onderdeel van het bouwproces. Tools zoals de Xamarin Test Cloud helpen door geautomatiseerde ui-tests op meerdere browserversies aan te bieden. Deze testregimes stellen ons in staat om te hopen dat de snelheid van bugs in een app tot een minimum kan worden beperkt.

Typische webapplicaties hebben veel live componenten. Naast de client (in een browser of apparaat-app) en de webserver is er waarschijnlijk een aanzienlijke backendverwerking. Misschien is de backend is een pijplijn van componenten, of een lossere verzameling van samenwerkende stukken. En velen van hen zullen niet in uw controle - ze zijn externe diensten waarvan u afhankelijk bent.

In configuraties als deze kan het moeilijk en onrendabel zijn om elke mogelijke storingsmodus te testen of te voorzien, anders dan in het live systeem zelf. 

### <a name="questions-"></a>Vragen...
Enkele vragen die we stellen bij de ontwikkeling van een websysteem:

* Loopt mijn app vast? 
* Wat is er precies gebeurd? - Als het niet een verzoek, ik wil weten hoe het daar kwam. We hebben een spoor van gebeurtenissen nodig...
* Is mijn app snel genoeg? Hoe lang duurt het om te reageren op typische verzoeken?
* Kan de server de belasting aan? Wanneer het aantal aanvragen stijgt, houdt de reactietijd dan stabiel?
* Hoe responsief zijn mijn afhankelijkheden - de REST API's, databases en andere componenten die mijn app aanroept. In het bijzonder, als het systeem traag is, is het mijn component, of krijg ik trage reacties van iemand anders?
* Is mijn app up of down? Kan het worden gezien uit de hele wereld? Laat het me weten als het stopt ....
* Wat is de oorzaak? Was de fout in mijn component of een afhankelijkheid? Is het een communicatieprobleem?
* Hoeveel gebruikers hebben er last van? Als ik meer dan één kwestie moet aanpakken, wat is dan het belangrijkste?

## <a name="what-is-application-insights"></a>Wat is Application Insights?
![Basisworkflow van Application Insights](./media/devops/020.png)

1. Application Insights instrumenten uw app en stuurt telemetrie over het terwijl de app wordt uitgevoerd. Of u de Application Insights SDK in de app inbouwen of u instrumentatie toepassen tijdens runtime. De vorige methode is flexibeler, omdat u uw eigen telemetrie toevoegen aan de reguliere modules.
2. De telemetrie wordt verzonden naar de Application Insights-portal, waar deze wordt opgeslagen en verwerkt. (Hoewel Application Insights wordt gehost in Microsoft Azure, kan het alle web-apps controleren - niet alleen Azure-apps.)
3. De telemetrie wordt u gepresenteerd in de vorm van grafieken en tabellen met gebeurtenissen.

Er zijn twee belangrijke soorten telemetrie: geaggregeerde en ruwe exemplaren. 

* Instantiegegevens omvatten bijvoorbeeld een rapport van een verzoek dat door uw web-app is ontvangen. U de details van een aanvraag vinden en inspecteren met behulp van de zoektool in de Portal Application Insights. Het exemplaar bevat gegevens zoals hoe lang het duurde voordat uw app op het verzoek heeft gereageerd, evenals de gevraagde URL, de geschatte locatie van de client en andere gegevens.
* Geaggregeerde gegevens omvatten het aantal gebeurtenissen per tijdseenheid, zodat u de snelheid van aanvragen vergelijken met de responstijden. Het bevat ook gemiddelden van statistieken, zoals responstijden voor aanvragen.

De belangrijkste categorieën gegevens zijn:

* Verzoeken aan uw app (meestal HTTP-verzoeken), met gegevens over URL, responstijd en succes of mislukking.
* Afhankelijkheden - REST- en SQL-oproepen van uw app, ook met URI, responstijden en succes
* Uitzonderingen, inclusief stapelsporen.
* Gegevens over de paginaweergave, die afkomstig zijn van de browsers van de gebruikers.
* Statistieken zoals prestatiemeteritems en statistieken die u zelf schrijft. 
* Aangepaste gebeurtenissen die u gebruiken om zakelijke gebeurtenissen bij te houden
* Logboeksporen die worden gebruikt voor het debuggen.

## <a name="case-study-real-madrid-fc"></a>Case Study: Real Madrid Fc
De webservice van [Real Madrid Football Club](https://www.realmadrid.com/) bedient ongeveer 450 miljoen fans over de hele wereld. Fans hebben er toegang toe, zowel via webbrowsers als via de mobiele apps van de Club. Fans kunnen niet alleen tickets boeken, maar ook toegang krijgen tot informatie en videoclips over resultaten, spelers en aankomende games. Ze kunnen zoeken met filters, zoals het aantal gescoorde doelpunten. Er zijn ook links naar sociale media. De gebruikerservaring is zeer persoonlijk en is ontworpen als een tweerichtingscommunicatie om fans te betrekken.

De oplossing [is een systeem van services en toepassingen op Microsoft Azure.](https://www.microsoft.com/inculture/sports/real-madrid/) Schaalbaarheid is een belangrijke vereiste: verkeer is variabel en kan zeer hoge volumes bereiken tijdens en rond wedstrijden.

Voor Real Madrid is het van vitaal belang om de prestaties van het systeem te controleren. Azure Application Insights biedt een uitgebreid overzicht over het hele systeem, wat zorgt voor een betrouwbaar en hoog serviceniveau. 

De Club krijgt ook diepgaand inzicht in haar fans: waar ze zijn (slechts 3% zijn in Spanje), welke interesse ze hebben in spelers, historische resultaten, en de komende wedstrijden, en hoe ze reageren op de wedstrijd resultaten.

De meeste van deze telemetriegegevens worden automatisch verzameld zonder toegevoegde code, wat de oplossing en de verminderde operationele complexiteit heeft vereenvoudigd.  Voor Real Madrid, Application Insights behandelt 3,8 miljard telemetrie punten per maand.

Real Madrid gebruikt de Power BI-module om hun telemetrie te bekijken.

![Power BI-weergave van telemetrie van Application Insights](./media/devops/080.png)

## <a name="smart-detection"></a>Slimme detectie
[Proactieve diagnostiek](../../azure-monitor/app/proactive-diagnostics.md) is een recente functie. Zonder speciale configuratie door u detecteert en waarschuwt Application Insights u automatisch over ongebruikelijke stijgingen in uitvalpercentages in uw app. Het is slim genoeg om een achtergrond van incidentele mislukkingen te negeren, en ook stijgingen die gewoon evenredig zijn aan een stijging van het aantal verzoeken. Dus bijvoorbeeld, als er een storing is in een van de services waarvan u afhankelijk bent, of als de nieuwe build die u zojuist hebt geïmplementeerd niet zo goed werkt, dan weet u het zodra u uw e-mail bekijkt. (En er zijn webhooks, zodat u andere apps activeren.)

Een ander aspect van deze functie voert een dagelijkse diepgaande analyse van uw telemetrie, op zoek naar ongebruikelijke patronen van prestaties die moeilijk te ontdekken zijn. Het kan bijvoorbeeld trage prestaties vinden die zijn gekoppeld aan een bepaald geografisch gebied of aan een bepaalde browserversie.

In beide gevallen vertelt de waarschuwing u niet alleen de symptomen die het heeft ontdekt, maar geeft het u ook gegevens die u nodig hebt om het probleem te diagnosticeren, zoals relevante uitzonderingsrapporten.

![E-mail van proactieve diagnostiek](./media/devops/030.png)

Klant Samtec zei: "Tijdens een recente functie cutover, vonden we een onder-schaal database die was het raken van de resource grenzen en het veroorzaken van time-outs. Proactieve detectie waarschuwingen kwam door letterlijk als we triaging het probleem, zeer dicht bij real-time zoals geadverteerd. Deze waarschuwing in combinatie met de Azure-platformwaarschuwingen heeft ons geholpen het probleem vrijwel direct op te lossen. Totale downtime < 10 minuten."

## <a name="live-metrics-stream"></a>Live Metrics Stream
Het implementeren van de nieuwste build kan een angstige ervaring zijn. Als er problemen zijn, wilt u er meteen over weten, zodat u zich indien nodig terugtrekken. Live Metrics Stream geeft je belangrijke statistieken met een latentie van ongeveer een seconde.

![Live statistieken](./media/devops/0040.png)

En hiermee u onmiddellijk een voorbeeld van eventuele fouten of uitzonderingen inspecteren.

![Gebeurtenissen met live-mislukkingen](./media/devops/002-live-stream-failures.png)

## <a name="application-map"></a>Toepassingskaart
Application Map detecteert automatisch uw toepassingstopologie en legt de prestatie-informatie er bovenop, om u eenvoudig prestatieknelpunten en problematische stromen in uw gedistribueerde omgeving te laten identificeren. Hiermee u toepassingsafhankelijkheden van Azure Services ontdekken. U het probleem triage door te begrijpen of het code-gerelateerd of afhankelijkheid gerelateerd en van een enkele plaats boor in gerelateerde diagnostische ervaring. Uw toepassing kan bijvoorbeeld mislukken als gevolg van prestatiedegradatie in SQL-laag. Met toepassingskaart u deze onmiddellijk bekijken en inzoomen op de SQL Index Advisor- of Query Insights-ervaring.

![Toepassingskaart](./media/devops/0050.png)

## <a name="application-insights-analytics"></a>Analytics voor applicatie-inzichten
Met [Analytics](../../azure-monitor/app/analytics.md)u willekeurige query's schrijven in een krachtige SQL-achtige taal.  Diagnose in de hele app-stack wordt eenvoudig naarmate verschillende perspectieven worden verbonden en u de juiste vragen stellen om serviceprestaties te correleren met bedrijfsstatistieken en klantervaring. 

U al uw telemetrie-instantie en metrische ruwe gegevens opvragen die zijn opgeslagen in de portal. De taal omvat filter- en join-, aggregatie- en andere bewerkingen. U velden berekenen en statistische analyses uitvoeren. Er zijn zowel tabelvormige als grafische visualisaties.

![Grafiek van analytics-query's en resultaten](./media/devops/0025.png)

Het is bijvoorbeeld gemakkelijk om:

* Segmenteer de aanvraagprestatiegegevens van uw toepassing op klantlagen om inzicht te krijgen in hun ervaring.
* Zoek naar specifieke foutcodes of aangepaste gebeurtenisnamen tijdens live site-onderzoeken.
* Inzoomen op het app-gebruik van specifieke klanten om te begrijpen hoe functies worden verworven en overgenomen.
* Houd sessies en responstijden bij voor specifieke gebruikers om ondersteunings- en operationele teams in staat te stellen direct klantenondersteuning te bieden.
* Bepaal veelgebruikte app-functies om vragen over prioriteit van functies te beantwoorden.

Klant DNN zei: "Application Insights heeft ons voorzien van het ontbrekende deel van de vergelijking voor de mogelijkheid om gegevens te combineren, sorteren, opvragen en filteren als dat nodig is. Door ons team in staat te stellen hun eigen vindingrijkheid en ervaring te gebruiken om gegevens te vinden met een krachtige querytaal, konden we inzichten vinden en problemen oplossen die we niet eens wisten dat we hadden. Veel interessante antwoorden komen uit de vragen te beginnen met *'Ik vraag me af of ...'.*"

## <a name="development-tools-integration"></a>Integratie van ontwikkelingstools
### <a name="configuring-application-insights"></a>Toepassingsinzichten configureren
Visual Studio en Eclipse hebben tools om de juiste SDK-pakketten te configureren voor het project dat u ontwikkelt. Er is een menuopdracht om Toepassingsinzichten toe te voegen.

Als u toevallig een traceloggingframework gebruikt, zoals Log4N, NLog of System.Diagnostics.Trace, dan krijgt u de optie om de logboeken naar Application Insights te sturen, samen met de andere telemetrie, zodat u de sporen gemakkelijk correleren met aanvragen, afhankelijkheid gesprekken en uitzonderingen.

### <a name="search-telemetry-in-visual-studio"></a>Zoeken naar telemetrie in Visual Studio
Tijdens het ontwikkelen en debuggen van een functie, u de telemetrie rechtstreeks bekijken en doorzoeken in Visual Studio, met behulp van dezelfde zoekfaciliteiten als in het webportaal.

En wanneer Application Insights een uitzondering registreert, u het gegevenspunt in Visual Studio bekijken en direct naar de relevante code gaan.

![Zoeken in Visual Studio](./media/devops/060.png)

Tijdens het debuggen heb je de mogelijkheid om de telemetrie in je ontwikkelmachine te houden, deze te bekijken in Visual Studio, maar zonder deze naar de portal te sturen. Deze lokale optie voorkomt het mengen van foutopsporing met productietelemetrie.

### <a name="work-items"></a>Werkitems
Wanneer een waarschuwing wordt gegenereerd, kan Application Insights automatisch een werkitem maken in uw werkvolgsysteem.

## <a name="but-what-about"></a>Maar hoe zit het...?
* [Privacy en opslag](../../azure-monitor/app/data-retention-privacy.md) - Uw telemetrie wordt bewaard op beveiligde Azure-servers.
* Prestaties - de impact is zeer laag. Telemetrie is batched.
* [Prijzen](../../azure-monitor/app/pricing.md) - U gratis aan de slag, en dat gaat door terwijl u in een laag volume bent.


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Volgende stappen
Aan de slag met Application Insights is eenvoudig. De belangrijkste opties zijn:

* [IIS-servers](../../azure-monitor/app/monitor-performance-live-website-now.md)en ook voor [Azure App Service.](../../azure-monitor/app/app-insights-overview.md)
* Instrument uw project tijdens de ontwikkeling. U dit doen voor [ASP.NET](../../azure-monitor/app/asp-net.md) of [Java-apps,](../../azure-monitor/app/java-get-started.md) evenals [Node.js](../../azure-monitor/app/nodejs.md) en een groot aantal andere [soorten.](../../azure-monitor/app/platforms.md) 
* Instrument [een webpagina](../../azure-monitor/app/javascript.md) door het toevoegen van een korte code fragment.

