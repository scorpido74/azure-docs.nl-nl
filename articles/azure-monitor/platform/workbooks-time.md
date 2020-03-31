---
title: Tijdparameters voor werkmappen in Azure Monitor
description: Complexe rapportage vereenvoudigen met vooraf gebouwde en aangepaste geparametereerde werkmappen
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 380b8a7ce286ab06b6935bf63bf3a0e82f371c2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658010"
---
# <a name="workbook-time-parameters"></a>Tijdsparameters voor werkmap

Met tijdparameters kunnen gebruikers de tijdcontext van de analyse instellen en wordt door bijna alle rapporten gebruikt. Het is relatief eenvoudig in te stellen en te gebruiken - zodat auteurs de tijdsbereiken kunnen opgeven die in de vervolgkeuzelijst moeten worden weergegeven, inclusief de optie voor aangepaste tijdbereiken. 

## <a name="creating-a-time-parameter"></a>Een tijdparameter maken
1. Begin met een lege werkmap in de bewerkingsmodus.
2. Kies _Parameters toevoegen_ in de koppelingen in de werkmap.
3. Klik op de blauwe _knop Parameter toevoegen._
4. Voer in het nieuwe parametervenster dat wordt weergegeven:
    1. Parameternaam:`TimeRange`
    2. Parametertype:`Time range picker`
    3. Vereist:`checked`
    4. Beschikbare tijdsbereiken: Laatste uur, Laatste 12 uur, Laatste 24 uur, Laatste 48 uur, Laatste 3 dagen, Laatste 7 dagen en Aangepaste tijdbereikselectie toestaan
5. Kies 'Opslaan' op de werkbalk om de parameter te maken.

    ![Afbeelding met de creatie van een parameter voor tijdsbereik](./media/workbooks-time/time-settings.png)

Zo ziet de werkmap eruit in de leesmodus.

![Afbeelding met een parameter voor tijdbereik in de leesmodus](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>Een tijdparameter verwijzen
### <a name="via-bindings"></a>Via Bindings
1. Voeg een querybesturingselement toe aan de werkmap en selecteer een Application Insights-bron.
2. De meeste werkmapbesturingselementen ondersteunen een _scopekiezer voor tijdbereik._ Open de vervolgkeuzelijst _Tijdbereik_ en selecteer de `{TimeRange}` groep in de groep tijdrangparameters onderaan.
3. Hiermee wordt de parameter tijdbereik gekoppeld aan het tijdsbereik van de grafiek. De tijdsomvang van de voorbeeldquery is nu 24 uur.
4. Query uitvoeren om de resultaten te bekijken

    ![Afbeelding met een parameter voor tijdsbereik waarnaar wordt verwezen via bindingen](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>In KQL
1. Voeg een querybesturingselement toe aan de werkmap en selecteer een Application Insights-bron.
2. Voer in de KQL een tijdbereikfilter in met de parameter:`| where timestamp {TimeRange}`
3. Dit breidt op queryevaluatietijd uit tot `| where timestamp > ago(1d)`, dat is de tijdbereikwaarde van de parameter.
4. Query uitvoeren om de resultaten te bekijken

    ![Afbeelding met een tijdsbereik waarnaar wordt verwezen in KQL](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>In tekst 
1. Voeg een tekstbesturingselement toe aan de werkmap.
2. Voer in de afwaardering`The chosen time range is {TimeRange:label}`
3. _Gereed bewerken kiezen_
4. Het tekstbesturingselement toont tekst: _Het gekozen tijdsbereik is Laatste 24 uur_

## <a name="time-parameter-options"></a>Tijdparameteropties
| Parameter | Uitleg | Voorbeeld |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | Tijdbereiklabel | Laatste 24 uur |
| `{TimeRange:label}` | Tijdbereiklabel | Laatste 24 uur |
| `{TimeRange:value}` | Waarde van tijdbereik | > geleden(1d) |
| `{TimeRange:query}` | Query voor tijdbereik | > geleden(1d) |
| `{TimeRange:start}` | Begintijd tijdsbereik | 3/20/2019 16:18 |
| `{TimeRange:end}` | Eindtijd tijdsafstand | 3/21/2019 16:18 |
| `{TimeRange:grain}` | De korrel van het tijdsbereik | 30 m |


### <a name="using-parameter-options-in-a-query"></a>Parameteropties gebruiken in een query
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>Volgende stappen

* [Ga aan de slag met](workbooks-visualizations.md) het leren van meer over werkmappen met veel uitgebreide visualisatiesopties.
* [Beheer](workbooks-access-control.md) en deel de toegang tot uw werkmapbronnen.
