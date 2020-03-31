---
title: Analyse van gebruikersretentie voor webtoepassingen met Azure Application Insights | Microsoft-documenten
description: Hoeveel gebruikers keren terug naar uw app?
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 05/03/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 37ae97d5fbc62e507f726c452999a7f6e7c989c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670964"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Gebruikersbewaaranalyse voor webapplicaties met Application Insights

Met de bewaarfunctie in [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) u analyseren hoeveel gebruikers terugkeren naar uw app en hoe vaak ze bepaalde taken uitvoeren of doelen bereiken. Als u bijvoorbeeld een gamesite uitvoert, u het aantal gebruikers dat terugkeert naar de site vergelijken nadat u een game hebt verloren met het nummer dat terugkeert na het winnen. Deze kennis kan u helpen zowel uw gebruikerservaring als uw bedrijfsstrategie te verbeteren.

## <a name="get-started"></a>Aan de slag

Als u nog geen gegevens ziet in de bewaartool in de Portal Application Insights, leest u hoe u aan [de slag met de gebruikstools.](usage-overview.md)

## <a name="the-retention-tool"></a>Het gereedschap Retentie

![Retentie-informatie](./media/usage-retention/retention.png)

1. Op de werkbalk kunnen gebruikers nieuwe bewaarrapporten maken, bestaande bewaarrapporten openen, het huidige bewaarrapport opslaan of opslaan als, wijzigingen in opgeslagen rapporten terugdraaien, gegevens over het rapport vernieuwen, rapport delen via e-mail of directe koppeling en toegang krijgen tot de documentatie Pagina. 
2. Standaard, retentie toont alle gebruikers die iets deed dan kwam terug en deed iets anders over een periode. U verschillende combinaties van gebeurtenissen selecteren om de focus op specifieke gebruikersactiviteiten te beperken.
3. Voeg een of meer filters toe aan eigenschappen. U zich bijvoorbeeld richten op gebruikers in een bepaald land of regio. Klik **op Bijwerken** nadat u de filters hebt ingesteld. 
4. Het algemene bewaardiagram toont een overzicht van gebruikersretentie over de geselecteerde periode. 
5. Het raster toont het aantal gebruikers dat wordt bewaard volgens de querybouwer in #2. Elke rij vertegenwoordigt een cohort van gebruikers die elke gebeurtenis hebben uitgevoerd in de weergegeven periode. Elke cel in de rij laat zien hoeveel van dat cohort in een latere periode ten minste één keer zijn teruggekeerd. Sommige gebruikers kunnen terugkeren in meer dan een periode. 
6. De insights-kaarten tonen top vijf initiërende gebeurtenissen en top vijf geretourneerde gebeurtenissen om gebruikers een beter inzicht te geven in hun retentierapport. 

![De muishover van de retentiemuis](./media/usage-retention/hover.png)

Gebruikers kunnen over cellen zweven op het retentie-hulpprogramma om toegang te krijgen tot de analyseknop en tooltips waarin wordt uitgelegd wat de cel betekent. Met de knop Analytics worden gebruikers naar het analytics-hulpprogramma gebracht met een vooraf ingevulde query om gebruikers uit de cel te genereren. 

## <a name="use-business-events-to-track-retention"></a>Zakelijke evenementen gebruiken om retentie bij te houden

Als u de meest nuttige retentieanalyse wilt krijgen, meet u gebeurtenissen die belangrijke bedrijfsactiviteiten vertegenwoordigen. 

Veel gebruikers kunnen bijvoorbeeld een pagina in uw app openen zonder het spel dat wordt weergegeven, te spelen. Het bijhouden van alleen de paginaweergaven zou daarom een onjuiste schatting geven van hoeveel mensen terugkeren om het spel te spelen nadat ze er eerder van hebben genoten. Om een duidelijk beeld te krijgen van terugkerende spelers, moet uw app een aangepaste gebeurtenis verzenden wanneer een gebruiker daadwerkelijk speelt.  

Het is een goede gewoonte om aangepaste gebeurtenissen te coderen die belangrijke zakelijke acties vertegenwoordigen en deze te gebruiken voor uw bewaaranalyse. Om de uitkomst van het spel vast te leggen, moet je een coderegel schrijven om een aangepaste gebeurtenis naar Application Insights te sturen. Als u het schrijft in de webpaginacode of in Node.JS, ziet het er als volgt uit:

```JavaScript
    appinsights.trackEvent("won game");
```

Of in ASP.NET servercode:

```csharp
   telemetry.TrackEvent("won game");
```

[Meer informatie over het schrijven van aangepaste gebeurtenissen](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Volgende stappen
- Als u gebruikservaringen wilt inschakelen, begint u met het verzenden van [aangepaste gebeurtenissen](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) of [paginaweergaven.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)
- Als u al aangepaste gebeurtenissen of paginaweergaven verzendt, raadpleegt u de hulpprogramma's Voor gebruik om te leren hoe gebruikers uw service gebruiken.
    - [Gebruikers, sessies, gebeurtenissen](usage-segmentation.md)
    - [Trechters](usage-funnels.md)
    - [Gebruikersstromen](usage-flows.md)
    - [Werkmappen](../../azure-monitor/app/usage-workbooks.md)
    - [Gebruikerscontext toevoegen](usage-send-user-context.md)


