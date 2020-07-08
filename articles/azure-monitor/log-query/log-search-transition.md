---
title: Gegevens weer geven en analyseren in azure Log Analytics | Microsoft Docs
description: Hulp voor gebruikers van Log Analytics logboek zoeken om te Azure Monitor logboek query-ervaring.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2019
ms.openlocfilehash: 6cc7fc94162ed101d06fbbbc4722867be46987b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77670131"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Overgang van Log Analytics logboek zoeken naar Azure Monitor-logboeken
De zoek opdracht in het logboek in Log Analytics is onlangs vervangen door een nieuwe ervaring voor het analyseren van Azure Monitor Logboeken. De pagina zoeken in Logboeken is momenteel nog steeds toegankelijk via de menu opdracht **Logboeken (klassiek)** op de pagina **log Analytics werk ruimten** in de Azure Portal, maar wordt wel 15 februari 2019. In dit artikel worden de verschillen beschreven tussen de twee ervaringen waarmee u kunt overstappen van zoeken in Logboeken. 

## <a name="filter-results-of-a-query"></a>Resultaten van een query filteren
In zoeken in Logboeken wordt een lijst met filters weer gegeven wanneer de zoek resultaten worden bezorgd. Selecteer een filter en klik op **Toep assen** om de query uit te voeren met het geselecteerde filter.

![Zoek filter voor logboek](media/log-search-transition/filter-log-search.png)

Selecteer *filter (preview)* in azure monitor-Logboeken om filters weer te geven. Klik op het filter pictogram om extra filters weer te geven. Selecteer een filter en klik op **Toep assen & uitvoeren** om de query uit te voeren met het geselecteerde filter.

![Logboeken filteren](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Aangepaste velden extra heren 
In zoeken in Logboeken haalt u [aangepaste velden](../platform/custom-fields.md) uit de lijst weergave, waarbij het menu van een veld de actie _velden uit de tabel_bevat.

![Velden voor het opzoeken van zoeken in Logboeken](media/log-search-transition/extract-fields-log-search.png)

In Azure Monitor logboeken haalt u aangepaste velden uit de tabel weergave. Vouw een record uit door te klikken op de pijl naar links en klik vervolgens op het beletsel teken om de actie voor het _uitpakken van velden_ te openen.

![Velden voor het uitpakken van Logboeken](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Functies en computer groepen
Als u een zoek opdracht wilt opslaan in zoeken in Logboeken, selecteert u **opgeslagen Zoek opdrachten** en voegt u een naam, categorie en query tekst **toe** . Maak een [computer groep](../platform/computer-groups.md) door een functie alias toe te voegen.

![Zoeken in logboeken opslaan](media/log-search-transition/save-search-log-search.png)

Selecteer **Opslaan**om de huidige query in azure monitor-logboeken op te slaan. Wijzig **Opslaan als** in _functie_ en geef een **functie alias** op om een [functie](functions.md)te maken. Selecteer _deze query opslaan als een computer groep_ om de functie-alias voor een [computer groep](../platform/computer-groups.md)te gebruiken.

![Logboek query opslaan](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Opgeslagen query's
In zoeken in Logboeken zijn uw opgeslagen query's beschikbaar via de actie balk item **opgeslagen Zoek opdrachten**. In Azure Monitor-Logboeken opent u opgeslagen query's vanuit [query Explorer](../log-query/get-started-portal.md#save-queries).

![Query Verkenner](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Inzoomen op samenvattings rijen
In zoeken in Logboeken kunt u klikken op een rij in een samenvatte query om een andere query te starten waarin gedetailleerde records in die rij worden weer gegeven.

![Detail analyse logboek zoeken](media/log-search-transition/drilldown-search.png)

In Azure Monitor logboeken moet u de query wijzigen om deze records te retour neren. Vouw een van de rijen in de resultaten uit en klik op de **+** volgende waarde om deze aan de query toe te voegen. Vervolgens voert u een opmerking uit bij de opdracht **samen vatting** . Voer de query opnieuw uit.

![DrillDown van Azure Monitor-logboeken](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Actie ondernemen
In zoeken in Logboeken kunt u [een runbook starten](take-action.md) vanuit een Zoek resultaat door **actie ondernemen**te selecteren.

![Actie ondernemen](media/log-search-transition/take-action-log-search.png)

Maak in Azure Monitor logboeken [een waarschuwing van de logboek query](../platform/alerts-log.md). Een actie groep configureren met een of meer acties die worden uitgevoerd als reactie op de waarschuwing.

![Actiegroep](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de nieuwe [Azure monitor-logboeken](get-started-portal.md).
