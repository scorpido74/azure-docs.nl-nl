---
title: Analyse van gebruiker, sessie en gebeurtenis in Azure Application Insights
description: Demografische analyse van gebruikers van uw web-app.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 56059304026b060e2215ce73e0e94e3200573a14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670981"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Gebruikers, sessies en gebeurtenissenanalyse in Application Insights

Ontdek wanneer mensen uw web-app gebruiken, in welke pagina's ze het meest geïnteresseerd zijn, waar uw gebruikers zich bevinden en welke browsers en besturingssystemen ze gebruiken. Analyseer bedrijfs- en gebruikstelemetrie met [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

![Schermafbeelding van gebruikers van Application Insights](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>Aan de slag

Als u nog geen gegevens ziet in de gebruikers,sessies of gebeurtenissenbladen in de Portal Application Insights, leest u hoe u aan [de slag met de gebruikstools.](usage-overview.md)

## <a name="the-users-sessions-and-events-segmentation-tool"></a>De segmentatietool Gebruikers, Sessies en Gebeurtenissen

Drie van de gebruiksbladen gebruiken hetzelfde gereedschap om telemetrie vanuit uw web-app vanuit drie perspectieven te snijden en te dobbelen. Door de gegevens te filteren en te splitsen, u inzichten ontdekken over het relatieve gebruik van verschillende pagina's en functies.

* **Gebruikerstool:** hoeveel mensen uw app en de bijbehorende functies hebben gebruikt.  Gebruikers worden geteld met behulp van anonieme id's die zijn opgeslagen in browsercookies. Eén persoon die verschillende browsers of machines gebruikt, wordt als meer dan één gebruiker geteld.
* **Sessiestool:** hoeveel sessies van gebruikersactiviteit bepaalde pagina's en functies van uw app hebben opgenomen. Een sessie wordt geteld na een half uur inactiviteit van de gebruiker, of na 24 uur continu gebruik.
* **Gebeurtenistool:** hoe vaak bepaalde pagina's en functies van uw app worden gebruikt. Een paginaweergave wordt geteld wanneer een browser een pagina vanuit uw app laadt, op voorwaarde dat u deze hebt [bewerkt.](../../azure-monitor/app/javascript.md) 

    Een aangepaste gebeurtenis vertegenwoordigt een gebeurtenis van iets gebeurt in uw app, vaak een interactie van de gebruiker, zoals een knop klik of de voltooiing van een taak. U voegt code in uw app in om aangepaste gebeurtenissen te [genereren.](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)

## <a name="querying-for-certain-users"></a>Query's voor bepaalde gebruikers

Verken verschillende groepen gebruikers door de queryopties boven aan het gereedschap Gebruikers aan te passen:

* Toon: Kies een cohort van gebruikers om te analyseren.
* Wie heeft gebruikt: kies aangepaste gebeurtenissen en paginaweergaven.
* Tijdens: Kies een tijdsbereik.
* Door: Kies hoe u de gegevens emmert, hetzij op een bepaalde periode of door een andere eigenschap, zoals browser of stad.
* Gesplitst door: Kies een eigenschap waarmee de gegevens kunnen worden gesplitst of gesegmenterd. 
* Filters toevoegen: beperk de query tot bepaalde gebruikers, sessies of gebeurtenissen op basis van hun eigenschappen, zoals browser of plaats. 
 
## <a name="saving-and-sharing-reports"></a>Rapporten opslaan en delen 
U gebruikersrapporten opslaan, alleen privé voor u in de sectie Mijn rapporten, of met iedereen worden gedeeld met deze toepassingsstatistiekenbron in de sectie Gedeelde rapporten.

Een koppeling naar een rapport gebruikers, sessies of gebeurtenissen delen; klik **op Delen** op de werkbalk en kopieer de koppeling.

Een kopie van de gegevens delen in een rapport Gebruikers, Sessies of Gebeurtenissen; Klik **op Delen** op de werkbalk en klik vervolgens op het **Woordpictogram** om een Word-document met de gegevens te maken. Of klik op het **Woord-pictogram** boven het hoofddiagram.

## <a name="meet-your-users"></a>Maak kennis met uw gebruikers

In de sectie **Voldoen aan uw gebruikers** vindt u informatie over vijf voorbeeldgebruikers die overeenkomen met de huidige query. Het bekijken en verkennen van het gedrag van individuen, naast aggregaten, kan inzicht geven over hoe mensen uw app daadwerkelijk gebruiken.

## <a name="next-steps"></a>Volgende stappen

- Als u gebruikservaringen wilt inschakelen, begint u met het verzenden van [aangepaste gebeurtenissen](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) of [paginaweergaven.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)
- Als u al aangepaste gebeurtenissen of paginaweergaven verzendt, raadpleegt u de hulpprogramma's Voor gebruik om te leren hoe gebruikers uw service gebruiken.
    - [Trechters](usage-funnels.md)
    - [Retentie](usage-retention.md)
    - [Gebruikersstromen](usage-flows.md)
    - [Werkmappen](../../azure-monitor/app/usage-workbooks.md)
    - [Gebruikerscontext toevoegen](usage-send-user-context.md)
