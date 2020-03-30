---
title: Querytypen en querysamenstelling
titleSuffix: Azure Cognitive Search
description: Basisbeginselen voor het maken van een zoekopdracht in Azure Cognitive Search, waarbij parameters worden gebruikt om resultaten te filteren, te selecteren en te sorteren.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 902f3628235cc8a4524ddc4dd8a5327592fe47e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282820"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Querytypen en -samenstelling in Azure Cognitive Search

In Azure Cognitive Search is een query een volledige specificatie van een retourbewerking. Parameters op de aanvraag bieden wedstrijdcriteria voor het vinden van documenten in een index, die velden op te nemen of uit te sluiten, uitvoeringsinstructies doorgegeven aan de motor, en richtlijnen voor het vormgeven van het antwoord. Niet nader`search=*`gespecificeerde ( ), een query wordt uitgevoerd tegen alle doorzoekbare velden als een full text search operation, het retourneren van een niet-gescoord resultaat ingesteld in willekeurige volgorde.

Het volgende voorbeeld is een representatieve query die is gemaakt in de [REST-API](https://docs.microsoft.com/rest/api/searchservice/search-documents). Dit voorbeeld is gericht op de [demo-index](search-get-started-portal.md) van hotels en bevat algemene parameters.

```
{
    "queryType": "simple" 
    "search": "+New York +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

+ **`queryType`** stelt de parser in, dat is ofwel de [standaard eenvoudige queryparser](search-query-simple-examples.md) (optimaal voor full text search), of de [volledige Lucene query parser](search-query-lucene-examples.md) die wordt gebruikt voor geavanceerde queryconstructies zoals reguliere expressies, proximity search, fuzzy en wildcard search, om er maar een paar te noemen.

+ **`search`** biedt de wedstrijdcriteria, meestal tekst, maar vaak vergezeld van booleaanse operatoren. Enkele zelfstandige *term* termen zijn termquery's. Inquote-ingesloten meerdelige query's zijn *belangrijke woordgroepquery's.* Zoeken kan ongedefinieerd zijn, **`search=*`** zoals in , maar meer waarschijnlijk bestaat uit termen, zinnen en operators vergelijkbaar met wat wordt weergegeven in het voorbeeld.

+ **`searchFields`** hiermee wordt query-uitvoering beperkt tot specifieke velden. Elk veld dat wordt toegeschreven als *doorzoekbaar* in het indexschema, is een kandidaat voor deze parameter.

De antwoorden worden ook gevormd door de parameters die u in de query opneemt. In het voorbeeld bestaat de resultaatset uit **`select`** velden die in de instructie worden vermeld. Alleen velden die als *opvraagbaar zijn gemarkeerd,* kunnen in een $select-instructie worden gebruikt. Bovendien worden alleen **`top`** de 10 hits geretourneerd **`count`** in deze query, terwijl u vertelt hoeveel documenten over het algemeen overeenkomen, wat meer kan zijn dan wat wordt geretourneerd. In deze query worden rijen gesorteerd op beoordeling in aflopende volgorde.

In Azure Cognitive Search is query-uitvoering altijd tegen één index, geverifieerd met behulp van een api-toets in de aanvraag. In REST worden beide geleverd in aanvraagkoppen.

### <a name="how-to-run-this-query"></a>Deze query uitvoeren

Als u deze query wilt uitvoeren, gebruikt u [Zoekverkenner en de demo-index voor hotels](search-get-started-portal.md). 

U deze querytekenreeks in de zoekbalk van de verkenner plakken:`search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Hoe querybewerkingen worden ingeschakeld door de index

Indexontwerp en queryontwerp zijn nauw gekoppeld in Azure Cognitive Search. Een essentieel feit om van te voren te weten is dat het *indexschema*, met attributen op elk veld, bepaalt welk type query u bouwen. 

Indexkenmerken op een veld stellen de toegestane bewerkingen in - of een veld *doorzoekbaar* is in de index, *opvraagbaar* in resultaten, *sorteerbaar,* *filterbaar,* enzovoort. In de voorbeeldquerytekenreeks werkt `"$orderby": "Rating"` alleen omdat het veld Beoordeling is gemarkeerd als *sorteerbaar* in het indexschema. 

![Indexdefinitie voor het hotelvoorbeeld](./media/search-query-overview/hotel-sample-index-definition.png "Indexdefinitie voor het hotelvoorbeeld")

De bovenstaande schermafbeelding is een gedeeltelijke lijst met indexkenmerken voor de steekproef van hotels. U het volledige indexschema in de portal bekijken. Zie [API IndexREST maken](https://docs.microsoft.com/rest/api/searchservice/create-index)voor meer informatie over indexkenmerken.

> [!Note]
> Sommige queryfunctionaliteit is indexbreed ingeschakeld in plaats van per veld. Deze mogelijkheden omvatten: [synoniem kaarten,](search-synonyms.md) [aangepaste analyzers](index-add-custom-analyzers.md), [suggester constructen (voor automatisch aanvullen en voorgestelde query's)](index-add-suggesters.md), [scoren logica voor het rangschikken van resultaten](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Elementen van een queryaanvraag

Query's zijn altijd gericht op één index. U niet deelnemen aan indexen of aangepaste of tijdelijke gegevensstructuren maken als querydoel. 

Vereiste elementen in een queryaanvraag zijn de volgende onderdelen:

+ Verzameling van serviceeindpunten en indexdocumenten, uitgedrukt als een URL die vaste en door de gebruiker gedefinieerde componenten bevat:**`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`**(REST only) is noodzakelijk omdat er te allen tijde meer dan één versie van de API beschikbaar is. 
+ **`api-key`**, een query of admin api-key, verifieert het verzoek aan uw service.
+ **`queryType`**, eenvoudig of vol, die kan worden weggelaten als u de ingebouwde standaard eenvoudige syntaxis gebruikt.
+ **`search`** of **`filter`** geeft de wedstrijdcriteria op, die niet kunnen worden gespecificeerd als u een lege zoekopdracht wilt uitvoeren. Beide querytypen worden besproken in termen van de eenvoudige parser, maar zelfs geavanceerde query's vereisen de zoekparameter voor het doorgeven van complexe queryexpressies.

Alle andere zoekparameters zijn optioneel. Zie [Index (REST) maken voor](https://docs.microsoft.com/rest/api/searchservice/create-index)de volledige lijst met kenmerken. Zie Hoe zoeken in [volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)voor een nadere blik op de manier waarop parameters worden gebruikt tijdens de verwerking.

## <a name="choose-apis-and-tools"></a>API's en hulpprogramma's kiezen

In de volgende tabel worden de API's en op de tool gebaseerde benaderingen voor het indienen van query's weergegeven.

| Methodologie | Beschrijving |
|-------------|-------------|
| [Search Explorer (portal)](search-explorer.md) | Biedt een zoekbalk en opties voor index- en api-versieselecties. Resultaten worden geretourneerd als JSON-documenten. Aanbevolen voor verkenning, testen en validatie. <br/>[Meer informatie.](search-get-started-portal.md#query-index) | 
| [Postbode of andere REST-tools](search-get-started-postman.md) | Web testtools zijn een uitstekende keuze voor het formuleren van REST-oproepen. De REST API ondersteunt elke mogelijke bewerking in Azure Cognitive Search. In dit artikel vindt u meer informatie over het instellen van een HTTP-aanvraagkop- en hoofdtekst voor het verzenden van aanvragen naar Azure Cognitive Search.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Client die kan worden gebruikt om een Azure Cognitive Search-index op te vragen.  <br/>[Meer informatie.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Zoekdocumenten (REST-API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | GET- of POST-methoden op een index, met queryparameters voor extra invoer.  |

## <a name="choose-a-parser-simple--full"></a>Kies een parser: eenvoudig | Volledige

Azure Cognitive Search zit bovenop Apache Lucene en geeft u de keuze tussen twee queryparsers voor het verwerken van typische en gespecialiseerde query's. Aanvragen met behulp van de eenvoudige parser worden geformuleerd met behulp van de [eenvoudige querysyntaxis](query-simple-syntax.md), geselecteerd als standaard voor de snelheid en effectiviteit in tekstquery's in vrije vorm. Deze syntaxis ondersteunt een aantal algemene zoekoperatoren, waaronder de vakgroep EN, OF NIET, zin, achtervoegsel en voorrang.

De [volledige syntaxis van Lucene query,](query-Lucene-syntax.md#bkmk_syntax)ingeschakeld wanneer u aan het verzoek toevoegt, `queryType=full` toont de veelgebruikte en expressieve querytaal die is ontwikkeld als onderdeel van Apache [Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). De volledige syntaxis breidt de eenvoudige syntaxis uit. Elke query die u schrijft voor de eenvoudige syntaxis wordt uitgevoerd onder de volledige Lucene parser. 

De volgende voorbeelden illustreren het punt: dezelfde query, maar met verschillende queryType-instellingen, leveren verschillende resultaten op. In de eerste `^3` query `historic` wordt de after behandeld als onderdeel van de zoekterm. Het top-ranked resultaat voor deze vraag is "Marquis Plaza & Suites", die *oceaan* in zijn beschrijving heeft

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

Dezelfde query met behulp van de volledige `^3` Lucene parser interpreteert als een in-field term booster. Schakelen parsers verandert de rang, met resultaten met de term *historische* verplaatsen naar de top.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typen query's

Azure Cognitive Search ondersteunt een breed scala aan querytypen. 

| Querytype | Gebruik | Voorbeelden en meer informatie |
|------------|--------|-------------------------------|
| Zoeken naar tekst in vrije vorm | Zoekparameter en parser| Full text search scant naar een of meer termen in alle *doorzoekbare* velden in uw index en werkt zoals u zou verwachten dat een zoekmachine als Google of Bing werkt. Het voorbeeld in de inleiding is full text search.<br/><br/>Full text search ondergaat tekstanalyse met behulp van de standaard Lucene analyzer (standaard) om alle termen te verlagen, stopwoorden als "de" te verwijderen. U de standaardinstelling overschrijven met [niet-Engelse analysators](index-add-language-analyzers.md#language-analyzer-list) of [gespecialiseerde taalagnostische analysators](index-add-custom-analyzers.md#AnalyzerTable) die tekstanalyse wijzigen. Een voorbeeld is [trefwoord](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) dat de volledige inhoud van een veld als één token behandelt. Dit is handig voor gegevens zoals postcodes, id's en sommige productnamen. | 
| Gefilterd zoeken | [OData-filterexpressie](query-odata-filter-orderby-syntax.md) en parser | Filterquery's evalueren een booleaanse expressie over alle *filterbare* velden in een index. In tegenstelling tot zoeken komt een filterquery overeen met de exacte inhoud van een veld, inclusief hoofdlettergevoeligheid op tekenreeksvelden. Een ander verschil is dat filterquery's worden uitgedrukt in de syntaxis van OData. <br/>[Voorbeeld van filterexpressie](search-query-simple-examples.md#example-3-filter-queries) |
| Op geografische locaties zoeken | [Edm.GeographyPoint-type](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) op het veld, filterexpressie en parser | Coördinaten die zijn opgeslagen in een veld met een Edm.GeographyPoint worden gebruikt voor 'zoekbij mij in de buurt' of zoekbesturingselementen op basis van kaarten. <br/>[Voorbeeld van geo-zoeken](search-query-simple-examples.md#example-5-geo-search)|
| Zoeken in bereik | filterexpressie en eenvoudige parser | In Azure Cognitive Search worden bereikquery's gebouwd met behulp van de parameter filter. <br/>[Voorbeeld van het filter bereik](search-query-simple-examples.md#example-4-range-filters) | 
| [Zoeken in velden](query-lucene-syntax.md#bkmk_fields) | Zoekparameter en Volledige parser | Maak een samengestelde queryexpressie die is gericht op één veld. <br/>[Voorbeeld van zoeken in velden](search-query-lucene-examples.md#example-2-fielded-search) |
| [fuzzy zoeken](query-lucene-syntax.md#bkmk_fuzzy) | Zoekparameter en Volledige parser | Komt overeen met voorwaarden met een vergelijkbare constructie of spelling. <br/>[Voorbeeld van fuzzy zoeken](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [nabijheidzoeken](query-lucene-syntax.md#bkmk_proximity) | Zoekparameter en Volledige parser | Hiermee worden termen gevonden die dicht bij elkaar liggen in een document. <br/>[Voorbeeld van nabijheidzoeken](search-query-lucene-examples.md#example-4-proximity-search) |
| [term stimuleren](query-lucene-syntax.md#bkmk_termboost) | Zoekparameter en Volledige parser | Hiermee rangschikt u een document hoger als het de versterkte term bevat, ten opzichte van andere die dat niet doen. <br/>[Voorbeeld van termboosting](search-query-lucene-examples.md#example-5-term-boosting) |
| [zoeken naar reguliere expressies](query-lucene-syntax.md#bkmk_regex) | Zoekparameter en Volledige parser | Komt overeen op basis van de inhoud van een reguliere expressie. <br/>[Voorbeeld van reguliere expressie](search-query-lucene-examples.md#example-6-regex) |
|  [zoeken naar jokertekens of voorvoegsel](query-lucene-syntax.md#bkmk_wildcard) | Zoekparameter en Volledige parser | Overeenkomsten op basis van een`~`voorvoegsel en`?`tilde ( ) of enkel teken ( ). <br/>[Voorbeeld van jokerzoekvoorbeeld](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Zoekresultaten beheren 

Queryresultaten worden gestreamd als JSON-documenten in de REST API, maar als u .NET-API's gebruikt, wordt serialisatie ingebouwd. U resultaten vormgeven door parameters in te stellen voor de query en specifieke velden voor het antwoord te selecteren.

Parameters op de query kunnen worden gebruikt om de resultatenset op de volgende manieren te structureren:

+ Het aantal documenten in de resultaten beperken of batcheren (standaard 50)
+ Velden selecteren die in de resultaten moeten worden opgenomen
+ Een sorteervolgorde instellen
+ Het toevoegen van hit highlights om de aandacht te vestigen op overeenkomende termen in de hoofdtekst van de zoekresultaten

### <a name="tips-for-unexpected-results"></a>Tips voor onverwachte resultaten

Af en toe, de stof en niet de structuur van de resultaten zijn onverwacht. Wanneer queryresultaten niet zijn wat u verwacht te zien, u deze querywijzigingen proberen om te zien of de resultaten verbeteren:

+ Wijzigen **`searchMode=any`** (standaard) **`searchMode=all`** om overeenkomsten op alle criteria te vereisen in plaats van een van de criteria. Dit geldt vooral wanneer booleaanse operatoren de query zijn opgenomen.

+ Wijzig de querytechniek als tekst of lexicale analyse nodig is, maar het querytype sluit taalkundige verwerking uit. In full text search, tekst of lexicale analyse autocorrects voor spelfouten, enkelvoud-meervoud woord vormen, en zelfs onregelmatige werkwoorden of zelfstandige naamwoorden. Voor sommige query's, zoals fuzzy of wildcard search, maakt tekstanalyse geen deel uit van de pijplijn voor queryparsing. Voor sommige scenario's zijn reguliere expressies gebruikt als tijdelijke oplossing. 

### <a name="paging-results"></a>Resultaten pagineren
Azure Cognitive Search maakt het eenvoudig om paging van zoekresultaten te implementeren. Door de **`top`** **`skip`** parameters en parameters te gebruiken, u probleemloos zoekaanvragen uitgeven waarmee u de totale set zoekresultaten ontvangen in beheerbare, geordende subsets die eenvoudig goede zoek-ui-praktijken mogelijk maken. Tijdens het ontvangen van deze kleinere subsets van resultaten kunt u ook het aantal documenten weergeven in het totale aantal zoekresultaten.

Meer informatie over het paging-zoekresultaten vindt u in het artikel [Zoekresultaten op de pagina in Azure Cognitive Search.](search-pagination-page-layout.md)

### <a name="ordering-results"></a>Resultaten ordenen
Wanneer u resultaten voor een zoekopdracht ontvangt, u vragen dat Azure Cognitive Search de resultaten die zijn geordend op basis van waarden in een specifiek veld, weergeeft. Azure Cognitive Search bestelt standaard de zoekresultaten op basis van de rang van de zoekscore van elk document, die is afgeleid van [TF-IDF.](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)

Als u wilt dat Azure Cognitive Search uw resultaten die zijn besteld **`orderby`** op basis van een andere waarde dan de zoekscore, terugkrijgt, u de zoekparameter gebruiken. U de waarde **`orderby`** van de parameter opgeven om veldnamen en aanroepen naar de [** `geo.distance()` functie**](query-odata-filter-orderby-syntax.md) voor georuimtelijke waarden op te nemen. Elke expressie kan `asc` worden gevolgd door aan te geven **`desc`** dat de resultaten worden aangevraagd in oplopende volgorde, en om aan te geven dat de resultaten worden aangevraagd in aflopende volgorde. De standaard oplopende volgorde.


### <a name="hit-highlighting"></a>Markeren
In Azure Cognitive Search wordt het benadrukken van het exacte gedeelte van **`highlight`** de **`highlightPreTag`** zoekresultaten **`highlightPostTag`** dat overeenkomt met de zoekopdracht eenvoudig gemaakt met behulp van de , en parameters. U opgeven welke *doorzoekbare* velden hun overeenkomende tekst moeten benadrukken en de exacte tekenreekstags opgeven die moeten worden toegevoegd aan het begin en einde van de overeenkomende tekst die Azure Cognitive Search retourneert.

## <a name="see-also"></a>Zie ook

+ [Hoe zoeken in volledige tekst werkt in Azure Cognitive Search (queryparsing-architectuur)](search-lucene-query-architecture.md)
+ [Zoekverkenner](search-explorer.md)
+ [Hoe query's in .NET](search-query-dotnet.md)
+ [Vragen in REST](search-create-index-rest-api.md)
