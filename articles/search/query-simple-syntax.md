---
title: Vereenvoudigde querysyntaxis
titleSuffix: Azure Cognitive Search
description: Verwijzing voor de eenvoudige query syntaxis die wordt gebruikt voor Zoek opdrachten in volledige tekst in azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: d07364e20cc11abc52ad9b308eb5daed8a65c146
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923378"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Eenvoudige query syntaxis in azure Cognitive Search

Azure Cognitive Search implementeert twee op lucene gebaseerde query talen: [eenvoudige query-parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) en de [lucene query-parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

De eenvoudige parser is flexibeler en probeert een aanvraag te interpreteren, zelfs als deze niet perfect is samengesteld. Als gevolg van deze flexibiliteit is dit de standaard instelling voor query's in azure Cognitive Search. 

De eenvoudige syntaxis wordt gebruikt voor query-expressies die zijn door gegeven in de `search` para meter van een aanvraag voor een [Zoek opdracht](/rest/api/searchservice/search-documents)en niet moeten worden verward met de [OData-syntaxis](query-odata-filter-orderby-syntax.md) die wordt gebruikt voor de para meter [$filter-EXPRESSIES](search-filters.md) van dezelfde Zoek document-API. De `search` `$filter` para meters en hebben een andere syntaxis, met hun eigen regels voor het maken van query's, escape reeksen, enzovoort.

Hoewel de eenvoudige parser is gebaseerd op de klasse [Apache Lucene Simple query parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) , sluit de implementatie in azure Cognitive Search onduidelijke zoek actie uit. Als u [fuzzy Search](search-query-fuzzy.md) of andere geavanceerde query formulieren nodig hebt, moet u in plaats daarvan de alternatieve [volledige-lucene-query syntaxis](query-lucene-syntax.md) gebruiken.

## <a name="invoke-simple-parsing"></a>Eenvoudige parsering aanroepen

Eenvoudige syntaxis is de standaard instelling. Aanroep is alleen nodig als u de syntaxis van volledig naar eenvoudig opnieuw instelt. Als u de syntaxis expliciet wilt instellen, gebruikt u de `queryType` Zoek parameter. Geldige waarden zijn `queryType=simple` of `queryType=full` , waar `simple` de standaard waarde is, en `full` roept de [volledige lucene-query-parser](query-lucene-syntax.md) aan voor meer geavanceerde query's. 

## <a name="syntax-fundamentals"></a>Syntaxis basis

Alle tekst met een of meer voor waarden wordt beschouwd als een geldig start punt voor het uitvoeren van query's. Azure Cognitive Search komt overeen met documenten die een of meer van de voor waarden bevatten, inclusief eventuele variaties die tijdens de analyse van de tekst zijn gevonden.

Net zoals dit klinkt, is er één aspect van de uitvoering van query's in azure Cognitive Search die *mogelijk* onverwachte resultaten oplevert, in plaats van de zoek resultaten te verminderen, omdat meer voor waarden en Opera tors worden toegevoegd aan de invoer teken reeks. Of deze uitbrei ding daad werkelijk plaatsvindt, is afhankelijk van de opname van een NOT-operator, gecombineerd met een **Search mode** parameter instelling die bepaalt hoe niet wordt geïnterpreteerd in termen van en of of gedrag. Zie [niet-operator](#not-operator)voor meer informatie.

### <a name="precedence-operators-grouping"></a>Prioriteits operatoren (groepering)

U kunt haakjes gebruiken om subquery's te maken, inclusief Opera tors in de instructie van de haak. Zoekt bijvoorbeeld naar `motel+(wifi|luxury)` documenten met de term ' Motel ' en ' WiFi ' of ' luxe ' (of beide).

Veld groepering is vergelijkbaar, maar bereiken de groepering op één veld. `hotelAmenities:(gym+(wifi|pool))`Zoekt bijvoorbeeld naar het veld "hotelAmenities" voor "Gym" en "WiFi" of "Gym" en "pool".  

### <a name="escaping-search-operators"></a>Zoek operatoren voor Escapes  

In de eenvoudige syntaxis bevatten Zoek operatoren de volgende tekens: `+ | " ( ) ' \`  

Als een van deze tekens deel uitmaakt van een token in de index, kunt u deze door een voor voegsel op te maken met één back slash ( `\` ) in de query. Stel dat u een aangepaste Analyzer hebt gebruikt voor de volledige term-tokening en de index bevat de teken reeks "luxe en Hotel". Als u een exacte overeenkomst voor dit token wilt krijgen, voegt u een escape-teken in:  `search=luxury\+hotel` . 

Er zijn twee uitzonde ringen op deze regel, waar Escapes niet nodig is om dingen eenvoudig te maken voor de meest voorkomende gevallen:  

+ De operator NOT `-` moet alleen worden ontsnapd als dit het eerste teken na een spatie is. Als de `-` in het midden wordt weer gegeven (bijvoorbeeld in `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` ), kunt u de Escapes overs Laan.

+ De operator achtervoegsel `*` moet alleen worden ontsnapd als dit het laatste teken vóór een spatie is. Als de `*` in het midden wordt weer gegeven (bijvoorbeeld in `4*4=16` ), is er geen escape-tekens nodig.

> [!NOTE]  
> Standaard worden woorden op afbreek streepjes, spaties, ampersands en andere tekens tijdens de [lexicale analyse](search-lucene-query-architecture.md#stage-2-lexical-analysis)verwijderd en verbroken. Als u wilt dat speciale tekens in de query reeks blijven staan, hebt u mogelijk een analyse programma nodig dat deze in de index behoudt. Enkele keuze mogelijkheden zijn micro soft Natural [Language-analyse](index-add-language-analyzers.md)functies, waarmee woorden met een afbreek streepje worden behouden, of een aangepaste analyse functie voor complexere patronen. Zie voor meer informatie [gedeeltelijke termen, patronen en speciale tekens](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Onveilige en gereserveerde tekens in Url's coderen

Zorg ervoor dat alle onveilige en gereserveerde tekens worden gecodeerd in een URL. Bijvoorbeeld, ' # ' is een onveilig teken, omdat het een fragment/anker-id in een URL is. Het teken moet worden gecodeerd `%23` als het wordt gebruikt in een URL. ' & ' en ' = ' zijn voor beelden van gereserveerde tekens wanneer ze para meters opwaarderen en waarden opgeven in azure Cognitive Search. Raadpleeg [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) voor meer informatie.

Onveilige tekens zijn ``" ` < > # % { } | \ ^ ~ [ ]`` . Gereserveerde tekens zijn `; / ? : @ = + &` .

### <a name="querying-for-special-characters"></a>Query's uitvoeren voor speciale tekens

In sommige gevallen wilt u mogelijk zoeken naar een speciaal teken, zoals het ' ❤ ' Emoji of het teken ' € '. In dat geval moet u er ook voor zorgen dat de analyse die u gebruikt, deze tekens niet uitfiltert.  In de standaard analyse worden veel van de speciale tekens genegeerd, zodat deze geen tokens worden in de index.

De eerste stap is daarom het gebruik van een analyse functie waarmee u rekening moet houden met de tokens van deze elementen. De "White"-analyse functie houdt bijvoorbeeld rekening met alle teken reeksen gescheiden door spaties als tokens, zodat de teken reeks "❤" als een token wordt beschouwd. Daarnaast kunt u met een analyse functie zoals micro soft English Analyzer (en micro soft) de teken reeks ' € ' in overweging nemen als een token. U kunt [een analyse programma testen](/rest/api/searchservice/test-analyzer) om te zien welke tokens worden gegenereerd voor een bepaalde query.

Wanneer u Unicode-tekens gebruikt, moet u ervoor zorgen dat symbolen op de juiste wijze in de query-URL staan (bijvoorbeeld voor ' ❤ ' zou de escape-reeks gebruiken `%E2%9D%A4+` ). Postman doet deze vertaling automatisch.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a> Limieten voor query grootte

 Er is een limiet voor de grootte van query's die u kunt verzenden naar Azure Cognitive Search. U kunt met name de meeste 1024-componenten hebben (expressies gescheiden door en, of, enzovoort). Er is ook een limiet van ongeveer 32 KB op de grootte van een afzonderlijke term in een query. Als uw toepassing Zoek query's programmatisch genereert, raden we u aan om deze zodanig te ontwerpen dat er geen query's van een ongebonden grootte worden gegenereerd.  

## <a name="boolean-search"></a>Booleaanse zoek actie

U kunt Booleaanse Opera tors (en, of, niet) insluiten in een query reeks om een uitgebreide set criteria te maken waarmee overeenkomende documenten worden gevonden. 

### <a name="and-operator-"></a>Operator en `+`

De operator AND is een plus teken. Zoekt bijvoorbeeld naar `wifi + luxury` documenten met zowel `wifi` als `luxury` .

### <a name="or-operator-"></a>OR-operator `|`

De operator OR is een verticale streep of een sluis teken. Zoekt bijvoorbeeld naar `wifi | luxury` documenten met ofwel `wifi` of `luxury` beide.

<a name="not-operator"></a>

### <a name="not-operator--"></a>GEEN operator `-`

De operator NOT is een minteken. U kunt bijvoorbeeld `wifi –luxury` zoeken naar documenten die de `wifi` term en/of niet hebben `luxury` .

De para meter **Search mode** voor een query-aanvraag bepaalt of een term met de operator Not ANDed of ORed is met andere voor waarden in de query (ervan uitgaande dat er geen `+` operator is of `|` de andere voor waarden). Geldige waarden zijn `any` of `all` .

`searchMode=any` Hiermee verg root u het intrekken van query's door meer resultaten op te nemen `-` . deze worden standaard geïnterpreteerd als "of niet". Komt bijvoorbeeld `wifi -luxury` overeen met documenten die de voor waarden bevatten of die de `wifi` term niet bevatten `luxury` .

`searchMode=all` verhoogt de nauw keurigheid van query's door minder resultaten op te nemen en wordt standaard geïnterpreteerd als "en niet". Komt bijvoorbeeld `wifi -luxury` overeen met documenten die de term bevatten `wifi` en niet de term ' luxe ' bevatten. Dit is weliswaar een meer intuïtief gedrag voor de `-` operator. Daarom kunt u overwegen `searchMode=all` in plaats van te gebruiken `searchMode=any` Als u zoek acties wilt optimaliseren in plaats van intrekken, *en* uw gebruikers vaak de `-` operator gebruiken in Zoek opdrachten.

Houd bij het kiezen van een **Search mode** -instelling rekening met de gebruikers interactie patronen voor query's in verschillende toepassingen. Gebruikers die op zoek zijn naar informatie, hebben waarschijnlijk vaker een operator in een query opgenomen, in tegens telling tot e-commerce sites met meer ingebouwde navigatie structuren.

<a name="prefix-search"></a>

## <a name="wildcard-prefix-matching--"></a>Joker teken voorvoegsel (*,?)

Voor de query's ' begint met ' voegt u een operator suffix toe als tijdelijke aanduiding voor de rest van een term. Gebruik een asterisk `*` voor meerdere tekens of `?` voor één teken. Bijvoorbeeld, komt `lingui*` overeen met "linguïstische" of "Linguini", het hoofdletter gebruik wordt genegeerd. 

Net als bij filters zoekt een voorvoegsel query naar een exacte overeenkomst. Zo is er geen relevantie score (alle resultaten krijgen een zoek Score van 1,0). Houd er rekening mee dat voorvoegsel query's langzaam kunnen zijn, met name als de index groot is en het voor voegsel uit een klein aantal tekens bestaat. Een alternatieve methodologie, zoals Edge n-gram tokening, kan sneller worden uitgevoerd.

Gebruik voor andere varianten van joker tekens, zoals achtervoegsel of infix dat overeenkomt met het einde of het midden van een term, de [volledige lucene-syntaxis voor Zoek opdrachten met Joker tekens](query-lucene-syntax.md#bkmk_wildcard).

## <a name="phrase-search-"></a>Woordgroepen zoeken `"`

Een term zoeken is een query voor een of meer voor waarden, waarbij een van de voor waarden als een overeenkomst wordt beschouwd. Een woordgroepen zoekopdracht is een exacte woord groep tussen aanhalings tekens `" "` . Als u bijvoorbeeld `Roach Motel` (zonder aanhalings tekens) zoekt naar documenten met `Roach` en/of `Motel` ergens in een wille keurige volg orde, `"Roach Motel"` (met aanhalings tekens), komen alleen documenten overeen met die hele woord groep samen en in die volg orde (de lexicale analyse is nog steeds van toepassing).

## <a name="see-also"></a>Zie ook  

+ [Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Query voorbeelden voor eenvoudige Zoek opdrachten](search-query-simple-examples.md)
+ [Query voorbeelden voor volledige lucene-Zoek opdrachten](search-query-lucene-examples.md)
+ [REST API voor documenten zoeken](/rest/api/searchservice/Search-Documents)
+ [Lucene-query syntaxis](query-lucene-syntax.md)
+ [Syntaxis voor OData-expressies](query-odata-filter-orderby-syntax.md)