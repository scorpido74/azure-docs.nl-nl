---
title: Interactieve rapporten maken met Azure Monitor-werkmappen | Microsoft-documenten
description: Complexe rapportage vereenvoudigen met vooraf gebouwde en aangepaste geparametereerde werkmappen
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 15543f7f761c707e8eff8e0cc0a0e4532475ddf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670998"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Interactieve rapporten maken met Azure Monitor-werkmappen

Werkmappen combineren tekst, [Analytics-query's,](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)Azure Metrics en parameters in uitgebreide interactieve rapporten. Werkmappen kunnen worden bewerkt door andere teamleden die toegang hebben tot dezelfde Azure-resources.

Werkmappen zijn handig voor scenario's zoals:

* Het gebruik van uw app verkennen wanneer u de statistieken van de rente niet van tevoren kent: aantal gebruikers, retentiepercentages, conversiepercentages, enz. In tegenstelling tot andere hulpprogramma's voor gebruiksanalyse, u met werkmappen meerdere soorten visualisaties en analyses combineren, waardoor ze ideaal zijn voor dit soort verkenning en verkenning in vrije vorm.
* Je team uitleggen hoe een nieuw uitgebrachte functie presteert, door gebruikersaantallen voor belangrijke interacties en andere statistieken weer te geven.
* Deel de resultaten van een A/B-experiment in uw app met andere leden van uw team. U de doelen voor het experiment met tekst uitleggen en vervolgens elke gebruiksstatistiek en Analytics-query weergeven die wordt gebruikt om het experiment te evalueren, samen met duidelijke call-outs voor de vraag of elke statistiek boven of onder het doel was.
* Het melden van de impact van een storing op het gebruik van uw app, het combineren van gegevens, tekstuitleg en een discussie over de volgende stappen om uitval in de toekomst te voorkomen.

## <a name="starting-with-a-template-or-saved-workbook"></a>Beginnen met een sjabloon of een opgeslagen werkmap

Een werkmap bestaat uit secties die bestaan uit onafhankelijk bewerkbare grafieken, tabellen, tekst en invoerbesturingselementen. Om werkmappen beter te begrijpen, is het het beste om er een te openen. 

Selecteer **Werkmappen** in het linkermenu vanuit de Applicatie-insights-ervaring voor uw app.

![Schermafbeelding van navigatie in werkmappen](./media/usage-workbooks/001-workbooks.png)

Hiermee wordt een werkmapgalerie met een aantal vooraf gebouwde werkmappen gestart om u op weg te helpen.

![Schermafbeelding van werkmapgalerie](./media/usage-workbooks/002-workbook-gallery.png)

We beginnen met de **standaardsjabloon**, die zich onder het kopje Snel aan de **slag bevindt.**

![Schermafbeelding van werkmapgalerie](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Werkmapsecties bewerken, herschikken, klonen en verwijderen

Werkmappen hebben twee modi: **bewerkingsmodus**en **leesmodus**. Wanneer de standaardwerkmap voor het eerst wordt gestart, wordt deze geopend in **de bewerkingsmodus**. Hier ziet u alle inhoud van de werkmap, inclusief alle stappen en parameters die anders verborgen zijn. **De leesmodus** biedt een vereenvoudigde weergave van rapportstijl. Hiermee u de complexiteit die in het maken van een rapport ging, abstraheren terwijl u de onderliggende mechanica slechts een paar klikken verwijderd wanneer dat nodig is voor modificatie.

![Besturingselementen voor bewerkingsbewerking van de sectie Bewerking van bewerkingen van bewerkingen van bewerkingen van bewerkingen van bewerkingen van toepassingsstatistieken](./media/usage-workbooks/editing-controls-new.png)

1. Wanneer u klaar bent met het bewerken van een sectie, klikt u op **Gereed bewerken** in de linkerbenedenhoek van de sectie.

2. Als u een duplicaat van een sectie wilt maken, klikt u op het pictogram **Deze sectie klonen.** Het maken van dubbele secties is een geweldige manier om een query te herhalen zonder eerdere iteraties te verliezen.

3. Als u een sectie in een werkmap wilt omhoog wilt verplaatsen, klikt u op het pictogram **Omhoog** of **Omlaag.**

4. Als u een sectie permanent wilt verwijderen, klikt u op het pictogram **Verwijderen.**

## <a name="adding-text-and-markdown-sections"></a>Secties voor tekst en Markdown toevoegen

Door koppen, uitleg en commentaar toe te voegen aan uw werkmappen, u een reeks tabellen en grafieken omzetten in een verhaal. Tekstsecties in werkmappen ondersteunen de [syntaxis van markeren](https://daringfireball.net/projects/markdown/) voor tekstopmaak, zoals koppen, vetgedrukte, cursief en lijsten met opsommingstekens.

Als u een tekstsectie aan uw werkmap wilt toevoegen, gebruikt u de knop **Tekst toevoegen** onder aan de werkmap of onder aan een sectie.

## <a name="adding-query-sections"></a>Secties voor query's toevoegen

![Sectie Query in werkmappen](./media/usage-workbooks/analytics-section-new.png)

Als u querysectie wilt toevoegen aan uw werkmap, gebruikt u de knop **Query toevoegen** onder aan de werkmap of onder aan een sectie.

Querysecties zijn zeer flexibel en kunnen worden gebruikt om vragen te beantwoorden zoals:

* Hoeveel uitzonderingen heeft uw site in dezelfde periode als een daling van het gebruik?
* Wat was de verdeling van de laadtijden van pagina's voor gebruikers die een pagina bekijken?
* Hoeveel gebruikers hebben een aantal pagina's op uw site bekeken, maar niet een andere set pagina's? Dit kan handig zijn om te begrijpen of u clusters van gebruikers hebt die `join` verschillende `kind=leftanti` subsets van de functionaliteit van uw site gebruiken (gebruik de operator met de modifier in de [Kusto-querytaal).](/azure/kusto/query/)

U bent ook niet alleen beperkt tot query's vanuit de context van de toepassing waaruit u de werkmap hebt gestart. U vragen stellen over meerdere door Application Insights bewaakte apps en Log Analytics-werkruimten, zolang u toegang hebt tot deze bronnen.

Als u wilt query's **app** uitvoeren vanuit extra externe resources Voor Application Insights, gebruikt u de app-id.

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Deze query combineert aanvragen van drie verschillende toepassingen. Een app met de naam app01, een app met de naam app02 en de aanvragen van de lokale Application Insights-bron.

Als u gegevens wilt ophalen uit **workspace** een externe werkruimte Log Analytics, gebruikt u de werkruimte-id.

Voor meer informatie over cross-resource query's verwijzen wij u naar de [officiële richtlijnen](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

### <a name="advanced-analytic-query-settings"></a>Geavanceerde analytische query-instellingen

Elke sectie heeft zijn eigen geavanceerde instellingen, ![die toegankelijk zijn via het](./media/usage-workbooks/005-settings.png) instellingenpictogram Application Insights Workbooks sectie bewerken besturingselementen gelegen aan de rechterkant van de knop **Parameters toevoegen.**

![Besturingselementen voor bewerkingsbewerking van de sectie Bewerking van bewerkingen van bewerkingen van bewerkingen van bewerkingen van bewerkingen van toepassingsstatistieken](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Aangepaste breedte**    | Stel dit in om van een item een willekeurige grootte te maken, zodat u veel items op één regel plaatsen, zodat u uw grafieken en tabellen beter ordenen in rijke interactieve rapporten.  |
   | **Voorwaardelijk zichtbaar** | Gebruik dit om stappen te verbergen op basis van een parameter in de leesmodus. |
   | **Een parameter exporteren**| Hierdoor kan een geselecteerde rij in het raster of de grafiek ertoe leiden dat latere stappen waarden wijzigen of zichtbaar worden.  |
   | **Query weergeven wanneer deze niet wordt bewerkt** | Hiermee wordt de query boven de grafiek of tabel weergegeven, zelfs in de leesmodus.
   | **Knop Openen weergeven in analytics wanneer u niet bewerkt wordt** | Hiermee wordt het blauwe Analytics-pictogram toegevoegd aan de rechterhoek van de grafiek om toegang met één klik mogelijk te maken.|

De meeste van deze instellingen zijn vrij intuïtief, maar om te begrijpen **Een parameter exporteren** is het beter om een werkmap te onderzoeken die gebruik maakt van deze functionaliteit.

Een van de vooraf gebouwde werkmappen biedt informatie over actieve gebruikers.

Het eerste deel van de werkmap is gebaseerd op analytische querygegevens:

![Besturingselementen voor bewerkingsbewerking van de sectie Bewerking van bewerkingen van bewerkingen van bewerkingen van bewerkingen van bewerkingen van toepassingsstatistieken](./media/usage-workbooks/003-active-users.png)

Het tweede deel is ook gebaseerd op analytische querygegevens, maar als u een rij in de eerste tabel selecteert, wordt de inhoud van de grafiek interactief bijgewerkt:

![Besturingselementen voor bewerkingsbewerking van de sectie Bewerking van bewerkingen van bewerkingen van bewerkingen van bewerkingen van bewerkingen van toepassingsstatistieken](./media/usage-workbooks/004-active-users-trend.png)

 Dit is mogelijk door het gebruik van de **Analysequery Wanneer een item is geselecteerd, exporteert u een geavanceerde parameterinstellingen** die zijn ingeschakeld in de Analytics-query van de tabel.

![Besturingselementen voor bewerkingsbewerking van de sectie Bewerking van bewerkingen van bewerkingen van bewerkingen van bewerkingen van bewerkingen van toepassingsstatistieken](./media/usage-workbooks/007-settings-export.png)

De tweede analysequery maakt vervolgens gebruik van de geëxporteerde waarden wanneer een rij is geselecteerd. Als er geen rij is geselecteerd, wordt de rij standaard weergegeven die de algemene waarden vertegenwoordigt. 

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

## <a name="adding-metrics-sections"></a>Secties voor metrische gegevens toevoegen

Metrische gegevens geven u volledige toegang tot azure monitor-metrische gegevens in uw interactieve rapporten. Veel van de vooraf gebouwde werkmappen bevatten zowel analytische querygegevens als metrische gegevens, zodat u optimaal profiteren van het beste van beide functies op één plek. U hebt ook de mogelijkheid om metrische gegevens op te halen uit bronnen in een van de abonnementen waartoe u toegang hebt.

Hier is een voorbeeld van virtuele machinegegevens die in een werkmap worden getrokken om een rastervisualisatie van CPU-prestaties te bieden:

![Besturingselementen voor bewerkingsbewerking van de sectie Bewerking van bewerkingen van bewerkingen van bewerkingen van bewerkingen van bewerkingen van toepassingsstatistieken](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Secties voor parameters toevoegen

Met werkmapparameters u waarden in de werkmap wijzigen zonder dat u de query- of tekstsecties handmatig hoeft te bewerken.  Dit verwijdert de vereiste om de onderliggende analytics-querytaal te begrijpen en vergroot de potentiële doelgroep van rapportage op basis van werkmap sterk.

De waarden van parameters worden vervangen in query-, tekst- of andere parametersecties door de naam van de parameter in accolades te plaatsen, zoals ``{parameterName}``.  Parameternamen zijn beperkt tot vergelijkbare regels als JavaScript-id's, in principe alfabetische tekens of underscores, gevolgd door alfanumerieke tekens of underscores. **A1** is bijvoorbeeld toegestaan, maar **1a** is niet toegestaan.

Parameters zijn lineair, vanaf de bovenkant van een werkmap en stromen naar latere stappen.  Parameters die later in een werkmap worden gedeclareerd, kunnen de parameters die verder naar boven zijn gedeclareerd, overschrijven.  Hiermee kunnen ook parameters die query's gebruiken om toegang te krijgen tot de waarden van parameters die verder zijn gedefinieerd.  Binnen de stap van een parameter zelf zijn parameters ook lineair, van links naar rechts, waarbij parameters rechts kunnen afhangen van een parameter die eerder in diezelfde stap is gedeclareerd.
 
Er zijn vier verschillende soorten parameters die momenteel worden ondersteund:

  |         |          |
   | ---------------- |:-----|
   | **Tekst**    | de gebruiker bewerkt een tekstvak en u optioneel een query leveren om de standaardwaarde in te vullen. |
   | **Vervolgkeuzelijst** | De gebruiker kiest uit een set waarden. |
   | **Tijdbereikkiezer**| De gebruiker kiest uit een vooraf gedefinieerde set tijdbereikwaarden of kiest uit een aangepast tijdsbereik.|
   | **Resourcekiezer** | De gebruiker kiest uit de bronnen die voor de werkmap zijn geselecteerd.|

### <a name="using-a-text-parameter"></a>Een tekstparameter gebruiken

De waarde die een gebruiker in het tekstvak typt, wordt direct in de query vervangen, zonder te ontsnappen of te citeren. Als de waarde die u nodig hebt een tekenreeks is, moet de query aanhalingstekens rond de parameter hebben (zoals **{parameter}'**).

Hierdoor kan de waarde in een tekstvak overal worden gebruikt. Het kan een tabelnaam, kolomnaam, functienaam, operator, enz.

Het tekstparametertype heeft een instelling **Standaardwaarde ophalen van analysequery**, waarmee de auteur van de werkmap een query kan gebruiken om de standaardwaarde voor dat tekstvak in te vullen.

Wanneer u de standaardwaarde van een analysequery gebruikt, wordt alleen de eerste waarde van de eerste rij (rij 0, kolom 0) gebruikt als standaardwaarde. Daarom is het raadzaam om uw query te beperken tot slechts één rij en één kolom. Alle andere gegevens die door de query worden geretourneerd, worden genegeerd. 

Welke waarde de query retourneert, wordt direct vervangen door geen ontkomen of aanhalen. Als de query geen rijen retourneert, is het resultaat van de parameter een lege tekenreeks (als de parameter niet vereist is) of ongedefinieerd (als de parameter vereist is).

### <a name="using-a-dropdown"></a>Een vervolgkeuzelijst gebruiken

Met het parametertype vervolgkeuzelijst u een vervolgkeuzebesturingselement maken, zodat u een of meerdere waarden selecteren.

De vervolgkeuzelijst wordt ingevuld door een analysequery. Als de query één kolom retourneert, zijn de waarden in die kolom zowel de **waarde** als het **label** in het vervolgkeuzebesturingselement. Als de query twee kolommen retourneert, is de eerste kolom de **waarde**en is de tweede kolom het **label** dat wordt weergegeven in de vervolgkeuzelijst.  Als de query drie kolommen retourneert, wordt de derde kolom gebruikt om de standaardselectie in die vervolgkeuzelijst aan te geven.  Deze kolom kan elk type zijn, maar de eenvoudigste is het gebruik van ol of numerieke typen, waarbij 0 vals is en 1 waar is.

 Als de kolom een tekenreekstype is, wordt null/lege tekenreeks als onwaar beschouwd en wordt elke andere waarde als waar beschouwd. Voor enkele selectie-vervolgkeuzeklassen wordt de eerste waarde met een werkelijke waarde gebruikt als de standaardselectie.  Voor meerdere selectievervolgkeuzewaarden worden alle waarden met een werkelijke waarde gebruikt als de standaard geselecteerde set. De items in de vervolgkeuzelijst worden weergegeven in de volgorde waarin de query rijen heeft geretourneerd. 

Laten we eens kijken naar de parameters die aanwezig zijn in het rapport Actieve gebruikers. Klik op het bewerkingssymbool naast **TimeRange**.

![Besturingselementen voor bewerkingsbewerking van de sectie Bewerking van bewerkingen van bewerkingen van bewerkingen van bewerkingen van bewerkingen van toepassingsstatistieken](./media/usage-workbooks/009-time-range.png)

Hiermee wordt het menu-item Parameter bewerken gestart:

![Besturingselementen voor bewerkingsbewerking van de sectie Bewerking van bewerkingen van bewerkingen van bewerkingen van bewerkingen van bewerkingen van toepassingsstatistieken](./media/usage-workbooks/010-time-range-edit.png)

De query maakt gebruik van een functie van de analytics query taal genaamd een **datatable** waarmee u een willekeurige tabel, vol met inhoud, uit de lucht! Bijvoorbeeld de volgende analysequery:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Genereert het resultaat:

![Besturingselementen voor bewerkingsbewerking van de sectie Bewerking van bewerkingen van bewerkingen van bewerkingen van bewerkingen van bewerkingen van toepassingsstatistieken](./media/usage-workbooks/011-data-table.png)

Een meer toepasselijk voorbeeld is het gebruik van een vervolgkeuzelijst om uit een reeks landen/regio's op naam te kiezen:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

De query geeft de resultaten als volgt weer:

![Land dropdown](./media/usage-workbooks/012-country-dropdown.png)

Dropdowns zijn ongelooflijk krachtige tools voor het aanpassen en maken van interactieve rapporten.

### <a name="time-range-parameters"></a>Parameters voor tijdbereik

Hoewel u uw eigen parameter voor het aangepaste tijdbereik maken via het type vervolgkeuzeparameter, u ook het parametertype out-of-box-tijdbereik gebruiken als u niet dezelfde mate van flexibiliteit nodig hebt. 

Parametertypen voor tijdsbereik hebben 15 standaardbereiken die van vijf minuten tot de laatste 90 dagen gaan. Er is ook een optie om aangepaste tijdbereikselectie toe te staan, waardoor de operator van het rapport expliciete start- en stopwaarden voor het tijdsbereik kan kiezen.

### <a name="resource-picker"></a>Resourcekiezer

Het parametertype resourcekiezer geeft u de mogelijkheid om uw rapport te gebruiken voor bepaalde typen resources. Een voorbeeld van vooraf gebouwde werkmap die gebruikmaakt van het type resourcekiezer, is de werkmap **Failure Insights.**

![Land dropdown](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Werkmappen opslaan en delen met uw team

Werkmappen worden opgeslagen in een Application Insights-bron, hetzij in de sectie **Mijn rapporten** die privé voor u is, hetzij in de sectie **Gedeelde rapporten** die toegankelijk is voor iedereen die toegang heeft tot de toepassingsstatistiekenbron. Als u alle werkmappen in de resource wilt weergeven, klikt u op de knop **Openen** op de actiebalk.

Een werkmap delen die momenteel in **Mijn rapporten**staat:

1. Klik **op Openen** op de actiebalk
2. Klik op de "..." knop naast de werkmap die u wilt delen
3. Klik **op Verplaatsen naar gedeelde rapporten**.

Als u een werkmap wilt delen met een koppeling of via e-mail, klikt u op **Delen** in de actiebalk. Houd er rekening mee dat ontvangers van de koppeling toegang tot deze bron in de Azure-portal nodig hebben om de werkmap te bekijken. Om bewerkingen uit te voeren, hebben ontvangers ten minste inzendermachtigingen voor de bron nodig.

Een koppeling aan een werkmap vastmaken aan een Azure-dashboard:

1. Klik **op Openen** op de actiebalk
2. Klik op de "..." knop naast de werkmap die u wilt vastmaken
3. Klik op **Vastmaken aan dashboard**.

## <a name="contributing-workbook-templates"></a>Werkmapsjablonen bijdragen

Heb je een geweldige werkmapsjabloon gemaakt en wil je deze delen met de community? Ga voor meer informatie naar onze [GitHub repo.](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)

## <a name="next-steps"></a>Volgende stappen
- Als u gebruikservaringen wilt inschakelen, begint u met het verzenden van [aangepaste gebeurtenissen](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) of [paginaweergaven.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)
- Als u al aangepaste gebeurtenissen of paginaweergaven verzendt, raadpleegt u de hulpprogramma's Voor gebruik om te leren hoe gebruikers uw service gebruiken.
    - [Gebruikers, sessies, gebeurtenissen](../../azure-monitor/app/usage-segmentation.md)
    - [Trechters](../../azure-monitor/app/usage-funnels.md)
    - [Retentie](../../azure-monitor/app/usage-retention.md)
    - [Gebruikersstromen](../../azure-monitor/app/usage-flows.md)
    - [Gebruikerscontext toevoegen](../../azure-monitor/app/usage-send-user-context.md)
