---
title: Interactieve rapporten maken met Azure Monitor werkmappen | Micro soft docs
description: Vereenvoudig complexe rapportage met vooraf samengestelde en aangepaste werkmappen met para meters
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 15543f7f761c707e8eff8e0cc0a0e4532475ddf8
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670998"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Interactieve rapporten maken met Azure Monitor werkmappen

Werkmappen combi neren tekst, [analyse query's](https://docs.microsoft.com/azure/application-insights/app-insights-analytics), Azure-metrische gegevens en para meters in uitgebreide interactieve rapporten. Werkmappen kunnen worden bewerkt door andere team leden die toegang hebben tot dezelfde Azure-resources.

Werkmappen zijn handig voor scenario's zoals:

* Het gebruik van uw app verkennen wanneer u niet weet wat de metrische gegevens zijn die van belang zijn: aantal gebruikers, retentie tarieven, conversie tarieven, enzovoort. In tegens telling tot andere hulpprogram ma's voor gebruiks analyse kunt u met werkmappen meerdere soorten visualisaties en analyses combi neren, waardoor ze ideaal zijn voor dit soort vrije-vorm onderzoek.
* Uitleg over uw team hoe een nieuw uitgebrachte functie wordt uitgevoerd, door het aantal gebruikers voor sleutel interacties en andere metrische gegevens weer te geven.
* De resultaten van een A/B-experiment in uw app delen met andere leden van uw team. U kunt de doel stellingen voor het experiment uitleggen met tekst, vervolgens elke gebruiks metriek en analyse query weer geven die wordt gebruikt voor het evalueren van het experiment, samen met duidelijke aanroep-outs voor of elke metriek boven of onder-doel is.
* Rapportage van de impact van een storing in het gebruik van uw app, het combi neren van gegevens, uitleg van teksten en een bespreking van de volgende stappen om storingen in de toekomst te voor komen.

## <a name="starting-with-a-template-or-saved-workbook"></a>Beginnen met een sjabloon of opgeslagen werkmap

Een werkmap bestaat uit secties die bestaan uit onafhankelijk Bewerk bare grafieken, tabellen, tekst en invoer besturings elementen. Voor een beter begrip van werkmappen kunt u het beste een up openen. 

Selecteer **werkmappen** in het menu links in de Application Insights-ervaring voor uw app.

![Scherm afbeelding van navigatie naar werkmappen](./media/usage-workbooks/001-workbooks.png)

Hiermee opent u een werkmap galerie met een aantal vooraf gemaakte werkmappen waarmee u aan de slag kunt.

![Scherm opname van werkmap galerie](./media/usage-workbooks/002-workbook-gallery.png)

We beginnen met de **standaard sjabloon**, die zich onder de kop **Quick Start**bevindt.

![Scherm opname van werkmap galerie](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Werkmap secties bewerken, ordenen, klonen en verwijderen

Werkmappen hebben twee modi: **bewerkings modus**en **Lees modus**. Wanneer de standaard werkmap voor het eerst wordt gestart, wordt deze in de **bewerkings modus**geopend. Hiermee wordt de inhoud van de werkmap weer gegeven, inclusief alle stappen en para meters die op een andere manier verborgen zijn. De **Lees modus** toont een vereenvoudigde rapport stijl weergave. Op deze manier kunt u de complexiteit van het maken van een rapport samen stellen, terwijl de onderliggende mechanismen nog maar een paar muis klikken hebben wanneer dat nodig is voor wijzigingen.

![Sectie besturings elementen voor het bewerken van Application Insights-werkmappen](./media/usage-workbooks/editing-controls-new.png)

1. Wanneer u klaar bent met het bewerken van een sectie, klikt u op **gereed bewerken** in de linkerbenedenhoek van de sectie.

2. Als u een duplicaat van een sectie wilt maken, klikt u op het pictogram **deze sectie klonen** . Het maken van dubbele secties is een uitstekende manier om een query te herhalen zonder dat vorige herhalingen verloren gaan.

3. Als u een sectie in een werkmap omhoog wilt verplaatsen, klikt u op het pictogram **omhoog** of **omlaag** .

4. Als u een sectie permanent wilt verwijderen, klikt u op het pictogram **verwijderen** .

## <a name="adding-text-and-markdown-sections"></a>Secties voor tekst en prijs verlaging toevoegen

Door koppen, uitleg en commentaar toe te voegen aan uw werkmappen kunt u een set tabellen en grafieken omzetten in een verhalende tabel. Tekst secties in werkmappen ondersteunen de [Afkortings syntaxis](https://daringfireball.net/projects/markdown/) voor tekst opmaak, zoals kopteksten, vet, cursief en lijsten met opsommings tekens.

Als u een tekst sectie wilt toevoegen aan uw werkmap, gebruikt u de knop **tekst toevoegen** onder aan de werkmap of aan de onderkant van een sectie.

## <a name="adding-query-sections"></a>Query secties toevoegen

![Query sectie in werkmappen](./media/usage-workbooks/analytics-section-new.png)

Als u een query sectie wilt toevoegen aan uw werkmap, gebruikt u de knop **query toevoegen** onder aan de werkmap of aan de onderkant van een sectie.

Query secties zijn zeer flexibel en kunnen worden gebruikt om vragen te beantwoorden zoals:

* Hoeveel uitzonde ringen is uw site tijdens dezelfde periode gegenereerd als een afname in het gebruik?
* Wat was de verdeling van pagina laad tijden voor gebruikers die een pagina bekijken?
* Hoeveel gebruikers hebben een aantal pagina's op uw site weer gegeven, maar niet een andere set pagina's? Dit kan handig zijn als u clusters hebt met gebruikers die verschillende subsets van de functionaliteit van uw site gebruiken (gebruik de `join`-operator met de `kind=leftanti` modifier in de [query taal Kusto](/azure/kusto/query/)).

U kunt ook niet alleen een query uitvoeren vanuit de context van de toepassing waaruit u de werkmap hebt gestart. U kunt een query uitvoeren op meerdere Application Insights bewaakte apps en Log Analytics werk ruimten, zolang u hiervoor toegangs rechten voor deze resources hebt.

Als u een query wilt uitvoeren vanuit extra externe Application Insights resources, gebruikt u de **app** -id.

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Met deze query worden aanvragen van drie verschillende toepassingen gecombineerd. Een app met de naam app01, een app met de naam app02 en de aanvragen van de lokale Application Insights resource.

Als u gegevens uit een externe Log Analytics werk ruimte wilt ophalen, gebruikt u de **werk ruimte** -id.

Raadpleeg de [officiële richt lijnen](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search)voor meer informatie over query's voor meerdere resources.

### <a name="advanced-analytic-query-settings"></a>Geavanceerde instellingen voor analytische query's

Elke sectie heeft zijn eigen geavanceerde instellingen, die toegankelijk zijn via het pictogram instellingen ![Application Insights sectie besturings elementen bewerken](./media/usage-workbooks/005-settings.png) rechts van de knop **para meters toevoegen** .

![Sectie besturings elementen voor het bewerken van Application Insights-werkmappen](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Aangepaste breedte**    | Stel dit in om een item een wille keurige grootte te geven, zodat u veel items op één regel kunt aanpassen, zodat u uw grafieken en tabellen beter in een uitgebreide, interactieve rapporten ordent.  |
   | **Voorwaardelijk zichtbaar** | Gebruik deze om stappen te verbergen op basis van een para meter in de Lees modus. |
   | **Een para meter exporteren**| Hiermee kan een geselecteerde rij in het raster of diagram worden uitgevoerd om waarden te wijzigen of zichtbaar te worden.  |
   | **Query weer geven wanneer deze niet wordt bewerkt** | Hiermee wordt de query boven de grafiek of tabel weer gegeven, zelfs in de Lees modus.
   | **De knop openen in Analytics weer geven wanneer deze niet wordt bewerkt** | Hiermee voegt u het pictogram Blue Analytics toe aan de rechter bovenhoek van de grafiek om toegang met één klik toe te staan.|

De meeste van deze instellingen zijn redelijk intuïtief, maar om **een para meter** te kunnen exporteren, is het beter om een werkmap te onderzoeken die gebruikmaakt van deze functionaliteit.

Een van de vooraf gemaakte werkmappen bevat informatie over actieve gebruikers.

De eerste sectie van de werkmap is gebaseerd op analyse query gegevens:

![Sectie besturings elementen voor het bewerken van Application Insights-werkmappen](./media/usage-workbooks/003-active-users.png)

De tweede sectie is ook gebaseerd op analyse query gegevens, maar als u een rij in de eerste tabel selecteert, wordt de inhoud van de grafiek interactief bijgewerkt:

![Sectie besturings elementen voor het bewerken van Application Insights-werkmappen](./media/usage-workbooks/004-active-users-trend.png)

 Dit is mogelijk via het gebruik van de optie **Wanneer een item is geselecteerd, een geavanceerde instellingen voor een para meter exporteren** die zijn ingeschakeld in de analyse query van de tabel.

![Sectie besturings elementen voor het bewerken van Application Insights-werkmappen](./media/usage-workbooks/007-settings-export.png)

De tweede analyse query gebruikt vervolgens de geëxporteerde waarden wanneer een rij wordt geselecteerd. Als er geen rij is geselecteerd, wordt standaard de rij weer geven die de algemene waarden vertegenwoordigt. 

```
let start = startofday(ago({TimeRange} + {Metric}));
union customEvents, pageViews
| where timestamp >= start
| where name in ({Activities}) or '*' in ({Activities}) or ('%' in ({Activities}) and itemType == 'pageView') or ('#' in ({Activities}) and itemType == 'customEvent')
{OtherFilters}
| where '{Filter}' == '' or '{Filter}' == '🔸 Overall' or {AnalyzeBy} == replace('🔹 ', '', '{Filter}')
| evaluate activity_engagement(user_Id, timestamp, start, now(), 1d, {Metric})
| where timestamp >= startofday(ago({TimeRange}))
| project timestamp, ["Active User"] = dcount_activities_outer
| render timechart 
```

## <a name="adding-metrics-sections"></a>Secties met metrische gegevens toevoegen

Met metrische secties beschikt u over volledige toegang tot het opnemen van Azure Monitor metrische gegevens in uw interactieve rapporten. Veel van de vooraf ontwikkelde werk bladen bevatten zowel analytische query gegevens als metrische gegevens, zodat u optimaal gebruik kunt maken van het beste van beide functies op één plek. U hebt ook de mogelijkheid om de metrische gegevens van resources op te halen in een van de abonnementen waartoe u toegang hebt.

Hier volgt een voor beeld van gegevens van virtuele machines die worden opgehaald in een werkmap om een raster visualisatie van CPU-prestaties te bieden:

![Sectie besturings elementen voor het bewerken van Application Insights-werkmappen](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Parameter secties toevoegen

Met de para meters van de werkmap kunt u waarden in de werkmap wijzigen zonder de query-of tekst secties hand matig te bewerken.  Dit betekent dat het niet nodig is om inzicht te krijgen in de onderliggende analyse query taal en de potentiële doel groep van rapporten op basis van een werkmap aanzienlijk uit te breiden.

De waarden van para meters worden vervangen in de secties query, tekst of andere para meters door de naam van de para meter tussen accolades, zoals ``{parameterName}``, te plaatsen.  Parameter namen zijn beperkt tot vergelijk bare regels als Java script-id's, in principe alfabetische tekens of onderstrepingen, gevolgd door alfanumerieke tekens of onderstrepingen. Zo is **a1** toegestaan, maar **1a** is niet toegestaan.

Para meters zijn lineair, vanaf de bovenkant van een werkmap en doorlopend naar latere stappen.  Para meters die later in een werkmap zijn gedeclareerd, kunnen de variabelen overschrijven die zijn gedeclareerd.  Op deze manier kunt u ook para meters gebruiken om toegang te krijgen tot de waarden van para meters die verder worden gedefinieerd.  Binnen de stap zelf van een para meter zijn para meters ook lineair, van links naar rechts, waarbij para meters aan de rechter kant kunnen afhankelijk zijn van een para meter die eerder in diezelfde stap is gedeclareerd.
 
Er zijn vier verschillende typen para meters die momenteel worden ondersteund:

  |         |          |
   | ---------------- |:-----|
   | **Tekst**    | de gebruiker zal een tekstvak bewerken en u kunt desgewenst een query opgeven om de standaard waarde in te vullen. |
   | **Vervolg keuzelijst** | De gebruiker kiest uit een reeks waarden. |
   | **Tijds bereik kiezer**| De gebruiker kiest uit een vooraf gedefinieerde set waarden voor tijds bereik of kiest uit een aangepast tijds bereik.|
   | **Resource kiezer** | De gebruiker kiest uit de resources die voor de werkmap zijn geselecteerd.|

### <a name="using-a-text-parameter"></a>Een tekst parameter gebruiken

De waarde die een gebruiker in het tekstvak typt, wordt rechtstreeks in de query vervangen zonder dat er een Escape of aanhalings tekens worden geplaatst. Als de waarde die u nodig hebt, een teken reeks is, moet de query aanhalings tekens hebben rondom de para meter (bijvoorbeeld **{para meter}** ).

Hierdoor kan de waarde in een tekstvak overal worden gebruikt. Dit kan een tabel naam, kolom naam, functie naam, operator, enzovoort zijn.

Het type van de tekst parameter heeft de instelling **standaard waarde ophalen uit Analytics query**, waarmee de auteur van de werkmap een query kan gebruiken om de standaard waarde voor het tekstvak te vullen.

Wanneer u de standaard waarde uit een Analytics-query gebruikt, wordt alleen de eerste waarde van de eerste rij (rij 0, kolom 0) als de standaard waarde gebruikt. Daarom is het raadzaam om uw query te beperken tot slechts één rij en één kolom. Alle andere gegevens die door de query worden geretourneerd, worden genegeerd. 

Welke waarde de query retourneert, wordt direct vervangen zonder Escape of aanhalings tekens. Als de query geen rijen retourneert, is het resultaat van de para meter een lege teken reeks (als de para meter niet is vereist) of niet gedefinieerd (als de para meter is vereist).

### <a name="using-a-dropdown"></a>Een vervolg keuzelijst gebruiken

Met het parameter type vervolg keuzelijst kunt u een besturings element voor vervolg keuzelijst maken, waardoor een of meer waarden kunnen worden geselecteerd.

De vervolg keuzelijst wordt ingevuld door een Analytics-query. Als de query één kolom retourneert, zijn de waarden in die kolom zowel de **waarde** als het **Label** in het besturings element vervolg keuzelijst. Als de query twee kolommen retourneert, is de eerste kolom de **waarde**en de tweede kolom is het **Label** dat wordt weer gegeven in de vervolg keuzelijst.  Als de query drie kolommen retourneert, wordt de derde kolom gebruikt om de standaard selectie in die vervolg keuzelijst aan te geven.  Deze kolom kan elk wille keurig type zijn, maar het eenvoudigste is het gebruik van BOOL of numeric-typen, waarbij 0 false is en 1 True is.

 Als de kolom een teken reeks type is, wordt Null/lege teken reeks als onwaar beschouwd en wordt een andere waarde als True beschouwd. Voor vervolg keuzelijsten met één selectie wordt de eerste waarde met de waarde True als de standaard selectie gebruikt.  Bij meerdere selectie vervolg keuzelijsten worden alle waarden met de waarde True als de standaard geselecteerde set gebruikt. De items in de vervolg keuzelijst worden weer gegeven in de volg orde waarin de query rijen heeft geretourneerd. 

Laten we eens kijken naar de para meters die aanwezig zijn in het rapport actieve gebruikers. Klik op het bewerkings symbool naast **time Range**.

![Sectie besturings elementen voor het bewerken van Application Insights-werkmappen](./media/usage-workbooks/009-time-range.png)

Hiermee wordt het menu-item para meter bewerken geopend:

![Sectie besturings elementen voor het bewerken van Application Insights-werkmappen](./media/usage-workbooks/010-time-range-edit.png)

De query gebruikt een functie van de Analytics-query taal, een **DataTable** , waarmee u een wille keurige tabel, vol inhoud, uit een dunne lucht kunt genereren. Bijvoorbeeld de volgende analyse query:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Genereert het resultaat:

![Sectie besturings elementen voor het bewerken van Application Insights-werkmappen](./media/usage-workbooks/011-data-table.png)

Een meer van toepassing voor beeld is het gebruik van een vervolg keuzelijst voor het kiezen van een set landen/regio's op naam:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

De query geeft de resultaten als volgt weer:

![Vervolg keuzelijst land](./media/usage-workbooks/012-country-dropdown.png)

Vervolg keuzelijsten zijn uiterst krachtige hulp middelen voor het aanpassen en maken van interactieve rapporten.

### <a name="time-range-parameters"></a>Tijds bereik parameters

Hoewel u uw eigen para meter voor een aangepast tijds bereik kunt maken via het parameter type vervolg keuzelijst, kunt u ook het parameter type out-of-Box time-out gebruiken als u niet dezelfde mate van flexibiliteit nodig hebt. 

De para meters voor het tijds bereik hebben 15 standaardbereiken van vijf minuten tot de laatste 90 dagen. Er is ook een optie voor het toestaan van aangepaste tijds bereik selectie, waarmee de operator van het rapport expliciete start-en stop waarden voor het tijds bereik kan kiezen.

### <a name="resource-picker"></a>Resource kiezer

Met het parameter type voor de resource kiezer kunt u het bereik van uw rapport beperken tot bepaalde typen resources. Een voor beeld van een vooraf gemaakte werkmap die gebruikmaakt van het resource Picker-type is de **fout Insights** -werkmap.

![Vervolg keuzelijst land](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Werkmappen opslaan en delen met uw team

Werkmappen worden opgeslagen in een Application Insights resource, hetzij in de sectie **mijn rapporten** die persoonlijk is voor u of in de sectie **gedeelde rapporten** die toegankelijk is voor iedereen die toegang heeft tot de Application Insights resource. Als u alle werkmappen in de resource wilt weer geven, klikt u op de knop **openen** in de actie balk.

Een werkmap delen die momenteel deel uitmaken van **mijn rapporten**:

1. Klik op **openen** in de actie balk
2. Klik op de '... ' Naast de werkmap die u wilt delen
3. Klik op **verplaatsen naar gedeelde rapporten**.

Als u een werkmap met een koppeling of via e-mail wilt delen, klikt u op **delen** in de actie balk. Houd er rekening mee dat ontvangers van de koppeling toegang nodig hebben tot deze resource in de Azure Portal om de werkmap weer te geven. Voor het maken van bewerkingen moeten ontvangers ten minste Inzender machtigingen hebben voor de resource.

Een koppeling naar een werkmap vastmaken aan een Azure-dash board:

1. Klik op **openen** in de actie balk
2. Klik op de '... ' Naast de werkmap die u wilt vastmaken
3. Klik op **Vastmaken aan dashboard**.

## <a name="contributing-workbook-templates"></a>Bijdraagtde werkmap sjablonen

Hebt u een sjabloon voor een meester werkmap gemaakt en wilt u deze delen met de Community? Ga voor meer informatie naar onze [github opslag plaats](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md).

## <a name="next-steps"></a>Volgende stappen
- Begin met het verzenden van [aangepaste gebeurtenissen](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) of [pagina weergaven](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)om gebruiks ervaringen in te scha kelen.
- Als u al aangepaste gebeurtenissen of pagina weergaven verzendt, kunt u de gebruiks hulpprogramma's verkennen om te leren hoe gebruikers uw service gebruiken.
    - [Gebruikers, sessies, gebeurtenissen](../../azure-monitor/app/usage-segmentation.md)
    - [Trechters](../../azure-monitor/app/usage-funnels.md)
    - [Retentie](../../azure-monitor/app/usage-retention.md)
    - [Gebruikersstromen](../../azure-monitor/app/usage-flows.md)
    - [Gebruikers context toevoegen](../../azure-monitor/app/usage-send-user-context.md)
