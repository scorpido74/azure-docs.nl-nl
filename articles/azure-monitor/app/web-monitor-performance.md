---
title: De status en het gebruik van uw app bewaken met Application Insights
description: Aan de slag met Application Insights. Analyseer het gebruik, de beschik baarheid en prestaties van uw on-premises of Microsoft Azure toepassingen.
ms.topic: conceptual
ms.date: 05/10/2018
ms.reviewer: sdash
ms.openlocfilehash: d624286d214a86364fe85192bf5ede885d4b6a78
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323448"
---
# <a name="monitor-performance-in-web-applications"></a>Prestaties in webtoepassingen controleren


Zorg ervoor dat uw toepassing goed presteert en kom snel te weten over eventuele fouten. [Application Insights][start] geeft u informatie over prestatie problemen en uitzonde ringen en helpt u bij het vinden en diagnosticeren van de hoofd oorzaken.

Application Insights kunt Java-en ASP.NET-webtoepassingen en-services, WCF-services bewaken. Ze kunnen lokaal, op virtuele machines of als Microsoft Azure websites worden gehost. 

Aan de client zijde kan Application Insights telemetrie maken op basis van webpagina's en een groot aantal verschillende apparaten, waaronder iOS-, Android-en Windows Store-apps.

## <a name="set-up-performance-monitoring"></a><a name="setup"></a>Prestatie bewaking instellen
Als u Application Insights nog niet aan uw project hebt toegevoegd (dat wil zeggen, als het geen ApplicationInsights.config heeft), kiest u een van de volgende manieren om aan de slag te gaan:

* [ASP.NET-web-apps](./asp-net.md)
  * [Uitzonderings bewaking toevoegen](./asp-net-exceptions.md)
  * [Afhankelijkheids bewaking toevoegen](./monitor-performance-live-website-now.md)
* [Java EE web apps](./java-in-process-agent.md)

## <a name="exploring-performance-metrics"></a><a name="view"></a>Metrische prestatie gegevens verkennen
Blader in [het Azure Portal](https://portal.azure.com)naar de Application Insights resource die u voor uw toepassing hebt ingesteld. De Blade overzicht bevat algemene prestatie gegevens:

Klik op een grafiek om meer details weer te geven en om de resultaten gedurende een langere periode weer te geven. Klik bijvoorbeeld op de tegel aanvragen en selecteer vervolgens een tijds bereik:

![Klik op door naar meer gegevens en selecteer een tijds bereik](./media/web-monitor-performance/appinsights-48metrics.png)

Klik op een grafiek om te kiezen welke metrische gegevens worden weer gegeven of Voeg een nieuwe grafiek toe en selecteer de metrische gegevens:

![Klik op een grafiek om de metrische gegevens te kiezen](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Schakel alle metrische gegevens uit** om de volledige selectie te zien die beschikbaar is. De metrische gegevens vallen in groepen. Als een lid van een groep is geselecteerd, worden alleen de andere leden van die groep weer gegeven.

## <a name="what-does-it-all-mean-performance-tiles-and-reports"></a><a name="metrics"></a>Wat betekent dit allemaal? Tegels en rapporten van prestaties
Er zijn diverse prestatie gegevens die u kunt ophalen. Laten we beginnen met de apps die standaard worden weer gegeven op de Blade van de toepassing.

### <a name="requests"></a>Aanvragen
Het aantal HTTP-aanvragen dat in een opgegeven periode is ontvangen. Vergelijk dit met de resultaten in andere rapporten om te zien hoe uw app zich gedraagt als de belasting varieert.

HTTP-aanvragen bevatten alle GET-of POST-aanvragen voor pagina's, gegevens en afbeeldingen.

Klik op de tegel om het aantal specifieke Url's te bepalen.

### <a name="average-response-time"></a>Gemiddelde reactie tijd
Hiermee wordt de tijd gemeten tussen een webaanvraag die uw toepassing invoert en het antwoord dat wordt geretourneerd.

Op de punten wordt een zwevend gemiddelde weer gegeven. Als er veel aanvragen zijn, is er mogelijk een afwijkend van het gemiddelde zonder een duidelijke piek of DIP in de grafiek.

Zoek naar ongebruikelijke pieken. In het algemeen wordt de reactie tijd verwacht met een toename in aanvragen. Als de toename niet evenredig is, kan uw app een resource limiet hebben, zoals CPU of de capaciteit van een service die wordt gebruikt.

Klik op de tegel om tijden voor specifieke Url's op te halen.

![Scherm opname van het deel venster status van de toepassing, waarin lijn grafieken worden weer gegeven in de loop van de tijd van de zwevende gemiddelden van aanvragen en de reactie tijd.](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Langzaamste aanvragen
![Scherm afbeelding van een lijst met de traagste aanvragen en hun reactie tijden.](./media/web-monitor-performance/appinsights-44slowest.png)

Hier ziet u welke aanvragen mogelijk prestatie afstemming nodig hebben.

### <a name="failed-requests"></a>Mislukte aanvragen
![Scherm afbeelding van een lijn diagram in de loop van de tijd van het aantal mislukte aanvragen; dat wil zeggen, aanvragen waarvoor niet-onderschepte uitzonde ringen zijn gegenereerd.](./media/web-monitor-performance/appinsights-46failed.png)

Een aantal aanvragen dat niet-onderschepte uitzonde ringen heeft veroorzaakt.

Klik op de tegel om de details van specifieke fouten te bekijken en selecteer een afzonderlijke aanvraag om het detail venster weer te geven. 

Voor afzonderlijke inspecties wordt alleen een representatief voor beeld van fouten bewaard.

### <a name="other-metrics"></a>Andere metrische gegevens
Als u wilt zien welke andere metrische gegevens u kunt weer geven, klikt u op een grafiek en schakelt u de selectie van alle metrische gegevens uit om de volledige set weer te geven. Klik (i) om de definitie van elke metriek weer te geven.

![De selectie van alle metrische gegevens opheffen om de hele set weer te geven](./media/web-monitor-performance/appinsights-62allchoices.png)

Als u een wille keurige waarde selecteert, worden de andere gegevens uitgeschakeld die niet in hetzelfde diagram kunnen worden weer gegeven.

## <a name="set-alerts"></a>Waarschuwingen instellen
Voeg een waarschuwing toe om een melding te ontvangen van een e-mail bericht van ongebruikelijke waarden van een wille keurige waarde. U kunt kiezen of u het e-mail bericht wilt verzenden naar de account beheerders of naar specifieke e-mail adressen.

![Scherm afbeelding van het dialoog venster een waarschuwings regel toevoegen, samen met scherm opnamen die zijn verbonden via pijlen die laten zien hoe dat dialoog venster van Metrics Explorer kan worden bereikt.](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

Stel de resource in vóór de andere eigenschappen. Kies niet de webtest-resources als u waarschuwingen wilt instellen voor metrische gegevens over prestaties of gebruik.

Let op de eenheden waarin u wordt gevraagd de drempel waarde in te voeren.

*Ik zie de knop Waarschuwing toevoegen niet.* -Is dit een groeps account waarvoor u alleen-lezen toegang hebt? Neem contact op met de account beheerder.

## <a name="diagnosing-issues"></a><a name="diagnosis"></a>Problemen vaststellen
Hier volgen enkele tips voor het zoeken en diagnosticeren van prestatie problemen:

* Stel [webtests][availability] in om te worden gewaarschuwd als uw website uitvalt of onjuist of traag reageert. 
* Vergelijk het aantal aanvragen met andere metrische gegevens om te zien of er fouten of trage reacties zijn gerelateerd aan de belasting.
* U kunt in uw code [tracerings instructies invoegen en zoeken][diagnostic] om problemen op te sporen.
* Bewaak uw web-app in bewerking met [Live Metrics stream][livestream].
* Leg de status van uw .NET-toepassing vast met [Snapshot debugger][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Prestatie knelpunten opsporen en oplossen met prestatie onderzoek

U kunt de ervaring voor prestatie onderzoek gebruiken om trage bewerkingen in uw web-app te bekijken. U kunt snel een specifieke trage bewerking selecteren en [Profiler](./profiler.md) gebruiken om te zorgen dat de langzame bewerkingen worden uitgevoerd naar code. Met de nieuwe duur verdeling die voor de geselecteerde bewerking wordt weer gegeven, kunt u in één oogopslag zien hoe slecht de ervaring is voor uw klanten. U kunt zien hoeveel van uw gebruikers interacties voor elke trage bewerking zijn beïnvloed. In het volgende voor beeld hebben we besloten om de ervaring voor het ophalen van klanten/details te bekijken. In de duur van de distributie ziet u dat er drie pieken zijn. De meest linkse Prikker is ongeveer 400 MS en vormt een fantastische reactie ervaring. Middelste Prikker is ongeveer 1,2 s en vertegenwoordigt een mediocre-ervaring. Tot slot op de 3,6 s hebben we nog een kleine piek die de 99e-percentiel ervaring vertegenwoordigt, waardoor onze klanten waarschijnlijk niet meer tevreden kunnen raken. Deze ervaring is tien keer langzamer dan de geweldige ervaring voor dezelfde bewerking. 

![Ontvang klanten/Details drie duur pieken](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Om een beter beeld te krijgen van de gebruikers ervaringen voor deze bewerking, kunnen we een groter tijds bereik selecteren. We kunnen vervolgens ook minder tijd inzoomen op een specifiek tijd venster waarin de bewerking is vertraagd. In het volgende voor beeld is het tijds bereik van de standaard periode van 24 uur gewijzigd in een periode van zeven dagen en vervolgens ingezoomd op de 9:47 tot 12:47 tijd venster tussen di de twaalfde en de 13. Zowel de duur van de distributie als het aantal voorbeeld-en Profiler-traceringen zijn aan de rechter kant bijgewerkt.

![Klanten/Details drie duur pieken in zeven dagen bereiken met een tijd venster](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Om in te zoomen op de trage ervaring, wordt de duur van de 95e en het 99e percentiel in de volgende stap in de tijd ingezoomd. Deze vertegenwoordigen de 4% van gebruikers interacties die traag zijn.

![Klanten/Details drie duur pieken in zeven dagen bereiken met een tijd venster](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

We kunnen de representatieve voor beelden nu bekijken, door te klikken op de knop voor beelden of door te klikken op de representatieve Profiler traceringen door op de knop Profiler traceringen. In dit voor beeld zijn er vier traceringen die zijn verzameld voor het ophalen van klanten/Details in het tijd venster en de duur van het bereik van de rente.

Soms bevindt het probleem zich niet in uw code, maar in een afhankelijkheid van uw code aanroepen. U kunt overschakelen naar het tabblad Afhankelijkheden in de weer gave prestatie sorteren om dergelijke langzame afhankelijkheden te onderzoeken. Standaard is de prestatie weergave trending gemiddelden, maar wat u eigenlijk wilt bekijken is het 95e percentiel (of de 99e, voor het geval u een rijpere Service bewaakt). In het volgende voor beeld hebben we gefocust op de langzame Azure BLOB-afhankelijkheid, waar we PUT fabrikamaccount aanroepen. Het goede cluster rond 40 MS, terwijl de trage aanroepen naar dezelfde afhankelijkheid drie keer langzamer zijn, clustering rond 120 MS. Het is niet veel van deze aanroepen om toe te voegen om ervoor te zorgen dat de betreffende bewerking merkbaar langzamer verloopt. U kunt inzoomen op de representatieve voor beelden en Profiler traceringen, net zoals u dat kunt doen met het tabblad bewerkingen.

![Klanten/Details drie duur pieken in zeven dagen bereiken met een tijd venster](./media/web-monitor-performance/SlowDependencies95thTrend.png)

In de ervaring voor prestatie onderzoek ziet u relevante inzichten aan de kant van de voor beeld-set die u hebt gekozen om zich te richten op. De beste manier om alle beschik bare inzichten te bekijken, is om over te scha kelen naar een periode van 30 dagen en vervolgens algemeen te selecteren om inzicht te krijgen in alle bewerkingen voor de afgelopen maand.

![Klanten/Details drie duur pieken in zeven dagen bereiken met een tijd venster](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next-steps"></a><a name="next"></a>Volgende stappen
[Webtests][availability] : laat webaanvragen met regel matige intervallen van over de hele wereld naar uw toepassing worden verzonden.

[Diagnostische traceringen vastleggen en zoeken][diagnostic] : plaats tracerings aanroepen en SIFT door de resultaten om problemen te lokaliseren.

[Gebruiks tracking][usage] : Ontdek hoe mensen uw toepassing gebruiken.

[Probleem oplossing][qna] -en Q & A



<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[greenbrown]: ./asp-net.md
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md
[usage]: usage-overview.md
[livestream]: ./live-stream.md
[snapshot]: ./snapshot-debugger.md

