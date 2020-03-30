---
title: "Interactieve rapporten maken: Azure Monitor voor VM's met werkmappen"
description: Vereenvoudig complexe rapportage met vooraf gedefinieerde en aangepaste geparameteriseerde werkmappen voor Azure Monitor voor VM's.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: a6ab126c3a5b0d2a82b17fac42dcc9e20f6aba3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480450"
---
# <a name="create-interactive-reports-azure-monitor-for-vms-with-workbooks"></a>Interactieve rapporten maken: Azure Monitor voor VM's met werkmappen

Werkmappen combineren tekst, [logboekquery's,](../log-query/query-language.md)statistieken en parameters in uitgebreide interactieve rapporten. Werkmappen kunnen worden bewerkt door andere teamleden die toegang hebben tot dezelfde Azure-resources.

Werkmappen zijn handig voor scenario's zoals:

* Het verkennen van het gebruik van uw virtuele machine wanneer u niet weet wat de statistieken van belang op voorhand: CPU-gebruik, schijfruimte, geheugen, netwerk afhankelijkheden, enz. In tegenstelling tot andere hulpprogramma's voor gebruiksanalyse, u met werkmappen meerdere soorten visualisaties en analyses combineren, waardoor ze ideaal zijn voor dit soort verkenning en verkenning in vrije vorm.
* Uw team uitleggen hoe een recent ingerichte VM presteert, door statistieken weer te geven voor belangrijke tellers en andere logboekgebeurtenissen.
* Het delen van de resultaten van een formaat experiment van uw VM met andere leden van uw team. U de doelen voor het experiment met tekst uitleggen en vervolgens elke gebruiksmetrische en analysequery's weergeven die worden gebruikt om het experiment te evalueren, samen met duidelijke call-outs voor de vraag of elke statistiek boven of onder het doel was.
* Het rapporteren van de impact van een storing op het gebruik van uw VM, het combineren van gegevens, tekstuitleg en een discussie over de volgende stappen om uitval in de toekomst te voorkomen.

In de volgende tabel worden de werkmappen samengevat die Azure Monitor voor VM's bevat om u op weg te helpen.

| Werkmap | Beschrijving | Bereik |
|----------|-------------|-------|
| Prestaties | Biedt een aanpasbare versie van onze toplijst- en grafiekenweergave in één werkmap die gebruikmaakt van alle prestatiemeteritems van Log Analytics die u hebt ingeschakeld.| Op schaal |
| Prestatiemeteritems | Een top-n-grafiekweergave over een brede set prestatiemeteritems. | Op schaal |
| Verbindingen | Verbindingen bieden een diepgaand beeld van de inkomende en uitgaande verbindingen van uw bewaakte VM's. | Op schaal |
| Actieve poorten | Bevat een lijst met de processen die zijn gebonden aan de poorten op de bewaakte VM's en hun activiteit in het gekozen tijdsbestek. | Op schaal |
| Open poorten | Geeft het aantal poorten dat is geopend op uw bewaakte VM's en de details op die geopende poorten. | Op schaal |
| Mislukte verbindingen | Geef het aantal mislukte verbindingen weer op de bewaakte VM's, de fouttrend en als het percentage fouten in de loop van de tijd toeneemt. | Op schaal |
| Beveiliging en audit | Een analyse van uw TCP/IP-verkeer dat rapporteert over algemene verbindingen, schadelijke verbindingen, waarbij de IP-eindpunten zich wereldwijd bevinden.  Om alle functies in te schakelen, moet u beveiligingsdetectie inschakelen. | Op schaal |
| TCP Traffic | Een gerangschikt rapport voor uw bewaakte VM's en hun verzonden, ontvangen en totale netwerkverkeer in een raster en weergegeven als een trendlijn. | Op schaal |
| Vergelijking van verkeer | Met deze werkmappen u netwerkverkeerstrends voor één machine of een groep machines vergelijken. | Op schaal |
| Prestaties | Biedt een aanpasbare versie van onze prestatieweergave die gebruikmaakt van alle prestatiemeteritems van Log Analytics die u hebt ingeschakeld. | Eén VM | 
| Verbindingen | Verbindingen bieden een diepgaand overzicht van de inkomende en uitgaande verbindingen van uw vm. | Eén VM |
 
## <a name="creating-a-new-workbook"></a>Een nieuwe werkmap maken

Een werkmap bestaat uit secties die bestaan uit onafhankelijk bewerkbare grafieken, tabellen, tekst en invoerbesturingselementen. Om werkmappen beter te begrijpen, beginnen we met het openen van een sjabloon en lopen we door het maken van een aangepaste werkmap. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer **Virtuele machines**.

3. Selecteer een VM in de lijst.

4. Selecteer op de VM-pagina in de sectie **Monitoring** de optie **Insights**.

5. Selecteer op de pagina VM-statistieken het tabblad **Prestaties** of **Kaarten** en selecteer **Werkmappen weergeven** in de koppeling op de pagina. Selecteer Ga naar Galerie **in**de vervolgkeuzelijst .

    ![Schermafbeelding van de vervolgkeuzelijst Werkmap](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Hiermee wordt de werkmapgalerie gestart met een aantal vooraf gebouwde werkmappen om u op weg te helpen.

7. Maak een nieuwe werkmap door **Nieuw te**selecteren .

    ![Schermafbeelding van werkmapgalerie](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Werkmapsecties bewerken

Werkmappen hebben twee modi: **bewerkingsmodus**en **leesmodus**. Wanneer een nieuwe werkmap voor het eerst wordt gestart, wordt deze geopend in **de bewerkingsmodus.** Het toont alle inhoud van de werkmap, inclusief alle stappen en parameters die anders verborgen zijn. **De leesmodus** biedt een vereenvoudigde weergave van rapportstijl. Leesmodus u de complexiteit die ging in het maken van een rapport, terwijl nog steeds met de onderliggende mechanica slechts een paar klikken verwijderd wanneer dat nodig is voor wijziging abstract.

![Besturingselementen voor bewerkingsbesturingselementen voor de sectie bewerken van de sectie Voor VMs-werkmappen](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Wanneer u klaar bent met het bewerken van een sectie, klikt u op **Gereed bewerken** in de linkerbenedenhoek van de sectie.

2. Als u een duplicaat van een sectie wilt maken, klikt u op het pictogram **Deze sectie klonen.** Het maken van dubbele secties is een geweldige manier om een query te herhalen zonder eerdere iteraties te verliezen.

3. Als u een sectie in een werkmap wilt omhoog wilt verplaatsen, klikt u op het pictogram **Omhoog** of **Omlaag.**

4. Als u een sectie permanent wilt verwijderen, klikt u op het pictogram **Verwijderen.**

## <a name="adding-text-and-markdown-sections"></a>Secties voor tekst en Markdown toevoegen

Door koppen, uitleg en commentaar toe te voegen aan uw werkmappen, u een reeks tabellen en grafieken omzetten in een verhaal. Tekstsecties in werkmappen ondersteunen de [syntaxis van markeren](https://daringfireball.net/projects/markdown/) voor tekstopmaak, zoals koppen, vetgedrukte, cursief en lijsten met opsommingstekens.

Als u een tekstsectie aan uw werkmap wilt toevoegen, gebruikt u de knop **Tekst toevoegen** onder aan de werkmap of onder aan een sectie.

## <a name="adding-query-sections"></a>Secties voor query's toevoegen

![Sectie Query in werkmappen](media/vminsights-workbooks/005-workbook-query-section.png)

Als u querysectie wilt toevoegen aan uw werkmap, gebruikt u de knop **Query toevoegen** onder aan de werkmap of onder aan een sectie.

Querysecties zijn zeer flexibel en kunnen worden gebruikt om vragen te beantwoorden zoals:

* Hoe was mijn CPU-gebruik in dezelfde periode als een toename van het netwerkverkeer?
* Wat was de trend in de beschikbare schijfruimte in de afgelopen maand?
* Hoeveel netwerkverbindingsfouten heeft mijn VM de afgelopen twee weken ervaren? 

U bent ook niet alleen beperkt tot query's vanuit de context van de virtuele machine waarvan u de werkmap hebt gestart. U query's uitvoeren op meerdere virtuele machines en Log Analytics-werkruimten, zolang u toegang hebt tot deze bronnen.

Gegevens opnemen van andere Log Analytics-werkruimten of uit **workspace** een specifieke Application Insights-app met behulp van de werkruimte-id. Voor meer informatie over cross-resource query's, raadpleegt u de [officiële richtlijnen](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Geavanceerde analytische query-instellingen

Elke sectie heeft zijn eigen geavanceerde instellingen, ![die toegankelijk zijn](media/vminsights-workbooks/006-settings.png) via de instellingen Workbooks sectie bewerken besturingselementen pictogram gelegen aan de rechterkant van de knop **Parameters toevoegen.**

![Besturingselementen voor bewerkingsbesturingselementen voor de sectie bewerken van de sectie Voor VMs-werkmappen](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Aangepaste breedte**    | Hiermee maakt u van een item een willekeurige grootte, zodat u veel items op één regel plaatsen, zodat u uw grafieken en tabellen beter ordenen in rijke interactieve rapporten.  |
| **Voorwaardelijk zichtbaar** | Geef op om stappen te verbergen op basis van een parameter in de leesmodus. |
| **Een parameter exporteren**| Laat een geselecteerde rij in het raster of de grafiek ertoe leiden dat latere stappen waarden wijzigen of zichtbaar worden.  |
| **Query weergeven wanneer deze niet wordt bewerkt** | Hiermee geeft u de query boven de grafiek of tabel weer, zelfs in de leesmodus.
| **Knop Openen weergeven in analytics wanneer u niet bewerkt wordt** | Hiermee voegt u het blauwe Analytics-pictogram toe aan de rechterhoek van de grafiek om toegang met één klik mogelijk te maken.|

De meeste van deze instellingen zijn vrij intuïtief, maar om te begrijpen **Een parameter exporteren** is het beter om een werkmap te onderzoeken die gebruik maakt van deze functionaliteit.

Een van de vooraf gebouwde werkmappen - **TCP Traffic**, biedt informatie over verbindingsstatistieken van een VM.

Het eerste deel van de werkmap is gebaseerd op logboekquerygegevens. De tweede sectie is ook gebaseerd op logquerygegevens, maar het selecteren van een rij in de eerste tabel zal de inhoud van de grafieken interactief bijwerken:

![Besturingselementen voor bewerkingsbesturingselementen voor de sectie bewerken van de sectie Voor VMs-werkmappen](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

Het gedrag is mogelijk door het gebruik van de **Optie Waarin een item is geselecteerd, exporteert u een geavanceerde parameterinstellingen** die zijn ingeschakeld in de logboekquery van de tabel.

![Besturingselementen voor bewerkingsbesturingselementen voor de sectie bewerken van de sectie Voor VMs-werkmappen](media/vminsights-workbooks/009-settings-export.png)

De tweede logboekquery maakt vervolgens gebruik van de geëxporteerde waarden wanneer een rij is geselecteerd om een set waarden te maken die vervolgens worden gebruikt door de sectiekop en grafieken. Als er geen rij is geselecteerd, worden de sectiekop en grafieken verborgen. 

De verborgen parameter in de tweede sectie gebruikt bijvoorbeeld de volgende verwijzing uit de rij die in het raster is geselecteerd:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Secties voor metrische gegevens toevoegen

Metrische gegevens geven u volledige toegang tot azure monitor-metrische gegevens in uw interactieve rapporten. In Azure Monitor voor VM's bevatten de vooraf gebouwde werkmappen doorgaans analytische querygegevens in plaats van metrische gegevens.  U ervoor kiezen om werkmappen met metrische gegevens te maken, zodat u optimaal profiteren van het beste van beide functies op één plek. U hebt ook de mogelijkheid om metrische gegevens op te halen uit bronnen in een van de abonnementen waartoe u toegang hebt.

Hier is een voorbeeld van virtuele machinegegevens die in een werkmap worden getrokken om een rastervisualisatie van CPU-prestaties te bieden:

![Besturingselementen voor bewerkingsbesturingselementen voor de sectie bewerken van de sectie Voor VMs-werkmappen](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Secties voor parameters toevoegen

Met werkmapparameters u waarden in de werkmap wijzigen zonder dat u de query- of tekstsecties handmatig hoeft te bewerken. Dit verwijdert de vereiste om de onderliggende analytics-querytaal te begrijpen en vergroot de potentiële doelgroep van rapportage op basis van werkmap sterk.

De waarden van parameters worden vervangen in query-, tekst- of andere parametersecties door de naam van de parameter in accolades te plaatsen, zoals ``{parameterName}``. Parameternamen zijn beperkt tot vergelijkbare regels als JavaScript-id's, alfabetische tekens of underscores, gevolgd door alfanumerieke tekens of underscores. **A1** is bijvoorbeeld toegestaan, maar **1a** is niet toegestaan.

Parameters zijn lineair, vanaf de bovenkant van een werkmap en stromen naar latere stappen.  Parameters die later in een werkmap worden gedeclareerd, kunnen parameters overschrijven die eerder zijn gedeclareerd. Hiermee kunnen ook parameters die query's gebruiken om toegang te krijgen tot de waarden van eerder gedefinieerde parameters. Binnen de stap van een parameter zelf zijn parameters ook lineair, van links naar rechts, waarbij parameters rechts kunnen afhangen van een parameter die eerder in diezelfde stap is gedeclareerd.
 
Er zijn vier verschillende soorten parameters, die momenteel worden ondersteund:

|                  |      |
| ---------------- |:-----|
| **Tekst**    | Hiermee kan de gebruiker een tekstvak bewerken en u optioneel een query leveren om de standaardwaarde in te vullen. |
| **Vervolgkeuzelijst** | Hiermee kan de gebruiker kiezen uit een set waarden. |
| **Tijdbereikkiezer**| Hiermee kan de gebruiker kiezen uit een vooraf gedefinieerde set tijdbereikwaarden of kiezen uit een aangepast tijdsbereik.|
| **Resourcekiezer** | Hiermee kan de gebruiker kiezen uit de bronnen die voor de werkmap zijn geselecteerd.|

### <a name="using-a-text-parameter"></a>Een tekstparameter gebruiken

De waarde die een gebruiker in het tekstvak typt, wordt direct in de query vervangen, zonder te ontsnappen of te citeren. Als de waarde die u nodig hebt een tekenreeks is, moet de query aanhalingstekens rond de parameter hebben (zoals **{parameter}'**).

Met de parameter tekst kan de waarde in een tekstvak overal worden gebruikt. Het kan een tabelnaam, kolomnaam, functienaam, operator, enz.  Het tekstparametertype heeft een instelling **Standaardwaarde ophalen van analysequery**, waarmee de auteur van de werkmap een query kan gebruiken om de standaardwaarde voor dat tekstvak in te vullen.

Wanneer u de standaardwaarde van een logboekquery gebruikt, wordt alleen de eerste waarde van de eerste rij (rij 0, kolom 0) gebruikt als standaardwaarde. Daarom is het raadzaam om uw query te beperken tot slechts één rij en één kolom. Alle andere gegevens die door de query worden geretourneerd, worden genegeerd. 

Welke waarde de query retourneert, wordt direct vervangen door geen ontkomen of aanhalen. Als de query geen rijen retourneert, is het resultaat van de parameter een lege tekenreeks (als de parameter niet vereist is) of ongedefinieerd (als de parameter vereist is).

### <a name="using-a-drop-down"></a>Een vervolgkeuzelijst gebruiken

Met het parametertype vervolgkeuzelijst u een vervolgkeuzebesturingselement maken, zodat u een of meerdere waarden selecteren.

De vervolgkeuzelijst wordt ingevuld door een logboekquery of JSON. Als de query één kolom retourneert, zijn de waarden in die kolom zowel de waarde als het label in het vervolgkeuzebesturingselement. Als de query twee kolommen retourneert, is de eerste kolom de waarde en is de tweede kolom het label dat wordt weergegeven in de vervolgkeuzelijst. Als de query drie kolommen retourneert, wordt de derde kolom gebruikt om de standaardselectie in die vervolgkeuzelijst aan te geven. Deze kolom kan elk type zijn, maar de eenvoudigste is het gebruik van ol of numerieke typen, waarbij 0 vals is en 1 waar is.

Als de kolom een tekenreekstype is, wordt null/lege tekenreeks als onwaar beschouwd en wordt elke andere waarde als waar beschouwd. Voor enkele selectievervolgkeuzewaarden wordt de eerste waarde met een werkelijke waarde gebruikt als standaardselectie.  Voor meerdere vervolgkeuzevervolgkeuzewaarden worden alle waarden met een werkelijke waarde gebruikt als de standaardgeselecteerde set. De items in de vervolgkeuzelijst worden weergegeven in de volgorde waarin de query rijen heeft geretourneerd. 

Laten we eens kijken naar de parameters die aanwezig zijn in het rapport Verbindingenoverzicht. Klik op het bewerkingssymbool naast **Richting**.

![Besturingselementen voor bewerkingsbesturingselementen voor de sectie bewerken van de sectie Voor VMs-werkmappen](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Hiermee wordt het **menu-item Parameter bewerken** gestart.

![Besturingselementen voor bewerkingsbesturingselementen voor de sectie bewerken van de sectie Voor VMs-werkmappen](media/vminsights-workbooks/012-workbook-edit-parameter.png)

Met de JSON u een willekeurige tabel genereren die is gevuld met inhoud. De volgende JSON genereert bijvoorbeeld twee waarden in de vervolgkeuzelijst:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Een meer toepasselijk voorbeeld is het gebruik van een vervolgkeuzelijst om te kiezen uit een set prestatiemeteritems op naam:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

De query geeft de resultaten als volgt weer:

![Perf teller dropdown](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Drop-downs zijn ongelooflijk krachtige tools voor het aanpassen en maken van interactieve rapporten.

### <a name="time-range-parameters"></a>Parameters voor tijdbereik

Hoewel u uw eigen parameter voor het aangepaste tijdbereik maken via het type vervolgkeuzeparameter, u ook het parametertype out-of-box-tijdbereik gebruiken als u niet dezelfde mate van flexibiliteit nodig hebt. 

Parametertypen voor tijdsbereik hebben 15 standaardbereiken die van vijf minuten tot de laatste 90 dagen gaan. Er is ook een optie om aangepaste tijdbereikselectie toe te staan, waardoor de operator van het rapport expliciete start- en stopwaarden voor het tijdsbereik kan kiezen.

### <a name="resource-picker"></a>Resourcekiezer

Het parametertype resourcekiezer geeft u de mogelijkheid om uw rapport te gebruiken voor bepaalde typen resources. Een voorbeeld van een vooraf gebouwde werkmap die gebruikmaakt van het type resourcekiezer, is de werkmap **Prestaties.**

![Vervolgkeuzelijst voor werkruimten](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Werkmappen opslaan en delen met uw team

Werkmappen worden opgeslagen in een Logboekanalysewerkruimte of een bron voor virtuele machines, afhankelijk van hoe u toegang krijgt tot de werkmappengalerie. De werkmap kan worden opgeslagen in de sectie **Mijn rapporten** die privé voor u is of in de sectie **Gedeelde rapporten** die toegankelijk is voor iedereen die toegang heeft tot de bron. Als u alle werkmappen in de resource wilt weergeven, klikt u op de knop **Openen** op de actiebalk.

Een werkmap delen die momenteel in **Mijn rapporten**staat:

1. Klik **op Openen** op de actiebalk
2. Klik op de "..." knop naast de werkmap die u wilt delen
3. Klik **op Verplaatsen naar gedeelde rapporten**.

Als u een werkmap wilt delen met een koppeling of via e-mail, klikt u op **Delen** in de actiebalk. Houd er rekening mee dat ontvangers van de koppeling toegang tot deze bron in de Azure-portal nodig hebben om de werkmap te bekijken. Om bewerkingen uit te voeren, hebben ontvangers ten minste inzendermachtigingen voor de bron nodig.

Een koppeling aan een werkmap vastmaken aan een Azure-dashboard:

1. Klik **op Openen** op de actiebalk
2. Klik op de "..." knop naast de werkmap die u wilt vastmaken
3. Klik op **Vastmaken aan dashboard**.

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure VM Performance weergeven](vminsights-performance.md)om beperkingen en algemene VM-prestaties te identificeren.

- Zie [Azure Monitor for VM-toewijzing weergeven voor](vminsights-maps.md)meer informatie over gedetecteerde toepassingsafhankelijkheden.
