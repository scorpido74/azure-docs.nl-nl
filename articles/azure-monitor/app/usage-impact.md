---
title: Gebruikseffecten van Azure Application Insights | Microsoft-documenten
description: Analyseer hoe verschillende eigenschappen mogelijk van invloed zijn op conversiepercentages voor delen van uw apps.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 787221c4df3f06029d19ee779a28bb763723f27d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671032"
---
# <a name="impact-analysis-with-application-insights"></a>Impactanalyse met Application Insights

Impact analyseert hoe laadtijden en andere eigenschappen de conversieratio's voor verschillende delen van uw app beïnvloeden. Om het preciezer te zeggen, het ontdekt hoe **elke dimensie** van een **paginaweergave,** **aangepaste gebeurtenis**of **aanvraag** van invloed is op het gebruik van een andere **paginaweergave** of **aangepaste gebeurtenis**. 

![Impacttool](./media/usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Weet je nog steeds niet zeker wat Impact doet?

Een manier om impact te zien is als het ultieme hulpmiddel om ruzies te regelen met iemand in je team over hoe traagheid in een bepaald aspect van je site van invloed is op de vraag of gebruikers blijven hangen. Hoewel gebruikers een bepaalde mate van traagheid kunnen tolereren, geeft Impact u inzicht in hoe u optimalisatie en prestaties het beste balanceren om de gebruikersconversie te maximaliseren.

Maar het analyseren van prestaties is slechts een subset van de mogelijkheden van Impact. Aangezien Impact aangepaste gebeurtenissen en dimensies ondersteunt, is het beantwoorden van vragen zoals hoe de browserkeuze van de gebruiker correleert met verschillende conversiepercentages slechts een paar klikken verwijderd.

![Schermafbeelding conversie door browsers](./media/usage-impact/0004-browsers.png)

> [!NOTE]
> Uw toepassingsstatistiekenbron moet paginaweergaven of aangepaste gebeurtenissen bevatten om het gereedschap Impact te gebruiken. [Meer informatie over het instellen van uw app om paginaweergaven automatisch te verzamelen met de Application Insights JavaScript SDK.](../../azure-monitor/app/javascript.md) Houd er ook rekening mee dat, aangezien u correlatie analyseert, de steekproefgrootte van belang is.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>Heeft de laadtijd van pagina's invloed op het aantal mensen dat op mijn pagina wordt geconverteerd?

Als u vragen wilt beantwoorden met het gereedschap Impact, kiest u een eerste paginaweergave, aangepaste gebeurtenis of aanvraag.

![Impacttool](./media/usage-impact/0002-dropdown.png)

1. Selecteer een paginaweergave in de vervolgkeuzelijst **Voor de paginaweergave.**
2. Laat de **analyse achter hoe de vervolgkeuzelijst** op de standaardselectie van **Duur** (In deze context is **Duur** een alias voor de laadtijd **van pagina's**.)
3. Selecteer een aangepaste gebeurtenis voor **de gevolgen van het gebruik van** vervolgkeuzelijst. Deze gebeurtenis moet overeenkomen met een ui-element in de paginaweergave die u in stap 1 hebt geselecteerd.

![Schermafbeelding van resultaten](./media/usage-impact/0003-results.png)

In dit geval als **productpagina** laadtijd verhoogt de conversieratio **naar Aankoop Product geklikt** gaat naar beneden. Op basis van de bovenstaande verdeling kan een optimale laadduur van 3,5 seconden worden gericht om een potentiële conversieratio van 55% te bereiken. Verdere prestatieverbeteringen om de laadtijd tot onder de 3,5 seconden te verminderen, correleren momenteel niet met extra conversievoordelen.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Wat moet ik doen als ik paginaweergaven of laadtijden op aangepaste manieren bijhouden?

Impact ondersteunt zowel standaard- als aangepaste eigenschappen en metingen. Gebruik wat je wilt. In plaats van duur, gebruikt u filters op de primaire en secundaire gebeurtenissen om specifieker te worden.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Zetten gebruikers uit verschillende landen of regio's tegen verschillende tarieven om?

1. Selecteer een paginaweergave in de vervolgkeuzelijst **Voor de paginaweergave.**
2. Kies 'Land of regio' in **analyseren hoe de vervolgkeuzelijst**
3. Selecteer een aangepaste gebeurtenis die overeenkomt met een ui-element in de paginaweergave die u in stap 1 hebt gekozen, voor **de gevolgen van het gebruik van** vervolgkeuzelijst.

In dit geval passen de resultaten niet meer in een continu x-asmodel zoals in het eerste voorbeeld. In plaats daarvan wordt een visualisatie gepresenteerd die vergelijkbaar is met een gesegmenteerde trechter. Sorteer **op gebruik** om de variatie van conversie naar uw aangepaste gebeurtenis te bekijken op basis van land/regio.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Hoe berekent de impacttool deze conversiepercentages?

Onder de motorkap is de Impact tool afhankelijk van de [Pearson correlatiecoëfficiënt](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). De resultaten worden berekend tussen -1 en 1 met -1 die een negatieve lineaire correlatie vertegenwoordigen en 1 die een positieve lineaire correlatie vertegenwoordigt.

De basisuitsplitsing van de werking van impactanalyse is als volgt:

Laat _A_ = de hoofdpaginaweergave/aangepaste gebeurtenis/aanvraag die u selecteert in de eerste vervolgkeuzelijst. (**Voor de paginaweergave**).

Laat _B_ = de secundaire paginaweergave/aangepaste gebeurtenis die u selecteert **(van invloed op het gebruik van**).

Impact kijkt naar een voorbeeld van alle sessies van gebruikers in het geselecteerde tijdsbereik. Voor elke sessie wordt gekeken naar elk optreden van _A_.

Sessies worden vervolgens opgesplitst in twee verschillende soorten _subsessies_ op basis van een van de twee voorwaarden:

- Een geconverteerde subsessie bestaat uit een sessie die eindigt met een _B-gebeurtenis_ en omvat alle _A-gebeurtenissen_ die plaatsvinden voorafgaand aan _B._
- Een ongeconverteerde subsessie treedt op wanneer alle _A's_plaatsvinden zonder terminal _B._

Hoe impact uiteindelijk wordt berekend, is afhankelijk van de vraag of we analyseren op basis van metrische of dimensie. Voor statistieken worden alle _A's_in een subsessie gemiddeld weergegeven. Overwegende dat voor de afmetingen de waarde van elke _A_ _1/N_ bijdraagt aan de aan _B_ toegekende waarde, wanneer _N_ het aantal _A's_in de subsessie is.

## <a name="next-steps"></a>Volgende stappen

- Als u gebruikservaringen wilt inschakelen, begint u met het verzenden van [aangepaste gebeurtenissen](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) of [paginaweergaven.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)
- Als u al aangepaste gebeurtenissen of paginaweergaven verzendt, raadpleegt u de hulpprogramma's Voor gebruik om te leren hoe gebruikers uw service gebruiken.
    - [Trechters](usage-funnels.md)
    - [Retentie](usage-retention.md)
    - [Gebruikersstromen](usage-flows.md)
    - [Werkmappen](../../azure-monitor/app/usage-workbooks.md)
    - [Gebruikerscontext toevoegen](usage-send-user-context.md)
