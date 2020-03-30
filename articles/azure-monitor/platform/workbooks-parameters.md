---
title: Azure Monitor-werkmappen die parameters maken
description: Complexe rapportage vereenvoudigen met vooraf gebouwde en aangepaste geparametereerde werkmappen
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 3e7dda85f1f890d5ae0eb4722c3e028b373fdcab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658214"
---
# <a name="workbook-parameters"></a>Werkmapparameters

Met parameters kunnen auteurs van werkmapinvoer van de consumenten verzamelen en ernaar verwijzen in andere delen van de werkmap , meestal om de resultatenset te bereiken of de juiste visual in te stellen. Het is een belangrijke mogelijkheid waarmee auteurs interactieve rapporten en ervaringen kunnen maken. 

Met werkmappen u bepalen hoe uw parameterbesturingselementen aan consumenten worden gepresenteerd : tekstvak versus drop down, single- versus multi-select, waarden uit tekst, JSON, KQL of Azure Resource Graph, enz.  

Ondersteunde parametertypen zijn:
* [Tijd](workbooks-time.md) - stelt een gebruiker in staat om te kiezen uit vooraf ingevulde tijdsbereiken of een aangepast bereik te selecteren
* [Vervolgkeuzelijst](workbooks-dropdowns.md) - stelt een gebruiker in staat om te kiezen uit een waarde of set waarden
* [Tekst](workbooks-text.md) - stelt een gebruiker in staat om willekeurige tekst in te voeren
* [Resource](workbooks-resources.md) - stelt een gebruiker in staat om een of meer Azure-bronnen te selecteren
* [Abonnement](workbooks-resources.md) - stelt een gebruiker in staat een of meer Azure-abonnementsbronnen te selecteren
* Resourcetype - stelt een gebruiker in staat een of meer waarden voor Azure-bronnentype te selecteren
* Locatie - stelt een gebruiker in staat een of meer Azure-locatiewaarden te selecteren

Naar deze parameterwaarden kan in andere delen van werkmappen worden verwezen, hetzij via bindingen of waardeuitbreidingen.

## <a name="creating-a-parameter"></a>Een parameter maken
1. Begin met een lege werkmap in de bewerkingsmodus.
2. Kies _Parameters toevoegen_ in de koppelingen in de werkmap.
3. Klik op de blauwe _knop Parameter toevoegen._
4. Voer in het nieuwe parametervenster dat wordt weergegeven:
    1. Parameternaam: `TimeRange` *(merk op dat __parameternamen__ **geen** spaties of speciale tekens kunnen bevatten)*
    2. Weergavenaam: `Time Range` * __(weergavenamen__ kunnen echter spaties, speciale tekens, emoji's, enz.)*  
    2. Parametertype:`Time range picker`
    3. Vereist:`checked`
    4. Beschikbare tijdsbereiken: Laatste uur, Laatste 12 uur, Laatste 24 uur, Laatste 48 uur, Laatste 3 dagen, Laatste 7 dagen en Aangepaste tijdbereikselectie toestaan
5. Kies 'Opslaan' op de werkbalk om de parameter te maken.

   ![Afbeelding met de creatie van een parameter voor tijdsbereik](./media/workbooks-parameters/time-settings.png)

Dit is hoe de werkmap eruit zal zien in read-mode, in de "Pillen" stijl.

   ![Afbeelding met een parameter voor tijdbereik in de leesmodus](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>Een parameter verwijzen
### <a name="via-bindings"></a>Via Bindings
1. Voeg een querybesturingselement toe aan de werkmap en selecteer een Application Insights-bron.
2. Open de vervolgkeuzelijst _Tijdbereik_ en selecteer de `Time Range` optie in de sectie Parameters onderaan.
3. Hiermee wordt de parameter tijdbereik gekoppeld aan het tijdsbereik van de grafiek. De tijdsomvang van de voorbeeldquery is nu 24 uur.
4. Query uitvoeren om de resultaten te bekijken

    ![Afbeelding met een parameter voor tijdsbereik waarnaar wordt verwezen via bindingen](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>In KQL
1. Voeg een querybesturingselement toe aan de werkmap en selecteer een Application Insights-bron.
2. Voer in de KQL een tijdbereikfilter in met de parameter:`| where timestamp {TimeRange}`
3. Dit breidt op queryevaluatietijd uit tot `| where timestamp > ago(1d)`, dat is de tijdbereikwaarde van de parameter.
4. Query uitvoeren om de resultaten te bekijken

    ![Afbeelding met een tijdsbereik waarnaar wordt verwezen in KQL](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>In tekst 
1. Voeg een tekstbesturingselement toe aan de werkmap.
2. Voer in de afwaardering`The chosen time range is {TimeRange:label}`
3. _Gereed bewerken kiezen_
4. Het tekstbesturingselement toont tekst: _Het gekozen tijdsbereik is Laatste 24 uur_

## <a name="parameter-options"></a>Parameteropties
In de sectie `label` In _Tekst_ gebruikte de parameter in plaats van de waarde ervan. Parameters leggen verschillende dergelijke opties bloot, afhankelijk van het type - bijvoorbeeld tijdbereikkiezer staan waarde, label, query, begin, einde en korrel toe.

Gebruik `Previews` de sectie van het deelvenster _Parameter bewerken_ om de uitbreidingsopties voor uw parameter te bekijken:

![Afbeelding met parameteropties voor tijdbereik](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>Volgende stappen

* [Ga aan de slag met](workbooks-visualizations.md) het leren van meer over werkmappen met veel uitgebreide visualisatiesopties.
* [Beheer](workbooks-access-control.md) en deel de toegang tot uw werkmapbronnen.
