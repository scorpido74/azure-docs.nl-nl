---
title: Gebruikers-, sessie-en gebeurtenis analyse in Azure-toepassing Insights
description: Demografische analyse van gebruikers van uw web-app.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 10ea3c68695e7e4fb4950fbcc4a190e9f1d83f49
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797699"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Gebruikers, sessies en gebeurtenissen analyse in Application Insights

Ontdek wanneer mensen uw web-app gebruiken, op welke pagina's ze het meest geïnteresseerd zijn, waar uw gebruikers zich bevinden en welke browsers en besturings systemen ze gebruiken. Analyseer bedrijfs-en gebruiks telemetrie met behulp van [Azure-toepassing Insights](../../azure-monitor/app/app-insights-overview.md).

![Scherm opname van Application Insights gebruikers](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>Aan de slag

Als u nog geen gegevens ziet in de Blades gebruikers, sessies of gebeurtenissen in de Application Insights Portal, [leert u hoe u aan de slag kunt met de gebruiks hulpprogramma's](usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>Het hulp programma gebruikers, sessies en gebeurtenissen segmentatie

Drie van de gebruiks bladen gebruiken hetzelfde hulp programma om telemetrie van uw web-app vanuit drie perspectieven te segmenteren en te analyseren. Door de gegevens te filteren en te splitsen, kunt u inzichten opsporen over het relatieve gebruik van verschillende pagina's en functies.

* **Hulp programma gebruikers**: hoeveel mensen hebben uw app en de bijbehorende functies gebruikt.  Gebruikers worden geteld met anonieme Id's die zijn opgeslagen in browser cookies. Eén persoon die verschillende browsers of machines gebruikt, wordt als meer dan één gebruiker geteld.
* **Hulp programma voor sessies**: hoeveel sessies van gebruikers activiteit hebben bepaalde pagina's en functies van uw app opgenomen. Een sessie wordt geteld na een half uur van inactiviteit van de gebruiker, of na 24 uur na continu gebruik.
* **Hulp programma voor gebeurtenissen**: hoe vaak bepaalde pagina's en functies van uw app worden gebruikt. Een pagina weergave wordt geteld wanneer een browser een pagina uit uw app laadt, op voor waarde dat u [deze hebt geinstrumenteerd](../../azure-monitor/app/javascript.md). 

    Een aangepaste gebeurtenis vertegenwoordigt een exemplaar van iets wat er gebeurt in uw app, vaak een gebruikers interactie zoals het klikken op de knop of het volt ooien van een taak. U voegt code in uw app in om [aangepaste gebeurtenissen te genereren](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Query's uitvoeren voor bepaalde gebruikers

Verken verschillende groepen gebruikers door de query opties boven aan het hulp programma gebruikers aan te passen:

* Weer geven: Kies een cohort van gebruikers die u wilt analyseren.
* Wie heeft gebruikt: Kies aangepaste gebeurtenissen en pagina weergaven.
* Tijdens: Kies een tijds bereik.
* Op: Kies hoe de gegevens moeten worden gebuckd op basis van een bepaalde tijd of door een andere eigenschap zoals browser of plaats.
* Splitsen op: Kies een eigenschap waarmee u de gegevens wilt splitsen of segmenteren. 
* Filters toevoegen: de query beperken tot bepaalde gebruikers, sessies of gebeurtenissen op basis van hun eigenschappen, zoals browser of plaats. 
 
## <a name="saving-and-sharing-reports"></a>Rapporten opslaan en delen 
U kunt gebruikers rapporten opslaan, alleen persoonlijk aan u in de sectie Mijn rapporten, of delen met iedereen die toegang heeft tot deze Application Insights resource in de sectie gedeelde rapporten.

Een koppeling naar een rapport gebruikers, sessies of gebeurtenissen delen; Klik op **delen** in de werk balk en kopieer de koppeling.

Een kopie van de gegevens delen in een rapport gebruikers, sessies of gebeurtenissen; Klik op **delen** in de werk balk en klik vervolgens op het **woord pictogram** om een Word-document te maken met de gegevens. U kunt ook op het pictogram van het **woord** boven het hoofd diagram klikken.

## <a name="meet-your-users"></a>Voldoen aan uw gebruikers

De sectie **aan uw gebruikers voldoen** bevat informatie over vijf voorbeeld gebruikers die overeenkomen met de huidige query. Als aanvulling op aggregaties kunt u het gedrag van individuen overwegen en bekijken hoe mensen uw app daad werkelijk gebruiken.

## <a name="next-steps"></a>Volgende stappen

- Begin met het verzenden van [aangepaste gebeurtenissen](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) of [pagina weergaven](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)om gebruiks ervaringen in te scha kelen.
- Als u al aangepaste gebeurtenissen of pagina weergaven verzendt, kunt u de gebruiks hulpprogramma's verkennen om te leren hoe gebruikers uw service gebruiken.
    - [Trechters](usage-funnels.md)
    - [Bewaartermijn](usage-retention.md)
    - [Gebruikersstromen](usage-flows.md)
    - [Werkmappen](../../azure-monitor/platform/workbooks-overview.md)
    - [Gebruikers context toevoegen](usage-send-user-context.md)
