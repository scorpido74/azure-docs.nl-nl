---
title: De status en het gebruik van uw app bewaken met Application Insights
description: Ga aan de slag met Application Insights. Analyseer het gebruik, de beschikbaarheid en de prestaties van uw on-premises of Microsoft Azure-toepassingen.
ms.topic: conceptual
ms.date: 05/10/2018
ms.reviewer: sdash
ms.openlocfilehash: 873fc41585c387246d83008a8f97d6c4d9a32c3b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985062"
---
# <a name="monitor-performance-in-web-applications"></a>Prestaties in webtoepassingen controleren


Zorg ervoor dat uw toepassing goed presteert en kom snel achter eventuele fouten. [Application Insights][start] vertelt u over eventuele prestatieproblemen en uitzonderingen en helpt u bij het vinden en diagnosticeren van de onderliggende oorzaken.

Application Insights kan zowel Java- als ASP.NET webtoepassingen en -services, WCF-services, monitoren. Ze kunnen on-premises, op virtuele machines of als Microsoft Azure-websites worden gehost. 

Aan de clientzijde kan Application Insights telemetrie van webpagina's en een breed scala aan apparaten gebruiken, waaronder iOS-, Android- en Windows Store-apps.

## <a name="set-up-performance-monitoring"></a><a name="setup"></a>Prestatiebewaking instellen
Als u Application Insights nog niet aan uw project hebt toegevoegd (dat wil zeggen, als het niet applicationinsights.config heeft), kiest u een van de volgende manieren om aan de slag te gaan:

* [ASP.NET-web-apps](../../azure-monitor/app/asp-net.md)
  * [Uitzonderingsbewaking toevoegen](../../azure-monitor/app/asp-net-exceptions.md)
  * [Afhankelijkheidsbewaking toevoegen](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Java EE web-apps](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)

## <a name="exploring-performance-metrics"></a><a name="view"></a>Prestatiestatistieken verkennen
Blader in [de Azure-portal](https://portal.azure.com)naar de application Insights-bron die u voor uw toepassing hebt ingesteld. Het overzichtsblad toont basisprestatiegegevens:

Klik op een grafiek om meer details te zien en om resultaten voor een langere periode te bekijken. Klik bijvoorbeeld op de tegel Aanvragen en selecteer vervolgens een tijdsbereik:

![Klik door naar meer gegevens en selecteer een tijdsbereik](./media/web-monitor-performance/appinsights-48metrics.png)

Klik op een grafiek om te kiezen welke statistieken worden weergegeven, of voeg een nieuwe grafiek toe en selecteer de statistieken:

![Klik op een grafiek om statistieken te kiezen](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Schakel alle statistieken uit** om de volledige selectie te bekijken die beschikbaar is. De statistieken vallen in groepen; wanneer een lid van een groep wordt geselecteerd, worden alleen de andere leden van die groep weergegeven.

## <a name="what-does-it-all-mean-performance-tiles-and-reports"></a><a name="metrics"></a>Wat betekent het allemaal? Prestatietegels en -rapporten
Er zijn verschillende prestatiestatistieken die u krijgen. Laten we beginnen met degenen die standaard op het toepassingsblad worden weergegeven.

### <a name="requests"></a>Aanvragen
Het aantal HTTP-aanvragen dat in een bepaalde periode is ontvangen. Vergelijk dit met de resultaten van andere rapporten om te zien hoe uw app zich gedraagt terwijl de belasting varieert.

HTTP-verzoeken bevatten alle GET- of POST-verzoeken voor pagina's, gegevens en afbeeldingen.

Klik op de tegel om tellingen voor specifieke URL's te krijgen.

### <a name="average-response-time"></a>Gemiddelde responstijd
Hiermee meet u de tijd tussen een webaanvraag die uw toepassing invoert en het antwoord dat wordt geretourneerd.

De punten tonen een voortschrijdend gemiddelde. Als er veel aanvragen zijn, kunnen er enkele zijn die afwijken van het gemiddelde zonder een duidelijke piek of dip in de grafiek.

Zoek naar ongewone pieken. In het algemeen, verwachten reactietijd te stijgen met een stijging van de verzoeken. Als de stijging onevenredig is, kan uw app een resourcelimiet raken, zoals CPU of de capaciteit van een service die wordt gebruikt.

Klik op de tegel om tijden op te halen voor specifieke URL's.

![](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Langzaamste aanvragen
![](./media/web-monitor-performance/appinsights-44slowest.png)

Hiermee ziet u welke aanvragen mogelijk prestatieafstemming nodig hebben.

### <a name="failed-requests"></a>Mislukte aanvragen
![](./media/web-monitor-performance/appinsights-46failed.png)

Een telling van verzoeken die niet gevangen uitzonderingen wierpen.

Klik op de tegel om de details van specifieke fouten te bekijken en selecteer een individueel verzoek om de details ervan te bekijken. 

Alleen een representatieve steekproef van storingen wordt bewaard voor individuele inspectie.

### <a name="other-metrics"></a>Andere statistieken
Als u wilt zien welke andere statistieken u weergeven, klikt u op een grafiek en schakelt u alle statistieken uit om de volledige beschikbare set weer te geven. Klik (i) om de definitie van elke statistiek te bekijken.

![Schakel alle statistieken uit om de hele set te bekijken](./media/web-monitor-performance/appinsights-62allchoices.png)

Als u een statistiek selecteert, worden de anderen uitgeschakeld die niet in dezelfde grafiek kunnen worden weergegeven.

## <a name="set-alerts"></a>Waarschuwingen instellen
Als u per e-mail op de hoogte wilt worden gesteld van ongebruikelijke waarden van een statistiek, voegt u een waarschuwing toe. U ervoor kiezen om de e-mail naar de accountbeheerders of naar specifieke e-mailadressen te sturen.

![](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

Stel de resource in vóór de andere eigenschappen. Kies niet de webtestbronnen als u waarschuwingen wilt instellen voor prestatie- of gebruiksstatistieken.

Let op de eenheden waarin u wordt gevraagd de drempelwaarde in te voeren.

*Ik zie de knop Waarschuwing toevoegen niet.* - Is dit een groepsaccount waartoe je alleen-lezen toegang hebt? Neem contact op met de accountbeheerder.

## <a name="diagnosing-issues"></a><a name="diagnosis"></a>Problemen diagnosticeren
Hier zijn een paar tips voor het vinden en diagnosticeren van prestatieproblemen:

* Stel [webtests][availability] in om te worden gewaarschuwd als uw website uitvalt of onjuist of langzaam reageert. 
* Vergelijk het aantal aanvragen met andere statistieken om te zien of fouten of trage respons gerelateerd zijn aan belasting.
* [Voeg traceringsverklaringen][diagnostic] in en zoek in uw code om problemen op te sporen.
* Houd uw web-app in werking met [Live Metrics Stream][livestream].
* Leg de status van uw .NET-toepassing vast met [Momentopnamefoutopsporing][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Prestatieknelpunten zoeken en oplossen met ervaring in prestatieonderzoek

U de prestatie-onderzoekservaring gebruiken om traag presterende bewerkingen in uw web-app te bekijken. U snel een specifieke langzame bewerking selecteren en [Profiler](../../azure-monitor/app/profiler.md) gebruiken om de onderliggende bewerkingen te laten coderen. Met behulp van de nieuwe duurverdeling die wordt weergegeven voor de geselecteerde bewerking, u snel in één oogopslag beoordelen hoe slecht de ervaring is voor uw klanten. U zien hoeveel van uw gebruikersinteracties zijn beïnvloed voor elke langzame bewerking. In het volgende voorbeeld hebben we besloten om de ervaring voor get-klanten/details-bewerking nader te bekijken. In de duurverdeling kunnen we zien dat er drie pieken zijn. De meest linkse piek is ongeveer 400 ms en vertegenwoordigt een geweldige responsieve ervaring. Middelste spike is ongeveer 1,2 s en vertegenwoordigt een middelmatige ervaring. Tot slot op de 3.6 s hebben we nog een kleine piek die de 99e percentiel ervaring vertegenwoordigt, die waarschijnlijk zal leiden tot onze klanten te verlaten ontevreden. Die ervaring is tien keer langzamer dan de geweldige ervaring voor dezelfde operatie. 

![GET-klanten/Details drie duurpieken](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Om een beter beeld te krijgen van de gebruikerservaringen voor deze bewerking, kunnen we een groter tijdsbereik selecteren. We kunnen dan ook beperken in de tijd op een specifiek tijdvenster waar de operatie traag was. In het volgende voorbeeld zijn we overgestapt van het standaard tijdsbereik van 24 uur naar het tijdsbereik van 7 dagen en vervolgens ingezoomd op het tijdvenster van 9:47 naar 12:47 tussen Di de 12e en Wed de 13e. Zowel de duurverdeling als het aantal voorbeeld- en profielsporen zijn aan de rechterkant bijgewerkt.

![GET-klanten/-gegevens drie duurpieken in 7 dagen bereik met een tijdvenster](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Om de langzame ervaringen te beperken, zoomen we vervolgens in op de duur die tussen de 95e en het 99e percentiel valt. Deze vertegenwoordigen de 4% van de gebruikersinteracties die traag waren.

![GET-klanten/-gegevens drie duurpieken in 7 dagen bereik met een tijdvenster](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

We kunnen nu ofwel kijken naar de representatieve monsters, door te klikken op de knop Monsters, of op de representatieve profiler sporen, door te klikken op de Profiler sporen knop. In dit voorbeeld zijn er vier sporen verzameld voor GET-klanten/-details in het tijdvenster en de duur van de interesse.

Soms zit het probleem niet in uw code, maar in een afhankelijkheid die uw code aanroept. U overschakelen naar het tabblad Afhankelijkheden in de prestatietriageweergave om dergelijke langzame afhankelijkheden te onderzoeken. Standaard is de prestatieweergave trending gemiddelden, maar wat je echt wilt kijken is het 95e percentiel (of de 99e, in het geval u toezicht houdt op een volwassen service). In het volgende voorbeeld hebben we ons gericht op de langzame Azure BLOB-afhankelijkheid, waar we PUT-fabrikamaccount noemen. De goede ervaringen cluster rond 40 ms, terwijl de langzame oproepen naar dezelfde afhankelijkheid zijn drie keer langzamer, clustering rond 120 ms. Het duurt niet veel van deze oproepen op te tellen om de respectieve operatie merkbaar vertragen veroorzaken. U inzoomen op de representatieve voorbeelden en profilersporen, net zoals u met het tabblad Bewerkingen.

![GET-klanten/-gegevens drie duurpieken in 7 dagen bereik met een tijdvenster](./media/web-monitor-performance/SlowDependencies95thTrend.png)

De ervaring met prestatieonderzoek toont relevante inzichten samen met de voorbeeldset waarop u zich hebt gaan richten. De beste manier om alle beschikbare inzichten te bekijken, is door over te schakelen naar een tijdsbereik van 30 dagen en vervolgens Algemeen te selecteren om inzichten te zien over alle bewerkingen van de afgelopen maand.

![GET-klanten/-gegevens drie duurpieken in 7 dagen bereik met een tijdvenster](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next-steps"></a><a name="next"></a>Volgende stappen
[Webtests][availability] - Laat webverzoeken regelmatig naar uw toepassing sturen vanuit de hele wereld.

[Diagnostische traceringssporen vastleggen en doorzoeken][diagnostic] - Tracecalls invoegen en de resultaten doorzoeken om problemen te lokaliseren.

[Gebruikstracking][usage] - Ontdek hoe mensen uw toepassing gebruiken.

[Probleemoplossing][qna] - en Q & A



<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[usage]: usage-overview.md
[livestream]: ../../azure-monitor/app/live-stream.md
[snapshot]: ../../azure-monitor/app/snapshot-debugger.md



