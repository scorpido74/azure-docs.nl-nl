---
title: Een eenvoudige query maken
titleSuffix: Azure Cognitive Search
description: Leer bijvoorbeeld door query's uit te voeren op basis van de eenvoudige syntaxis voor zoeken in volledige tekst, zoeken in filters, geozoeken, gefacetteerd zoeken op basis van een Azure Cognitive Search-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3a801af7b97954510139a009a6d1344b281cf056
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261802"
---
# <a name="create-a-simple-query-in-azure-cognitive-search"></a>Een eenvoudige query maken in Azure Cognitive Search

In Azure Cognitive Search roept de [eenvoudige querysyntaxis](query-simple-syntax.md) de standaardqueryparser aan voor het uitvoeren van zoekopdrachten met volledige tekst op basis van een index. Deze parser is snel en behandelt veelvoorkomende scenario's, waaronder zoeken in volledige tekst, gefilterd en gefacetteerd zoeken en geo-search. 

In dit artikel gebruiken we voorbeelden om de eenvoudige `search=` syntaxis te illustreren, waarbij de parameter van een bewerking [Zoekdocumenten](https://docs.microsoft.com/rest/api/searchservice/search-documents) wordt groepert.

Een alternatieve querysyntaxis is [Full Lucene](query-lucene-syntax.md), dat complexere querystructuren ondersteunt, zoals fuzzy en wildcard search, wat extra tijd kan kosten om te verwerken. Zie De volledige syntaxis van [Lucene gebruiken](search-query-lucene-examples.md)voor meer informatie en voorbeelden die de volledige syntaxis van Lucene weergeven.

## <a name="formulate-requests-in-postman"></a>Verzoeken formuleren in Postbode

De volgende voorbeelden maken gebruik van een NYC Jobs-zoekindex die bestaat uit vacatures die beschikbaar zijn op basis van een gegevensset die wordt geleverd door het [OpenData-initiatief van](https://nycopendata.socrata.com/) de stad New York. Deze gegevens mogen niet als actueel of volledig worden beschouwd. De index bevindt zich op een sandbox-service van Microsoft, wat betekent dat u geen Azure-abonnement of Azure Cognitive Search nodig hebt om deze query's uit te proberen.

Wat je wel nodig hebt is Postbode of een gelijkwaardig hulpmiddel voor het uitgeven van HTTP-aanvraag op GET. Zie [Quickstart: Azure Cognitive Search REST API verkennen met Postman](search-get-started-postman.md)voor meer informatie.

### <a name="set-the-request-header"></a>De aangezochte koptekst instellen

1. Stel **inhoudstype** in de `application/json`koptekst van het verzoek in op .

2. Voeg een **api-toets toe**en stel `252044BE3886FE4A8E3BAA4F595114BB`deze in op deze tekenreeks: . Dit is een querysleutel voor de sandbox-zoekservice die de NYC-banenindex host.

Nadat u de aanvraagkophebt opgegeven, u deze opnieuw gebruiken voor alle query's in dit artikel, waarbij alleen de **zoektekenreeks wordt** verwisseld. 

  ![De aanvraagheader voor Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>De aanvraag-URL instellen

Request is een GET-opdracht gekoppeld aan een URL met het Azure Cognitive Search-eindpunt en de zoektekenreeks.

  ![De aanvraagheader voor Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

URL-samenstelling heeft de volgende elementen:

+ **`https://azs-playground.search.windows.net/`** is een sandbox-zoekservice die wordt onderhouden door het Azure Cognitive Search-ontwikkelingsteam. 
+ **`indexes/nycjobs/`** is de NYC Jobs index in de indexen verzameling van die dienst. Zowel de servicenaam als de index zijn vereist op de aanvraag.
+ **`docs`** is de verzameling documenten met alle doorzoekbare inhoud. De query-api-toets in de aanvraagkop werkt alleen bij leesbewerkingen die gericht zijn op het verzamelen van documenten.
+ **`api-version=2019-05-06`** hiermee wordt de api-versie ingesteld, een vereiste parameter op elke aanvraag.
+ **`search=*`** is de querytekenreeks, die in de eerste query null is, waardoor de eerste 50 resultaten (standaard) worden retournerd.

## <a name="send-your-first-query"></a>Uw eerste query verzenden

Plak als verificatiestap het volgende verzoek in GET en klik op **Verzenden**. Resultaten worden geretourneerd als uitgebreide JSON-documenten. Volledige documenten worden geretourneerd, zodat u alle velden en alle waarden zien.

Plak deze URL in een REST-client als validatiestap en om de documentstructuur weer te geven.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

De querytekenreeks **`search=*`** is een niet-gespecificeerde zoekopdracht die overeenkomt met null of leeg zoeken. Het is niet bijzonder nuttig, maar het is de eenvoudigste zoekopdracht die je doen.

Optioneel u **`$count=true`** aan de URL toevoegen om een aantal documenten terug te geven dat overeenkomt met de zoekcriteria. Op een lege zoektekenreeks, dit is alle documenten in de index (ongeveer 2800 in het geval van NYC Jobs).

## <a name="how-to-invoke-simple-query-parsing"></a>Eenvoudige queryparsing aanroepen

Voor interactieve query's hoeft u niets op te geven: eenvoudig is de standaardinstelling. Als u in code eerder **queryType=volledig** hebt aangeroepen voor de volledige querysyntaxis, u de standaardinstelling opnieuw instellen met **queryType=simple**.

## <a name="example-1-field-scoped-query"></a>Voorbeeld 1: Query met veldbereik

Dit eerste voorbeeld is niet parser-specifiek, maar we leiden ermee om het eerste fundamentele queryconcept te introduceren: containment. In dit voorbeeld worden queryuitvoeringen en het antwoord op slechts enkele specifieke velden uitgevoerd. Weten hoe u een leesbare JSON-respons structureren, is belangrijk wanneer uw tool Postman of Search Explorer is. 

Voor beknoptheid richt de query zich alleen op het *business_title* veld en geeft alleen bedrijfstitels worden geretourneerd. De syntaxis is **searchFields** om query-uitvoering te beperken tot alleen het business_title veld en **selecteer** om op te geven welke velden in het antwoord zijn opgenomen.

### <a name="partial-query-string"></a>Tekenreeks gedeeltelijke query

```http
searchFields=business_title&$select=business_title&search=*
```

Hier is dezelfde query met meerdere velden in een lijst met komma's.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>Volledige URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=business_title&$select=business_title&search=*
```

Het antwoord op deze query moet lijken op de volgende schermafbeelding.

  ![Reactie postervoorbeeld](media/search-query-lucene-examples/postman-sample-results.png)

Je hebt misschien de zoekscore in de reactie opgemerkt. Uniforme scores van 1 optreden wanneer er geen rang is, hetzij omdat de zoekopdracht niet full text search was, of omdat er geen criteria zijn toegepast. Voor null zoeken zonder criteria, rijen komen terug in willekeurige volgorde. Wanneer u werkelijke criteria opneemt, ziet u zoekscores evolueren naar betekenisvolle waarden.

## <a name="example-2-look-up-by-id"></a>Voorbeeld 2: Opzoeken op id

Dit voorbeeld is een beetje atypisch, maar wanneer u zoekgedrag evalueert, u de volledige inhoud van een specifiek document inspecteren om te begrijpen waarom het is opgenomen of uitgesloten van resultaten. Als u één document in zijn geheel wilt retourneren, gebruikt u een [opzoekbewerking](https://docs.microsoft.com/rest/api/searchservice/lookup-document) om de document-id door te geven.

Alle documenten hebben een unieke id. Als u de syntaxis voor een opzoekquery wilt uitproberen, retourneert u eerst een lijst met document-id's, zodat u er een vinden om te gebruiken. Voor NYC-jobs worden de id's in het `id` veld opgeslagen.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=id&$select=id&search=*
```

Het volgende voorbeeld is een opzoekquery `id` waarbij een specifiek document wordt teruggegeven op basis van "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", die als eerste verscheen in het vorige antwoord. Met de volgende query wordt het hele document geretourneerd, niet alleen geselecteerde velden. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2019-05-06&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Voorbeeld 3: Filterquery's

[Filtersyntaxis](https://docs.microsoft.com/azure/search/search-query-odata-filter) is een OData-expressie die u gebruiken met **zoeken** of op zichzelf. Een zelfstandig filter, zonder zoekparameter, is handig wanneer de filterexpressie documenten van belang volledig kan kwalificeren. Zonder querystring is er geen lexicale of linguïstische analyse, geen score (alle scores zijn 1) en geen rangschikking. Let op: de zoektekenreeks is leeg.

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Samen wordt het filter eerst op de hele index toegepast en vervolgens wordt de zoekopdracht uitgevoerd op de resultaten van het filter. Filters zijn dus nuttig om de resultaten van de zoekopdracht te verbeteren, doordat het aantal documenten dat moet worden doorzocht, wordt verminderd.

  ![Queryrespons filteren](media/search-query-simple-examples/filtered-query.png)

Als u dit wilt uitproberen in Postman met GET, u in deze tekenreeks plakken:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Een andere krachtige manier om **`search.ismatch*()`** filter en zoeken te combineren is door in een filterexpressie, waar u een zoekopdracht in het filter gebruiken. Deze filterexpressie gebruikt een wildcard op *het plan* om business_title te selecteren, waaronder het termenplan, de planner, de planning enzovoort.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Zie [search.ismatch in 'Voorbeelden filteren' voor](https://docs.microsoft.com/azure/search/search-query-odata-full-text-search-functions#examples)meer informatie over de functie.

## <a name="example-4-range-filters"></a>Voorbeeld 4: Bereikfilters

Gereedschapsfiltering wordt **`$filter`** ondersteund via expressies voor elk gegevenstype. In de volgende voorbeelden wordt gezocht over numerieke en tekenreeksvelden. 

Gegevenstypen zijn belangrijk in bereikfilters en werken het beste wanneer numerieke gegevens zich in numerieke velden bevinden en tekenreeksgegevens in tekenreeksvelden. Numerieke gegevens in tekenreeksvelden zijn niet geschikt voor bereiken omdat numerieke tekenreeksen niet vergelijkbaar zijn in Azure Cognitive Search. 

De volgende voorbeelden zijn in post-indeling voor leesbaarheid (numeriek bereik, gevolgd door tekstbereik):

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  ![Bereikfilter voor numerieke bereiken](media/search-query-simple-examples/rangefilternumeric.png)


```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  ![Bereikfilter voor tekstbereiken](media/search-query-simple-examples/rangefiltertext.png)

U deze ook uitproberen in Postman met GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> Faceting over waardenbereiken is een veelvoorkomende vereiste voor zoektoepassingen. Zie 'Filteren op basis van een bereik' voor meer informatie en voorbeelden over het bouwen van filters voor facetnavigatiestructuren [in *Hoe gefacetteerde navigatie te implementeren.*](search-faceted-navigation.md#filter-based-on-a-range)

## <a name="example-5-geo-search"></a>Voorbeeld 5: Geo-search

De voorbeeldindex bevat een geo_location veld met breedte- en lengtecoördinaten. In dit voorbeeld wordt de [geo.distance-functie](https://docs.microsoft.com/azure/search/search-query-odata-geo-spatial-functions#examples) gebruikt die filtert op documenten binnen de omtrek van een startpunt, tot een willekeurige afstand (in kilometers) die u verstrekt. U de laatste waarde in de query (4) aanpassen om het oppervlak van de query te verkleinen of vergroten.

Het volgende voorbeeld is in post-indeling voor leesbaarheid:

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
Voor meer leesbare resultaten worden zoekresultaten bijgesneden met een taak-id, functietitel en de werklocatie. De startcoördinaten werden verkregen uit een willekeurig document in de index (in dit geval voor een werklocatie op Staten eiland.

Je dit ook uitproberen in Postman met GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Voorbeeld 6: Zoekprecisie

Term queries zijn enkele termen, misschien veel van hen, die onafhankelijk worden geëvalueerd. Woordgroepquery's worden tussen aanhalingstekens ingesloten en geëvalueerd als een letterlijke tekenreeks. Precisie van de match wordt gecontroleerd door operators en searchMode.

Voorbeeld 1: **`&search=fire`** retourneert 150 resultaten, waarbij alle overeenkomsten het woord vuur ergens in het document bevatten.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire
```

Voorbeeld 2: **`&search=fire department`** retourneert de resultaten van 2002. Wedstrijden worden geretourneerd voor documenten die brand of brandweer bevatten.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire department
```

Voorbeeld 3: **`&search="fire department"`** geeft 82 resultaten weer. Het bijvoegen van de tekenreeks in aanhalingstekens is een letterlijke zoekopdracht op beide termen en overeenkomsten worden gevonden op tokenized termen in de index die bestaat uit de gecombineerde termen. Dit verklaart waarom **`search=+fire +department`** een zoekopdracht als niet gelijkwaardig is. Beide voorwaarden zijn vereist, maar worden onafhankelijk gescand. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Voorbeeld 7: Booleans met searchMode

Eenvoudige syntaxis ondersteunt booleaanse operatoren in de vorm van tekens (`+, -, |`). De parameter searchMode informeert afwegingen tussen `searchMode=any` precisie en terugroeping, waarbij het terugroepen wordt `searchMode=all` goedgekeurd (matching op alle criteria kwalificeert een document voor de resultaatset) en het begunstigen van precisie (alle criteria moeten worden geëvenaard). De standaardinstelling is `searchMode=any`, wat verwarrend kan zijn als u een query met meerdere operatoren stapelt en breder wordt in plaats van smallere resultaten. Dit geldt met name voor NOT, waarbij alle documenten "die geen specifieke term bevatten" bevatten.

Met behulp van de standaard searchMode (alle), 2800 documenten worden geretourneerd: die met de meerdelige term "brandweer", plus alle documenten die niet de term "Metrotech Center".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![zoekmodus elke](media/search-query-simple-examples/searchmodeany.png)

Zoekmodus wijzigen `all` om een cumulatief effect op criteria af te dwingen en een kleinere resultaatset - 21 documenten - bestaande uit documenten met de volledige zin "brandweer", minus die taken op het adres van het Metrotech Center.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![zoekmodus alles](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>Voorbeeld 8: Resultaten structureren

Verschillende parameters bepalen welke velden zich in de zoekresultaten bevinden, het aantal documenten dat in elke batch wordt geretourneerd en sorteervolgorde. In dit voorbeeld worden enkele van de vorige voorbeelden opnieuw **$select** opgedoken, waardoor de resultaten worden beperkt tot specifieke velden met behulp van de $select-instructie- en letterlijke zoekcriteria, waardoor 82 overeenkomsten worden retournerd 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Als u aan het vorige voorbeeld wordt toegevoegd, u sorteren op titel. Deze sortering werkt omdat civil_service_title in de index kan worden *gesorteerd.*

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Paging resultaten wordt geïmplementeerd met behulp van de **$top** parameter, in dit geval het retourneren van de top 5 documenten:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Sla de eerste batch over om de volgende 5 te krijgen:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Volgende stappen
Probeer query's op te geven in uw code. In de volgende koppelingen wordt uitgelegd hoe u zoekopdrachten instelt voor zowel .NET als de REST API met behulp van de standaardsyntaxis.

* [Uw index opvragen met de .NET SDK](search-query-dotnet.md)
* [Uw index opvragen met de REST-API](search-create-index-rest-api.md)

Aanvullende syntaxisverwijzing, queryarchitectuur en voorbeelden zijn te vinden in de volgende koppelingen:

+ [Voorbeelden van lucene syntaxisquery's voor het maken van geavanceerde query's](search-query-lucene-examples.md)
+ [Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Vereenvoudigde querysyntaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Volledige Lucene-query](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Syntaxis van filteren en orderen](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
