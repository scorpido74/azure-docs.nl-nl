---
title: De syntaxis van de volledige Lucene-query gebruiken
titleSuffix: Azure Cognitive Search
description: Lucene-querysyntaxis voor fuzzy search, proximity search, term boosting, regular expression search en wildcard searches in an Azure Cognitive Search service.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bc691299f38d562aee5c08a89e10372331663f8e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262805"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>De syntaxis van de zoekopdracht "volledig" Lucene gebruiken (geavanceerde query's in Azure Cognitive Search)

Bij het maken van query's voor Azure Cognitive Search u de [standaardeenvoudige queryparser](query-simple-syntax.md) vervangen door de uitgebreidere [Lucene Query Parser in Azure Cognitive Search](query-lucene-syntax.md) om gespecialiseerde en geavanceerde querydefinities te formuleren. 

De Lucene parser ondersteunt complexe queryconstructies, zoals zoekopdrachten met een veldbereik, fuzzy search, infix en achtervoegsel wildcard zoeken, zoeken in de nabijheid, term boosting en reguliere expressie zoeken. De extra kracht wordt geleverd met extra verwerkingsvereisten, dus u moet een iets langere uitvoeringstijd verwachten. In dit artikel u voorbeelden doorlopen waarin querybewerkingen worden weergegeven die beschikbaar zijn bij het gebruik van de volledige syntaxis.

> [!Note]
> Veel van de gespecialiseerde queryconstructies die zijn ingeschakeld via de volledige syntaxis van Lucene query, worden niet [door de tekst geanalyseerd,](search-lucene-query-architecture.md#stage-2-lexical-analysis)wat verrassend kan zijn als u stemming of lemmatisatie verwacht. Lexicale analyse wordt alleen uitgevoerd onder volledige voorwaarden (een term query of woordgroep query). Querytypen met onvolledige termen (voorvoegselquery, wildcardquery, regexquery, fuzzy query) worden rechtstreeks aan de querystructuur toegevoegd, waarbij de analysefase wordt omzeild. De enige transformatie die wordt uitgevoerd op termen van gedeeltelijke query' s, is een lagere behuizing. 
>

## <a name="formulate-requests-in-postman"></a>Verzoeken formuleren in Postbode

De volgende voorbeelden maken gebruik van een NYC Jobs-zoekindex die bestaat uit vacatures die beschikbaar zijn op basis van een gegevensset die wordt geleverd door het [OpenData-initiatief van](https://opendata.cityofnewyork.us/) de stad New York. Deze gegevens mogen niet als actueel of volledig worden beschouwd. De index bevindt zich op een sandbox-service van Microsoft, wat betekent dat u geen Azure-abonnement of Azure Cognitive Search nodig hebt om deze query's uit te proberen.

Wat je wel nodig hebt is Postbode of een gelijkwaardig hulpmiddel voor het uitgeven van HTTP-aanvraag op GET. Zie [Verkennen met REST-clients voor](search-get-started-postman.md)meer informatie.

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

De querytekenreeks **`search=*`** is een niet-gespecificeerde zoekopdracht die overeenkomt met null of leeg zoeken. Het is de eenvoudigste zoekopdracht die je doen.

Optioneel u **`$count=true`** aan de URL toevoegen om een aantal documenten terug te geven dat overeenkomt met de zoekcriteria. Op een lege zoektekenreeks, dit is alle documenten in de index (ongeveer 2800 in het geval van NYC Jobs).

## <a name="how-to-invoke-full-lucene-parsing"></a>Hoe volledige Lucene parsing beroep

Voeg **queryType=vol toe** om de volledige querysyntaxis aan te roepen, door de standaardsyntaxis van eenvoudige query's te overschrijven. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&search=*
```

In alle voorbeelden in dit artikel wordt de parameter **queryType=full** search opgegeven, wat aangeeft dat de volledige syntaxis wordt verwerkt door de Lucene Query Parser. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Voorbeeld 1: Query die is weergegeven in een lijst met velden

Dit eerste voorbeeld is niet Lucene-specifiek, maar we leiden ermee om het eerste fundamentele queryconcept te introduceren: veldscope. In dit voorbeeld worden de hele query en het antwoord op slechts enkele specifieke velden in de ruimte gekaderd. Weten hoe u een leesbare JSON-respons structureren, is belangrijk wanneer uw tool Postman of Search Explorer is. 

Voor beknoptheid richt de query zich alleen op het *business_title* veld en geeft alleen bedrijfstitels worden geretourneerd. De parameter **searchFields** beperkt query-uitvoering tot alleen het veld business_title en **selecteert** welke velden in het antwoord zijn opgenomen.

### <a name="search-expression"></a>Zoekexpressie

```http
&search=*&searchFields=business_title&$select=business_title
```

Hier is dezelfde query met meerdere velden in een lijst met komma's.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

De spaties na de komma's zijn optioneel.

> [!Tip]
> Vergeet bij het gebruik van de REST API vanuit uw toepassingscode niet om url-coderen parameters zoals `$select` en `searchFields`.

### <a name="full-url"></a>Volledige URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

Het antwoord op deze query moet lijken op de volgende schermafbeelding.

  ![Reactie postervoorbeeld](media/search-query-lucene-examples/postman-sample-results.png)

Je hebt misschien de zoekscore in de reactie opgemerkt. Uniforme scores van 1 optreden wanneer er geen rang is, hetzij omdat de zoekopdracht niet full text search was, of omdat er geen criteria zijn toegepast. Voor null zoeken zonder criteria, rijen komen terug in willekeurige volgorde. Wanneer u werkelijke zoekcriteria opneemt, ziet u zoekscores evolueren naar betekenisvolle waarden.

## <a name="example-2-fielded-search"></a>Voorbeeld 2: Zoeken in velden

Volledige Lucene syntaxis ondersteunt het scoping individuele zoekexpressies naar een specifiek veld. In dit voorbeeld wordt gezocht naar bedrijfstitels met de term senior erin, maar niet junior.

### <a name="search-expression"></a>Zoekexpressie

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Hier is dezelfde query met meerdere velden.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>Volledige URL

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  ![Reactie postervoorbeeld](media/search-query-lucene-examples/intrafieldfilter.png)

U een zoekbewerking met velden definiëren met de syntaxis **fieldName:searchExpression,** waarbij de zoekexpressie een enkel woord of een woordgroep kan zijn, of een complexere expressie tussen haakjes, optioneel met Booleaanse operatoren. Enkele voorbeelden zijn de volgende:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Zorg ervoor dat u meerdere tekenreeksen binnen aanhalingstekens plaatst als u wilt dat beide tekenreeksen als één entiteit worden geëvalueerd, zoals in dit geval zoeken naar twee verschillende locaties in het `state` veld. Zorg er ook voor dat de operator wordt gekapitaliseerd zoals u ziet met NOT en AND.

Het veld dat is opgegeven in **veldNaam:zoekexpressie** moet een doorzoekbaar veld zijn. Zie [Index maken (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) voor meer informatie over hoe indexkenmerken worden gebruikt in velddefinities.

> [!NOTE]
> In het bovenstaande voorbeeld hoefden we `searchFields` de parameter niet te gebruiken omdat in elk deel van de query een veldnaam expliciet is opgegeven. U de `searchFields` parameter echter nog steeds gebruiken als u een query wilt uitvoeren waarbij bepaalde onderdelen zijn ondergebracht bij een bepaald veld en de rest op meerdere velden van toepassing kan zijn. De `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` query komt bijvoorbeeld `senior NOT junior` alleen `business_title` overeen met het veld, terwijl `posting_type` deze overeenkomt met 'extern' met het veld. De veldnaam in **veldNaam:searchExpression** heeft altijd `searchFields` voorrang op de parameter, daarom hoeven we `business_title` in `searchFields` dit voorbeeld niet in de parameter op te nemen.

## <a name="example-3-fuzzy-search"></a>Voorbeeld 3: Fuzzy zoeken

Volledige Lucene syntax is ondersteunt ook fuzzy zoeken, matching op termen die een soortgelijke constructie hebben. Als u een vage zoekopdracht wilt `~` doen, wordt het tildesymbool aan het einde van een enkel woord toegevoegd met een optionele parameter, een waarde tussen 0 en 2, die de bewerkingsafstand aangeeft. Bijvoorbeeld, `blue~` of `blue~1` zou terugkeren blauw, blauw, en lijm.

### <a name="search-expression"></a>Zoekexpressie

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Zinnen worden niet direct ondersteund, maar u een vage overeenkomst opgeven voor onderdelen van een woordgroep.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>Volledige URL

Met deze query wordt gezocht naar taken met de term 'associate' (opzettelijk verkeerd gespeld):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Fuzzy zoekreactie](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> Vage query's worden niet [geanalyseerd.](search-lucene-query-architecture.md#stage-2-lexical-analysis) Querytypen met onvolledige termen (voorvoegselquery, wildcardquery, regexquery, fuzzy query) worden rechtstreeks aan de querystructuur toegevoegd, waarbij de analysefase wordt omzeild. De enige transformatie die wordt uitgevoerd op onvolledige querytermen, is een lagere behuizing.
>

## <a name="example-4-proximity-search"></a>Voorbeeld 4: Zoeken in nabijheid
Nabijheidszoekopdrachten worden gebruikt om termen te vinden die dicht bij elkaar in een document liggen. Plaats een tilde "~" symbool aan het einde van een zin, gevolgd door het aantal woorden dat de nabijheidsgrens maakt. Bijvoorbeeld, "hotel luchthaven"~ 5 vindt de voorwaarden hotel en luchthaven binnen 5 woorden van elkaar in een document.

### <a name="search-expression"></a>Zoekexpressie

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>Volledige URL

In deze query, voor taken met de term "senior analist" waar het wordt gescheiden door niet meer dan een woord:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Nabijheidsquery](media/search-query-lucene-examples/proximity-before.png)

Probeer het opnieuw verwijderen van de woorden tussen de term "senior analist". U ziet dat 8 documenten worden geretourneerd voor deze query in tegenstelling tot 10 voor de vorige query.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Voorbeeld 5: Term boosting
Term boosting verwijst naar het rangschikken van een document hoger als het de versterkte term bevat, ten opzichte van documenten die de term niet bevatten. Als u een term wilt promoten, gebruikt u het caret,"-symbool met een boostfactor (een getal) aan het einde van de term die u zoekt. 

### <a name="full-urls"></a>Volledige URL's

In deze "voor" query, zoeken naar banen met de term *computer analist* en merk op dat er geen resultaten met zowel woorden *computer* en *analist,* maar *computer* banen zijn aan de top van de resultaten.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Term stimuleren voor](media/search-query-lucene-examples/termboostingbefore.png)

In de "na" query, herhaal de zoekopdracht, dit keer het stimuleren van de resultaten met de term *analist* over de term *computer* als beide woorden niet bestaan. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
Een meer menselijke leesbare versie `search=business_title:computer analyst^2`van de bovenstaande query is . Voor een werkbare `^2` query, `%5E2`is gecodeerd als , dat is moeilijker te zien.

  ![Term stimuleren na](media/search-query-lucene-examples/termboostingafter.png)

Term boosting verschilt van scoreprofielen in die zin dat scoreprofielen bepaalde velden stimuleren, in plaats van specifieke termen. In het volgende voorbeeld worden de verschillen geïllustreerd.

Overweeg een scoreprofiel dat overeenkomsten in een bepaald veld verhoogt, zoals **genre** in het voorbeeld van musicstoreindex. Term boosting kan worden gebruikt om bepaalde zoektermen hoger dan andere verder te stimuleren. 'Rock^2 electronic' zal bijvoorbeeld documenten stimuleren die de zoektermen in het **genreveld** hoger zijn dan andere doorzoekbare velden in de index. Bovendien zullen documenten die de zoekterm "rock" bevatten, hoger worden gerangschikt dan de andere zoekterm "elektronisch" als gevolg van de term boost-waarde (2).

Bij het instellen van het factorniveau, hoe hoger de boostfactor, hoe relevanter de term is ten opzichte van andere zoektermen. Standaard is de boostfactor 1. Hoewel de boostfactor positief moet zijn, kan deze minder dan 1 zijn (bijvoorbeeld 0,2).


## <a name="example-6-regex"></a>Voorbeeld 6: Regex

Bij een zoekopdracht met reguliere expressievindt u een overeenkomst op basis van de inhoud tussen voorwaartse slashes "/", zoals gedocumenteerd in de [klasse RegExp.](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)

### <a name="search-expression"></a>Zoekexpressie

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>Volledige URL

Zoek in deze query naar vacatures met de `search=business_title:/(Sen|Jun)ior/`term Senior of Junior: .

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Regex-query](media/search-query-lucene-examples/regex.png)

> [!Note]
> Regex-query's worden niet [geanalyseerd.](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis) De enige transformatie die wordt uitgevoerd op onvolledige querytermen, is een lagere behuizing.
>

## <a name="example-7-wildcard-search"></a>Voorbeeld 7: Zoeken naar jokertekens
U algemeen erkende syntaxis\*gebruiken voor meerdere ( ) of enkele (?) tekenwildcardzoekopdrachten. Let op de Lucene query parser ondersteunt het gebruik van deze symbolen met een enkele term, en niet een zin.

### <a name="search-expression"></a>Zoekexpressie

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>Volledige URL

Zoek in deze query naar taken die het voorvoegsel 'prog' bevatten, waaronder bedrijfstitels met de termen programmeren en programmeur erin. Je geen * of? symbool als het eerste teken van een zoekopdracht.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![Wildcardquery](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> Wildcardquery's worden niet [geanalyseerd.](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis) De enige transformatie die wordt uitgevoerd op onvolledige querytermen, is een lagere behuizing.
>

## <a name="next-steps"></a>Volgende stappen
Probeer de Lucene Query Parser op te geven in uw code. In de volgende koppelingen wordt uitgelegd hoe u zoekopdrachten instelt voor zowel .NET als de REST API. De koppelingen gebruiken de standaardsyntaxis van eenvoudige regels, dus u moet toepassen wat u uit dit artikel hebt geleerd om het **querytype**op te geven.

* [Uw index opvragen met de .NET SDK](search-query-dotnet.md)
* [Uw index opvragen met de REST-API](search-create-index-rest-api.md)

Aanvullende syntaxisverwijzing, queryarchitectuur en voorbeelden zijn te vinden in de volgende koppelingen:

+ [Voorbeelden van eenvoudige syntaxisquery's](search-query-simple-examples.md)
+ [Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Vereenvoudigde querysyntaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Volledige Lucene-querysyntaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)