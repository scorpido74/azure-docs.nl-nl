---
title: Lucene-querysyntaxis
titleSuffix: Azure Cognitive Search
description: Verwijzing voor de volledige lucene-query syntaxis, zoals gebruikt in azure Cognitive Search voor joker tekens, fuzzy zoeken, RegEx en andere geavanceerde query constructies.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: 0bb8474b30c05e21a62ded1fa2cb8a6df8e4e321
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112185"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>De Lucene-query syntaxis in azure Cognitive Search

U kunt query's schrijven op Azure Cognitive Search op basis van de syntaxis van de Rich [lucene query-parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) voor gespecialiseerde query formulieren: Joker teken, fuzzy zoeken, proximity Search, reguliere expressies zijn enkele voor beelden. Een deel van de syntaxis van de Lucene-query-parser is [in azure Cognitive Search geïmplementeerd](search-lucene-query-architecture.md), met uitzonde ring van *Zoek opdrachten voor bereiken* die zijn gemaakt in azure Cognitive Search via `$filter` expressies. 

## <a name="how-to-invoke-full-parsing"></a>Volledige parsering aanroepen

Stel de `queryType` Search-para meter in om op te geven welke parser moet worden gebruikt. Geldige waarden zijn `simple|full`, met `simple` als de standaard waarde en `full` voor lucene. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Voor beeld met volledige syntaxis

In het volgende voor beeld wordt gezocht naar documenten in de index met behulp van de Lucene-query syntaxis, duidelijk in de para meter `queryType=full`. Deze query retourneert hotels waar het veld categorie de term ' budget ' bevat en alle Doorzoek bare velden met de zin ' recent renovated '. Documenten met de zin ' recent renovated ' worden hoger als resultaat van de term Boost waarde (3).  

De para meter `searchMode=all` is relevant in dit voor beeld. Wanneer Opera tors zich op de query bevinden, moet u in het algemeen `searchMode=all` instellen om ervoor te zorgen dat *alle* criteria overeenkomen.

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

Zie voor meer voor beelden de voor [beelden van Lucene-query syntaxis voor het maken van query's in Azure Cognitive Search](search-query-lucene-examples.md). Zie [Zoeken in documenten &#40;Azure Cognitive Search rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)voor meer informatie over het opgeven van de volledige afhankelijkheid van query parameters.

> [!NOTE]  
>  Azure Cognitive Search biedt ook ondersteuning voor [eenvoudige query syntaxis](query-simple-syntax.md), een eenvoudige en krachtige query taal die kan worden gebruikt voor het zoeken naar eenvoudige tref woorden.  

##  <a name="bkmk_syntax"></a>Syntaxis basis  
 De volgende syntaxis basis is van toepassing op alle query's die gebruikmaken van de Lucene-syntaxis.  

### <a name="operator-evaluation-in-context"></a>Operator evaluatie in de context

Plaatsing bepaalt of een symbool wordt geïnterpreteerd als een operator of alleen een ander teken in een teken reeks.

In de volledige syntaxis van Lucene wordt bijvoorbeeld de tilde (~) gebruikt voor zowel fuzzy zoek acties als proximity-Zoek opdrachten. Wanneer deze wordt geplaatst na een woord groep met een aanhalings teken, wordt proximity-zoek opdracht aangeroepen. Wanneer deze aan het einde van een termijn wordt geplaatst, roept de zoek actie voor fuzzy aan.

Binnen een termijn, zoals ' bedrijfs ~ analist ', wordt het teken niet geëvalueerd als een operator. In dit geval, ervan uitgaande dat de query een term of woordgroepen query is, [zoekt volledige tekst](search-lucene-query-architecture.md) met [lexicale analyse](search-lucene-query-architecture.md#stage-2-lexical-analysis) de ~ en verbreekt de term ' bedrijfs ~ analist ' in twee: bedrijf of analist.

Het bovenstaande voor beeld is de tilde (~), maar hetzelfde principe geldt voor elke operator.

### <a name="escaping-special-characters"></a>Speciale tekens in een escape teken

 Speciale tekens moeten worden voorafgegaan om te worden gebruikt als onderdeel van de Zoek tekst. U kunt ze weglaten door ze te voorzien van een back slash (\\). Voor beelden van speciale tekens die moeten worden voorafgegaan, zijn onder meer de volgende:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Gebruik bijvoorbeeld \\\*om een Joker teken te escapepen.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Onveilige en gereserveerde tekens in Url's coderen

 Zorg ervoor dat alle onveilige en gereserveerde tekens worden gecodeerd in een URL. Bijvoorbeeld, ' # ' is een onveilig teken omdat het een fragement/anker-id in een URL is. Het teken moet worden gecodeerd naar `%23` als het wordt gebruikt in een URL. ' & ' en ' = ' zijn voor beelden van gereserveerde tekens wanneer ze para meters opwaarderen en waarden opgeven in azure Cognitive Search. Raadpleeg [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) voor meer informatie.

 Onveilige tekens zijn ``" ` < > # % { } | \ ^ ~ [ ]``. Gereserveerde tekens zijn `; / ? : @ = + &`.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Prioriteits operatoren: groeperen en groeperen van velden  
 U kunt haakjes gebruiken om subquery's te maken, inclusief Opera tors in de instructie van de haak. `motel+(wifi||luxury)` zoekt bijvoorbeeld naar documenten met de term ' Motel ' en ' WiFi ' of ' luxe ' (of beide).

Veld groepering is vergelijkbaar, maar bereiken de groepering op één veld. `hotelAmenities:(gym+(wifi||pool))` doorzoekt bijvoorbeeld het veld "hotelAmenities" voor "Gym" en "WiFi" of "Gym" en "pool".  

### <a name="searchmode-parameter-considerations"></a>Overwegingen voor Search mode-para meter  
 De impact van `searchMode` op query's, zoals beschreven in de [eenvoudige query syntaxis in Azure Cognitive Search](query-simple-syntax.md), is gelijk aan de Lucene-query syntaxis. Het gevolg is dat `searchMode` in combi natie met niet-Opera tors kan leiden tot resultaten van query's die mogelijk ongebruikelijk lijken als u geen duidelijke gevolgen hebt voor de manier waarop u de para meter instelt. Als u de standaard, `searchMode=any`en een NOT-operator gebruikt, wordt de bewerking berekend als een OR-actie, zodat ' Amsterdam ' niet alle steden retourneert die niet Seattle zijn.  

##  <a name="bkmk_boolean"></a>Booleaanse Opera tors (en, of, niet) 
 Geef altijd tekst Booleaanse Opera tors (en, of, niet) op in hoofd letters.  

### <a name="or-operator-or-or-"></a>OF operator `OR` of `||`

De operator OR is een verticale streep of een sluis teken. Bijvoorbeeld: `wifi || luxury` zoekt naar documenten met ofwel ' WiFi ' of ' luxe ', of beide. Omdat of de standaard operator voor samen voegen is, kunt u dit ook laten verlopen, zodat `wifi luxury` het equivalent is van `wifi || luxuery`.

### <a name="and-operator-and--or-"></a>EN operator `AND`, `&&` of `+`

De operator AND is een ampersand of een plus teken. Bijvoorbeeld: `wifi && luxury` zoekt naar documenten die zowel ' WiFi ' als ' luxe ' bevatten. Het plus teken (+) wordt gebruikt voor de vereiste voor waarden. `+wifi +luxury` bijvoorbeeld bepaalt dat beide termen ergens in het veld van één document moeten worden weer gegeven.


### <a name="not-operator-not--or--"></a>GEEN operator `NOT`, `!` of `-`

De operator NOT is een uitroep teken of het minteken. Bijvoorbeeld: `wifi !luxury` zoekt naar documenten met de term ' WiFi ' en/of niet ' luxe '. Met de optie `searchMode` bepaalt u of een term met de operator NOT ANDed of ORed met de andere voor waarden in de query in het afwezigheid van een + of | | and. Intrekken dat `searchMode` kan worden ingesteld op `any`(standaard) of `all`.

Het gebruik van `searchMode=any` verg root het intrekken van query's door meer resultaten op te nemen en wordt standaard geïnterpreteerd als "of niet". `wifi -luxury` komt bijvoorbeeld overeen met documenten die de term *WiFi* bevatten of de termen die niet de term *luxe bevatten.*

Met `searchMode=all` verhoogt u de nauw keurigheid van query's door minder resultaten op te nemen, en standaard-wordt geïnterpreteerd als "en niet". `wifi -luxury` komt bijvoorbeeld overeen met documenten die de term `wifi` bevatten en niet de term `luxury`bevatten. Dit is weliswaar een meer intuïtief gedrag voor de-operator. Daarom kunt u overwegen om `searchMode=all` over `searchMode=any` te kiezen als u zoek acties wilt optimaliseren voor een nauw keurigheid in plaats van intrekken *en* uw gebruikers vaak de operator `-` gebruiken in Zoek opdrachten.

##  <a name="bkmk_querysizelimits"></a>Beperkingen voor de query grootte  
 Er is een limiet voor de grootte van query's die u kunt verzenden naar Azure Cognitive Search. U kunt met name de meeste 1024-componenten hebben (expressies gescheiden door en, of, enzovoort). Er is ook een limiet van ongeveer 32 KB op de grootte van een afzonderlijke term in een query. Als uw toepassing Zoek query's programmatisch genereert, raden we u aan om deze zodanig te ontwerpen dat er geen query's van een ongebonden grootte worden gegenereerd.  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a>Score Joker teken en regex-query's
 Azure Cognitive Search maakt gebruik van scores op basis van frequentie ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) voor tekst query's. Voor joker tekens en regex-query's waarbij de reik wijdte van termen mogelijk breed kan zijn, wordt de frequentie factor genegeerd om te voor komen dat de rang schikking overeenkomt met overeenkomsten van rarer-voor waarden. Alle overeenkomsten worden op dezelfde manier behandeld voor Zoek opdrachten met Joker tekens en regex.

##  <a name="bkmk_fields"></a>Zoek opdracht in veld  
U kunt een zoek bewerking in een veld definiëren met de syntaxis `fieldName:searchExpression`, waarbij de zoek expressie één woord of een zin of een complexere expressie tussen haakjes, optioneel met Booleaanse Opera Tors, kan zijn. Enkele voor beelden zijn:  

- genre: Jazz geen geschiedenis  

- artiesten:("mijl Davis" "John Coltrane")

Zorg ervoor dat u meerdere teken reeksen tussen aanhalings tekens plaatst als u wilt dat beide teken reeksen als één entiteit worden geëvalueerd. in dit geval zoekt u twee afzonderlijke artiesten in het veld `artists`.  

Het in `fieldName:searchExpression` opgegeven veld moet een `searchable` veld zijn.  Zie [index maken](https://docs.microsoft.com/rest/api/searchservice/create-index) voor meer informatie over het gebruik van index kenmerken in veld definities.  

> [!NOTE]
> Wanneer u zoek expressies met een formule gebruikt, hoeft u de para meter `searchFields` niet te gebruiken omdat voor elke zoek expressie in de velden expliciet een veld naam is opgegeven. U kunt echter nog steeds de para meter `searchFields` gebruiken als u een query wilt uitvoeren waarbij sommige onderdelen naar een specifiek veld zijn beperkt, en de rest kan van toepassing zijn op verschillende velden. Zo zou de query `search=genre:jazz NOT history&searchFields=description` alleen overeenkomen met `jazz` in het veld `genre`, terwijl het `NOT history` met het veld `description` zou overeenkomen. De naam van het veld in `fieldName:searchExpression` heeft altijd voor rang op de para meter `searchFields`, in dit voor beeld is het niet nodig om `genre` op te nemen in de para meter `searchFields`.

##  <a name="bkmk_fuzzy"></a>Zoek actie op fuzzy  
 Bij een fuzzy zoek opdracht vindt u overeenkomsten met een vergelijk bare constructie. Per [lucene-documentatie](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)zijn zoek acties op basis van [Damerau-Levenshtein-afstand](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance). Bij fuzzy Zoek opdrachten kan een term worden uitgebreid tot het maximum van 50 voor waarden die voldoen aan de afstands criteria. 

 Als u een fuzzy zoek opdracht wilt uitvoeren, gebruikt u het tilde ' ~ ' aan het einde van één woord met een optionele para meter, een getal tussen 0 en 2 (standaard) waarmee de bewerkings afstand wordt opgegeven. Bijvoorbeeld: ' Blue ~ ' of ' Blue ~ 1 ' retourneert ' Blue ', ' blauw ' en ' lijm '.

 Fuzzy Search kan alleen worden toegepast op termen, geen zinsdelen, maar u kunt de tilde aan elke afzonderlijke term afzonderlijk toevoegen in een naam of woord groep met meerdere delen. Zo komt ' Unviersty ~ van ~ ' Wshington ~ ' overeen met ' University of Washington '.
 

##  <a name="bkmk_proximity"></a>Proximity Search  
 Proximity-Zoek opdrachten worden gebruikt om termen te vinden die zich in een document nabij elkaar bevinden. Voeg een tilde ' ~ ' aan het einde van een woord groep toe, gevolgd door het aantal woorden dat de grens van de nabijheid heeft gemaakt. Zo vinden `"hotel airport"~5` de termen ' Hotel ' en ' lucht haven ' binnen vijf woorden van elkaar in een document.  


##  <a name="bkmk_termboost"></a>Term versterking  
 De term Boosting verwijst naar een hoger niveau voor een document als dit de gestimuleerde term bevat, ten opzichte van documenten die de term niet bevatten. Dit verschilt van Score profielen in dat Score profiel om bepaalde velden in plaats van specifieke voor waarden te stimuleren.  

In het volgende voor beeld worden de verschillen toegelicht. Stel dat er een score profiel is dat overeenkomt met de overeenkomsten in een bepaald veld, bijvoorbeeld *genre* in het [musicstoreindex-voor beeld](index-add-scoring-profiles.md#bkmk_ex). De term versterking kan worden gebruikt om bepaalde zoek termen hoger te verhogen dan andere. `rock^2 electronic` worden bijvoorbeeld documenten die de zoek termen in het veld genre bevatten hoger dan andere Doorzoek bare velden in de index. Daarnaast worden documenten met de zoek term *Rock* hoger gerangschikt dan de andere zoek term *elektronisch* als resultaat van de waarde voor de verhoging van de term (2).  

 Als u een term wilt verhogen, gebruikt u het caret-teken (^) met een boost factor (een getal) aan het einde van de term die u zoekt. U kunt ook zinsdelen verg Roten. Hoe hoger de Boost-factor, hoe relevanter de term relatief is ten opzichte van andere zoek termen. Standaard is de Boost factor 1. Hoewel de Boost-factor positief moet zijn, kan deze kleiner zijn dan 1 (bijvoorbeeld 0,20).  

##  <a name="bkmk_regex"></a>Reguliere expressie zoeken  
 Een reguliere expressie zoekt zoekt naar een overeenkomst op basis van de inhoud tussen slashes '/', zoals beschreven in de [klasse RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).  

 Als u bijvoorbeeld documenten zoekt met ' Motel ' of ' Hotel ', geeft u `/[mh]otel/`op.  Zoek opdrachten in reguliere expressies worden vergeleken met enkele woorden.   

##  <a name="bkmk_wildcard"></a>Joker tekens zoeken  
 U kunt de algemeen erkende syntaxis voor meerdere (*) of enkelvoudige (?) tekens gebruiken als joker tekens. Opmerking de Lucene-query-parser ondersteunt het gebruik van deze symbolen met één term en geen woord groep.  

 Als u bijvoorbeeld documenten zoekt met de woorden met het voor voegsel ' Note ', zoals ' notitie ' of ' Klad blok ', geeft u ' Opmerking * ' op.  

> [!NOTE]  
>  U kunt geen * of gebruiken. symbool als het eerste teken van een zoek opdracht.  
>  Er wordt geen tekst analyse uitgevoerd voor zoek query's met Joker tekens. Tijdens het uitvoeren van de query worden de Joker teken query termen vergeleken met de geanalyseerde voor waarden in de zoek index en uitgevouwen.

## <a name="see-also"></a>Zie ook  

+ [Documenten zoeken](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Syntaxis van de OData-expressie voor filters en sorteren](query-odata-filter-orderby-syntax.md)   
+ [Eenvoudige query syntaxis in azure Cognitive Search](query-simple-syntax.md)   
