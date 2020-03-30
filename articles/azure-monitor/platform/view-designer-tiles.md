---
title: Een naslaggids voor de tegels Designer weergeven in Azure Monitor | Microsoft Documenten
description: Met View Designer in Azure Monitor u aangepaste weergaven maken die worden weergegeven in de Azure-portal en verschillende visualisaties bevatten over gegevens in de werkruimte Log Analytics. Dit artikel is een referentiegids voor de instellingen voor de tegels die beschikbaar zijn in uw aangepaste weergaven.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2018
ms.openlocfilehash: 0320be3a2cfbb96367799577a6e56bcf5da87dcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658503"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Naslaggids voor designertegels weergeven in Azure-monitor
Door View Designer in Azure Monitor te gebruiken, u verschillende aangepaste weergaven maken in de Azure-portal waarmee u gegevens in uw werkruimte Log Analytics visualiseren. Dit artikel is een referentiegids voor de instellingen voor de tegels die beschikbaar zijn in uw aangepaste weergaven.

Zie voor meer informatie over View Designer:

* [Weergaveontwerper:](view-designer.md)biedt een overzicht van View Designer en procedures voor het maken en bewerken van aangepaste weergaven.
* [Verwijzing naar visualisatieonderdelen:](view-designer-parts.md)biedt een referentiehandleiding voor de instellingen voor de visualisatieonderdelen die beschikbaar zijn in uw aangepaste weergaven.


De beschikbare View Designer-tegels worden beschreven in de volgende tabel:  

| Tegel | Beschrijving |
|:--- |:--- |
| [Nummer](#number-tile) |Het aantal records uit een query. |
| [Twee getallen](#two-numbers-tile) |Het aantal records van twee verschillende query's. |
| [Ringdiagram](#donut-tile) | Een grafiek die is gebaseerd op een query, met een overzichtswaarde in het midden. |
| Lijndiagram en -uitroep | Een lijndiagram dat is gebaseerd op een query en een afroep met een overzichtswaarde. |
| [Lijndiagram](#line-chart-tile) |Een lijndiagram dat is gebaseerd op een query. |
| [Twee tijdlijnen](#two-timelines-tile) | Een kolomdiagram met twee reeksen, elk op basis van een afzonderlijke query. |

In de volgende secties worden de tegeltypen en hun eigenschappen in detail beschreven.

> [!NOTE]
> Tegels in weergaven zijn gebaseerd op [logboekquery's](../log-query/log-query-overview.md) in uw werkruimte Log Analytics. Ze ondersteunen momenteel geen [kruisbronquery's](../log-query/cross-workspace-query.md) om gegevens uit Application Insights op te halen.

## <a name="number-tile"></a>Tegel Getal
Op de tegel **Getal** wordt zowel het aantal records van een logboekquery als een label weergegeven.

![Tegel Getal](media/view-designer-tiles/tile-number.png)

| Instelling | Beschrijving |
|:--- |:--- |
| Name |De tekst die boven aan de tegel wordt weergegeven. |
| Beschrijving |De tekst die wordt weergegeven onder de tegelnaam. |
| **Tegel** | |
| Legenda |De tekst die onder de waarde wordt weergegeven. |
| Query’s uitvoeren |De query die wordt uitgevoerd. Het aantal records dat door de query wordt geretourneerd, wordt weergegeven. |
| **Geavanceerde** |**> Verificatie van de gegevensstroom** |
| Ingeschakeld |Selecteer deze koppeling als verificatie van de gegevensstroom moet zijn ingeschakeld voor de tegel. Deze benadering biedt een alternatief bericht als gegevens niet beschikbaar zijn. U gebruikt de aanpak gewoonlijk om een bericht te geven tijdens de tijdelijke periode waarin de weergave is geïnstalleerd en de gegevens beschikbaar komen. |
| Query’s uitvoeren |De query die wordt uitgevoerd om te bepalen of gegevens beschikbaar zijn voor de weergave. Als de query geen resultaten oplevert, wordt een bericht weergegeven in plaats van de waarde van de hoofdquery. |
| Bericht |Het bericht dat wordt weergegeven als de verificatiequery voor gegevensstromen geen gegevens retourneert. Als u geen bericht geeft, wordt een statusbericht *uitvoeren* weergegeven. |


## <a name="two-numbers-tile"></a>Tegel Twee getallen
Met deze tegel wordt het aantal records van twee verschillende logboekquery's en een label voor elk weergegeven.

![Tegel Twee getallen](media/view-designer-tiles/tile-two-numbers.png)

| Instelling | Beschrijving |
|:--- |:--- |
| Name |De tekst die boven aan de tegel wordt weergegeven. |
| Beschrijving |De tekst die wordt weergegeven onder de tegelnaam. |
| **Eerste tegel** | |
| Legenda |De tekst die onder de waarde wordt weergegeven. |
| Query’s uitvoeren |De query die wordt uitgevoerd. Het aantal records dat door de query wordt geretourneerd, wordt weergegeven. |
| **Tweede tegel** | |
| Legenda |De tekst die onder de waarde wordt weergegeven. |
| Query’s uitvoeren |De query die wordt uitgevoerd. Het aantal records dat door de query wordt geretourneerd, wordt weergegeven. |
| **Geavanceerde** |**> Verificatie van de gegevensstroom** |
| Ingeschakeld |Selecteer deze koppeling als verificatie van de gegevensstroom moet zijn ingeschakeld voor de tegel. Deze benadering biedt een alternatief bericht als gegevens niet beschikbaar zijn. U gebruikt de aanpak gewoonlijk om een bericht te geven tijdens de tijdelijke periode waarin de weergave is geïnstalleerd en de gegevens beschikbaar komen. |
| Query’s uitvoeren |De query die wordt uitgevoerd om te bepalen of gegevens beschikbaar zijn voor de weergave. Als de query geen resultaten oplevert, wordt een bericht weergegeven in plaats van de waarde van de hoofdquery. |
| Bericht |Het bericht dat wordt weergegeven als de verificatiequery voor gegevensstromen geen gegevens retourneert. Als u geen bericht geeft, wordt een statusbericht *uitvoeren* weergegeven. |


## <a name="donut-tile"></a>Donuttegel
De **tegel Donut** geeft één getal weer dat een waardekolom in een logboekquery samenvat. De donut geeft op grafische plaats de resultaten van de top drie records weer.

![Donuttegel](media/view-designer-tiles/tile-donut.png)

| Instelling | Beschrijving |
|:--- |:--- |
| Name |De tekst die boven aan de tegel wordt weergegeven. |
| Beschrijving |De tekst die wordt weergegeven onder de tegelnaam. |
| **Ringdiagram** | |
| Query’s uitvoeren |De query die wordt uitgevoerd voor de donut. De eerste eigenschap is een tekstwaarde en de tweede eigenschap is een numerieke waarde. Deze query gebruikt gewoonlijk het *zoekwoord meten* om de resultaten samen te vatten. |
| **Ringdiagram** |**> Centrum** |
| Tekst |De tekst die wordt weergegeven onder de waarde in de donut. |
| Bewerking |De bewerking die wordt uitgevoerd op de eigenschap value om deze samen te vatten als één waarde.<ul><li>Som: Voeg de waarden van alle records toe aan de eigenschapswaarde.</li><li>Percentage: Percentage van de samengevatte waarden uit records met de eigenschapswaarde in vergelijking met de samengevatte waarden van alle records.</li></ul> |
| Resultaatwaarden die worden gebruikt in de centerbewerking |Selecteer optioneel het plusteken (+) om een of meer waarden toe te voegen. De resultaten van de query zijn beperkt tot records met de eigenschapswaarden die u opgeeft. Als er geen waarden worden toegevoegd, worden alle records in de query opgenomen. |
| **Ringdiagram** |**> Extra opties** |
| Kleuren |De kleur die wordt weergegeven voor elk van de drie bovenste eigenschappen. Als u alternatieve kleuren voor specifieke eigenschapswaarden wilt opgeven, gebruikt u *Geavanceerde kleurtoewijzing*. |
| Geavanceerde kleurtoewijzing |Hiermee geeft u een kleur weer die specifieke eigenschapswaarden vertegenwoordigt. Als de opgegeven waarde in de top drie staat, wordt de alternatieve kleur weergegeven in plaats van de standaardkleur. Als de eigenschap niet in de top drie staat, wordt de kleur niet weergegeven. |
| **Geavanceerde** |**> Verificatie van de gegevensstroom** |
| Ingeschakeld |Selecteer deze koppeling als verificatie van de gegevensstroom moet zijn ingeschakeld voor de tegel. Deze benadering biedt een alternatief bericht als gegevens niet beschikbaar zijn. U gebruikt de aanpak gewoonlijk om een bericht te geven tijdens de tijdelijke periode waarin de weergave is geïnstalleerd en de gegevens beschikbaar komen. |
| Query’s uitvoeren |De query die wordt uitgevoerd om te bepalen of gegevens beschikbaar zijn voor de weergave. Als de query geen resultaten oplevert, wordt een bericht weergegeven in plaats van de waarde van de hoofdquery. |
| Bericht |Het bericht dat wordt weergegeven als de verificatiequery voor gegevensstromen geen gegevens retourneert. Als u geen bericht geeft, wordt een statusbericht *uitvoeren* weergegeven. |


## <a name="line-chart-tile"></a>Lijndiagramtegel
Deze tegel is een lijndiagram dat meerdere reeksen uit een logboekquery in de loop van de tijd weergeeft. 

![Lijndiagram en uitroepquet](media/view-designer-tiles/tile-line-chart.png)

| Instelling | Beschrijving |
|:--- |:--- |
| Name |De tekst die boven aan de tegel wordt weergegeven. |
| Beschrijving |De tekst die wordt weergegeven onder de tegelnaam. |
| **Lijndiagram** | |
| Query’s uitvoeren |De query die wordt uitgevoerd voor het lijndiagram. De eerste eigenschap is een tekstwaarde en de tweede eigenschap is een numerieke waarde. Deze query gebruikt gewoonlijk het *zoekwoord meten* om de resultaten samen te vatten. Als de query het *intervaltrefwoord* gebruikt, wordt in de x-as dit tijdsinterval gebruikt. Als de query het *intervaltrefwoord* niet gebruikt, gebruikt de x-as uurintervallen. |
| **Lijndiagram** |**> Y-as** |
| Logaritmische schaal gebruiken |Selecteer deze koppeling om een logaritmische schaal voor de y-as te gebruiken. |
| Eenheden |Geef de eenheden op voor de waarden die door de query worden geretourneerd. Deze informatie wordt gebruikt om labels in de grafiek weer te geven die de waardetypen aangeven en optioneel voor het converteren van de waarden. Het **eenheidstype** geeft de categorie van de eenheid op en definieert de huidige **eenheidstypewaarden** die beschikbaar zijn. Als u een waarde selecteert in **Converteren naar,** worden de numerieke waarden geconverteerd van het type **Huidige eenheid** naar het type **Converteren naar** tekst. |
| Aangepast label |De tekst die wordt weergegeven voor de y-as naast het label voor het *type Eenheid.* Als er geen label is opgegeven, wordt alleen het type *Eenheid* weergegeven. |
| **Geavanceerde** |**> Verificatie van de gegevensstroom** |
| Ingeschakeld |Selecteer deze koppeling als verificatie van de gegevensstroom moet zijn ingeschakeld voor de tegel. Deze benadering biedt een alternatief bericht als gegevens niet beschikbaar zijn. U gebruikt de aanpak gewoonlijk om een bericht te geven tijdens de tijdelijke periode waarin de weergave is geïnstalleerd en de gegevens beschikbaar komen. |
| Query’s uitvoeren |De query die wordt uitgevoerd om te bepalen of gegevens beschikbaar zijn voor de weergave. Als de query geen resultaten oplevert, wordt een bericht weergegeven in plaats van de waarde van de hoofdquery. |
| Bericht |Het bericht dat wordt weergegeven als de verificatiequery voor gegevensstromen geen gegevens retourneert. Als u geen bericht geeft, wordt een statusbericht *uitvoeren* weergegeven. |


## <a name="line-chart-and-callout-tile"></a>Lijndiagram en uitroepquet
Deze tegel heeft zowel een lijndiagram dat meerdere reeksen uit een logboekquery in de loop van de tijd weergeeft als een uitroep met een samengevatte waarde. 

![Lijndiagram en uitroepquet](media/view-designer-tiles/tile-line-chart-callout.png)

| Instelling | Beschrijving |
|:--- |:--- |
| Name |De tekst die boven aan de tegel wordt weergegeven. |
| Beschrijving |De tekst die wordt weergegeven onder de tegelnaam. |
| **Lijndiagram** | |
| Query’s uitvoeren |De query die wordt uitgevoerd voor het lijndiagram. De eerste eigenschap is een tekstwaarde en de tweede eigenschap is een numerieke waarde. Deze query gebruikt gewoonlijk het *zoekwoord meten* om de resultaten samen te vatten. Als de query het *intervaltrefwoord* gebruikt, wordt in de x-as dit tijdsinterval gebruikt. Als de query het *intervaltrefwoord* niet gebruikt, gebruikt de x-as uurintervallen. |
| **Lijndiagram** |**> Roepout** |
| Titel van het uitroepuroep | De tekst die boven de afroepwaarde wordt weergegeven. |
| De naam van de reeks |De waarde van de eigenschap serie die moet worden gebruikt als de afroepwaarde. Als er geen reeks wordt opgegeven, worden alle records uit de query gebruikt. |
| Bewerking |De bewerking die wordt uitgevoerd op de eigenschap value om deze samen te vatten als één waarde voor het uitroep.<ul><li>Gemiddelde: het gemiddelde van de waarden uit alle records.</li><li>Aantal: het aantal records dat door de query wordt geretourneerd.</li><li>Laatste voorbeeld: de waarde van het laatste interval dat in de grafiek is opgenomen.</li><li>Max: de maximale waarde van de intervallen die in de grafiek zijn opgenomen.</li><li>Min: De minimumwaarde van de intervallen die in de grafiek zijn opgenomen.</li><li>Som: de som van de waarden uit alle records.</li></ul> |
| **Lijndiagram** |**> Y-as** |
| Logaritmische schaal gebruiken |Selecteer deze koppeling om een logaritmische schaal voor de y-as te gebruiken. |
| Eenheden |Geef de eenheden op voor de waarden die door de query moeten worden geretourneerd. Deze informatie wordt gebruikt om grafieklabels weer te geven die de waardetypen aangeven en, optioneel, de waarden om te zetten. Het *type eenheid* geeft de categorie van de eenheid op en definieert de beschikbare *typewaarden huidige eenheid.* Als u een waarde selecteert in *Converteren naar*, worden de numerieke waarden geconverteerd van het type *Huidige eenheid* naar het type *Converteren naar* tekst. |
| Aangepast label |De tekst die wordt weergegeven voor de y-as naast het label voor het *type Eenheid.* Als er geen label is opgegeven, wordt alleen het type *Eenheid* weergegeven. |
| **Geavanceerde** |**> Verificatie van de gegevensstroom** |
| Ingeschakeld |Selecteer deze koppeling als verificatie van de gegevensstroom moet zijn ingeschakeld voor de tegel. Deze benadering biedt een alternatief bericht als gegevens niet beschikbaar zijn. U gebruikt de aanpak gewoonlijk om een bericht te geven tijdens de tijdelijke periode waarin de weergave is geïnstalleerd en de gegevens beschikbaar komen. |
| Query’s uitvoeren |De query die wordt uitgevoerd om te bepalen of gegevens beschikbaar zijn voor de weergave. Als de query geen resultaten oplevert, wordt een bericht weergegeven in plaats van de waarde van de hoofdquery. |
| Bericht |Het bericht dat wordt weergegeven als de verificatiequery voor gegevensstromen geen gegevens retourneert. Als u geen bericht geeft, wordt een statusbericht *uitvoeren* weergegeven. |


## <a name="two-timelines-tile"></a>Tegel Twee tijdlijnen
De tegel **Twee tijdlijnen** geeft de resultaten van twee logboekquery's in de loop van de tijd weer als kolomdiagrammen. Voor elke serie wordt een uitroep weergegeven. 

![Tegel Twee tijdlijnen](media/view-designer-tiles/tile-two-timelines.png)

| Instelling | Beschrijving |
|:--- |:--- |
| Name |De tekst die boven aan de tegel wordt weergegeven. |
| Beschrijving |De tekst die wordt weergegeven onder de tegelnaam. |
| Eerste grafiek | |
| Legenda |De tekst die wordt weergegeven onder de oproep voor de eerste serie. |
| Kleur |De kleur die wordt gebruikt voor de kolommen in de eerste reeks. |
| Grafiekquery |De query die wordt uitgevoerd voor de eerste reeks. Het aantal records over elk tijdsinterval wordt weergegeven door de grafiekkolommen. |
| Bewerking |De bewerking die wordt uitgevoerd op de eigenschap value om deze samen te vatten als één waarde voor het uitroep.<ul><li>Gemiddelde: het gemiddelde van de waarden uit alle records.</li><li>Aantal: het aantal records dat door de query wordt geretourneerd.</li><li>Laatste voorbeeld: de waarde van het laatste interval dat in de grafiek is opgenomen.</li><li>Max: de maximale waarde van de intervallen die in de grafiek zijn opgenomen.</li></ul> |
| **Tweede grafiek** | |
| Legenda |De tekst die wordt weergegeven onder de oproep voor de tweede reeks. |
| Kleur |De kleur die wordt gebruikt voor de kolommen in de tweede reeks. |
| Grafiekquery |De query die wordt uitgevoerd voor de tweede reeks. Het aantal records over elk tijdsinterval wordt weergegeven door de grafiekkolommen. |
| Bewerking |De bewerking die wordt uitgevoerd op de eigenschap value om deze samen te vatten als één waarde voor het uitroep.<ul><li>Gemiddelde: het gemiddelde van de waarden uit alle records.</li><li>Aantal: het aantal records dat door de query wordt geretourneerd.</li><li>Laatste voorbeeld: de waarde van het laatste interval dat in de grafiek is opgenomen.</li><li>Max: de maximale waarde van de intervallen die in de grafiek zijn opgenomen. |
| **Geavanceerde** |**> Verificatie van de gegevensstroom** |
| Ingeschakeld |Selecteer deze koppeling als verificatie van de gegevensstroom moet zijn ingeschakeld voor de tegel. Deze benadering biedt een alternatief bericht als gegevens niet beschikbaar zijn. U gebruikt de aanpak gewoonlijk om een bericht te geven tijdens de tijdelijke periode waarin de weergave is geïnstalleerd en de gegevens beschikbaar komen. |
| Query’s uitvoeren |De query die wordt uitgevoerd om te bepalen of gegevens beschikbaar zijn voor de weergave. Als de query geen resultaten oplevert, wordt een bericht weergegeven in plaats van de waarde van de hoofdquery. |
| Bericht |Het bericht dat wordt weergegeven als de verificatiequery voor gegevensstromen geen gegevens retourneert. Als u geen bericht geeft, wordt een statusbericht *uitvoeren* weergegeven. |


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [logboekquery's](../log-query/log-query-overview.md) om de query's in tegels te ondersteunen.
* [Visualisatieonderdelen](view-designer-parts.md) toevoegen aan uw aangepaste weergave.
