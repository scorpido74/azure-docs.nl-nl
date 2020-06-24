---
title: Lucene-querysyntaxis
titleSuffix: Azure Cognitive Search
description: Verwijzing voor de volledige lucene-query syntaxis, zoals gebruikt in azure Cognitive Search voor joker tekens, fuzzy zoeken, RegEx en andere geavanceerde query constructies.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/23/2020
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
ms.openlocfilehash: efef4a5e3fd846b06d73211bc6cf63a8ecf24438
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85255344"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>De Lucene-query syntaxis in azure Cognitive Search

U kunt query's schrijven op Azure Cognitive Search op basis van de syntaxis van de Rich [lucene query-parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) voor gespecialiseerde query formulieren: Joker teken, fuzzy zoeken, proximity Search, reguliere expressies zijn enkele voor beelden. Veel van de eigenschappen van de Lucene-query-parser zijn [geïmplementeerd in azure Cognitive Search](search-lucene-query-architecture.md), met uitzonde ring van *Zoek opdrachten voor bereiken* die zijn gemaakt in azure Cognitive Search door middel van `$filter` expressies. 

> [!NOTE]
> De syntaxis Full lucene wordt gebruikt voor query-expressies die zijn door gegeven in de **Zoek** parameter van de [Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents) -API, en niet om te verwarren met de [OData-syntaxis](query-odata-filter-orderby-syntax.md) die wordt gebruikt voor de para meter [$filter](search-filters.md) van die API. Deze verschillende syntaxis hebben hun eigen regels voor het maken van query's, teken reeksen, enzovoort.

## <a name="invoke-full-parsing"></a>Volledige parsering aanroepen

Stel de `queryType` Zoek parameter in om op te geven welke parser moet worden gebruikt. Geldige waarden zijn onder andere `simple|full` `simple` de standaard waarde en `full` voor lucene. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Voor beeld met volledige syntaxis

In het volgende voor beeld wordt gezocht naar documenten in de index met behulp van de Lucene-query syntaxis, duidelijk in de `queryType=full` para meter. Deze query retourneert hotels waar het veld categorie de term ' budget ' bevat en alle Doorzoek bare velden met de zin ' recent renovated '. Documenten met de zin ' recent renovated ' worden hoger als resultaat van de term Boost waarde (3).  

De `searchMode=all` para meter is relevant in dit voor beeld. Wanneer Opera tors zich op de query bevinden, moet u in het algemeen instellen `searchMode=all` dat *alle* criteria overeenkomen.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2019-05-06&querytype=full
```

 U kunt ook POST gebruiken:  

```
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

Zie voor meer voor beelden de voor [beelden van Lucene-query syntaxis voor het maken van query's in Azure Cognitive Search](search-query-lucene-examples.md). Zie [zoeken naar documenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)voor meer informatie over het opgeven van de volledige afhankelijkheid van query parameters.

> [!NOTE]  
>  Azure Cognitive Search biedt ook ondersteuning voor [eenvoudige query syntaxis](query-simple-syntax.md), een eenvoudige en krachtige query taal die kan worden gebruikt voor het zoeken naar eenvoudige tref woorden.  

##  <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a>Syntaxis basis  

de volgende syntaxis basis is van toepassing op alle query's die gebruikmaken van de Lucene-syntaxis.  

### <a name="operator-evaluation-in-context"></a>Operator evaluatie in de context

Plaatsing bepaalt of een symbool wordt geïnterpreteerd als een operator of alleen een ander teken in een teken reeks.

In de volledige syntaxis van Lucene wordt bijvoorbeeld de tilde (~) gebruikt voor zowel fuzzy zoek acties als proximity-Zoek opdrachten. Wanneer deze wordt geplaatst na een woord groep met een aanhalings teken, wordt proximity-zoek opdracht aangeroepen. Wanneer deze aan het einde van een termijn wordt geplaatst, roept de zoek actie voor fuzzy aan.

Binnen een termijn, zoals ' bedrijfs ~ analist ', wordt het teken niet geëvalueerd als een operator. In dit geval, ervan uitgaande dat de query een term of woordgroepen query is, [zoekt volledige tekst](search-lucene-query-architecture.md) met [lexicale analyse](search-lucene-query-architecture.md#stage-2-lexical-analysis) de ~ en verbreekt de term ' bedrijfs ~ analist ' in twee: bedrijf of analist.

Het bovenstaande voor beeld is de tilde (~), maar hetzelfde principe geldt voor elke operator.

### <a name="escaping-special-characters"></a>Speciale tekens in een escape teken

Als u een van de zoek operatoren wilt gebruiken als onderdeel van de Zoek tekst, moet u het teken door een voor voegsel van een back slash () laten staan `\` . Als u bijvoorbeeld een zoek opdracht met Joker tekens `https://` `://` wilt uitvoeren, waarbij u een deel van de query teken reeks opgeeft, geeft u op `search=https\:\/\/*` . Op dezelfde manier kan een patroon voor een telefoon nummer er als volgt uitzien `\+1 \(800\) 642\-7676` .

Voor de volgende speciale tekens moet u een escape-teken gebruiken:  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> Hoewel het samen voegen van tokens in beslag houdt, kan de [lexicale analyse](search-lucene-query-architecture.md#stage-2-lexical-analysis) tijdens het indexeren deze verwijderen. De standaard-lucene Analyzer verbreekt bijvoorbeeld woorden op afbreek streepjes, spaties en andere tekens. Als u speciale tekens in de query teken reeks nodig hebt, hebt u mogelijk een analyse programma nodig waarmee ze in de index worden bewaard. Enkele keuze mogelijkheden zijn micro soft Natural [Language-analyse](index-add-language-analyzers.md)functies, waarmee woorden met een afbreek streepje worden behouden, of een aangepaste analyse functie voor complexere patronen. Zie voor meer informatie [gedeeltelijke termen, patronen en speciale tekens](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Onveilige en gereserveerde tekens in Url's coderen

Zorg ervoor dat alle onveilige en gereserveerde tekens worden gecodeerd in een URL. Bijvoorbeeld, ' # ' is een onveilig teken, omdat het een fragment/anker-id in een URL is. Het teken moet worden gecodeerd `%23` als het wordt gebruikt in een URL. ' & ' en ' = ' zijn voor beelden van gereserveerde tekens wanneer ze para meters opwaarderen en waarden opgeven in azure Cognitive Search. Raadpleeg [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) voor meer informatie.

Onveilige tekens zijn ``" ` < > # % { } | \ ^ ~ [ ]`` . Gereserveerde tekens zijn `; / ? : @ = + &` .

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>Limieten voor query grootte

 Er is een limiet voor de grootte van query's die u kunt verzenden naar Azure Cognitive Search. U kunt met name de meeste 1024-componenten hebben (expressies gescheiden door en, of, enzovoort). Er is ook een limiet van ongeveer 32 KB op de grootte van een afzonderlijke term in een query. Als uw toepassing Zoek query's programmatisch genereert, raden we u aan om deze zodanig te ontwerpen dat er geen query's van een ongebonden grootte worden gegenereerd.  

### <a name="precedence-operators-grouping"></a>Prioriteits operatoren (groepering)

 U kunt haakjes gebruiken om subquery's te maken, inclusief Opera tors in de instructie van de haak. Zoekt bijvoorbeeld naar `motel+(wifi||luxury)` documenten met de term ' Motel ' en ' WiFi ' of ' luxe ' (of beide).

Veld groepering is vergelijkbaar, maar bereiken de groepering op één veld. `hotelAmenities:(gym+(wifi||pool))`Zoekt bijvoorbeeld naar het veld "hotelAmenities" voor "Gym" en "WiFi" of "Gym" en "pool".  

##  <a name="boolean-search"></a><a name="bkmk_boolean"></a>Booleaanse zoek actie

 Geef altijd tekst Booleaanse Opera tors (en, of, niet) op in hoofd letters.  

### <a name="or-operator-or-or-"></a>OF operator `OR` of`||`

De operator OR is een verticale streep of een sluis teken. Bijvoorbeeld: `wifi || luxury` zoekt naar documenten met ofwel ' WiFi ' of ' luxe ' of beide. Omdat of de standaard operator voor samen voegen is, kunt u dit ook laten verlopen, zodat `wifi luxury` het equivalent is van `wifi || luxury` .

### <a name="and-operator-and--or-"></a>EN operator `AND` , `&&` of`+`

De operator AND is een ampersand of een plus teken. Bijvoorbeeld: `wifi && luxury` zoekt naar documenten die zowel "WiFi" als "luxe" bevatten. Het plus teken (+) wordt gebruikt voor de vereiste voor waarden. Voor beeld: `+wifi +luxury` bepaalt dat beide termen ergens in het veld van één document moeten worden weer gegeven.

### <a name="not-operator-not--or--"></a>GEEN operator `NOT` , `!` of`-`

De operator NOT is een minteken. U kunt bijvoorbeeld `wifi –luxury` zoeken naar documenten die de `wifi` term en/of niet hebben `luxury` .

De para meter **Search mode** voor een query-aanvraag bepaalt of een term met de operator Not ANDed of ORed is met andere voor waarden in de query (ervan uitgaande dat er geen `+` operator is of `|` de andere voor waarden). Geldige waarden zijn `any` of `all` .

`searchMode=any`Hiermee verg root u het intrekken van query's door meer resultaten op te nemen `-` . deze worden standaard geïnterpreteerd als "of niet". Komt bijvoorbeeld `wifi -luxury` overeen met documenten die de voor waarden bevatten of die de `wifi` term niet bevatten `luxury` .

`searchMode=all`verhoogt de nauw keurigheid van query's door minder resultaten op te nemen en wordt standaard geïnterpreteerd als "en niet". Komt bijvoorbeeld `wifi -luxury` overeen met documenten die de term bevatten `wifi` en niet de term ' luxe ' bevatten. Dit is weliswaar een meer intuïtief gedrag voor de `-` operator. Daarom kunt u overwegen `searchMode=all` in plaats van te gebruiken `searchMode=any` Als u zoek acties wilt optimaliseren in plaats van intrekken, *en* uw gebruikers vaak de `-` operator gebruiken in Zoek opdrachten.

Houd bij het kiezen van een **Search mode** -instelling rekening met de gebruikers interactie patronen voor query's in verschillende toepassingen. Gebruikers die op zoek zijn naar informatie, hebben waarschijnlijk vaker een operator in een query opgenomen, in tegens telling tot e-commerce sites met meer ingebouwde navigatie structuren.

##  <a name="fielded-search"></a><a name="bkmk_fields"></a>Zoek opdracht in veld

U kunt een zoek bewerking in een veld definiëren met de `fieldName:searchExpression` syntaxis, waarbij de zoek expressie één woord of een zin of een complexere expressie tussen haakjes, optioneel met Booleaanse Opera Tors, kan zijn. Enkele voor beelden zijn:  

- genre: Jazz geen geschiedenis  

- artiesten:("mijl Davis" "John Coltrane")

Zorg ervoor dat u meerdere teken reeksen tussen aanhalings tekens plaatst als u wilt dat beide teken reeksen als één entiteit worden geëvalueerd. in dit geval zoekt u twee afzonderlijke artiesten in het `artists` veld.  

Het opgegeven veld in `fieldName:searchExpression` moet een `searchable` veld zijn.  Zie [index maken](https://docs.microsoft.com/rest/api/searchservice/create-index) voor meer informatie over het gebruik van index kenmerken in veld definities.  

> [!NOTE]
> Wanneer u zoek expressies met een formule gebruikt, hoeft u de para meter niet te gebruiken `searchFields` omdat elke zoek expressie in een veld expliciet een veld naam heeft opgegeven. U kunt echter nog steeds de `searchFields` para meter gebruiken als u een query wilt uitvoeren waarbij sommige onderdelen naar een specifiek veld zijn beperkt, en de rest kan van toepassing zijn op verschillende velden. De query `search=genre:jazz NOT history&searchFields=description` zou bijvoorbeeld alleen overeenkomen met `jazz` het `genre` veld, terwijl deze overeenkomt `NOT history` met het `description` veld. De naam van het veld in `fieldName:searchExpression` heeft altijd voor rang op de `searchFields` para meter, wat in dit voor beeld de oorzaak is van het niet gebruiken van de para `genre` `searchFields` meter.

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a>Zoek actie op fuzzy

Een fuzzy zoek opdracht zoekt naar overeenkomsten met een vergelijk bare constructie, waardoor een term wordt uitgebreid tot het maximum van 50 voor waarden die voldoen aan de afstands criteria van twee of minder. Zie [fuzzy Search](search-query-fuzzy.md)voor meer informatie.

 Als u een fuzzy zoek opdracht wilt uitvoeren, gebruikt u het tilde ' ~ ' aan het einde van één woord met een optionele para meter, een getal tussen 0 en 2 (standaard) waarmee de bewerkings afstand wordt opgegeven. Bijvoorbeeld: ' Blue ~ ' of ' Blue ~ 1 ' retourneert ' Blue ', ' blauw ' en ' lijm '.

 Fuzzy Search kan alleen worden toegepast op termen, geen zinsdelen, maar u kunt de tilde aan elke afzonderlijke term afzonderlijk toevoegen in een naam of woord groep met meerdere delen. Zo komt ' Unviersty ~ van ~ ' Wshington ~ ' overeen met ' University of Washington '.
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a>Proximity Search

Proximity-Zoek opdrachten worden gebruikt om termen te vinden die zich in een document nabij elkaar bevinden. Voeg een tilde ' ~ ' aan het einde van een woord groep toe, gevolgd door het aantal woorden dat de grens van de nabijheid heeft gemaakt. `"hotel airport"~5`De termen ' Hotel ' en ' lucht haven ' worden bijvoorbeeld binnen vijf woorden van elkaar in een document weer te vinden.  


##  <a name="term-boosting"></a><a name="bkmk_termboost"></a>Term versterking

De term Boosting verwijst naar een hoger niveau voor een document als dit de gestimuleerde term bevat, ten opzichte van documenten die de term niet bevatten. Dit verschilt van Score profielen in dat Score profiel om bepaalde velden in plaats van specifieke voor waarden te stimuleren.  

In het volgende voor beeld worden de verschillen toegelicht. Stel dat er een score profiel is dat overeenkomt met de overeenkomsten in een bepaald veld, bijvoorbeeld *genre* in het [musicstoreindex-voor beeld](index-add-scoring-profiles.md#bkmk_ex). De term versterking kan worden gebruikt om bepaalde zoek termen hoger te verhogen dan andere. U kunt bijvoorbeeld `rock^2 electronic` documenten verhogen met de zoek termen in het veld genre hoger dan andere Doorzoek bare velden in de index. Daarnaast worden documenten met de zoek term *Rock* hoger gerangschikt dan de andere zoek term *elektronisch* als resultaat van de waarde voor de verhoging van de term (2).  

 Als u een term wilt verhogen, gebruikt u het caret-teken (^) met een boost factor (een getal) aan het einde van de term die u zoekt. U kunt ook zinsdelen verg Roten. Hoe hoger de Boost-factor, hoe relevanter de term relatief is ten opzichte van andere zoek termen. Standaard is de Boost factor 1. Hoewel de Boost-factor positief moet zijn, kan deze kleiner zijn dan 1 (bijvoorbeeld 0,20).  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a>Reguliere expressie zoeken  
 Een reguliere expressie zoekt een overeenkomst op basis van patronen die geldig zijn onder Apache Lucene, zoals beschreven in de [klasse RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html). In azure Cognitive Search een reguliere expressie tussen slashes `/` .

 Als u bijvoorbeeld documenten zoekt met ' Motel ' of ' Hotel ', geeft u op `/[mh]otel/` . Zoek opdrachten in reguliere expressies worden vergeleken met enkele woorden.

Voor sommige hulpprogram ma's en talen zijn aanvullende vereisten voor het escape-teken verplicht. Voor JSON worden teken reeksen met een slash met een schuine streep met een back slash: "microsoft.com/azure/" wordt `search=/.*microsoft.com\/azure\/.*/` als de `search=/.* <string-placeholder>.*/` reguliere expressie ingesteld, en `microsoft.com\/azure\/` is de teken reeks met een slash (voorwaarts).

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a>Joker tekens zoeken

U kunt de algemeen erkende syntaxis voor Zoek opdrachten met Joker tekens voor meerdere ( `*` ) of één ( `?` ) gebruiken. Bijvoorbeeld een query-expressie met `search=alpha*` als resultaat "alfanumeriek" of "alfabetisch". Opmerking de Lucene-query-parser ondersteunt het gebruik van deze symbolen met één term en geen woord groep.

De volledige lucene-syntaxis ondersteunt voor voegsels, infix en achtervoegsels. Als u echter alleen het voor voegsel van voor voegsels nodig hebt, kunt u de eenvoudige syntaxis gebruiken (voor voegsel matching wordt in beide ondersteund).

Achtervoegsels die overeenkomen met `*` `?` de teken reeks (zoals in `search=/.*numeric./` ) of infix match vereist volledige lucene-syntaxis en de reguliere expressie slash- `/` scheidings tekens. U kunt geen * of gebruiken. symbool als het eerste teken van een term of binnen een term zonder de `/` . 

> [!NOTE]  
> Als regel is het vergelijken van patronen traag, zodat u alternatieve methoden kunt verkennen, zoals Edge n-gram-tokening waarmee tokens voor reeksen van tekens in een term worden gemaakt. De index is groter, maar query's kunnen sneller worden uitgevoerd, afhankelijk van de patroon constructie en de lengte van teken reeksen die u wilt indexeren.
>
> Tijdens het parseren van query's worden query's die zijn geformuleerd als voor voegsel, achtervoegsel, Joker teken of reguliere expressies als-is door gegeven aan de query structuur, en wordt de [lexicale analyse](search-lucene-query-architecture.md#stage-2-lexical-analysis)overs Laan. Overeenkomsten worden alleen gevonden als de index de teken reeksen bevat in de indeling die uw query opgeeft. In de meeste gevallen hebt u tijdens het indexeren een alternatieve analyse nodig die de teken reeks integriteit behoudt, zodat gedeeltelijke term en patroon aanpassing met succes kan worden uitgevoerd. Zie voor meer informatie [partiële term Search in Azure Cognitive Search query's](search-query-partial-matching.md).

##  <a name="scoring-wildcard-and-regex-queries"></a><a name="bkmk_searchscoreforwildcardandregexqueries"></a>Score Joker teken en regex-query's

Azure Cognitive Search maakt gebruik van scores op basis van frequentie ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) voor tekst query's. Voor joker tekens en regex-query's waarbij de reik wijdte van termen mogelijk breed kan zijn, wordt de frequentie factor genegeerd om te voor komen dat de rang schikking overeenkomt met overeenkomsten van rarer-voor waarden. Alle overeenkomsten worden op dezelfde manier behandeld voor Zoek opdrachten met Joker tekens en regex.

## <a name="see-also"></a>Zie ook

+ [Query voorbeelden voor eenvoudige Zoek opdrachten](search-query-simple-examples.md)
+ [Query voorbeelden voor volledige lucene-Zoek opdrachten](search-query-lucene-examples.md)
+ [Documenten zoeken](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Syntaxis van de OData-expressie voor filters en sorteren](query-odata-filter-orderby-syntax.md)   
+ [Eenvoudige query syntaxis in azure Cognitive Search](query-simple-syntax.md)   
