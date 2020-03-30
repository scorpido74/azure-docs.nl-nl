---
title: Gegevens weergeven en analyseren in Azure Log Analytics | Microsoft Documenten
description: Hulp voor gebruikers van log Analytics-logboekzoekopdrachten naar azure monitor-logboekquery-ervaring.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2019
ms.openlocfilehash: 6cc7fc94162ed101d06fbbbc4722867be46987b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670131"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Overgang van logboekanalyselogboekzoeken naar Azure Monitor-logboeken
De logboekzoekopdracht in Log Analytics is onlangs vervangen door een nieuwe ervaring voor het analyseren van Azure Monitor-logboeken. De zoekpagina Log is momenteel nog steeds toegankelijk via het **menu-item Logboeken (klassiek)** op de pagina **Log Analytics-werkruimten** in de Azure-portal, maar wordt verwijderd op 15 februari 2019. In dit artikel worden verschillen beschreven tussen de twee ervaringen om u te helpen de overgang van logboekzoeken. 

## <a name="filter-results-of-a-query"></a>Resultaten van een query filteren
In Logboekzoeken wordt een lijst met filters weergegeven wanneer zoekresultaten worden geleverd. Selecteer een filter en klik op **Toepassen** om de query uit te voeren met het geselecteerde filter.

![Log Search-filter](media/log-search-transition/filter-log-search.png)

Selecteer *filter (voorbeeld)* in Azure Monitor-logboeken om filters weer te geven. Klik op het filterpictogram om bijtellingsfilters weer te geven. Selecteer een filter en klik op **Toepassen & uitvoeren** om de query uit te voeren met het geselecteerde filter.

![Logboekenfilter](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Aangepaste velden extraheren 
In Logboekzoeken haalt u [aangepaste velden](../platform/custom-fields.md) uit de lijstweergave, waarin het menu van een veld de _actievelden Uit tabel bevat._

![Uittreksels van logboekzoeken](media/log-search-transition/extract-fields-log-search.png)

In Azure Monitor-logboeken haalt u aangepaste velden uit de tabelweergave. Vouw een record uit door op de pijl naar links te klikken en vervolgens op de ellips te klikken om toegang te krijgen tot de actie _Velden extraheren._

![Uitwinvelden logboeken](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Functies en computergroepen
Als u een zoekopdracht wilt opslaan in Zoeken bij Logboek, selecteert u **Opgeslagen zoekopdrachten** en **Voegt u toe** om een naam, categorie en querytekst op te geven. Maak een [computergroep](../platform/computer-groups.md) door een functiealias toe te voegen.

![Logboekzoeken opslaan](media/log-search-transition/save-search-log-search.png)

Als u de huidige query wilt opslaan in Azure Monitor-logboeken, selecteert u **Opslaan**. Opslaan **wijzigen als** _functie_ en een **functiealias** bieden om een [functie](functions.md)te maken. Selecteer _Deze query opslaan als computergroep_ om de functiealias voor een [computergroep](../platform/computer-groups.md)te gebruiken .

![Logboekquery opslaan](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Opgeslagen query's
In Logboekzoeken zijn de opgeslagen query's beschikbaar via de zoekopdrachten op de actiebalk **waar hebt opgemaakt.** In Azure Monitor-logboeken u in Azure Monitor-logboeken opgeslagen query's openen vanuit [Query Explorer.](../log-query/get-started-portal.md#save-queries)

![Queryverkenner](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Inzoomen op samengevatte rijen
Klik in Logboekzoeken op een rij in een samengevatte query om een andere query te starten met gedetailleerde records in die rij.

![Drilldown voor zoeken aanmelden](media/log-search-transition/drilldown-search.png)

In Azure Monitor-logboeken moet u de query wijzigen om deze records terug te sturen. Vouw een van de rijen in **+** de resultaten uit en klik op het volgende naast de waarde om deze aan de query toe te voegen. Geef vervolgens commentaar op de opdracht **Samenvatten** en voer de query opnieuw uit.

![Azure Monitor-logboeken drilldown](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Actie ondernemen
In Logboekzoeken u [een runbook starten](take-action.md) vanuit een zoekresultaat door **Actie uitvoeren te**selecteren.

![Actie ondernemen](media/log-search-transition/take-action-log-search.png)

Maak in Azure Monitor-logboeken [een waarschuwing van de logboekquery](../platform/alerts-log.md). Configureer een actiegroep met een of meer acties die worden uitgevoerd in reactie op de waarschuwing.

![Actiegroep](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de nieuwe [Azure Monitor-logboekenervaring](get-started-portal.md).
