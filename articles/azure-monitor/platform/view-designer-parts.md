---
title: Een naslaggids voor de onderdelen Designer weergeven in Azure Monitor | Microsoft Documenten
description: Met View Designer in Azure Monitor u aangepaste weergaven maken die worden weergegeven in de Azure-portal en verschillende visualisaties bevatten over gegevens in de werkruimte Log Analytics. Dit artikel is een referentiehandleiding voor de instellingen voor de visualisatieonderdelen die beschikbaar zijn in uw aangepaste weergaven.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2018
ms.openlocfilehash: 7dc4164cac1998a02ae62d7682f9630aa4faa619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658554"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-azure-monitor"></a>Naslaggids voor visualisatieonderdelen van Designer weergeven in Azure Monitor
Door View Designer in Azure Monitor te gebruiken, u verschillende aangepaste weergaven maken in de Azure-portal waarmee u gegevens in uw werkruimte Log Analytics visualiseren. Dit artikel is een referentiehandleiding voor de instellingen voor de visualisatieonderdelen die beschikbaar zijn in uw aangepaste weergaven.

Zie voor meer informatie over View Designer:

* [Weergaveontwerper:](view-designer.md)biedt een overzicht van View Designer en procedures voor het maken en bewerken van aangepaste weergaven.
* [Tegelverwijzing:](view-designer-tiles.md)geeft een verwijzing naar de instellingen voor elke beschikbare tegel in uw aangepaste weergaven.


De beschikbare tegeltypen Van Ontwerper worden beschreven in de volgende tabel:

| Weergavetype | Beschrijving |
|:--- |:--- |
| [Lijst met query's](#list-of-queries-part) |Hiermee wordt een lijst met logboekquery's weergegeven. U elke query selecteren om de resultaten weer te geven. |
| [Nummer en lijst](#number-and-list-part) |De koptekst geeft één getal weer dat een aantal records uit een logboekquery weergeeft. De lijst geeft de top tien resultaten van een query weer, met een grafiek die de relatieve waarde van een numerieke kolom of de verandering ervan in de tijd aangeeft. |
| [Twee nummers en lijst](#two-numbers-and-list-part) |De koptekst geeft twee getallen weer die het aantal records van afzonderlijke logboekquery's weergeven. De lijst geeft de top tien resultaten van een query weer, met een grafiek die de relatieve waarde van een numerieke kolom of de verandering ervan in de tijd aangeeft. |
| [Donut en lijst](#donut-and-list-part) |De koptekst geeft één getal weer dat een waardekolom in een logboekquery samenvat. De donut geeft op grafische plaats de resultaten van de top drie records weer. |
| [Twee tijdlijnen en lijst](#two-timelines-and-list-part) |De kopgeeft de resultaten van twee logboekquery's in de loop van de tijd weer als kolomdiagrammen, met een uitroepdie een enkel getal weergeeft dat een waardekolom in een logboekquery samenvat. De lijst geeft de top tien resultaten van een query weer, met een grafiek die de relatieve waarde van een numerieke kolom of de verandering ervan in de tijd aangeeft. |
| [Informatie](#information-part) |De koptekst geeft statische tekst en een optionele koppeling weer. In de lijst worden een of meer items met een statische titel en tekst weergegeven. |
| [Lijndiagram, oproep- en lijst](#line-chart-callout-and-list-part) |De koptekst geeft een lijndiagram weer, met meerdere reeksen uit een logboekquery in de loop van de tijd en een uitroep met een samengevatte waarde. De lijst geeft de top tien resultaten van een query weer, met een grafiek die de relatieve waarde van een numerieke kolom of de verandering ervan in de tijd aangeeft. |
| [Lijndiagram en lijst](#line-chart-and-list-part) |De koptekst geeft een lijndiagram weer, met meerdere reeksen uit een logboekquery in de loop van de tijd. De lijst geeft de top tien resultaten van een query weer, met een grafiek die de relatieve waarde van een numerieke kolom of de verandering ervan in de tijd aangeeft. |
| [Stapel lijndiagrammenonderdeel](#stack-of-line-charts-part) |Hiermee worden drie afzonderlijke lijndiagrammen weergegeven, met meerdere reeksen uit een logboekquery in de loop van de tijd. |

In de volgende secties worden de tegeltypen en hun eigenschappen in detail beschreven.

> [!NOTE]
> Onderdelen in weergaven zijn gebaseerd op [logboekquery's](../log-query/log-query-overview.md) in uw werkruimte Log Analytics. Ze ondersteunen momenteel geen [kruisbronquery's](../log-query/cross-workspace-query.md) om gegevens uit Application Insights op te halen.

## <a name="list-of-queries-part"></a>Lijst met query's
In de lijst met query's wordt een lijst met logboekquery's weergegeven. U elke query selecteren om de resultaten weer te geven. De weergave bevat standaard één query en u **+ Query** selecteren om extra query's toe te voegen.

![Lijst met query's](media/view-designer-parts/view-list-queries.png)

| Instelling | Beschrijving |
|:--- |:--- |
| **Algemeen** | |
| Titel |De tekst die boven aan de weergave wordt weergegeven. |
| Nieuwe groep |Selecteer deze koppeling om een nieuwe groep in de weergave te maken, te beginnen bij de huidige weergave. |
| Vooraf geselecteerde filters |Een door komma's afgebakende lijst met eigenschappen die u in het linkerfiltervenster moet opnemen wanneer u een query selecteert. |
| Rendermodus |De eerste weergave die wordt weergegeven wanneer de query is geselecteerd. U alle beschikbare weergaven selecteren nadat ze de query hebben geopend. |
| **Query's** | |
| Zoekquery |De query die moet worden uitgevoerd. |
| Beschrijvende naam | De beschrijvende naam die wordt weergegeven. |

## <a name="number-and-list-part"></a>Nummer en lijstonderdeel
De koptekst geeft één getal weer dat een aantal records uit een logboekquery weergeeft. De lijst geeft de top tien resultaten van een query weer, met een grafiek die de relatieve waarde van een numerieke kolom of de verandering ervan in de tijd aangeeft.

![Lijst met query's](media/view-designer-parts/view-number-list.png)

| Instelling | Beschrijving |
|:--- |:--- |
| **Algemeen** | |
| Groepstitel |De tekst die boven aan de weergave wordt weergegeven. |
| Nieuwe groep |Selecteer deze koppeling om een nieuwe groep in de weergave te maken, te beginnen bij de huidige weergave. |
| Pictogram |Het afbeeldingsbestand dat naast het resultaat wordt weergegeven in de koptekst. |
| Pictogram gebruiken |Selecteer deze koppeling om het pictogram weer te geven. |
| **Titel** | |
| Legenda |De tekst die boven aan de koptekst wordt weergegeven. |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de koptekst. Het aantal records dat door de query wordt geretourneerd, wordt weergegeven. |
| Navigatie doorklikken | Actie die wordt ondernomen wanneer u op de koptekst klikt.  Zie [Algemene instellingen voor](#click-through-navigation)meer informatie. |
| **Lijst** | |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de lijst. De eerste twee eigenschappen voor de eerste tien records in de resultaten worden weergegeven. De eerste eigenschap is een tekstwaarde en de tweede eigenschap is een numerieke waarde. Er worden automatisch balken gemaakt die zijn gebaseerd op de relatieve waarde van de numerieke kolom.<br><br>Gebruik `Sort` de opdracht in de query om de records in de lijst te sorteren. Als u de query wilt uitvoeren en alle records wilt retourneren, u **Alles weergeven**selecteren. |
| Grafiek verbergen |Selecteer deze koppeling om de grafiek rechts van de numerieke kolom uit te schakelen. |
| Sparklines inschakelen |Selecteer deze koppeling om een sparkline weer te geven in plaats van een horizontale balk. Zie [Algemene instellingen voor](#sparklines)meer informatie. |
| Kleur |De kleur van de balken of sparklines. |
| Naam- en waardescheidingsteken |De scheidingsteken met één teken die moet worden gebruikt om de eigenschap tekst in meerdere waarden te ontlopen. Zie [Algemene instellingen voor](#sparklines)meer informatie. |
| Navigatie doorklikken | Actie die wordt ondernomen wanneer u op een item in de lijst klikt.  Zie [Algemene instellingen voor](#click-through-navigation)meer informatie. |
| **Lijst** |**kolomtitels>** |
| Name |De tekst die boven aan de eerste kolom wordt weergegeven. |
| Waarde |De tekst die boven aan de tweede kolom wordt weergegeven. |
| **Lijst** |**> Drempels** |
| Drempels inschakelen |Selecteer deze koppeling om drempelwaarden in te schakelen. Zie [Algemene instellingen voor](#thresholds)meer informatie. |

## <a name="two-numbers-and-list-part"></a>Twee getallen en lijstdeel
De koptekst heeft twee getallen die een aantal records van afzonderlijke logboekquery's weergeven. De lijst geeft de top tien resultaten van een query weer, met een grafiek die de relatieve waarde van een numerieke kolom of de verandering ervan in de tijd aangeeft.

![Lijstweergave Twee nummers &](media/view-designer-parts/view-two-numbers-list.png)

| Instelling | Beschrijving |
|:--- |:--- |
| **Algemeen** | |
| Groepstitel |De tekst die boven aan de weergave wordt weergegeven. |
| Nieuwe groep |Selecteer deze koppeling om een nieuwe groep in de weergave te maken, te beginnen bij de huidige weergave. |
| Pictogram |Het afbeeldingsbestand dat naast het resultaat wordt weergegeven in de koptekst. |
| Pictogram gebruiken |Selecteer deze koppeling om het pictogram weer te geven. |
| **Titelnavigatie** | |
| Navigatie doorklikken | Actie die wordt ondernomen wanneer u op de koptekst klikt.  Zie [Algemene instellingen voor](#click-through-navigation)meer informatie. |
| **Titel** | |
| Legenda |De tekst die boven aan de koptekst wordt weergegeven. |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de koptekst. Het aantal records dat door de query wordt geretourneerd, wordt weergegeven. |
| **Lijst** | |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de lijst. De eerste twee eigenschappen voor de eerste tien records in de resultaten worden weergegeven. De eerste eigenschap is een tekstwaarde en de tweede eigenschap is een numerieke waarde. Balken worden automatisch gemaakt op basis van de relatieve waarde van de numerieke kolom.<br><br>Gebruik `Sort` de opdracht in de query om de records in de lijst te sorteren. Als u de query wilt uitvoeren en alle records wilt retourneren, u **Alles weergeven**selecteren. |
| Grafiek verbergen |Selecteer deze koppeling om de grafiek rechts van de numerieke kolom uit te schakelen. |
| Sparklines inschakelen |Selecteer deze koppeling om een sparkline weer te geven in plaats van een horizontale balk. Zie [Algemene instellingen voor](#sparklines)meer informatie. |
| Kleur |De kleur van de balken of sparklines. |
| Bewerking |De bewerking die moet worden uitgevoerd voor de sparkline. Zie [Algemene instellingen voor](#sparklines)meer informatie. |
| Naam- en waardescheidingsteken |De scheidingsteken met één teken die moet worden gebruikt om de eigenschap tekst in meerdere waarden te ontlopen. Zie [Algemene instellingen voor](#sparklines)meer informatie. |
| Navigatie doorklikken | Actie die wordt ondernomen wanneer u op een item in de lijst klikt.  Zie [Algemene instellingen voor](#click-through-navigation)meer informatie. |
| **Lijst** |**kolomtitels>** |
| Name |De tekst die boven aan de eerste kolom wordt weergegeven. |
| Waarde |De tekst die boven aan de tweede kolom wordt weergegeven. |
| **Lijst** |**> Drempels** |
| Drempels inschakelen |Selecteer deze koppeling om drempelwaarden in te schakelen. Zie [Algemene instellingen voor](#thresholds)meer informatie. |

## <a name="donut-and-list-part"></a>Donut en lijstdeel
De koptekst geeft één getal weer dat een waardekolom in een logboekquery samenvat. De donut geeft op grafische plaats de resultaten van de top drie records weer.

![Donut- en lijstweergave](media/view-designer-parts/view-donut-list.png)

| Instelling | Beschrijving |
|:--- |:--- |
| **Algemeen** | |
| Groepstitel |De tekst die boven aan de tegel wordt weergegeven. |
| Nieuwe groep |Selecteer deze koppeling om een nieuwe groep in de weergave te maken, te beginnen bij de huidige weergave. |
| Pictogram |Het afbeeldingsbestand dat naast het resultaat wordt weergegeven in de koptekst. |
| Pictogram gebruiken |Selecteer deze koppeling om het pictogram weer te geven. |
| **Header** | |
| Titel |De tekst die boven aan de koptekst wordt weergegeven. |
| Subtitel |De tekst die wordt weergegeven onder de titel boven aan de koptekst. |
| **Ringdiagram** | |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de donut. De eerste eigenschap is een tekstwaarde en de tweede eigenschap is een numerieke waarde. |
| Navigatie doorklikken | Actie die wordt ondernomen wanneer u op de koptekst klikt.  Zie [Algemene instellingen voor](#click-through-navigation)meer informatie. |
| **Ringdiagram** |**> Centrum** |
| Tekst |De tekst die wordt weergegeven onder de waarde in de donut. |
| Bewerking |De bewerking die moet worden uitgevoerd op de eigenschap waarde om deze samen te vatten als één waarde.<ul><li>Som: Hiermee worden de waarden van alle records toegevoegd.</li><li>Percentage: de verhouding van de records die worden geretourneerd door de waarden in **Resultaatwaarden die in de centerbewerking worden gebruikt,** met de totale records in de query.</li></ul> |
| Resultaatwaarden die worden gebruikt in de centerbewerking |Selecteer optioneel het plusteken (+) om een of meer waarden toe te voegen. De resultaten van de query zijn beperkt tot records met de eigenschapswaarden die u opgeeft. Als er geen waarden worden toegevoegd, worden alle records in de query opgenomen. |
| **Aanvullende opties** |**> kleuren** |
| Kleur 1<br>Kleur 2<br>Kleur 3 |Selecteer de kleur voor elk van de waarden die in de donut worden weergegeven. |
| **Aanvullende opties** |**> Geavanceerde kleurtoewijzing** |
| Veldwaarde |Typ de naam van een veld om het als een andere kleur weer te geven als deze in de donut is opgenomen. |
| Kleur |Selecteer de kleur voor het unieke veld. |
| **Lijst** | |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de lijst. Het aantal records dat door de query wordt geretourneerd, wordt weergegeven. |
| Grafiek verbergen |Selecteer deze koppeling om de grafiek rechts van de numerieke kolom uit te schakelen. |
| Sparklines inschakelen |Selecteer deze koppeling om een sparkline weer te geven in plaats van een horizontale balk. Zie [Algemene instellingen voor](#sparklines)meer informatie. |
| Kleur |De kleur van de balken of sparklines. |
| Bewerking |De bewerking die moet worden uitgevoerd voor de sparkline. Zie [Algemene instellingen voor](#sparklines)meer informatie. |
| Naam- en waardescheidingsteken |De scheidingsteken met één teken die moet worden gebruikt om de eigenschap tekst in meerdere waarden te ontlopen. Zie [Algemene instellingen voor](#sparklines)meer informatie. |
| Navigatie doorklikken | Actie die wordt ondernomen wanneer u op een item in de lijst klikt.  Zie [Algemene instellingen voor](#click-through-navigation)meer informatie. |
| **Lijst** |**kolomtitels>** |
| Name |De tekst die boven aan de eerste kolom wordt weergegeven. |
| Waarde |De tekst die boven aan de tweede kolom wordt weergegeven. |
| **Lijst** |**> Drempels** |
| Drempels inschakelen |Selecteer deze koppeling om drempelwaarden in te schakelen. Zie [Algemene instellingen voor](#thresholds)meer informatie. |

## <a name="two-timelines-and-list-part"></a>Twee tijdlijnen en lijstonderdeel
De kopgeeft de resultaten van twee logboekquery's in de loop van de tijd weer als kolomdiagrammen, met een uitroepdie een enkel getal weergeeft dat een waardekolom in een logboekquery samenvat. De lijst geeft de top tien resultaten van een query weer, met een grafiek die de relatieve waarde van een numerieke kolom of de verandering ervan in de tijd aangeeft.

![Twee tijdlijnen en lijstweergave](media/view-designer-parts/view-two-timelines-list.png)

| Instelling | Beschrijving |
|:--- |:--- |
| **Algemeen** | |
| Groepstitel |De tekst die boven aan de tegel wordt weergegeven. |
| Nieuwe groep |Selecteer deze koppeling om een nieuwe groep in de weergave te maken, te beginnen bij de huidige weergave. |
| Pictogram |Het afbeeldingsbestand dat naast het resultaat wordt weergegeven in de koptekst. |
| Pictogram gebruiken |Selecteer deze koppeling om het pictogram weer te geven. |
| **Titelnavigatie** | |
| Navigatie doorklikken | Actie die wordt ondernomen wanneer u op de koptekst klikt.  Zie [Algemene instellingen voor](#click-through-navigation)meer informatie. |
| **Eerste<br>grafiek Tweede grafiek** | |
| Legenda |De tekst die wordt weergegeven onder de oproep voor de eerste serie. |
| Kleur |De kleur die u wilt gebruiken voor de kolommen in de reeks. |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de eerste reeks. Het aantal records over elk tijdsinterval wordt weergegeven door de grafiekkolommen. |
| Bewerking |De bewerking die moet worden uitgevoerd op de eigenschap value om deze samen te vatten als één waarde voor het uitroep.<ul><li>Som: de som van de waarden uit alle records.</li><li>Gemiddelde: het gemiddelde van de waarden uit alle records.</li><li>Laatste voorbeeld: de waarde van het laatste interval dat in de grafiek is opgenomen.</li><li>Eerste voorbeeld: de waarde van het eerste interval dat in de grafiek is opgenomen.</li><li>Aantal: het aantal records dat door de query wordt geretourneerd.</li></ul> |
| **Lijst** | |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de lijst. Het aantal records dat door de query wordt geretourneerd, wordt weergegeven. |
| Grafiek verbergen |Selecteer deze koppeling om de grafiek rechts van de numerieke kolom uit te schakelen. |
| Sparklines inschakelen |Selecteer deze koppeling om een sparkline weer te geven in plaats van een horizontale balk. Zie [Algemene instellingen voor](#sparklines)meer informatie. |
| Kleur |De kleur van de balken of sparklines. |
| Bewerking |De bewerking die moet worden uitgevoerd voor de sparkline. Zie [Algemene instellingen voor](#sparklines)meer informatie. |
| Navigatie doorklikken | Actie die wordt ondernomen wanneer u op een item in de lijst klikt.  Zie [Algemene instellingen voor](#click-through-navigation)meer informatie. |
| **Lijst** |**kolomtitels>** |
| Name |De tekst die boven aan de eerste kolom wordt weergegeven. |
| Waarde |De tekst die boven aan de tweede kolom wordt weergegeven. |
| **Lijst** |**> Drempels** |
| Drempels inschakelen |Selecteer deze koppeling om drempelwaarden in te schakelen. Zie [Algemene instellingen voor](#thresholds)meer informatie. |

## <a name="information-part"></a>Informatieonderdeel
De koptekst geeft statische tekst en een optionele koppeling weer. In de lijst worden een of meer items met een statische titel en tekst weergegeven.

![Informatieweergave](media/view-designer-parts/view-information.png)

| Instelling | Beschrijving |
|:--- |:--- |
| **Algemeen** | |
| Groepstitel |De tekst die boven aan de tegel wordt weergegeven. |
| Nieuwe groep |Selecteer deze koppeling om een nieuwe groep in de weergave te maken, te beginnen bij de huidige weergave. |
| Kleur |De achtergrondkleur voor de koptekst. |
| **Header** | |
| Installatiekopie |Het afbeeldingsbestand dat wordt weergegeven in de koptekst. |
| Label |De tekst die wordt weergegeven in de koptekst. |
| **Header** |**> koppeling** |
| Label |De linktekst. |
| URL |De url voor de link. |
| **Informatie-items** | |
| Titel |De tekst die wordt weergegeven voor de titel van elk item. |
| Inhoud |De tekst die voor elk item wordt weergegeven. |

## <a name="line-chart-callout-and-list-part"></a>Lijndiagram, uitroepnummer en lijstonderdeel
De koptekst geeft een lijndiagram weer met meerdere reeksen uit een logboekquery in de loop van de tijd en een uitroep met een samengevatte waarde. De lijst geeft de top tien resultaten van een query weer, met een grafiek die de relatieve waarde van een numerieke kolom of de verandering ervan in de tijd aangeeft.

![Lijndiagram, oproep- en lijstweergave](media/view-designer-parts/view-line-chart-callout-list.png)

| Instelling | Beschrijving |
|:--- |:--- |
| **Algemeen** | |
| Groepstitel |De tekst die boven aan de tegel wordt weergegeven. |
| Nieuwe groep |Selecteer deze koppeling om een nieuwe groep in de weergave te maken, te beginnen bij de huidige weergave. |
| Pictogram |Het afbeeldingsbestand dat naast het resultaat wordt weergegeven in de koptekst. |
| Pictogram gebruiken |Selecteer deze koppeling om het pictogram weer te geven. |
| **Header** | |
| Titel |De tekst die boven aan de koptekst wordt weergegeven. |
| Subtitel |De tekst die wordt weergegeven onder de titel boven aan de koptekst. |
| **Lijndiagram** | |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor het lijndiagram. De eerste eigenschap is een tekstwaarde en de tweede eigenschap is een numerieke waarde. Deze query gebruikt gewoonlijk het *zoekwoord meten* om de resultaten samen te vatten. Als de query het *intervaltrefwoord* gebruikt, wordt in de x-as van de grafiek dit tijdsinterval gebruikt. Als de query het *intervaltrefwoord* niet bevat, gebruikt de x-as uurintervallen. |
| Navigatie doorklikken | Actie die wordt ondernomen wanneer u op de koptekst klikt.  Zie [Algemene instellingen voor](#click-through-navigation)meer informatie. |
| **Lijndiagram** |**> Roepout** |
| Titel van het uitroepuroep |De tekst die boven de afroepwaarde wordt weergegeven. |
| Serienaam |Eigenschappenswaarde voor de reeks die moet worden gebruikt voor de afroepwaarde. Als er geen reeks wordt opgegeven, worden alle records uit de query gebruikt. |
| Bewerking |De bewerking die moet worden uitgevoerd op de eigenschap value om deze samen te vatten als één waarde voor het uitroep.<ul><li>Gemiddelde: het gemiddelde van de waarden uit alle records.</li><li>Aantal: het aantal records dat door de query wordt geretourneerd.</li><li>Laatste voorbeeld: de waarde van het laatste interval dat in de grafiek is opgenomen.</li><li>Max: de maximale waarde van de intervallen die in de grafiek zijn opgenomen.</li><li>Min: De minimumwaarde van de intervallen die in de grafiek zijn opgenomen.</li><li>Som: de som van de waarden uit alle records.</li></ul> |
| **Lijndiagram** |**> Y-as** |
| Logaritmische schaal gebruiken |Selecteer deze koppeling om een logaritmische schaal voor de y-as te gebruiken. |
| Eenheden |Geef de eenheden op voor de waarden die door de query moeten worden geretourneerd. Deze informatie wordt gebruikt om grafieklabels weer te geven die de waardetypen aangeven en, optioneel, de waarden om te zetten. Het *type eenheid* geeft de categorie van de eenheid op en definieert de beschikbare *typewaarden huidige eenheid.* Als u een waarde selecteert in *Converteren naar*, worden de numerieke waarden geconverteerd van het type *Huidige eenheid* naar het type *Converteren naar* tekst. |
| Aangepast label |De tekst die wordt weergegeven voor de y-as naast het label voor het *type Eenheid.* Als er geen label is opgegeven, wordt alleen het type *Eenheid* weergegeven. |
| **Lijst** | |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de lijst. Het aantal records dat door de query wordt geretourneerd, wordt weergegeven. |
| Grafiek verbergen |Selecteer deze koppeling om de grafiek rechts van de numerieke kolom uit te schakelen. |
| Sparklines inschakelen |Selecteer deze koppeling om een sparkline weer te geven in plaats van een horizontale balk. Zie [Algemene instellingen voor](#sparklines)meer informatie. |
| Kleur |De kleur van de balken of sparklines. |
| Bewerking |De bewerking die moet worden uitgevoerd voor de sparkline. Zie [Algemene instellingen voor](#sparklines)meer informatie. |
| Naam- en waardescheidingsteken |De scheidingsteken met één teken die moet worden gebruikt om de eigenschap tekst in meerdere waarden te ontlopen. Zie [Algemene instellingen voor](#sparklines)meer informatie. |
| Navigatie doorklikken | Actie die wordt ondernomen wanneer u op een item in de lijst klikt.  Zie [Algemene instellingen voor](#click-through-navigation)meer informatie. |
| **Lijst** |**kolomtitels>** |
| Name |De tekst die boven aan de eerste kolom wordt weergegeven. |
| Waarde |De tekst die boven aan de tweede kolom wordt weergegeven. |
| **Lijst** |**> Drempels** |
| Drempels inschakelen |Selecteer deze koppeling om drempelwaarden in te schakelen. Zie [Algemene instellingen voor](#thresholds)meer informatie. |

## <a name="line-chart-and-list-part"></a>Lijndiagram en lijstonderdeel
De koptekst geeft een lijndiagram weer met meerdere reeksen uit een logboekquery in de loop van de tijd. De lijst geeft de top tien resultaten van een query weer, met een grafiek die de relatieve waarde van een numerieke kolom of de verandering ervan in de tijd aangeeft.

![Lijndiagram en lijstweergave](media/view-designer-parts/view-line-chart-callout-list.png)

| Instelling | Beschrijving |
|:--- |:--- |
| **Algemeen** | |
| Groepstitel |De tekst die boven aan de tegel wordt weergegeven. |
| Nieuwe groep |Selecteer deze koppeling om een nieuwe groep in de weergave te maken, te beginnen bij de huidige weergave. |
| Pictogram |Het afbeeldingsbestand dat naast het resultaat wordt weergegeven in de koptekst. |
| Pictogram gebruiken |Selecteer deze koppeling om het pictogram weer te geven. |
| **Header** | |
| Titel |De tekst die boven aan de koptekst wordt weergegeven. |
| Subtitel |De tekst die wordt weergegeven onder de titel boven aan de koptekst. |
| **Lijndiagram** | |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor het lijndiagram. De eerste eigenschap is een tekstwaarde en de tweede eigenschap is een numerieke waarde. Deze query gebruikt gewoonlijk het *zoekwoord meten* om de resultaten samen te vatten. Als de query het *intervaltrefwoord* gebruikt, wordt in de x-as van de grafiek dit tijdsinterval gebruikt. Als de query het *intervaltrefwoord* niet bevat, gebruikt de x-as uurintervallen. |
| Navigatie doorklikken | Actie die wordt ondernomen wanneer u op de koptekst klikt.  Zie [Algemene instellingen voor](#click-through-navigation)meer informatie. |
| **Lijndiagram** |**> Y-as** |
| Logaritmische schaal gebruiken |Selecteer deze koppeling om een logaritmische schaal voor de y-as te gebruiken. |
| Eenheden |Geef de eenheden op voor de waarden die door de query moeten worden geretourneerd. Deze informatie wordt gebruikt om grafieklabels weer te geven die de waardetypen aangeven en, optioneel, de waarden om te zetten. Het *type eenheid* geeft de categorie van de eenheid op en definieert de beschikbare *typewaarden huidige eenheid.* Als u een waarde selecteert in *Converteren naar*, worden de numerieke waarden geconverteerd van het type *Huidige eenheid* naar het type *Converteren naar* tekst. |
| Aangepast label |De tekst die wordt weergegeven voor de y-as naast het label voor het *type Eenheid.* Als er geen label is opgegeven, wordt alleen het type *Eenheid* weergegeven. |
| **Lijst** | |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de lijst. Het aantal records dat door de query wordt geretourneerd, wordt weergegeven. |
| Grafiek verbergen |Selecteer deze koppeling om de grafiek rechts van de numerieke kolom uit te schakelen. |
| Sparklines inschakelen |Selecteer deze koppeling om een sparkline weer te geven in plaats van een horizontale balk. Zie [Algemene instellingen voor](#sparklines)meer informatie. |
| Kleur |De kleur van de balken of sparklines. |
| Bewerking |De bewerking die moet worden uitgevoerd voor de sparkline. Zie [Algemene instellingen voor](#sparklines)meer informatie. |
| Naam- en waardescheidingsteken |De scheidingsteken met één teken die moet worden gebruikt om de eigenschap tekst in meerdere waarden te ontlopen. Zie [Algemene instellingen voor](#sparklines)meer informatie. |
| Navigatie doorklikken | Actie die wordt ondernomen wanneer u op een item in de lijst klikt.  Zie [Algemene instellingen voor](#click-through-navigation)meer informatie. |
| **Lijst** |**kolomtitels>** |
| Name |De tekst die boven aan de eerste kolom wordt weergegeven. |
| Waarde |De tekst die boven aan de tweede kolom wordt weergegeven. |
| **Lijst** |**> Drempels** |
| Drempels inschakelen |Selecteer deze koppeling om drempelwaarden in te schakelen. Zie [Algemene instellingen voor](#thresholds)meer informatie. |

## <a name="stack-of-line-charts-part"></a>Stapel lijndiagrammenonderdeel
De stapel lijndiagrammen toont drie afzonderlijke lijndiagrammen, met meerdere reeksen uit een logboekquery in de loop van de tijd, zoals hier wordt weergegeven:

![Stapel lijndiagrammen](media/view-designer-parts/view-stack-line-charts.png)

| Instelling | Beschrijving |
|:--- |:--- |
| **Algemeen** | |
| Groepstitel |De tekst die boven aan de tegel wordt weergegeven. |
| Nieuwe groep |Selecteer deze koppeling om een nieuwe groep in de weergave te maken, te beginnen bij de huidige weergave. |
| Pictogram |Het afbeeldingsbestand dat naast het resultaat wordt weergegeven in de koptekst. |
| **Grafiek<br>1<br>Grafiek 2 Grafiek 3** |**> koptekst** |
| Titel |De tekst die boven aan de grafiek wordt weergegeven. |
| Subtitel |De tekst die wordt weergegeven onder de titel boven aan de grafiek. |
| **Grafiek<br>1<br>Grafiek 2 Grafiek 3** |**Lijndiagram** |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor het lijndiagram. De eerste eigenschap is een tekstwaarde en de tweede eigenschap is een numerieke waarde. Deze query gebruikt gewoonlijk het *zoekwoord meten* om de resultaten samen te vatten. Als de query het *intervaltrefwoord* gebruikt, wordt in de x-as van de grafiek dit tijdsinterval gebruikt. Als de query het *intervaltrefwoord* niet bevat, gebruikt de x-as uurintervallen. |
| Navigatie doorklikken | Actie die wordt ondernomen wanneer u op de koptekst klikt.  Zie [Algemene instellingen voor](#click-through-navigation)meer informatie. |
| **Grafiek** |**> Y-as** |
| Logaritmische schaal gebruiken |Selecteer deze koppeling om een logaritmische schaal voor de y-as te gebruiken. |
| Eenheden |Geef de eenheden op voor de waarden die door de query moeten worden geretourneerd. Deze informatie wordt gebruikt om grafieklabels weer te geven die de waardetypen aangeven en, optioneel, de waarden om te zetten. Het *type eenheid* geeft de categorie van de eenheid op en definieert de beschikbare *typewaarden huidige eenheid.* Als u een waarde selecteert in *Converteren naar*, worden de numerieke waarden geconverteerd van het type *Huidige eenheid* naar het type *Converteren naar* tekst. |
| Aangepast label |De tekst die wordt weergegeven voor de y-as naast het label voor het *type Eenheid.* Als er geen label is opgegeven, wordt alleen het type *Eenheid* weergegeven. |

## <a name="common-settings"></a>Algemene instellingen
In de volgende secties worden instellingen beschreven die veel voorkomen bij verschillende visualisatieonderdelen.

### <a name="name-and-value-separator"></a><a name="name-value-separator"></a>Naam- en waardescheidingsteken
De naam- en waardescheidingsteken is de scheidingsteken met één teken die moet worden gebruikt om de eigenschap tekst van een lijstquery in meerdere waarden te ontlopen. Als u een scheidingsteken opgeeft, u voor elk veld namen opgeven, gescheiden door dezelfde scheidingsteken in het vak **Naam.**

Denk bijvoorbeeld aan een eigenschap met de naam *Locatie* met waarden als *Redmond-Building 41* en *Bellevue-Building 12*. U een streepje (-) opgeven voor de naam en waardescheidingsteken en *Stadsgebouw* voor de naam. Deze aanpak ontlijnt elke waarde in twee eigenschappen genaamd *Stad* en *Gebouw.*

### <a name="click-through-navigation"></a><a name="click-through-navigation"></a>Doorkliknavigatie
Navigatie via de klik bepaalt welke actie wordt ondernomen wanneer u op een kop- of lijstitem in een weergave klikt.  Hiermee wordt een query geopend in de [Log Analytics](../../azure-monitor/log-query/portals.md) of wordt een andere weergave gestart.

In de volgende tabel worden de instellingen voor doorkliknavigatie beschreven.

| Instelling           | Beschrijving |
|:--|:--|
| Logboekzoeken (automatisch) | Logboekquery die moet worden uitgevoerd wanneer u een koptekstitem selecteert.  Dit is dezelfde logboekquery waarop het item is gebaseerd.
| Zoeken in logboeken        | Logboekquery die moet worden uitgevoerd wanneer u een item in een lijst selecteert.  Typ de query in het vak **Navigatiequery.**   Gebruik *{geselecteerd item}* om de syntaxis op te nemen voor het item dat de gebruiker heeft geselecteerd.  Als de query bijvoorbeeld een kolom met de naam *Computer* heeft en de navigatiequery *{geselecteerd item}* is, wordt een query zoals *Computer="MyComputer"* uitgevoerd wanneer u een computer selecteert. Als de navigatiequery *Type=Gebeurtenis {geselecteerd item}* is, wordt de query *Type=Gebeurteniscomputer="MijnComputer"* uitgevoerd. |
| Weergave              | Weergeven om te openen wanneer u een kopitem of een item in een lijst selecteert.  Selecteer de naam van een weergave in uw werkruimte in het vak **Naam weergeven.** |



### <a name="sparklines"></a><a name="sparklines"></a>Sparklines
Een sparkline is een klein lijndiagram dat de waarde van een lijstvermelding in de loop van de tijd illustreert. Voor visualisatieonderdelen met een lijst u selecteren of u een horizontale balk wilt weergeven, die de relatieve waarde van een numerieke kolom of een sparkline aangeeft, die de waarde in de loop van de tijd aangeeft.

In de volgende tabel worden de instellingen voor sparklines beschreven:

| Instelling | Beschrijving |
|:--- |:--- |
| Sparklines inschakelen |Selecteer deze koppeling om een sparkline weer te geven in plaats van een horizontale balk. |
| Bewerking |Als sparklines zijn ingeschakeld, is dit de bewerking die moet worden uitgevoerd op elke eigenschap in de lijst om de waarden voor de sparkline te berekenen.<ul><li>Laatste voorbeeld: de laatste waarde voor de reeks tijdens het tijdsinterval.</li><li>Max: de maximale waarde voor de serie gedurende het tijdsinterval.</li><li>Min: De minimumwaarde voor de reeks over het tijdinterval.</li><li>Som: de som van de waarden voor de reeks tijdens het tijdsinterval.</li><li>Samenvatting: gebruikt `measure` dezelfde opdracht als de query in de koptekst.</li></ul> |

### <a name="thresholds"></a><a name="thresholds"></a>Drempels
Door drempels te gebruiken, u een gekleurd pictogram naast elk item in een lijst weergeven. Drempels geven u een snelle visuele indicator van items die een bepaalde waarde overschrijden of binnen een bepaald bereik vallen. U bijvoorbeeld een groen pictogram weergeven voor items met een acceptabele waarde, geel als de waarde zich binnen een bereik bevindt dat een waarschuwing aangeeft en rood als deze een foutwaarde overschrijdt.

Wanneer u drempelwaarden inschakelt voor een onderdeel, moet u een of meer drempelwaarden opgeven. Als de waarde van een artikel groter is dan een drempelwaarde en lager is dan de volgende drempelwaarde, wordt de kleur voor die waarde gebruikt. Als het item groter is dan de hoogste drempelwaarde, wordt een andere kleur gebruikt. 

Elke drempelwaardeset heeft één drempelwaarde met een waarde van **Standaard**. Dit is de kleur die is ingesteld als er geen andere waarden worden overschreden. U drempelwaarden toevoegen of verwijderen door de knop **Toevoegen** (+) of Verwijderen (x) **te** selecteren.

In de volgende tabel worden de instellingen voor drempelwaarden beschreven:

| Instelling | Beschrijving |
|:--- |:--- |
| Drempels inschakelen |Selecteer deze koppeling om links van elke waarde een kleurenpictogram weer te geven. Het pictogram geeft de status van de waarde ten opzichte van opgegeven drempelwaarden aan. |
| Name |De naam van de drempelwaarde. |
| Drempelwaarde |De waarde voor de drempelwaarde. De statuskleur voor elk lijstitem is ingesteld op de kleur van de hoogste drempelwaarde die wordt overschreden door de waarde van het item. Als er geen drempelwaarden worden overschreden, wordt een standaardkleur gebruikt. |
| Kleur |De kleur die de drempelwaarde aangeeft. |

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [logboekquery's](../log-query/log-query-overview.md) ter ondersteuning van de query's in visualisatieonderdelen.
