---
title: Query typen en samen stelling-Azure Search
description: Basis principes voor het bouwen van een zoek query in Azure Search, met behulp van para meters voor het filteren, selecteren en sorteren van resultaten.
author: HeidiSteen
manager: nitinme
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 4646cb30ef7602da990e24f923c8eceada4debd0
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "71178039"
---
# <a name="query-types-and-composition-in-azure-search"></a>Query typen en samen stelling in Azure Search

In Azure Search is een query een volledige specificatie van een round-trip bewerking. De para meters in de aanvraag bieden match criteria voor het zoeken van documenten in een index, welke velden moeten worden opgenomen of uitgesloten, uitvoerings instructies worden door gegeven aan de engine en de richt lijnen voor het vorm geven van de reactie. Niet opgegeven (`search=*`), een query wordt uitgevoerd voor alle Doorzoek bare velden als een zoek bewerking in volledige tekst, waarbij een oneven resultaat wordt geretourneerd in een wille keurige volg orde.

Het volgende voor beeld is een representatieve query die is geconstrueerd in de [rest API](https://docs.microsoft.com/rest/api/searchservice/search-documents). In dit voor beeld wordt de index voor de [Hotels-demo](search-get-started-portal.md) en de algemene para meters opgenomen.

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

+ **`queryType`** stelt de parser in. Dit is de [standaard eenvoudige query-parser](search-query-simple-examples.md) (optimaal voor zoeken in volledige tekst) of de [volledige lucene-query-parser](search-query-lucene-examples.md) die wordt gebruikt voor geavanceerde query constructies zoals reguliere expressies, proximity Search, fuzzy en Zoek opdracht met Joker tekens.

+ **`search`** biedt de overeenkomende criteria, meestal tekst, maar vaak vergezeld van Booleaanse Opera tors. Enkele zelfstandige termen zijn *term* query's. Met een aanhalings teken voor meerdere delen worden query's voor de *sleutel woordgroepen* uitgevoerd. De zoek opdracht kan niet worden gedefinieerd, zoals in **`search=*`** , maar dit is waarschijnlijk een aantal termen, zinsdelen en Opera tors die vergelijkbaar zijn met wat er in het voor beeld wordt weer gegeven.

+ **`searchFields`** beperkt de uitvoering van query's naar specifieke velden. Elk veld waarvoor een *zoekbaar* kenmerk is in het index schema, is een kandidaat voor deze para meter.

Antwoorden worden ook gevormd door de para meters die u in de query opneemt. In het voor beeld bestaat de resultatenset uit velden die worden weer gegeven in de **`select`** -instructie. Alleen velden die zijn gemarkeerd als *ophalen* kunnen worden gebruikt in een $SELECT-instructie. Daarnaast worden alleen de **`top`** tien treffers geretourneerd in deze query, terwijl **`count`** u vertelt hoeveel documenten er allemaal overeenkomen, wat meer kan zijn dan wat er wordt geretourneerd. In deze query worden rijen gesorteerd op waardering in aflopende volg orde.

In Azure Search wordt de uitvoering van query's altijd vergeleken met één index, geauthenticeerd met een API-sleutel die in de aanvraag is opgenomen. In REST worden beide weer gegeven in aanvraag headers.

### <a name="how-to-run-this-query"></a>Deze query uitvoeren

U kunt deze query uitvoeren met behulp van [Search Explorer en de index van de hotels](search-get-started-portal.md). 

U kunt deze query teken reeks in de zoek balk van de Explorer plakken: `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Hoe query bewerkingen worden ingeschakeld door de index

Het ontwerp van de index en het query ontwerp zijn nauw gekoppeld aan Azure Search. Een essentieel feit dat u vooraf moet weten, is dat het *index schema*, met kenmerken voor elk veld, bepaalt welk soort query u kunt maken. 

Index kenmerken voor een veld stel de toegestane bewerkingen in: Hiermee stelt u in dat een veld in de index kan worden *doorzocht* en kan worden *opgehaald* , *gesorteerd*, *gefilterd*, enzovoort. In de voorbeeld query teken reeks werkt `"$orderby": "Rating"` alleen, omdat het veld classificatie als *sorteerbaar* is gemarkeerd in het index schema. 

![Index definitie voor het voor beeld van een hotel](./media/search-query-overview/hotel-sample-index-definition.png "Index definitie voor het voor beeld van een hotel")

De bovenstaande scherm afbeelding is een gedeeltelijke lijst met index kenmerken voor het voor beeld van hotels. U kunt het volledige index schema weer geven in de portal. Zie [Create index rest API](https://docs.microsoft.com/rest/api/searchservice/create-index)voor meer informatie over index kenmerken.

> [!Note]
> Sommige query functies zijn in index-breed en niet per veld. Deze mogelijkheden omvatten: [synoniemen](search-synonyms.md), [aangepaste analyse](index-add-custom-analyzers.md)functies, [suggestie constructies (voor automatisch aanvullen en voorgestelde query's)](index-add-suggesters.md), [Score logica voor classificatie resultaten](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Elementen van een query aanvraag

Query's zijn altijd gericht op één index. U kunt geen indexen samen voegen of aangepaste of tijdelijke gegevens structuren maken als een query doel. 

Vereiste elementen voor een query aanvraag zijn onder andere de volgende onderdelen:

+ Verzameling service-eind punten en index documenten, uitgedrukt als een URL met vaste en door de gebruiker gedefinieerde onderdelen: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (alleen rest) is nodig omdat meer dan één versie van de API te allen tijde beschikbaar is. 
+ **`api-key`** , ofwel een query of beheerder-API-sleutel, verifieert de aanvraag bij uw service.
+ **`queryType`** , een eenvoudige of volledige, die kan worden wegge laten als u de ingebouwde standaard eenvoudige syntaxis gebruikt.
+ **`search`** of **`filter`** biedt de overeenkomende criteria, die niet kunnen worden opgegeven als u een lege zoek opdracht wilt uitvoeren. Beide query typen worden besproken in termen van de eenvoudige parser, maar zelfs geavanceerde query's vereisen de zoek parameter voor het door geven van complexe query expressies.

Alle andere zoek parameters zijn optioneel. Zie [Create Index (rest) (Engelstalig)](https://docs.microsoft.com/rest/api/searchservice/create-index)voor een volledige lijst met kenmerken. Zie [Hoe zoeken in volledige tekst werkt in azure Search](search-lucene-query-architecture.md)voor meer informatie over hoe para meters worden gebruikt tijdens de verwerking.

## <a name="choose-apis-and-tools"></a>Kies Api's en hulpprogram ma's

De volgende tabel geeft een lijst van de Api's en hulp op basis van gereedschappen voor het verzenden van query's.

| Methodologie | Beschrijving |
|-------------|-------------|
| [Search Explorer (Portal)](search-explorer.md) | Voorziet in een zoek balk en opties voor selecties van index en API-versie. Resultaten worden geretourneerd als JSON-documenten. Aanbevolen voor verkennen, testen en valideren. <br/>[Meer informatie.](search-get-started-portal.md#query-index) | 
| [Postman of andere REST-hulp middelen](search-get-started-postman.md) | Webtest-hulpprogram ma's zijn een uitstekende keuze voor het formuleren van REST-aanroepen. De REST API ondersteunt elke mogelijke bewerking in Azure Search. In dit artikel vindt u informatie over het instellen van een HTTP-aanvraag header en hoofd tekst voor het verzenden van aanvragen naar Azure Search.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Client die kan worden gebruikt om een Azure Search index op te vragen.  <br/>[Meer informatie.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Documenten zoeken (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | GET-of POST-methoden voor een index met behulp van query parameters voor aanvullende invoer.  |

## <a name="choose-a-parser-simple--full"></a>Kies een parser: eenvoudig | waard

Azure Search bevindt zich boven Apache Lucene en biedt u een keuze tussen twee query-parsers voor het verwerken van typische en gespecialiseerde query's. Aanvragen die gebruikmaken van de eenvoudige parser worden geformuleerd met behulp van de [eenvoudige query syntaxis](query-simple-syntax.md), geselecteerd als de standaard waarde voor de snelheid en effectiviteit in vrije-tekst query's. Deze syntaxis ondersteunt een aantal veelgebruikte Zoek operatoren, waaronder de Opera tors en, of, niet, frase, achtervoegsel en voor rang.

De [volledige lucene-query syntaxis](query-Lucene-syntax.md#bkmk_syntax), ingeschakeld wanneer u `queryType=full` toevoegt aan de aanvraag, geeft de uitgebreide en duidelijke query taal weer die is ontwikkeld als onderdeel van [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). De volledige syntaxis breidt de eenvoudige syntaxis uit. Alle query's die u voor de eenvoudige syntaxis schrijft, worden uitgevoerd onder de volledige lucene-parser. 

In de volgende voor beelden ziet u het punt: dezelfde query, maar met verschillende query type-instellingen kunt u verschillende resultaten opleveren. In de eerste query wordt de `^3` na `historic` beschouwd als onderdeel van de zoek term. Het hoogste geclassificeerde resultaat voor deze query is "Marquis Plaza & Suites", die *Oceaan* in de beschrijving heeft

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

Dezelfde query die gebruikmaakt van de volledige lucene-parser, interpreteert `^3` als een in-Field Booster. Switch parsers wijzigen de positie, met de resultaten met de term *historisch* naar boven.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typen query's

Azure Search ondersteunt een breed scala aan query typen. 

| Query type | Gebruik | Voor beelden en meer informatie |
|------------|--------|-------------------------------|
| Zoek opdracht in vrije tekst | Zoek parameter en beide parsers| Zoek opdrachten in volledige tekst scans voor een of meer voor waarden in alle *Doorzoek* bare velden in uw index en werkt op de manier waarop u een zoek machine zoals Google of Bing verwacht. Het voor beeld in de inleiding is zoeken in volledige tekst.<br/><br/>Met zoeken in volledige tekst wordt tekst analyse met behulp van de standaard-lucene Analyzer (standaard) gebruikt om alle voor waarden te kleine letters te verwijderen, stop woorden zoals ' de '. U kunt de standaard instelling overschrijven met [niet-Engelse analyses](index-add-language-analyzers.md#language-analyzer-list) of [gespecialiseerde neutraal-](index-add-custom-analyzers.md#AnalyzerTable) analyse functies waarmee tekst analyse wordt gewijzigd. Een voor beeld is een [tref woord](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) dat de volledige inhoud van een veld als één token behandelt. Dit is handig voor gegevens zoals post codes, Id's en sommige product namen. | 
| Gefilterde zoek opdracht | [OData-filter expressie](query-odata-filter-orderby-syntax.md) en een van beide parsers | Met filter query's wordt een booleaanse expressie voor alle *filter* bare velden in een index geëvalueerd. In tegens telling tot zoeken komt een filter query overeen met de exacte inhoud van een veld, inclusief hoofdletter gevoeligheid voor teken reeks velden. Een ander verschil is dat filter query's worden weer gegeven in de OData-syntaxis. <br/>[Voor beeld van filter expressie](search-query-simple-examples.md#example-3-filter-queries) |
| Op geografische locaties zoeken | Het [type EDM. GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) in het veld, filter expressie en beide parsers | Coördinaten die zijn opgeslagen in een veld met een EDM. GeographyPoint worden gebruikt voor de besturings elementen "dichtbij zoeken" of op basis van een kaart. <br/>[Voor beeld van geo-zoeken](search-query-simple-examples.md#example-5-geo-search)|
| Bereik zoeken | filter expressie en eenvoudige parser | In Azure Search worden bereik query's gemaakt met behulp van de filter parameter. <br/>[Voor beeld van Range-filter](search-query-simple-examples.md#example-4-range-filters) | 
| [Zoek opdracht in veld](query-lucene-syntax.md#bkmk_fields) | Zoek parameter en volledige parser | Bouw een samengestelde query-expressie die is gericht op één veld. <br/>[Voor beeld van een zoek opdracht naar een veld](search-query-lucene-examples.md#example-2-fielded-search) |
| [Zoek actie op fuzzy](query-lucene-syntax.md#bkmk_fuzzy) | Zoek parameter en volledige parser | Komt overeen met de termen met een vergelijk bare constructie of spelling. <br/>[Voor beeld van fuzzy zoeken](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [Proximity Search](query-lucene-syntax.md#bkmk_proximity) | Zoek parameter en volledige parser | Hiermee worden zoek termen in een document in de buurt. <br/>[Voor beeld van proximity Search](search-query-lucene-examples.md#example-4-proximity-search) |
| [term versterking](query-lucene-syntax.md#bkmk_termboost) | Zoek parameter en volledige parser | Hiermee wordt een document hoger gerangschikt als het de gestimuleerde term bevat, ten opzichte van andere. <br/>[Voor beeld van een betere term](search-query-lucene-examples.md#example-5-term-boosting) |
| [reguliere expressie zoeken](query-lucene-syntax.md#bkmk_regex) | Zoek parameter en volledige parser | Komt overeen met de inhoud van een reguliere expressie. <br/>[Voor beeld van een reguliere expressie](search-query-lucene-examples.md#example-6-regex) |
|  [Joker teken of voor voegsel zoeken](query-lucene-syntax.md#bkmk_wildcard) | Zoek parameter en volledige parser | Komt overeen met een voor voegsel en een tilde (`~`) of één teken (`?`). <br/>[Zoek voorbeeld voor joker tekens](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Zoek resultaten beheren 

Query resultaten worden gestreamd als JSON-documenten in de REST API, hoewel u .NET Api's gebruikt, is serialisatie ingebouwd. U kunt de resultaten van de vorm bepalen door para meters in te stellen voor de query en specifieke velden te selecteren voor het antwoord.

De para meters in de query kunnen worden gebruikt om de resultatenset op de volgende manieren te structureren:

+ Het aantal documenten in de resultaten beperken of batcheren (standaard 50)
+ Velden selecteren die in de resultaten moeten worden meegenomen
+ Een sorteer volgorde instellen
+ Treffers toevoegen om de aandacht te vestigen op overeenkomende voor waarden in de hoofd tekst van de zoek resultaten

### <a name="tips-for-unexpected-results"></a>Tips voor onverwachte resultaten

Af en toe zijn de stoffen en niet de structuur van de resultaten onverwacht. Wanneer de resultaten van de query niet naar verwachting worden weer geven, kunt u deze query wijzigingen proberen om te zien of het resultaat wordt verbeterd:

+ Wijzig **`searchMode=any`** (standaard) in **`searchMode=all`** om overeenkomsten te vereisen voor alle criteria in plaats van aan de criteria. Dit geldt met name wanneer Boole-Opera tors worden opgenomen in de query.

+ Wijzig de query techniek als tekst-of lexicale analyse nood zakelijk is, maar het query type belet taal verwerking. In zoeken in volledige tekst, tekst-of lexicale analyse auto correctie voor spel fouten, enkelvoudige Word-formulieren en zelfs onregelmatige woorden of zelfstandige naam woorden. Voor sommige query's, zoals fuzzy of joker tekens, is tekst analyse geen onderdeel van de pijp lijn voor het parseren van query's. In sommige gevallen worden reguliere expressies gebruikt als tijdelijke oplossing. 

### <a name="paging-results"></a>Resultaten pagineren
Met Azure Search kunt u gemakkelijk zoekresultaten oproepen. Met behulp van de para meters **`top`** en **`skip`** kunt u probleemloos Zoek opdrachten verzenden waarmee u de totale set met zoek resultaten in beheer bare, bestelde subsets waarmee u gemakkelijk goede zoek acties in de UI kunt kunnen ontvangen. Tijdens het ontvangen van deze kleinere subsets van resultaten kunt u ook het aantal documenten weergeven in het totale aantal zoekresultaten.

Zie het artikel [Zoekresultaten oproepen in Azure Search](search-pagination-page-layout.md) voor meer informatie over het oproepen van zoekresultaten.

### <a name="ordering-results"></a>Resultaten ordenen
De resultaten van een zoekopdracht kunnen door Azure Search worden geordend op de waarde in een bepaald veld. Standaard ordent Azure Search zoekresultaten op basis van de positie van de zoekscore van elk document, die is afgeleid van [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Als u wilt dat Azure Search uw resultaten krijgt te retour neren met een andere waarde dan de zoek Score, kunt u de **`orderby`** Zoek parameter gebruiken. U kunt de waarde van de para meter **`orderby`** opgeven om veld namen op te vragen en aanroepen naar de [ **`geo.distance()` functie**](query-odata-filter-orderby-syntax.md) voor georuimtelijke waarden. Elke expressie kan worden gevolgd door `asc` om aan te geven dat de resultaten in oplopende volg orde worden aangevraagd en **`desc`** om aan te geven dat de resultaten in aflopende volg orde worden aangevraagd. De standaard oplopende volgorde.


### <a name="hit-highlighting"></a>Markeren
In Azure Search is het benadrukken van het exacte deel van de zoek resultaten dat overeenkomt met de zoek query eenvoudig gemaakt met behulp van de para meters **`highlight`** , **`highlightPreTag`** en **`highlightPostTag`** . U kunt aangeven in welke *doorzoekbare* velden de tekst moet worden benadrukt. Ook kunt u de exacte tekenreekslabels opgeven die moeten worden toegevoegd aan het begin en einde van de overeenkomstige tekst die Azure Search retourneert.

## <a name="see-also"></a>Zie ook

+ [De manier waarop zoeken in volledige tekst werkt in Azure Search (architectuur voor het parseren van query's)](search-lucene-query-architecture.md)
+ [Zoek Verkenner](search-explorer.md)
+ [Query's uitvoeren in .NET](search-query-dotnet.md)
+ [Query's uitvoeren in REST](search-create-index-rest-api.md)
