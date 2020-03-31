---
title: Een gefacetteerde hiërarchie van navigatiecategorieën toevoegen
titleSuffix: Azure Cognitive Search
description: Voeg gefacetteerde navigatie toe voor zelfsturende filtering in zoektoepassingen die worden geïntegreerd met Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5f4435ca213584fff84f3ddad9bda6f7e06628a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283158"
---
# <a name="how-to-implement-faceted-navigation-in-azure-cognitive-search"></a>Faceted Navigation implementeren in Azure Cognitive Search

Gefacetteerde navigatie is een filtermechanisme dat zelfgestuurde drilldownnavigatie biedt in zoektoepassingen. De term 'gefacetteerde navigatie' is misschien onbekend, maar je hebt het waarschijnlijk al eerder gebruikt. Zoals in het volgende voorbeeld blijkt, is gefacetteerde navigatie niets meer dan de categorieën die worden gebruikt om resultaten te filteren.

 ![Azure Cognitive Search Job Portal Demo](media/search-faceted-navigation/azure-search-faceting-example.png "Azure Cognitive Search Job Portal Demo")

Gefacetteerde navigatie is een alternatief toegangspunt om te zoeken. Het biedt een handig alternatief voor het typen van complexe zoekexpressies met de hand. Facetten kunnen u helpen vinden wat u zoekt, terwijl ervoor te zorgen dat u geen nul resultaten te krijgen. Als ontwikkelaar u met facetten de meest nuttige zoekcriteria voor het navigeren door uw zoekindex blootleggen. In online retail toepassingen, faceted navigatie wordt vaak gebouwd over merken, afdelingen (kinderschoenen), grootte, prijs, populariteit, en ratings. 

De implementatie van gefacetteerde navigatie verschilt per zoektechnologieën. In Azure Cognitive Search wordt gefacetteerde navigatie gebouwd op querytijd met velden die u eerder in uw schema hebt toegeschreven.

-   In de query's die uw toepassing maakt, moet een query *facetqueryparameters* verzenden om de beschikbare facetfilterwaarden voor die documentresultaatset te krijgen.

-   Als u de documentresultatenset daadwerkelijk wilt `$filter` bijsnijden, moet de toepassing ook een expressie toepassen.

In uw toepassingsontwikkeling vormt het schrijven van code die query's construeert het grootste deel van het werk. Veel van de toepassingsgedragingen die u zou verwachten van gefacetteerde navigatie worden geleverd door de service, inclusief ingebouwde ondersteuning voor het definiëren van bereiken en het ophalen van tellingen voor facetresultaten. De service bevat ook verstandige standaardinstellingen die u helpen onhandelbare navigatiestructuren te vermijden. 

## <a name="sample-code-and-demo"></a>Voorbeeldcode en demo
In dit artikel wordt een zoekportal voor vacatures als voorbeeld gebruikt. Het voorbeeld wordt geïmplementeerd als een ASP.NET MVC-toepassing.

- Bekijk en test de werkende demo online op [Azure Cognitive Search Job Portal Demo](https://aka.ms/azjobsdemo).

- Download de code van de [Repo Azure-Samples op GitHub.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)

## <a name="get-started"></a>Aan de slag
Als je nieuw bent in de ontwikkeling van zoekopdrachten, de beste manier om te denken van faceted navigatie is dat het toont de mogelijkheden voor self-directed search. Het is een soort drill-down zoekervaring, gebaseerd op vooraf gedefinieerde filters, die worden gebruikt voor het snel verfijnen van zoekresultaten door middel van point-and-click-acties. 

### <a name="interaction-model"></a>Interactiemodel

De zoekervaring voor gefacetteerde navigatie is iteratief, dus laten we beginnen met het te begrijpen als een reeks query's die zich ontvouwen in reactie op acties van gebruikers.

Het uitgangspunt is een toepassingspagina die gefacetteerde navigatie biedt, meestal geplaatst op de periferie. Gefacetteerde navigatie is vaak een structuur van de structuur van de structuur, met selectievakjes voor elke waarde of klikbare tekst. 

1. Een query die naar Azure Cognitive Search wordt verzonden, geeft de gefacetteerde navigatiestructuur op via een of meer facetqueryparameters. De query kan bijvoorbeeld `facet=Rating`, misschien `:values` `:sort` met een of optie om de presentatie verder te verfijnen, bevatten.
2. De presentatielaag maakt een zoekpagina weer die gefacetteerde navigatie biedt, met behulp van de facetten die op de aanvraag zijn opgegeven.
3. Als u een gefacetteerde navigatiestructuur met classificatie bevat, klikt u op "4" om aan te geven dat alleen producten met een classificatie van 4 of hoger moeten worden weergegeven. 
4. Als antwoord stuurt de toepassing een query met`$filter=Rating ge 4` 
5. De presentatielaag werkt de pagina bij en geeft een gereduceerd resultaatset weer, met alleen die items die voldoen aan de nieuwe criteria (in dit geval producten met een score van 4 en meer).

Een facet is een queryparameter, maar verwar deze niet met queryinvoer. Het wordt nooit gebruikt als selectiecriteria in een query. Denk in plaats daarvan aan facetqueryparameters als ingangen van de navigatiestructuur die terugkomt in het antwoord. Voor elke facetqueryparameter die u opgeeft, evalueert Azure Cognitive Search hoeveel documenten zich in de gedeeltelijke resultaten voor elke facetwaarde bevinden.

Let `$filter` op de in stap 4. Het filter is een belangrijk aspect van gefacetteerde navigatie. Hoewel facetten en filters onafhankelijk zijn in de API, hebt u beide nodig om de ervaring te leveren die u van plan bent. 

### <a name="app-design-pattern"></a>Ontwerppatroon voor apps

In toepassingscode moet het patroon facetqueryparameters gebruiken om de gefaceteerde navigatiestructuur te retourneren, samen met facetresultaten, plus een $filter expressie.  De filterexpressie verwerkt de klikgebeurtenis op de facetwaarde. Denk aan `$filter` de expressie als de code achter het daadwerkelijk bijsnijden van zoekresultaten die zijn teruggebracht naar de presentatielaag. Als u een facet Kleuren geeft, wordt `$filter` het klikken op de kleur Rood geïmplementeerd via een expressie die alleen de items selecteert die een kleur rood hebben. 

### <a name="query-basics"></a>Basisbeginselen van query

In Azure Cognitive Search wordt een aanvraag opgegeven via een of meer queryparameters (zie [Zoekdocumenten](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) voor een beschrijving van elk query). Geen van de queryparameters is vereist, maar u moet er ten minste één hebben om een query geldig te laten zijn.

Precisie, begrepen als de mogelijkheid om irrelevante hits uit te filteren, wordt bereikt door een of beide van deze expressies:

-   **zoeken=**  
    De waarde van deze parameter vormt de zoekexpressie. Het kan een enkel stuk tekst zijn, of een complexe zoekexpressie die meerdere termen en operatoren bevat. Op de server wordt een zoekexpressie gebruikt voor zoeken in volledige tekst, waarbij zoekbare velden in de index worden opgevraagd voor overeenkomende termen, resultaten in rangvolgorde retourneren. Als u `search` op null bent ingesteld, is queryuitvoering `search=*`over de gehele index (dat wil zeggen). In dit geval zijn andere elementen van `$filter` de query, zoals een of een `($filter`scoreprofiel, de`scoringProfile` `$orderby`belangrijkste factoren die van invloed zijn op welke documenten worden geretourneerd ) en in welke volgorde (of ).

-   **$filter=**  
    Een filter is een krachtig mechanisme voor het beperken van de grootte van zoekresultaten op basis van de waarden van specifieke documentkenmerken. A `$filter` wordt eerst geëvalueerd, gevolgd door facetinglogica die de beschikbare waarden en bijbehorende tellingen voor elke waarde genereert

Complexe zoekexpressies verlagen de prestaties van de query. Gebruik waar mogelijk goed geconstrueerde filterexpressies om de precisie te verhogen en de queryprestaties te verbeteren.

Als u beter wilt begrijpen hoe een filter meer precisie toevoegt, vergelijkt u een complexe zoekexpressie met een expressie met een filterexpressie:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq 'Seattle' and Parking and Type ne 'motel'`

Beide vragen zijn geldig, maar de tweede is superieur als je op zoek bent naar niet-motels met parkeergelegenheid in Seattle.
-   De eerste query is afhankelijk van die specifieke woorden die worden vermeld of niet worden vermeld in tekenreeksvelden zoals Naam, Beschrijving en elk ander veld met doorzoekbare gegevens.
-   De tweede query zoekt naar precieze overeenkomsten op gestructureerde gegevens en is waarschijnlijk veel nauwkeuriger.

Controleer in toepassingen met gefacetteerde navigatie of elke gebruikersactie via een gefacetteerde navigatiestructuur gepaard gaat met een vernauwing van de zoekresultaten. Als u de resultaten wilt beperken, gebruikt u een filterexpressie.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Een gefacetteerde navigatie-app bouwen
U implementeert gefacetteerde navigatie met Azure Cognitive Search in uw toepassingscode die de zoekaanvraag bouwt. De gefacetteerde navigatie is afhankelijk van elementen in uw schema die u eerder hebt gedefinieerd.

Vooraf gedefinieerd in uw zoekindex is het `Facetable [true|false]` indexkenmerk dat is ingesteld op geselecteerde velden om het gebruik ervan in een gefacetteerde navigatiestructuur in te schakelen of uit te schakelen. Zonder `"Facetable" = true`kan een veld niet worden gebruikt in facetnavigatie.

De presentatielaag in uw code biedt de gebruikerservaring. Hierin moeten de samenstellende delen van de gefacetteerde navigatie worden vermeld, zoals het label, de waarden, de selectievakjes en de telling. De Azure Cognitive Search REST API is platform agnostisch, dus gebruik welke taal en platform u wilt. Het belangrijkste is om UI-elementen op te nemen die incrementele vernieuwing ondersteunen, met bijgewerkte ui-status als elk extra facet is geselecteerd. 

Bij querytijd maakt uw toepassingscode `facet=[string]`een aanvraag met een aanvraagparameter die het veld biedt om te faceten. Een query kan meerdere facetten `&facet=color&facet=category&facet=rating`hebben, zoals elk, gescheiden door eensand (&) karakter.

Toepassingscode moet ook `$filter` een expressie construeren om de klikgebeurtenissen in gefacetteerde navigatie te verwerken. A `$filter` vermindert de zoekresultaten en gebruikt de facetwaarde als filtercriteria.

Azure Cognitive Search retourneert de zoekresultaten op basis van een of meer termen die u invoert, samen met updates van de gefacetteerde navigatiestructuur. In Azure Cognitive Search is gefacetteerde navigatie een constructie op één niveau, met facetwaarden en telt het aantal resultaten dat voor elk wordt gevonden.

In de volgende secties bekijken we hoe we elk onderdeel kunnen bouwen.

<a name="buildindex"></a>

## <a name="build-the-index"></a>De index samenstellen
Faceting is per veld ingeschakeld in de index, via dit `"Facetable": true`indexkenmerk: .  
Alle veldtypen die mogelijk kunnen worden gebruikt `Facetable` in gefacetteerde navigatie zijn standaard. Dergelijke veldtypen `Edm.String` `Edm.DateTimeOffset`omvatten , en alle numerieke veldtypen (in wezen `Edm.GeographyPoint`zijn alle veldtypen facetable, behalve , die niet kunnen worden gebruikt in gefacetteerde navigatie). 

Bij het samenstellen van een index is het maken van faceting een aanbevolen manier om faceting expliciet uit te schakelen voor velden die nooit als facet mogen worden gebruikt.  In het bijzonder moeten tekenreeksvelden voor singleton-waarden, zoals een `"Facetable": false` ID of productnaam, worden ingesteld om te voorkomen dat ze per ongeluk (en ineffectief) worden gebruikt in gefacetteerde navigatie. Door faceting uit te schakelen waar u het niet nodig hebt, blijft de grootte van de index klein en verbetert het doorgaans de prestaties.

Hieronder volgt een deel van het schema voor de voorbeeld-app Job Portal Demo, die van sommige kenmerken wordt bijgesneden om de grootte te verkleinen:

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { "name": "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { "name": "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { "name": "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

Zoals u zien in `Facetable` het voorbeeldschema, wordt uitgeschakeld voor tekenreeksvelden die niet als facetten moeten worden gebruikt, zoals ID-waarden. Door faceting uit te schakelen waar u het niet nodig hebt, blijft de grootte van de index klein en verbetert het doorgaans de prestaties.

> [!TIP]
> Als aanbevolen praktijk neemt u de volledige set indexkenmerken voor elk veld op. Hoewel `Facetable` deze standaard is ingeschakeld voor bijna alle velden, kan het doelbewust instellen van elk kenmerk u helpen bij het nadenken over de implicaties van elke schemabeslissing. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Controleer de gegevens
De kwaliteit van uw gegevens heeft een direct effect op de vraag of de gefacetteerde navigatiestructuur materialiseert zoals u verwacht. Het beïnvloedt ook het gemak van het bouwen van filters om de resultaatset te verminderen.

Als u wilt faceten op merk of prijs, moet elk document waarden bevatten voor *merknaam* en *productprijs* die geldig, consistent en productief zijn als filteroptie.

Hier zijn een paar herinneringen van wat te schrobben voor:

* Voor elk veld dat u wilt facet door, vraag jezelf af of het waarden die geschikt zijn als filters in self-directed search bevat. De waarden moeten kort, beschrijvend en voldoende onderscheidend zijn om een duidelijke keuze tussen concurrerende opties te bieden.
* Spelfouten of bijna overeenkomende waarden. Als u facet op Kleur, en veldwaarden omvatten Oranje en Ornage (een spelfout), een facet op basis van de kleur veld zou pick-up beide.
* Mixed case tekst kan ook ravage aanrichten in faceted navigatie, met oranje en Oranje verschijnen als twee verschillende waarden. 
* Enkele en meervoudsversies van dezelfde waarde kunnen resulteren in een apart facet voor elk.

Zoals u zich voorstellen, is zorgvuldigheid bij het voorbereiden van de gegevens een essentieel aspect van effectieve gefacetteerde navigatie.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>De gebruikersinterface bouwen
Als u vanaf de presentatielaag werkt, u vereisten ontdekken die anders gemist kunnen worden en begrijpen welke mogelijkheden essentieel zijn voor de zoekervaring.

In termen van gefacetteerde navigatie geeft uw web- of toepassingspagina de gefacetteerde navigatiestructuur weer, detecteert u gebruikersinvoer op de pagina en voegt u de gewijzigde elementen in. 

Voor webtoepassingen wordt AJAX vaak gebruikt in de presentatielaag, omdat u hiermee incrementele wijzigingen vernieuwen. U ook ASP.NET MVC of een ander visualisatieplatform gebruiken dat verbinding kan maken met een Azure Cognitive Search-service via HTTP. De voorbeeldtoepassing waarnaar in dit artikel wordt verwezen - de **Azure Cognitive Search Job Portal Demo** - is toevallig een ASP.NET MVC-toepassing.

In het voorbeeld is gefacetteerde navigatie ingebouwd in de pagina met zoekresultaten. In het volgende voorbeeld, uit het `index.cshtml` bestand van de voorbeeldtoepassing, wordt de statische HTML-structuur weergegeven voor het weergeven van gefacetteerde navigatie op de pagina met zoekresultaten. De lijst met facetten wordt dynamisch gebouwd of opnieuw opgebouwd wanneer u een zoekterm indient of een facet selecteert of wist.

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

Het volgende codefragment `index.cshtml` van de pagina bouwt dynamisch de HTML om het eerste facet, bedrijfstitel, weer te geven. Vergelijkbare functies bouwen dynamisch de HTML voor de andere facetten. Elk facet heeft een label en een telling, die het aantal items voor dat facet resultaat toont.

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

> [!TIP]
> Wanneer u de pagina met zoekresultaten ontwerpt, moet u een mechanisme toevoegen voor het wissen van facetten. Als u selectievakjes toevoegt, u eenvoudig zien hoe u de filters wissen. Voor andere lay-outs hebt u mogelijk een broodkruimelpatroon of een andere creatieve benadering nodig. In de voorbeeldtoepassing Job Search Portal `[X]` u bijvoorbeeld op het na geselecteerde facet klikken om het facet te wissen.

<a name="buildquery"></a>

## <a name="build-the-query"></a>De query bouwen
De code die u schrijft voor het bouwen van query's moet alle onderdelen van een geldige query opgeven, inclusief zoekexpressies, facetten, filters, scoreprofielen, alles wat wordt gebruikt om een aanvraag te formuleren. In deze sectie onderzoeken we waar facetten in een query passen en hoe filters worden gebruikt met facetten om een gereduceerde resultaatset te leveren.

Merk op dat facetten integraal zijn in deze voorbeeldtoepassing. De zoekervaring in de Job Portal Demo is ontworpen rond gefacetteerde navigatie en filters. De prominente plaatsing van gefacetteerde navigatie op de pagina toont het belang ervan aan. 

Een voorbeeld is vaak een goede plek om te beginnen. In het volgende voorbeeld, afkomstig uit het `JobsSearch.cs` bestand, wordt een aanvraag gemaakt waarmee facetnavigatie wordt gemaakt op basis van bedrijfstitel, locatie, boekingstype en minimumsalaris. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Een parameter facetquery is ingesteld op een veld en kan, afhankelijk van het gegevenstype, `count:<integer>` `sort:<>`verder `interval:<integer>`worden `values:<list>`geparameteriseerd door een door komma's afgebakende lijst die , , en . Een waardenlijst wordt ondersteund voor numerieke gegevens bij het instellen van bereiken. Zie [Zoekdocumenten (Azure Cognitive Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) voor gebruiksgegevens.

Samen met facetten, moet het verzoek geformuleerd door uw aanvraag ook filters bouwen om de set van kandidaat-documenten te beperken op basis van een facet waarde selectie. Voor een fietswinkel biedt gefacetteerde navigatie aanwijzingen voor vragen als *Welke kleuren, fabrikanten en soorten fietsen zijn beschikbaar?* Filteren beantwoordt vragen als *Welke fietsen zijn precies rood, mountainbikes, in deze prijsklasse?*. Wanneer u op 'Rood' klikt om aan te geven dat alleen `$filter=Color eq 'Red'`Rode producten moeten worden weergegeven, bevat de volgende query die de toepassing verzendt .

In het volgende `JobsSearch.cs` codefragment van de pagina wordt de geselecteerde bedrijfstitel aan het filter toegevoegd als u een waarde selecteert in het facet Bedrijfstitel.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Tips en best practices

### <a name="indexing-tips"></a>Indexeringstips
**De indexefficiëntie verbeteren als u geen zoekvak gebruikt**

Als uw toepassing uitsluitend gefacetteerde navigatie gebruikt (dat wil `searchable=false` `facetable=true` zeggen, geen zoekvak), u het veld markeren als, om een compactere index te produceren. Bovendien vindt indexering alleen plaats op hele facetwaarden, zonder woordbreuk of indexering van de onderdelen van een waarde van meerdere woorden.

**Opgeven welke velden als facetten kunnen worden gebruikt**

Bedenk dat het schema van de index bepaalt welke velden beschikbaar zijn om als facet te gebruiken. Ervan uitgaande dat een veld facetable is, geeft de query aan aan aan welke velden moet worden gefacett. Het veld waarmee u faceting geeft de waarden die onder het label worden weergegeven. 

De waarden die onder elk label worden weergegeven, worden uit de index opgehaald. Als het facetveld bijvoorbeeld *Kleur*is, zijn de waarden die beschikbaar zijn voor extra filtering de waarden voor dat veld - Rood, Zwart, enzovoort.

Alleen voor numerieke en DateTime-waarden u expliciet waarden instellen `facet=Rating,values:1|2|3|4|5`op het facetveld (bijvoorbeeld). Voor deze veldtypen is een waardenlijst toegestaan om de scheiding van facetresultaten in aaneengesloten bereiken te vereenvoudigen (bereiken op basis van numerieke waarden of perioden). 

**Standaard u slechts één niveau van gefacetteerde navigatie hebben** 

Zoals opgemerkt, is er geen directe ondersteuning voor het nestelen van facetten in een hiërarchie. Standaard ondersteunt gefacetteerde navigatie in Azure Cognitive Search slechts één niveau van filters. Er bestaan echter oplossingen. U een hiërarchische facetstructuur coderen in een `Collection(Edm.String)` met één ingangspunt per hiërarchie. Het implementeren van deze tijdelijke oplossing valt buiten het bereik van dit artikel. 

### <a name="querying-tips"></a>Tips voor het opvragen
**Velden valideren**

Als u de lijst met facetten dynamisch maakt op basis van niet-vertrouwde gebruikersinvoer, valideert u dat de namen van de gefacetteerde velden geldig zijn. Of ontsnap aan de namen `Uri.EscapeDataString()` bij het bouwen van URL's door gebruik te maken van .NET of het equivalent in uw platform naar keuze.

### <a name="filtering-tips"></a>Filtertips
**De zoekprecisie vergroten met filters**

Gebruik filters. Als u alleen op zoekexpressies vertrouwt, kan een document worden geretourneerd dat niet de precieze facetwaarde heeft in een van de velden.

**De zoekprestaties verbeteren met filters**

Filters beperken de set kandidaat-documenten voor zoekopdrachten en sluiten deze uit van de rangschikking. Als u een grote set documenten hebt, geeft het gebruik van een selectieve facetdrill-down u vaak betere prestaties.
  
**Alleen de gefacetteerde velden filteren**

Bij faceted drill-down wilt u meestal alleen documenten opnemen die de facetwaarde hebben in een specifiek (faceted) veld, niet overal in alle doorzoekbare velden. Het toevoegen van een filter versterkt het doelveld door de service alleen in het gefacetteerde veld te laten zoeken naar een overeenkomende waarde.

**Facetresultaten bijsnijden met meer filters**

Facetresultaten zijn documenten die in de zoekresultaten worden gevonden en die overeenkomen met een facetterm. In het volgende voorbeeld, in zoekresultaten voor *cloud computing,* 254 items hebben ook *interne specificatie* als een inhoudtype. Items sluiten elkaar niet noodzakelijkerwijs uit. Als een item voldoet aan de criteria van beide filters, wordt het in elk artikel geteld. Deze duplicatie is `Collection(Edm.String)` mogelijk bij het faceting op velden, die vaak worden gebruikt om documenttagging te implementeren.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Als u vindt dat facetresultaten consistent te groot zijn, raden we u aan meer filters toe te voegen om gebruikers meer opties te geven om de zoekopdracht te verkleinen.

### <a name="tips-about-result-count"></a>Tips over het aantal resultaten

**Het aantal items in de facetnavigatie beperken**

Voor elk gefacetteerd veld in de navigatiestructuur geldt een standaardlimiet van 10 waarden. Deze standaardis zinvol voor navigatiestructuren omdat de waardenlijst hiermee op een beheersbare grootte wordt weergegeven. U de standaardwaarde overschrijven door een waarde toe te schrijven om te tellen.

* `&facet=city,count:5`hiermee wordt aangegeven dat alleen de eerste vijf steden in de top gerangschikte resultaten worden geretourneerd als een facetresultaat. Overweeg een voorbeeldquery met een zoekterm van 'luchthaven' en 32 overeenkomsten. Als de `&facet=city,count:5`query dit opgeeft, worden alleen de eerste vijf unieke steden met de meeste documenten in de zoekresultaten opgenomen in de facetresultaten.

Let op het onderscheid tussen facet-resultaten en zoekresultaten. Zoekresultaten zijn alle documenten die overeenkomen met de query. Facet resultaten zijn de wedstrijden voor elke facet waarde. In het voorbeeld bevatten zoekresultaten plaatsnamen die niet in de facetclassificatielijst staan (5 in ons voorbeeld). Resultaten die worden gefilterd door middel van gefacetteerde navigatie zichtbaar worden wanneer u facetten duidelijk, of kies andere facetten naast City. 

> [!NOTE]
> Bespreken `count` wanneer er meer dan één type is, kan verwarrend zijn. In de volgende tabel vindt u een korte samenvatting van de manier waarop de term wordt gebruikt in azure cognitive search API, voorbeeldcode en documentatie. 

* `@colorFacet.count`<br/>
  In presentatiecode ziet u een parameter voor het aantal nummers op het facet, die wordt gebruikt om het aantal facetresultaten weer te geven. In facetresultaten geeft het aantal documenten aan dat overeenkomt met de facetterm of -bereik.
* `&facet=City,count:12`<br/>
  In een facetquery u het aantal instellen op een waarde.  De standaardinstelling is 10, maar u deze hoger of lager instellen. Als `count:12` u de top 12-overeenkomsten in facetresultaten opstelt, wordt het aantal documenten weergegeven.
* "`@odata.count`"<br/>
  In het queryantwoord geeft deze waarde het aantal overeenkomende items in de zoekresultaten aan. Gemiddeld is het groter dan de som van alle facetresultaten samen, vanwege de aanwezigheid van items die overeenkomen met de zoekterm, maar geen facetwaarde overeenkomsten hebben.

**Haal tellingen in facetresultaten**

Wanneer u een filter toevoegt aan een gefacetteerde query, `facet=Rating&$filter=Rating ge 4`kunt u de facetinstructie behouden (bijvoorbeeld). Technisch gezien is facet=Rating niet nodig, maar het bijhouden ervan geeft de tellingen van facetwaarden voor beoordelingen 4 en hoger. Als u bijvoorbeeld op '4' klikt en de query een filter bevat voor meer of gelijk aan '4', worden de tellingen geretourneerd voor elke beoordeling die 4 en hoger is.  

**Zorg ervoor dat u nauwkeurige facettellingen krijgt**

Onder bepaalde omstandigheden u merken dat facettellingen niet overeenkomen met de resultaatsets (zie [Gefaceteerde navigatie in Azure Cognitive Search (forumbericht).](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)

Facet-tellingen kunnen onjuist zijn als gevolg van de sharding-architectuur. Elke zoekindex heeft meerdere scherven en elke shard rapporteert de bovenste N-facetten op documenttelling, die vervolgens worden gecombineerd tot één resultaat. Als sommige shards veel overeenkomende waarden hebben, terwijl andere minder hebben, u merken dat sommige facetwaarden ontbreken of ondergeteld zijn in de resultaten.

Hoewel dit gedrag op elk gewenst moment kan veranderen, u, als u dit\<gedrag vandaag tegenkomt, er omheen werken door de telling kunstmatig op te leiden:> een groot aantal om volledige rapportage van elke shard af te dwingen. Als de waarde van het aantal: groter is dan of gelijk is aan het aantal unieke waarden in het veld, bent u verzekerd van nauwkeurige resultaten. Wanneer het aantal documenten echter hoog is, is er een prestatiestraf, dus gebruik deze optie verstandig.

### <a name="user-interface-tips"></a>Tips voor de gebruikersinterface
**Labels toevoegen voor elk veld in facetnavigatie**

Labels worden meestal gedefinieerd in de`index.cshtml` HTML of vorm (in de voorbeeldtoepassing). Er is geen API in Azure Cognitive Search voor facetnavigatielabels of andere metagegevens.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filteren op basis van een bereik
Faceting over waardenbereiken is een veelvoorkomende vereiste voor zoektoepassingen. Bereiken worden ondersteund voor numerieke gegevens en DateTime-waarden. U meer lezen over elke benadering in [Search Documents (Azure Cognitive Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

Azure Cognitive Search vereenvoudigt de opbouw van het bereik door twee benaderingen te bieden voor het berekenen van een bereik. Azure Cognitive Search maakt voor beide benaderingen de juiste bereiken, gezien de invoer die u hebt opgegeven. Als u bijvoorbeeld bereikwaarden van 10|20|30 opgeeft, worden er automatisch bereiken van 0-10, 10-20, 20-30. Uw toepassing kan optioneel alle lege intervallen verwijderen. 

**Aanpak 1: De parameter interval gebruiken**  
Als u prijsfacetten in stappen van $ 10 wilt instellen, geeft u het volgende op:`&facet=price,interval:10`

**Aanpak 2: Een waardenlijst gebruiken**  
Voor numerieke gegevens u een waardenlijst gebruiken.  Houd rekening met het `listPrice` facetbereik voor een veld dat als volgt wordt weergegeven:

  ![Lijst met voorbeeldwaarden](media/search-faceted-navigation/Facet-5-Prices.PNG "Lijst met voorbeeldwaarden")

Als u een facetbereik wilt opgeven zoals in de vorige schermafbeelding, gebruikt u een waardenlijst:

    facet=listPrice,values:10|25|100|500|1000|2500

Elk bereik wordt gebouwd met behulp van 0 als uitgangspunt, een waarde uit de lijst als eindpunt en vervolgens van het vorige bereik bijgesneden om afzonderlijke intervallen te maken. Azure Cognitive Search doet deze dingen als onderdeel van gefacetteerde navigatie. U hoeft geen code te schrijven voor het structureren van elk interval.

### <a name="build-a-filter-for-a-range"></a>Een filter bouwen voor een bereik
Als u documenten wilt filteren op basis `"ge"` van `"lt"` een bereik dat u selecteert, u de operatoren en filteroperatoren gebruiken in een expressie in twee delen die de eindpunten van het bereik definieert. Als u bijvoorbeeld het bereik 10-25 `listPrice` voor een veld `$filter=listPrice ge 10 and listPrice lt 25`kiest, is het filter . In de voorbeeldcode gebruikt de filterexpressie **prijsUit** en **priceTo-parameters** om de eindpunten in te stellen. 

  ![Query voor een reeks waarden](media/search-faceted-navigation/Facet-6-buildfilter.PNG "Query voor een reeks waarden")

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filter op basis van afstand
Het is gebruikelijk om filters te zien die u helpen bij het kiezen van een winkel, restaurant of bestemming op basis van de nabijheid van uw huidige locatie. Hoewel dit type filter eruit ziet als gefacetteerde navigatie, is het slechts een filter. We noemen het hier voor degenen onder u die specifiek op zoek zijn naar implementatie advies voor dat specifieke ontwerp probleem.

Er zijn twee Geospatial-functies in Azure Cognitive Search, **geo.distance** en **geo.intersects**.

* De **geo.distance-functie** retourneert de afstand in kilometers tussen twee punten. Een punt is een veld en andere is een constante doorgegeven als onderdeel van het filter. 
* De **geo.intersects** functie keert waar als een bepaald punt zich binnen een bepaalde veelhoek bevindt. Het punt is een veld en de veelhoek wordt opgegeven als een constante lijst met coördinaten die als onderdeel van het filter worden doorgegeven.

U filtervoorbeelden vinden in [de syntaxis van OData-expressie (Azure Cognitive Search).](query-odata-filter-orderby-syntax.md)

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Probeer de demo
De demo azure cognitive search job portal bevat de voorbeelden waarnaar in dit artikel wordt verwezen.

-   Bekijk en test de werkende demo online op [Azure Cognitive Search Job Portal Demo](https://aka.ms/azjobsdemo).

-   Download de code van de [Repo Azure-Samples op GitHub.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)

Houd tijdens het werken met zoekresultaten de URL in de gaten voor wijzigingen in de queryconstructie. Deze toepassing wordt toegevoegd facetten aan de URI als u elk een.

1. Als u de toewijzingsfunctionaliteit van de demo-app wilt gebruiken, krijgt u een Bing Maps-sleutel van het [Bing Maps Dev Center.](https://www.bingmapsportal.com/) Plak deze over de `index.cshtml` bestaande sleutel in de pagina. De `BingApiKey` instelling `Web.config` in het bestand wordt niet gebruikt. 

2. Voer de toepassing uit. Neem de optionele rondleiding of sluit het dialoogvenster af.
   
3. Voer een zoekterm in, zoals 'analist', en klik op het pictogram Zoeken. De query wordt snel uitgevoerd.
   
   Een gefacetteerde navigatiestructuur wordt ook geretourneerd met de zoekresultaten. Op de pagina met zoekresultaten bevat de gefacetteerde navigatiestructuur tellingen voor elk facetresultaat. Er zijn geen facetten geselecteerd, dus alle overeenkomende resultaten worden geretourneerd.
   
   ![Zoekresultaten voordat u facetten selecteert](media/search-faceted-navigation/faceted-search-before-facets.png "Zoekresultaten voordat u facetten selecteert")

4. Klik op een bedrijfstitel, locatie of minimumsalaris. Facetten waren null op de eerste zoekopdracht, maar als ze op waarden, de zoekresultaten worden bijgesneden van items die niet meer overeenkomen.
   
   ![Zoekresultaten na het selecteren van facetten](media/search-faceted-navigation/faceted-search-after-facets.png "Zoekresultaten na het selecteren van facetten")

5. Als u de gefacetteerde query wilt wissen, zodat u verschillende querygedragingen proberen, klikt u op de `[X]` na de geselecteerde facetten om de facetten te wissen.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Meer informatie
Bekijk [Azure Cognitive Search Deep Dive](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). Om 45:25 uur is er een demo over het implementeren van facetten.

Voor meer informatie over ontwerpprincipes voor gefacetteerde navigatie raden we de volgende links aan:

* [Ontwerppatronen: gefacetteerde navigatie](https://alistapart.com/article/design-patterns-faceted-navigation)
* [Front-end zorgen bij de uitvoering van Faceted Search - Deel 1](https://articles.uie.com/faceted_search2/)

