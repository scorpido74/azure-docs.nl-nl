---
title: Lucene-querysyntaxis
titleSuffix: Azure Cognitive Search
description: Referentie voor de volledige syntaxis van Lucene-query's, zoals gebruikt in Azure Cognitive Search voor wildcard, fuzzy search, RegEx en andere geavanceerde queryconstructen.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 1392f69bea09996e46ad4c112474f9067ff5a63d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656917"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Syntaxis van Lucene query in Azure Cognitive Search

U query's schrijven tegen Azure Cognitive Search op basis van de rijke syntaxis [van Lucene Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) voor gespecialiseerde queryformulieren: wildcard, fuzzy search, proximity search, reguliere expressies zijn enkele voorbeelden. Een groot deel van de syntaxis van Lucene Query Parser is [intact geïmplementeerd in Azure Cognitive Search,](search-lucene-query-architecture.md)met uitzondering van *bereikzoekopdrachten* die zijn gebouwd in Azure Cognitive Search via `$filter` expressies. 

> [!NOTE]
> De volledige syntaxis van Lucene wordt gebruikt voor queryexpressies die zijn doorgegeven in de **zoekparameter** van de API [voor zoekdocumenten,](https://docs.microsoft.com/rest/api/searchservice/search-documents) en niet te verwarren met de [syntaxis van OData](query-odata-filter-orderby-syntax.md) die wordt gebruikt voor de [$filter](search-filters.md) parameter van die API. Deze verschillende syntaxissen hebben hun eigen regels voor het construeren van query's, ontsnappen aan tekenreeksen, enzovoort.

## <a name="how-to-invoke-full-parsing"></a>Hoe volledige parsing beroep

Stel `queryType` de zoekparameter in om op te geven welke parser moet worden gebruikt. Geldige waarden `simple|full`zijn `simple` , met `full` als standaard, en voor Lucene. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Voorbeeld met volledige syntaxis

In het volgende voorbeeld worden documenten in de index gevonden `queryType=full` met behulp van de syntaxis van de Lucene-query, weergegeven in de parameter. Met deze query worden hotels geretourneerd waar het categorieveld de term 'budget' en alle doorzoekbare velden bevat met de term 'onlangs gerenoveerd'. Documenten met de zinsnede "onlangs gerenoveerd" worden hoger gerangschikt als gevolg van de term boost-waarde (3).  

De `searchMode=all` parameter is relevant in dit voorbeeld. Wanneer operators zich op de query bevinden, moet u er in het algemeen op instellen `searchMode=all` dat *alle* criteria worden geëvenaard.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2019-05-06&querytype=full
```

 U ook POST gebruiken:  

```
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

Zie Voorbeelden van [querysyntaxissvoorbeelden van Lucene query's voor het bouwen van query's in Azure Cognitive Search](search-query-lucene-examples.md)voor aanvullende voorbeelden. Zie [Zoekdocumenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)voor meer informatie over het opgeven van het volledige contingent queryparameters.

> [!NOTE]  
>  Azure Cognitive Search ondersteunt ook [Simple Query Syntax,](query-simple-syntax.md)een eenvoudige en robuuste querytaal die kan worden gebruikt voor eenvoudig zoeken naar zoekwoorden.  

##  <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a>Syntaxisfundamenten  
 De volgende syntaxisbasisbeginselen zijn van toepassing op alle query's die de syntaxis van Lucene gebruiken.  

### <a name="operator-evaluation-in-context"></a>Evaluatie van de exploitant in context

Plaatsing bepaalt of een symbool wordt geïnterpreteerd als een operator of gewoon een ander teken in een tekenreeks.

In de volledige syntaxis van Lucene wordt de tilde (~) bijvoorbeeld gebruikt voor zowel fuzzy search als proximity search. Wanneer geplaatst na een geciteerde zin, ~ beroept nabijheid zoeken. Wanneer geplaatst aan het einde van een term, ~ beroept fuzzy zoeken.

Binnen een term, zoals "business~analyst", wordt het personage niet geëvalueerd als operator. In dit geval, ervan uitgaande dat de query is een term of zinquery, [full text zoeken](search-lucene-query-architecture.md) met [lexicale analyse](search-lucene-query-architecture.md#stage-2-lexical-analysis) strips uit de ~ en breekt de term "business ~ analyst" in twee: business OF analist.

Het bovenstaande voorbeeld is de tilde (~), maar hetzelfde principe geldt voor elke operator.

### <a name="escaping-special-characters"></a>Ontsnappen aan speciale personages

 Speciale tekens moeten worden ontsnapt om te worden gebruikt als onderdeel van de zoektekst. U ontsnappen aan hen door\\prefixing ze met backslash ( ). Speciale tekens die moeten worden ontsnapt zijn onder andere:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Als u bijvoorbeeld wilt ontsnappen \\ \*aan een jokerteken, gebruikt u .

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Onveilige en gereserveerde tekens coderen in URL's

 Zorg ervoor dat alle onveilige en gereserveerde tekens zijn gecodeerd in een URL. '#' is bijvoorbeeld een onveilig teken omdat het een fragement/anker-id in een URL is. Het teken moet worden `%23` gecodeerd als het wordt gebruikt in een URL. '&' en '=' zijn voorbeelden van gereserveerde tekens omdat ze parameters afzonderen en waarden opgeven in Azure Cognitive Search. Zie [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) voor meer informatie.

 Onveilige tekens ``" ` < > # % { } | \ ^ ~ [ ]``zijn . Gereserveerde `; / ? : @ = + &`tekens zijn .

### <a name="precedence-operators-grouping-and-field-grouping"></a>Voorrangsoperatoren: groepering en veldgroepering  
 U haakjes gebruiken om subquery's te maken, inclusief operatoren binnen de bovenliggende instructie. Bijvoorbeeld, `motel+(wifi||luxury)` zal zoeken naar documenten met de "motel" term en ofwel "wifi" of "luxe" (of beide).

Veldgroepering is vergelijkbaar, maar scopes de groepering naar een enkel veld. Bijvoorbeeld, `hotelAmenities:(gym+(wifi||pool))` zoekt het veld "hotelVoorzieningen" voor "sportschool" en "wifi", of "sportschool" en "zwembad".  

### <a name="searchmode-parameter-considerations"></a>Overwegingen voor de parameter SearchMode  
 De impact `searchMode` van op query's, zoals beschreven in [De syntaxis van eenvoudige query's in Azure Cognitive Search,](query-simple-syntax.md)is ook van toepassing op de syntaxis van de Lucene-query. Namelijk, `searchMode` in combinatie met NOT operators kan resulteren in query resultaten die ongebruikelijk lijken als je niet duidelijk over de implicaties van hoe u de parameter instelt. Als u de `searchMode=any`standaardinstelling behoudt en een OPERATOR NOT gebruikt, wordt de bewerking berekend als een OF-actie, zodat 'New York' NOT "Seattle" alle steden retourneert die niet Seattle zijn.  

##  <a name="boolean-operators-and-or-not"></a><a name="bkmk_boolean"></a>Booleaanse operatoren (EN, OF, NIET) 
 Geef altijd tekst booleaanse operatoren (EN, OF, NIET) in alle caps.  

### <a name="or-operator-or-or-"></a>OR `OR` operator of`||`

De OR-operator is een verticaal balk- of pijpteken. Bijvoorbeeld: `wifi || luxury` zal zoeken naar documenten die ofwel "wifi" of "luxe" of beide. Omdat OR de standaardconjunctieoperator is, `wifi luxury` u deze `wifi || luxuery`ook weglaten, zodat dit het equivalent is van.

### <a name="and-operator-and--or-"></a>EN `AND`operator `&&` , of`+`

De AND operator is een ampersand of een plus teken. Bijvoorbeeld: `wifi && luxury` zal zoeken naar documenten die zowel "wifi" en "luxe". Het plusteken (+) wordt gebruikt voor vereiste termen. Bepaalt bijvoorbeeld `+wifi +luxury` dat beide termen ergens in het veld van één document moeten worden weergegeven.


### <a name="not-operator-not--or--"></a>NIET `NOT`operator, `!` of`-`

De OPERATOR NOT is een uitroepteken of het minteken. Bijvoorbeeld: `wifi !luxury` zal zoeken naar documenten die de "wifi" term hebben en / of hebben geen "luxe". De `searchMode` optie bepaalt of een term met de OPERATOR NOT ANDed of ORed is met de andere termen in de query bij afwezigheid van een + of || Operator. Terugroepactie `searchMode` die kan worden `any`ingesteld op `all`(standaard) of .

Het `searchMode=any` gebruik van verhogingen van query's door het opnemen van meer resultaten, en standaard - zal worden geïnterpreteerd als "OF niet". Bijvoorbeeld, `wifi -luxury` zal overeenkomen met documenten die ofwel de term *wifi* bevatten of die niet de term *luxe bevatten.*

Met `searchMode=all` behulp van verhoogt de precisie van query's door het opnemen van minder resultaten, en standaard - zal worden geïnterpreteerd als "EN NIET". Komt bijvoorbeeld `wifi -luxury` overeen met documenten `wifi` die de term `luxury`bevatten en bevatten de term niet . Dit is aantoonbaar een meer intuïtief gedrag voor de - operator. Daarom moet u overwegen `searchMode=all` `searchMode=any` om te kiezen voor als u zoekopdrachten wilt optimaliseren `-` voor precisie in plaats van terugroepen *en* uw gebruikers gebruiken de operator vaak in zoekopdrachten.

##  <a name="query-size-limitations"></a><a name="bkmk_querysizelimits"></a>Beperkingen voor querygrootte  
 Er is een limiet aan de grootte van query's die u naar Azure Cognitive Search verzenden. In het bijzonder u ten hoogste 1024 clausules (expressies gescheiden door EN, OF, enzovoort). Er is ook een limiet van ongeveer 32 KB op de grootte van een individuele term in een query. Als uw toepassing query's programmatisch genereert, raden we u aan deze op een zodanige manier te ontwerpen dat query's van niet-begrensde grootte niet worden gegenereerd.  

##  <a name="scoring-wildcard-and-regex-queries"></a><a name="bkmk_searchscoreforwildcardandregexqueries"></a>Joker- en regex-query's scoren
 Azure Cognitive Search maakt gebruik van frequentiegebaseerde scores[(TF-IDF)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)voor tekstquery's. Echter, voor wildcard en regex query's waar de reikwijdte van de termen potentieel breed kan zijn, wordt de frequentiefactor genegeerd om te voorkomen dat de rangschikking biasing naar wedstrijden van zeldzamere termen. Alle wedstrijden worden gelijk behandeld voor wildcard- en regex-zoekopdrachten.

##  <a name="fielded-search"></a><a name="bkmk_fields"></a>Zoeken in velden  
U een zoekbewerking met `fieldName:searchExpression` velden definiëren met de syntaxis, waarbij de zoekexpressie een enkel woord of een woordgroep kan zijn, of een complexere expressie tussen haakjes, optioneel met Booleaanse operatoren. Enkele voorbeelden zijn de volgende:  

- genre:jazz NIET geschiedenis  

- artiesten:("Miles Davis" "John Coltrane")

Zorg ervoor dat u meerdere tekenreeksen binnen aanhalingstekens plaatst als u wilt dat beide `artists` tekenreeksen als één entiteit worden geëvalueerd, in dit geval op zoek naar twee verschillende artiesten in het veld.  

Het veld `fieldName:searchExpression` dat is `searchable` opgegeven in een veld.  Zie [Index maken](https://docs.microsoft.com/rest/api/searchservice/create-index) voor meer informatie over hoe indexkenmerken worden gebruikt in velddefinities.  

> [!NOTE]
> Wanneer u zoekeninten met velden gebruikt, `searchFields` hoeft u de parameter niet te gebruiken omdat in elke zoekexpressie in het veld een veldnaam expliciet is opgegeven. U de `searchFields` parameter echter nog steeds gebruiken als u een query wilt uitvoeren waarbij bepaalde onderdelen zijn ondergebracht bij een bepaald veld en de rest op meerdere velden van toepassing kan zijn. De `search=genre:jazz NOT history&searchFields=description` query komt bijvoorbeeld `jazz` alleen `genre` overeen `NOT history` met het veld, terwijl deze overeenkomt met het `description` veld. De veldnaam `fieldName:searchExpression` die altijd wordt `searchFields` opgegeven, heeft voorrang op de parameter, daarom `genre` hoeven `searchFields` we in dit voorbeeld niet in de parameter op te nemen.

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a>Fuzzy zoeken  
 Een vage zoekopdracht vindt overeenkomsten in termen die een vergelijkbare constructie hebben. Per [Lucene documentatie](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), fuzzy zoekopdrachten zijn gebaseerd op [Damerau-Levenshtein Distance](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance). Vage zoekopdrachten kunnen een term uitbreiden tot maximaal 50 termen die voldoen aan de afstandscriteria. 

 Als u een vaag zoekopdracht wilt doen, gebruikt u het symbool tilde "~" aan het einde van een enkel woord met een optionele parameter, een getal tussen 0 en 2 (standaard), dat de bewerkingsafstand aangeeft. Bijvoorbeeld, "blue~" of "blue~1" zou terugkeren "blauw", "blues", en "lijm".

 Fuzzy zoeken kan alleen worden toegepast op termen, niet zinnen, maar u de tilde toe te passen aan elke term afzonderlijk in een meerdelige naam of zin. Bijvoorbeeld, "Unviersty ~ van ~ "Wshington~" zou overeenkomen op "Universiteit van Washington".
 

##  <a name="proximity-search"></a><a name="bkmk_proximity"></a>Zoeken in nabijheid  
 Nabijheidszoekopdrachten worden gebruikt om termen te vinden die dicht bij elkaar in een document liggen. Plaats een tilde "~" symbool aan het einde van een zin, gevolgd door het aantal woorden dat de nabijheidsgrens maakt. Bijvoorbeeld, `"hotel airport"~5` vindt de termen "hotel" en "luchthaven" binnen 5 woorden van elkaar in een document.  


##  <a name="term-boosting"></a><a name="bkmk_termboost"></a>Term stimuleren  
 Term boosting verwijst naar het rangschikken van een document hoger als het de versterkte term bevat, ten opzichte van documenten die de term niet bevatten. Dit verschilt van scoreprofielen in die zin dat scoreprofielen bepaalde velden stimuleren, in plaats van specifieke termen.  

In het volgende voorbeeld worden de verschillen geïllustreerd. Stel dat er een scoreprofiel is dat wedstrijden in een bepaald veld verhoogt, bijvoorbeeld *genre* in het [voorbeeld van musicstoreindex.](index-add-scoring-profiles.md#bkmk_ex) Term boosting kan worden gebruikt om bepaalde zoektermen hoger dan andere verder te stimuleren. Als `rock^2 electronic` u bijvoorbeeld documenten versterkt die de zoektermen in het genreveld hoger zijn dan andere doorzoekbare velden in de index. Verder worden documenten die de zoekterm *rock* bevatten hoger gerangschikt dan de andere zoekterm *elektronisch* als gevolg van de term boost waarde (2).  

 Om een term te stimuleren gebruik maken van de caret, "^", symbool met een boost factor (een getal) aan het einde van de term die u zoekt. U ook zinnen promoten. Hoe hoger de boostfactor, hoe relevanter de term zal zijn ten opzichte van andere zoektermen. Standaard is de boostfactor 1. Hoewel de boostfactor positief moet zijn, kan deze minder dan 1 zijn (bijvoorbeeld 0,20).  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a>Zoeken naar reguliere expressies  
 Bij een zoekopdracht met reguliere expressievindt u een overeenkomst op basis van de inhoud tussen voorwaartse slashes "/", zoals gedocumenteerd in de [klasse RegExp.](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)  

 Bijvoorbeeld, om documenten te vinden die "motel" `/[mh]otel/`of "hotel" bevatten, specificeert u . Zoekopdrachten met reguliere expressie worden gekoppeld aan afzonderlijke woorden.

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a>Zoeken naar jokertekens  
 U algemeen erkende syntaxis gebruiken voor meerdere (*) of enkele (?) tekenwildcardzoekopdrachten. Let op de Lucene query parser ondersteunt het gebruik van deze symbolen met een enkele term, en niet een zin.

Voorvoegsel zoeken maakt ook`*`gebruik van het sterretje ( ) karakter. Een queryexpressie van `search=note*` retourneert bijvoorbeeld 'notitieblok' of 'notitieblok'. Volledige lucene syntaxis is niet vereist voor prefix zoeken. De eenvoudige syntaxis ondersteunt dit scenario.

Achtervoegsel zoeken, waar `*` of `?` voorafgaat aan de string, vereist volledige Lucene syntaxis en een reguliere expressie (je geen gebruik maken van een * of? symbool als het eerste teken van een zoekopdracht). Onder de term "alfanumeriek"`search=/.*numeric.*/`vindt u de queryexpressie van ( ) de overeenkomst.

> [!NOTE]  
> Tijdens queryparsing worden query's die zijn geformuleerd als voorvoegsel, achtervoegsel, wildcard of reguliere expressies doorgegeven aan de querystructuur, waarbij [lexicale analyse](search-lucene-query-architecture.md#stage-2-lexical-analysis)wordt omzeild. Overeenkomsten worden alleen gevonden als de index de tekenreeksen bevat in de indeling die uw query opgeeft. In de meeste gevallen hebt u een alternatieve analyzer nodig tijdens het indexeren die de integriteit van de tekenreeks behoudt, zodat gedeeltelijke term- en patroonmatching slaagt. Zie Zoeken in Azure Cognitive Search voor meer informatie [in Azure Cognitive Search.](search-query-partial-matching.md)

## <a name="see-also"></a>Zie ook  

+ [Documenten zoeken](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Syntaxis van OData-expressie voor filters en sorteren](query-odata-filter-orderby-syntax.md)   
+ [Eenvoudige querysyntaxis in Azure Cognitive Search](query-simple-syntax.md)   
