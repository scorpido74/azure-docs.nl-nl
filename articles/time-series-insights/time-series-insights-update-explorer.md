---
title: Gegevens visualiseren in de Verkenner van Azure Time Series Insights Preview | Microsoft Docs
description: In dit artikel worden de functies en opties die beschikbaar zijn in de web-app van Azure Time Series Insights Preview explorer beschreven.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: a0d8f7cdace8d3e810dfcda6e54df5823840307c
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034328"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Gegevens visualiseren in de Preview-versie van de Verkenner

In dit document worden de functies en interface van de gebruikers interface en de gebruikers ervaring beschreven van de preview- [Web-app](https://insights.timeseries.azure.com/preview/demo)voor het Azure time series Insights voorbeeld. In het bijzonder wordt de indeling besproken van het gehoste voor beeld, opties voor het aanpassen van de interface en navigatie door de gegeven demo.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan met de Azure Time Series Insights preview Explorer, moet u:

* Een Time Series Insights-omgeving instellen hebt. Als u meer wilt weten over het inrichten van een exemplaar, probeert u de [Azure time series Insights preview](./time-series-insights-update-create-environment.md) -zelf studie.
* [Gegevens toegang bieden](./time-series-insights-data-access.md) tot de time series Insights omgeving die u voor het account hebt gemaakt. U kunt toegang tot andere ook over uzelf opgeven.
* Voeg een gebeurtenis bron toe aan de Time Series Insights omgeving om gegevens naar de omgeving te pushen:
  * Meer informatie [over hoe u verbinding maakt met een event hub](./time-series-insights-how-to-add-an-event-source-eventhub.md).
  * Meer informatie [over het maken van verbinding met een IOT-hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="learn-about-the-preview-explorer"></a>Meer informatie over de preview Explorer

De Verkenner van Azure Time Series Insights Preview bestaat uit de volgende elementen:

[![The Explorer-weer gave](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">Omgevings paneel</a>: Hiermee worden uw Azure Time Series Insights omgevingen weer gegeven.
- <a href="#navigation-menu">Navigatie menu</a>: Gebruik deze optie om te scha kelen tussen de pagina's **analyseren** en **model** .
- <a href="#hierarchy-tree">Hiërarchie structuur</a>: Gebruik deze optie om specifiek model en gegevens elementen te selecteren die moeten worden gediagrameerd.
- <a href="#preview-well">Goed tijd reeks</a>: Hiermee worden de momenteel geselecteerde gegevens elementen weer gegeven in tabel indeling met kleur codering.
- <a href="#preview-chart">Deel venster grafiek</a>: Hiermee wordt uw huidige werk grafiek weer gegeven.
- <a href="#time-editor-panel">Tijd lijn</a>: Gebruik dit om uw werk periode te wijzigen.
- <a href="#navigation-panel">App-balk</a>: Bevat uw opties voor gebruikers beheer, zoals de huidige Tenant. U kunt deze gebruiken om het thema en de taal instellingen te wijzigen.

## <a name="environment-drop-down-list"></a>Vervolg keuzelijst omgeving

In de vervolg keuzelijst omgeving worden alle Time Series Insights omgevingen weer gegeven waartoe u toegang hebt. De lijst bevat omgevingen met betalen per gebruik, zoals de preview-versie van Time Series Insights. De lijst bevat ook een S1/S2-omgeving, die algemeen beschikbaar is.

1. Selecteer de vervolg keuze pijl naast de weer gegeven omgeving.

   [![The-configuratie scherm](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Selecteer vervolgens de gewenste omgeving.

## <a name="navigation-menu"></a>Navigatiemenu

  [navigatie menu ![The](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

Gebruik het navigatie menu om twee weer gaven te selecteren:

* **Analyseren**: Gebruik dit diagram om uitgebreide analyses uit te voeren op uw gemodelleerde of niet-gemodelleerde tijdreeks gegevens.
* **Model**: Gebruik it om nieuwe Time Series Insights preview-typen,-hiërarchieën en-instanties naar uw Time Series Insights model te pushen.

## <a name="hierarchy-tree"></a>Hiërarchie structuur

In de hiërarchie structuur worden geselecteerde gegevens elementen weer gegeven, waaronder modellen, specifieke apparaten en Sens oren op uw apparaten.

### <a name="model-search-panel"></a>Het zoek paneel model

U kunt het deel venster voor het zoeken naar modellen gebruiken om eenvoudig uw time series-model hiërarchie te doorzoeken en te navigeren om de specifieke time series-exemplaren te vinden die u wilt weer geven in uw grafiek. Nadat u uw instanties hebt geselecteerd, worden deze toegevoegd aan zowel de huidige grafiek als de gegevens bron.

  [Zoek paneel ![The-model](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Model ontwerpen

De Azure Time Series Insights preview ondersteunt volledige bewerkingen voor maken, lezen, bijwerken en verwijderen van uw time series-model.

* **Type time series-model**: U kunt Time Series Insights typen gebruiken om variabelen of formules te definiëren voor het uitvoeren van berekeningen. Ze zijn gekoppeld aan een bepaald Time Series Insights exemplaar. Een type kan een of meer variabelen hebben.
* **Hiërarchie van Time Series-model**: Hiërarchieën zijn systematische organisaties van uw gegevens. Hiërarchieën weer de relaties tussen verschillende entiteiten in uw Time Series Insights-gegevens.
* **Exemplaar van de time series-model**: Exemplaren zijn de tijd reeks zelf. In de meeste gevallen zijn het **DeviceID** of **AssetID**, de unieke id van de asset in de omgeving.

Zie voor meer informatie over de Time Series-Model, [tijden reeks modellen](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Bron voorbeeld

Op de juiste weer gave worden exemplaar velden en andere meta gegevens gekoppeld aan geselecteerde Time Series Insights exemplaren. Door de selectie vakjes aan de rechter kant in te scha kelen, kunt u specifieke exemplaren verbergen of weer geven in de huidige grafiek. U kunt ook specifieke gegevens elementen uit uw huidige gegevens bron verwijderen door het besturings element rood **verwijderen** (prullenbak kan) aan de linkerkant van het element te selecteren.

  [1The-preview-versie van @no__t](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Als u de lay-out van de pagina **analyse** grafiek opnieuw wilt configureren, selecteert u het pictogram met het weglatings teken in de rechter bovenhoek:

  [opties voor de indeling @no__t 1Telemetry](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Als het volgende bericht wordt weer gegeven, heeft het exemplaar geen gegevens tijdens de geselecteerde tijds periode. Om het probleem op te lossen, verg root u de tijds Panne of bevestigt u dat het exemplaar gegevens pusht.
>
> ![Geen gegevens melding](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Voorbeeld grafiek

Met de grafiek kunt u Time Series Insights instanties als regels weer geven. U kunt het deel venster omgeving, het gegevens model en het configuratie scherm samen vouwen door de Web-besturings elementen te selecteren om de grafiek groter te maken.

  [overzicht van ![Preview-grafiek](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **Geselecteerd datum bereik**: Hiermee wordt bepaald welke gegevens elementen beschikbaar zijn voor visualisatie.

- Schuif knop voor het **binnenste datum bereik**: Gebruik de twee eindpunt besturings elementen door ze over de gewenste tijds duur te slepen.

- **Besturings element voor het samen vouwen van tijds duur**: Hiermee vouwt u de editor van het tijd paneel samen en breidt u deze uit.

- **Besturings element voor de indeling van de Y-as**: De beschik bare opties voor de y-as weer geven:

    * `Default`: Elke regel heeft een afzonderlijke y-as.
    * `Stacked`: Gebruik deze om meerdere lijnen op dezelfde y-as te stapelen, waarbij de y-as wordt gewijzigd op basis van de geselecteerde regel.
    * `Shared`: Alle gegevens van de y-as worden samen weer gegeven.

- **Huidig gegevens element**: Het momenteel geselecteerde gegevens element en de bijbehorende details.

Als u verder wilt inzoomen op een specifiek gegevens segment, klikt u met de rechter muisknop op een gegevens punt in de huidige grafiek en sleept u het geselecteerde gebied naar het eind punt van uw keuze. Klik met de rechter muisknop op het grijze geselecteerde gebied en selecteer **Inzoomen**, zoals wordt weer gegeven in de volgende afbeelding:

  [![Preview diagram zoomen](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Nadat u de **Zoom** actie hebt uitgevoerd, ziet u de geselecteerde gegevensset. Selecteer het besturings element voor de indeling van de y-as om de drie weer gaven van uw Time Series Insights gegevens op de y-as te door lopen.

  [![Preview grafiek y-as](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Hier ziet u een voor beeld van gedeelde Y-assen:

  [gedeelde Y-assen @no__t 1Preview](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Tijds editor paneel

Als u met Time Series Insights Preview werkt, selecteert u eerst een tijdsduur. Het geselecteerde tijds bereik bepaalt de gegevensset die beschikbaar is voor manipulatie met de Time Series Insights preview-objecten. De volgende Webbe sturings elementen zijn beschikbaar in Time Series Insights Preview voor het selecteren van de tijds periode van uw werk:

  [@no__t-selectie venster 1Time](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. Schuif knop voor het **binnenste datum bereik**: Gebruik de twee eindpunt besturings elementen door ze over de gewenste tijds duur te slepen. Dit binnenste datum bereik wordt beperkt door de schuif regelaar voor het buitenste datum bereik.

1. **Knoppen voor het datum bereik verg Roten of verkleinen**: Verg root of verklein uw tijds Panne door een van de knoppen voor het gewenste interval te selecteren.

1. **Besturings element voor het samen vouwen van tijds duur**: Met dit Webbe sturings element kunt u alle besturings elementen verbergen, met uitzonde ring van de binnenste schuif regelaar van het bereik.

1. **Besturings element voor de schuif regelaar voor de buitenste datum bereik**: Gebruik de besturings elementen voor het eind punt om het buitenste datum bereik te selecteren dat beschikbaar is voor het besturings element binnen het bereik van datums.

1. **Vervolg keuzelijst datum-bereik met snelle tijden**: Gebruik deze functie om snel te scha kelen tussen vooraf ingestelde tijdgebonden selecties, zoals de laatste **30 minuten**, de **laatste 12 uur**of een **aangepast bereik**. Als u deze waarde wijzigt, wijzigt ook de beschikbare interval bereiken die worden beschreven in de schuifregelaar voor grootte-interval.

1. **Schuif regelaar voor interval grootte**: Gebruik deze functie om in en uit te zoomen op dezelfde tijds duur. Deze actie biedt meer controle over verkeer tussen grote segmenten van de tijd. Het geeft vloeiende trends weer op segmenten van minder dan een milliseconde. U kunt deze gebruiken om nauw keurige delen van uw gegevens met hoge resolutie te bekijken. Startpunt van de schuifregelaar-standaard is ingesteld als de meest optimale weergave van de gegevens van uw selectie, waardoor resolutie, de snelheid van de query en de granulatie.

1. **Besturings element voor datum bereik tot en met Web**: Met dit Webbe sturings element kunt u eenvoudig de gewenste datum en tijd selecteren. U kunt ook het besturingselement gebruiken om over te schakelen tussen de verschillende tijdzones. Nadat u de wijzigingen hebt aangebracht die u wilt Toep assen op uw huidige werk ruimte, selecteert u **Opslaan**.

   [![To en uit selectie paneel](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Navigatie paneel

Het navigatie venster Time Series Insights Preview wordt boven aan uw Time Series Insights app weer gegeven. Het biedt de volgende functies.

### <a name="current-session-share-link-control"></a>Huidige bestandsshare koppeling sessiebeheer

  [pictogram ![Share](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Selecteer het pictogram nieuwe **share** om een URL-koppeling met uw team te delen.

  [![Share uw instantie-URL](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Sectie tenant

  [![Tenant selectie](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Hier worden de gegevens van uw huidige Time Series Insights aanmeldings account weer gegeven.
* Gebruik deze optie om te scha kelen tussen de beschik bare Time Series Insights-Thema's.
* Gebruik het om de preview- [demo-web-app](https://insights.timeseries.azure.com/preview/demo)weer te geven.

### <a name="theme-selection"></a>Thema selecteren

Als u een nieuw thema wilt selecteren, selecteert u het profiel pictogram in de rechter bovenhoek. Selecteer vervolgens **thema wijzigen**.

  [![Theme selectie](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Taal selectie is ook beschikbaar door uw profiel pictogram te selecteren.

Azure Time Series Insights Preview ondersteunt twee thema's:

* **Licht thema**: Het standaard thema dat in dit document wordt weer gegeven.
* **Donker thema**: Hiermee wordt de Explorer weer gegeven zoals hier:

  [![Selected-donker thema](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>Besturingselementen voor S1/S2-omgeving

### <a name="preview-terms-panel"></a>Voorbeeld venster voor waarden

In deze sectie geldt alleen voor bestaande S1/S2-omgevingen wordt geprobeerd de explorer gebruiken in de bijgewerkte gebruikersinterface. Mogelijk wilt u het algemeen beschik bare product en de preview-versie gebruiken in combi natie. Er is bepaalde functionaliteit van de bestaande gebruikersinterface aan de bijgewerkte explorer toegevoegd, maar krijgt u de volledige gebruikersinterface-ervaring voor S1/S2-omgeving in de bestaande Time Series Insights-Verkenner. 

In plaats van de hiërarchie ziet u het deel venster Time Series Insights voor waarden, waarin u query's in uw omgeving definieert. Gebruik dit om uw gegevens te filteren op basis van een predikaat.

  [![Where-query paneel](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

Het deelvenster met Time Series Insights Preview-voorwaarden editor bevat de volgende parameters:

**Where**: Gebruik de component WHERE om snel uw gebeurtenissen te filteren met behulp van de set operands die in de volgende tabel worden weer gegeven. Als u een zoekopdracht uitvoeren door een operand selecteren, wordt het predicaat automatisch bijgewerkt op basis van die zoekopdracht. Ondersteunde Operandtypen zijn onder andere:

| Bewerking | Ondersteunde typen   | Opmerkingen |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan NULL | Alle operands moeten zijn van hetzelfde type of NULL-constante. |
| `HAS` | Tekenreeks | Aan de rechter kant zijn alleen letterlijke teken reeksen met constanten toegestaan. Lege teken reeks en NULL zijn niet toegestaan. |

Zie de [Time Series-expressie (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)voor meer informatie over ondersteunde query bewerkingen en gegevens typen.

### <a name="examples-of-where-clauses"></a>Voor beelden van WHERE-componenten

  [voor beelden van ![Where-component](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Meting**: Een vervolg keuzelijst met alle numerieke kolommen (**dubbel**) die u kunt gebruiken als elementen voor uw huidige grafiek.

**Splitsen op**: In deze vervolg keuzelijst worden alle beschik bare categorische-kolommen (teken reeksen) in uw model weer gegeven waarop u uw gegevens kunt groeperen. U kunt Maxi maal vijf termen toevoegen die op dezelfde x-as worden weer gegeven. Voer de gewenste para meters in en selecteer **toevoegen** om een nieuwe term toe te voegen.

  [![Queried en gefilterde weer gave 1](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

U kunt elementen weer geven en verbergen in het deel venster grafiek door het zicht bare pictogram te selecteren, zoals wordt weer gegeven in de volgende afbeelding. Als u query's volledig wilt verwijderen, selecteert u de rode **X**.

  [![Queried en gefilterde weer gave twee](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [opslag en](./time-series-insights-update-storage-ingress.md) inkomend verkeer in de preview-versie van Azure time series Insights.
- Lees het Time Series Insights preview-document in [gegevens modellering](./time-series-insights-update-tsm.md).
- Meer informatie [over het vaststellen en oplossen van problemen met](./time-series-insights-update-how-to-troubleshoot.md) uw time series Insights-exemplaar.
