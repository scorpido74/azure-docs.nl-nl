---
title: Gegevens visualiseren in de Preview explorer - Azure Time Series Insights | Microsoft Documenten
description: Meer informatie over functies en opties die beschikbaar zijn in de Verkenner van De Voorbeelden van Inzichten in Azure Time-reeksen.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: c117510749abcf997e414371faa6dea1dc79ea7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75861758"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Explorer voor inzichten in Azure Time-reeksen

In dit artikel worden de verschillende functies en opties beschreven die beschikbaar zijn in de [demowebtoepassing](https://insights.timeseries.azure.com/preview/demo)Azure Time Series Insights Preview.

## <a name="prerequisites"></a>Vereisten

Als u aan de slag wilt met de Azure Time Series Insights Preview-verkenner, moet u het als:

* Zorg ervoor dat een Time Series Insights-omgeving is ingericht. Meer informatie over het inrichten van een instantie door de zelfstudie [van De Voorbeelden van Azure Time Series te](./time-series-insights-update-create-environment.md) lezen.
* [Geef gegevens toegang tot](./time-series-insights-data-access.md) de Time Series Insights-omgeving die u voor het account hebt gemaakt. U zowel anderen als uzelf toegang geven.
* Voeg een gebeurtenisbron toe aan de Time Series Insights-omgeving om gegevens naar de omgeving te pushen:
  * Meer informatie over [hoe u verbinding maken met een gebeurtenishub](./time-series-insights-how-to-add-an-event-source-eventhub.md) 
  * Meer informatie over [hoe u verbinding maken met een IoT-hub](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="explore-the-time-series-insights-preview-explorer"></a>Ontdek de Time Series Insights Preview explorer

De Azure Time Series Insights Preview-verkenner bestaat uit de volgende zeven elementen:

[![Overzicht van Time Series Insights Preview explorer](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Deelvenster Omgeving:](#1-environment-panel)geeft al uw Azure Time Series Insights-omgevingen weer.
1. [Navigatiebalk:](#2-navigation-bar)hiermee u schakelen tussen de **pagina's Analyseren** en **Modelleren.**
1. [Structuur en zoekpaneel hiërarchie:](#3-hierarchy-tree-and-search-panel)hiermee u specifieke gegevenselementen selecteren en zoeken die in kaart moeten worden gebracht.
1. [Tijdreeksen goed:](#4-time-series-well)toont al uw momenteel geselecteerde gegevenselementen.
1. [Grafiekpaneel:](#5-chart-panel)geeft uw huidige werkgrafiek weer.
1. [Tijdlijn:](#6-time-editor-panel)hiermee u de werktijd wijzigen.
1. [App-balk](#7-app-bar): Bevat uw gebruikersbeheeropties (zoals de huidige tenant) en stelt u in staat deze en taalinstellingen te wijzigen.


## <a name="1-environment-panel"></a>1. Milieupaneel

Het omgevingspaneel toont alle Time Series Insights-omgevingen waar toe u toegang hebt. De lijst bevat pay-as-you-go (Preview) omgevingen en S1/S2 omgevingen (Algemene beschikbaarheid). Selecteer eenvoudig de Time Series Insights-omgeving die u wilt gebruiken om er onmiddellijk naartoe te worden gebracht.

1. Selecteer de vervolgkeuzepijl naast de weergegeven omgeving.

   [![Milieupaneel](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Selecteer vervolgens de gewenste omgeving.

## <a name="2-navigation-bar"></a>2. Navigatiebalk

  [![De navigatiebalk](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

Gebruik de navigatiebalk om tussen twee weergaven te kiezen:

* **Analyseren:** gebruik het om uitgebreide analyses uit te voeren op uw gemodelleerde of niet-gemodelleerde tijdreeksgegevens.
* **Model:** gebruik het om nieuwe Time Series Insights Preview-typen, hiërarchieën en instanties naar uw Time Series Insights-model te pushen.

### <a name="model-authoring"></a>Modelauthoring

De Azure Time Series Insights Preview ondersteunt volledige beheer-, lees-, update- en verwijderbewerkingen (CRUD) op uw Tijdreeksmodel.

[![Het modelzoekpaneel](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Type timeseries-model:** u typen Time Series Insights gebruiken om variabelen of formules voor het uitvoeren van berekeningen te definiëren. Ze zijn gekoppeld aan een bepaalde Time Series Insights-instantie. Een type kan een of meer variabelen hebben.
* **Tijdreeksmodelhiërarchie**: Hiërarchieën zijn systematische organisaties van uw gegevens. Hiërarchieën geven de relaties tussen verschillende entiteiten weer in uw Time Series Insights-gegevens.
* **Instantie Time Series Model**: Instanties zijn de tijdreeksen zelf. In de meeste gevallen zijn dit de **DeviceID** of **AssetID**, de unieke id van het item in de omgeving.

Lees [Times Series Models](./time-series-insights-update-tsm.md)voor meer informatie over het Time Series Model.

## <a name="3-hierarchy-tree-and-search-panel"></a>3. Hiërarchiestructuur en zoekpaneel

Met de hiërarchiestructuur en het zoekpaneel u eenvoudig zoeken en navigeren in de hiërarchie [van het tijdreeksmodel](./time-series-insights-update-tsm.md) om de specifieke tijdreeksexemplaren te vinden die u in uw grafiek wilt weergeven. Wanneer u uw instanties selecteert, worden ze niet alleen aan de huidige grafiek toegevoegd, maar worden ze ook goed aan de gegevens toegevoegd. 

[![Structuur hiërarchie en zoekpaneel](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

In het deelvenster Zoekresultaten u uw resultaten ook bekijken in een hiërarchieweergave of een lijstweergave, zodat u gemakkelijk de exemplaren vinden die u wilt weergeven.
 
## <a name="4-time-series-well"></a>4. Tijdreeks goed

De put geeft instantievelden en andere metagegevens weer die zijn gekoppeld aan geselecteerde Time Series Insights-exemplaren. Door de selectievakjes aan de rechterkant in te schakelen, u specifieke exemplaren in de huidige grafiek verbergen of weergeven. 

  [![De Preview goed](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

U specifieke gegevenselementen uit uw huidige gegevens goed verwijderen door het rode **besturingselement Verwijderen** (prullenbak) aan de linkerkant van het element te selecteren. Met de put u ook bepalen hoe elk element in de grafiek wordt weergegeven. U ervoor kiezen om min/max-schaduwen, gegevenspunten toe te voegen, het element in de tijd te verschuiven en de instantie op een getrapte manier te visualiseren. 

Bovendien u met de verkenningscontrole eenvoudig tijdverschuivingen maken en plots verspreiden.  

  [![Indelingsopties](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Als het volgende bericht wordt weergegeven, heeft de instantie geen gegevens gedurende de geselecteerde tijdspanne. Als u het probleem wilt oplossen, verhoogt u de tijdspanne of bevestigt u dat de instantie gegevens pusht.
>
> ![Geen gegevensmelding](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. Deelvenster Grafiek

Met de grafiek u tijdreeksexemplaren weergeven als lijnen. U het configuratiescherm, het gegevensmodel en het configuratiescherm van de tijdspanne samenvouwen door op de webbesturingselementen te klikken om de grafiek groter te maken. 

  [![Overzicht van een voorbeeld van grafiek](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **Grafiektype:** bepaalt welke gegevenselementen beschikbaar zijn voor visualisatie.

1. **Intervalgrootte:** met het schuifregelaar gereedschap Intervalgrootte u in- en uitzoomen op intervallen gedurende dezelfde tijdspanne. Dit biedt een nauwkeurigere controle van de beweging tussen grote segmenten van de tijd die vloeiende trends laten zien tot segmenten zo klein als de milliseconde, zodat u gedetailleerde, hoge resolutie delen van uw gegevens controleren. Het standaarduitgangspunt van de schuifregelaar is ingesteld als de meest optimale weergave van de gegevens uit uw selectie; balanceren resolutie, querysnelheid en granulariteit.

1. **Zoom en pan:** Selecteer dit besturingselement om in te zoomen en de grafiek te pannen.

1. **Y-asbesturingselement**: Cycli door de beschikbare y-asweergaveopties:

    * `Stacked`: Elke regel heeft een individuele Y-as.
    * `Overlap`: Gebruik deze om meerdere lijnen op dezelfde Y-as te stapelen, waarbij de Y-asgegevens veranderen op basis van de geselecteerde lijn.
    * `Shared`: Alle Y-asgegevens worden samen weergegeven.

1. **Markeringselement:** het momenteel geselecteerde gegevenselement en de bijbehorende details.

U verder inzoomen op een specifiek gegevenssegment door **links op** een gegevenspunt in de huidige grafiek te klikken terwijl u de muis ingedrukt houdt en vervolgens het geselecteerde gebied naar het eindpunt van uw keuze sleept. **Klik met** de rechtermuisknop op het blauwe, geselecteerde gebied en selecteer **Zoomen** zoals hieronder wordt weergegeven. U ook de telemetriegebeurtenissen in de geselecteerde tijdspanne bekijken en downloaden.

  [![Voorbeeld van grafiekzoom](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

Nadat u de actie **Zoom hebt** uitgevoerd, wordt de geselecteerde gegevensset weergegeven. Selecteer het formaatbesturingselement dat u door de drie y-asrepresentaties van uw Time Series Insights-gegevens wilt doorlopen.

  [![Voorbeeld van grafiek y-as](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

Hier wordt een voorbeeld van een **overlappend diagram** gegeven:

  [![Overlappende grafiekoptie](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

Met de knop **Meer acties** wordt de optie Downloaden **als CSV**, Verbinding maken met Power **BI,** **Grafiekgegevens weergeven als tabel**en **Onbewerkte gebeurtenissen** verkennen weergegeven.

  [![Meer acties optie](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

Meer informatie over de optie **Verbinden met Power BI** in de native Power [BI-connector van Time Series Insights](concepts-power-bi.md).

## <a name="6-time-editor-panel"></a>6. Tijdeditorpanel

Wanneer u met Time Series Insights werkt, selecteert u eerst een tijdspanne. De geselecteerde tijdspanne bepaalt de gegevensset die beschikbaar is voor manipulatie met de time series Insights-updatewidgets.

  [![Venster Time selection](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> Een gedeelte van de tijdlijn wordt gemarkeerd in een oranje of oranje kleur om de reeks gegevens aan te geven die beschikbaar is in warme opslag.

De volgende webbesturingselementen zijn beschikbaar in de Time Series Insights-update voor het selecteren van uw werktijdspanne. 

  [![Exploratie goed controle](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **Schuifregelaar binnendatumbereik:** gebruik de twee eindpuntbesturingselementen door ze over de gewenste tijdspanne te slepen. Dit bereik van de binnendatum wordt beperkt door het schuifregelaarbesturingselement voor de buitenste datumbereik.

1. **Knoppen voor datumbereik vergroten en verkleinen:** verhoog of verlaag uw tijdspanne door een van beide knoppen te selecteren voor het gewenste interval.

1. **Besturingselement voor het invouwen**van de tijdspanne: met dit webbesturingselement u alle besturingselementen verbergen, behalve het schuifregelaargereedschap binnendatumbereik.

1. **Schuifregelaars voor het buitenste datumbereik**: Gebruik de eindpuntbesturingselementen om het buitenste datumbereik te selecteren, dat beschikbaar is voor uw besturingselement voor het binnenste datumbereik.

1. **Besturingselement tijdsafstandschuifregelaar**: Gebruik deze om snel te schakelen tussen vooraf ingestelde tijdspanneselecties, zoals de laatste **30 minuten,** de **laatste 12 uur**of een **aangepast bereik**. Als u deze waarde wijzigt, worden ook de beschikbare intervalbereiken gewijzigd die worden besproken in het schuifregelaargereedschap intervalgrootte.

   [![Van en naar het selectiepaneel](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. App-balk

Het navigatiepaneel Time Series Insights Preview wordt boven aan uw Time Series Insights-app weergegeven. Het biedt de volgende functionaliteiten:

### <a name="current-session-share-link-control"></a>Beheer van huidige koppelingskoppeling voor sessiedelen

  [![Pictogram Delen](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

Selecteer het nieuwe pictogram **Delen** om een URL-koppeling met uw team te delen.

  [![Uw instantie-URL delen](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>Tenantsectie

  [![Tenantselectie](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* Hiermee geeft u uw huidige aanmeldingsaccountgegevens van Time Series Insights weer.
* Gebruik het om te schakelen tussen de beschikbare Time Series Insights-thema's.
* Gebruik deze om de preview [demo web-app](https://insights.timeseries.azure.com/preview/demo)te bekijken.

### <a name="theme-selection"></a>Themaselectie

Als u een nieuw thema wilt selecteren, selecteert u het profielpictogram in de rechterbovenhoek. Selecteer vervolgens **Thema wijzigen**.

  [![Themaselectie](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> Taalselectie is ook beschikbaar door uw profielpictogram te selecteren.

Azure Time Series Insights Preview ondersteunt twee thema's:

* **Licht thema:** het standaardthema dat in dit document wordt weergegeven.
* **Donker thema**: Maakt de ontdekkingsreiziger zoals hier getoond:

  [![Geselecteerd donker thema](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2-omgevingscontroles

### <a name="preview-terms-panel"></a>Deelvenster Van het voorbeeld van het deelvenster Termen bekijken

Deze sectie is alleen van toepassing op bestaande S1/S2-omgevingen die proberen de verkenner in de bijgewerkte gebruikersinterface te gebruiken. Misschien wilt u het algemeen beschikbare product en Voorbeeld in combinatie gebruiken. We hebben een aantal functionaliteit van de bestaande gebruikersinterface toegevoegd aan de bijgewerkte explorer, maar u de volledige UI-ervaring voor de S1/S2-omgeving krijgen in de bestaande Time Series Insights-verkenner. 

In plaats van de hiërarchie wordt het deelvenster Termen van Time Series Insights weergegeven. Met het deelvenster Termen u query's in uw omgeving definiëren. Gebruik het om ook gegevens te filteren op basis van een predicaat.

  [![Waar het querypaneel](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

In het configuratiescherm van Time Series Insights Preview worden de volgende parameters weergegeven:

**Waar**: Gebruik de waarste clausule om uw gebeurtenissen snel te filteren met behulp van de set operands die in de volgende tabel worden vermeld. Als u een zoekopdracht uitvoert door een operand te selecteren, wordt het predicaat automatisch bijgewerkt op basis van die zoekopdracht. Ondersteunde operand types omvatten de volgende:

| Bewerking | Ondersteunde typen   | Opmerkingen |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Dubbel, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | Alle operands moeten van hetzelfde type zijn of NULL-constant zijn. |
| `HAS` | Tekenreeks | Alleen constante string liters zijn toegestaan aan de rechterkant. Lege tekenreeks en NULL zijn niet toegestaan. |

Lees [Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)voor meer informatie over ondersteunde querybewerkingen en gegevenstypen.

### <a name="examples-of-where-clauses"></a>Voorbeelden van waar clausules

  [![Wanneer clausulevoorbeelden](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Meting:** een vervolgkeuzelijst met alle numerieke kolommen **(Doubles)** die u gebruiken als elementen voor uw huidige grafiek.

**Splitst door**: In deze vervolgkeuzelijst worden alle beschikbare categorische kolommen (Tekenreeksen) in uw model weergegeven waarop u uw gegevens groeperen. U maximaal vijf termen toevoegen om op dezelfde x-as te bekijken. Voer de gewenste parameters in en selecteer **Toevoegen** om een nieuwe term toe te voegen.

  [![Opgevraagde en gefilterde weergave](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

U elementen in het grafiekpaneel weergeven en verbergen door het zichtbare pictogram te selecteren, zoals in de volgende afbeelding wordt weergegeven. Als u query's volledig wilt verwijderen, selecteert u de rode **X**.

  [![Een opgevraagde en gefilterde optie annuleren](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [opslag en invallen vindt](./time-series-insights-update-storage-ingress.md) u in de Preview van Azure Time Series Insights.

- Lees het document Van Time Series Insights Preview over [gegevensmodellering](./time-series-insights-update-tsm.md).

- Meer informatie over [het diagnosticeren en oplossen van](./time-series-insights-update-how-to-troubleshoot.md) uw Time Series Insights-exemplaar.
