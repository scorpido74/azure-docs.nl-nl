---
title: Vereenvoudigde querysyntaxis
titleSuffix: Azure Cognitive Search
description: Referentie voor de eenvoudige querysyntaxis die wordt gebruikt voor zoekopdrachten met volledige tekst in Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/12/2020
ms.openlocfilehash: 066190ff6b735d30db351ff90c0b6e5173b7f583
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258861"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Eenvoudige querysyntaxis in Azure Cognitive Search

Azure Cognitive Search implementeert twee op Lucene gebaseerde querytalen: [Simple Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) en de [Lucene Query Parser.](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 

In Azure Cognitive Search worden de eenvoudige querysyntaxis vage zoekbewerkingen uitgesloten. Gebruik in plaats daarvan de volledige syntaxis van Lucene voor [vaag zoeken](search-query-fuzzy.md).

> [!NOTE]
> De syntaxis van eenvoudige query's wordt gebruikt voor queryexpressies die zijn doorgegeven in de **zoekparameter** van de API [voor zoekdocumenten,](https://docs.microsoft.com/rest/api/searchservice/search-documents) en niet te verwarren met de [syntaxis van OData](query-odata-filter-orderby-syntax.md) die wordt gebruikt voor de [$filter](search-filters.md) parameter van die API. Deze verschillende syntaxissen hebben hun eigen regels voor het construeren van query's, ontsnappen aan tekenreeksen, enzovoort.
>
> Azure Cognitive Search biedt een alternatieve [volledige Lucene-querysyntaxis](query-lucene-syntax.md) voor complexere query's in de **zoekparameter.** Zie Hoe zoeken in volledige tekst werkt in [Azure Cognitive Search](search-lucene-query-architecture.md)voor meer informatie over queryparsing-architectuur en voordelen van elke syntaxis.

## <a name="invoke-simple-parsing"></a>Eenvoudige ontwering aanroepen

Eenvoudige syntaxis is de standaard. Aanroepen is alleen nodig als u de syntaxis van volledig naar eenvoudig zet. Als u de syntaxis `queryType` expliciet wilt instellen, gebruikt u de zoekparameter. Geldige waarden `queryType=simple` `queryType=full`omvatten `simple` of , waar `full` is de standaard, en roept de [volledige Lucene query parser](query-lucene-syntax.md) voor meer geavanceerde query's. 

## <a name="syntax-fundamentals"></a>Syntaxisfundamenten

Elke tekst met een of meer termen wordt beschouwd als een geldig uitgangspunt voor queryuitvoering. Azure Cognitive Search komt overeen met documenten die een of alle termen bevatten, inclusief eventuele variaties die tijdens de analyse van de tekst zijn gevonden.

Zo eenvoudig als dit klinkt, is er een aspect van query-uitvoering in Azure Cognitive Search dat onverwachte resultaten *kan* opleveren, waardoor de zoekresultaten worden verhoogd in plaats van te dalen naarmate er meer termen en operators aan de invoertekenreeks worden toegevoegd. Of deze uitbreiding daadwerkelijk plaatsvindt, hangt af van de opname van een NOT-operator, gecombineerd met een **parameterinstelling searchMode** die bepaalt hoe NIET wordt geïnterpreteerd in termen van EN of OF gedragingen. Zie [GEEN operator voor](#not-operator)meer informatie .

### <a name="precedence-operators-grouping"></a>Voorrangsoperatoren (groepering)

U haakjes gebruiken om subquery's te maken, inclusief operatoren binnen de bovenliggende instructie. Bijvoorbeeld, `motel+(wifi||luxury)` zal zoeken naar documenten met de "motel" term en ofwel "wifi" of "luxe" (of beide).

Veldgroepering is vergelijkbaar, maar scopes de groepering naar een enkel veld. Bijvoorbeeld, `hotelAmenities:(gym+(wifi||pool))` zoekt het veld "hotelVoorzieningen" voor "sportschool" en "wifi", of "sportschool" en "zwembad".  

### <a name="escaping-search-operators"></a>Ontsnappende zoekoperators  

Als u een van de zoekoperatoren als onderdeel van de zoektekst wilt gebruiken,`\`ontkomt u aan het teken door het vooraf op te lossen met een enkele backslash ( ). Voor een jokerzoekop `https://`op `://` bijvoorbeeld, waar maakt deel uit `search=https\:\/\/*`van de querytekenreeks, geeft u op . Op dezelfde manier kan een ontsnapt `\+1 \(800\) 642\-7676`telefoonnummer patroon er zo uitzien.

Speciale tekens die ontsnappen vereisen zijn de volgende:`- * ? \ /`  

Om het voor de meer typische gevallen eenvoudig te maken, zijn er twee uitzonderingen op deze regel waarbij ontsnappen niet nodig is:  

+ De NIET `-` operator hoeft alleen te worden ontsnapt als het het eerste teken na witruimte, niet als het in het midden van een termijn. De volgende GUID is bijvoorbeeld geldig zonder `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`het escape-teken: .

+ De achtervoegsel operator `*` hoeft alleen te worden ontsnapt als het het laatste teken voor witruimte, niet als het in het midden van een termijn. Er is `4*4=16` bijvoorbeeld geen backslash nodig.

> [!NOTE]  
> Hoewel ontsnappen tokens bij elkaar houdt, kan [lexicale analyse](search-lucene-query-architecture.md#stage-2-lexical-analysis) tijdens het indexeren ze uitkleden. De standaard Lucene analyzer verwijdert en breekt bijvoorbeeld woorden op koppeltekens, witruimte en andere tekens. Als u speciale tekens in de querytekenreeks nodig hebt, hebt u mogelijk een analyzer nodig die deze in de index behoudt. Sommige keuzes zijn Microsoft natural [language analyzers](index-add-language-analyzers.md), die afgebroken woorden behoudt, of een aangepaste analyzer voor meer complexe patronen. Zie [Gedeeltelijke termen, patronen en speciale tekens voor](search-query-partial-matching.md)meer informatie.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Onveilige en gereserveerde tekens coderen in URL's

Zorg ervoor dat alle onveilige en gereserveerde tekens zijn gecodeerd in een URL. '#' is bijvoorbeeld een onveilig teken omdat het een fragment/anker-id in een URL is. Het teken moet worden `%23` gecodeerd als het wordt gebruikt in een URL. '&' en '=' zijn voorbeelden van gereserveerde tekens omdat ze parameters afzonderen en waarden opgeven in Azure Cognitive Search. Zie [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) voor meer informatie.

Onveilige tekens ``" ` < > # % { } | \ ^ ~ [ ]``zijn . Gereserveerde `; / ? : @ = + &`tekens zijn .

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>Limieten voor querygrootte

 Er is een limiet aan de grootte van query's die u naar Azure Cognitive Search verzenden. In het bijzonder u ten hoogste 1024 clausules (expressies gescheiden door EN, OF, enzovoort). Er is ook een limiet van ongeveer 32 KB op de grootte van een individuele term in een query. Als uw toepassing query's programmatisch genereert, raden we u aan deze op een zodanige manier te ontwerpen dat query's van niet-begrensde grootte niet worden gegenereerd.  

## <a name="boolean-search"></a>Booleaanse zoekopdracht

U Booleaanse operatoren (EN, OF, NIET) insluiten in een querytekenreeks om een rijke set criteria te maken waartegen overeenkomende documenten worden gevonden. 

### <a name="and-operator-"></a>EN operator`+`

De AND operator is een pluspunt. Zoek bijvoorbeeld `wifi+luxury` naar documenten die `wifi` beide `luxury`bevatten en .

### <a name="or-operator-"></a>OR-operator`|`

De OR-operator is een verticaal balk- of pijpteken. Zoek bijvoorbeeld `wifi | luxury` naar documenten die `wifi` een `luxury` of beide bevatten.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NIET operator`-`

De OPERATOR NOT is een minteken. Zoek bijvoorbeeld `wifi –luxury` naar documenten die `wifi` de term hebben en/of niet hebben. `luxury`

De parameter **searchMode** op een queryaanvraag bepaalt of een term met de operator NOT ANDed `+` of `|` ORed is met andere termen in de query (ervan uitgaande dat er geen operator of operator op de andere voorwaarden is). Geldige waarden `any` `all`zijn o.a.

`searchMode=any`verhoogt het terugroepen van query's door `-` meer resultaten op te nemen en wordt standaard geïnterpreteerd als "OF NIET". Komt bijvoorbeeld `wifi -luxury` overeen met documenten `wifi` die de term bevatten `luxury`of documenten die de term niet bevatten.

`searchMode=all`verhoogt de precisie van query's door minder resultaten op te nemen, en standaard - zal worden geïnterpreteerd als "EN NIET". Bijvoorbeeld, `wifi -luxury` zal overeenkomen met documenten `wifi` die de term bevatten en bevatten niet de term "luxe". Dit is aantoonbaar een meer `-` intuïtief gedrag voor de operator. Daarom moet u `searchMode=all` overwegen `searchMode=any` om zoekopdrachten te optimaliseren voor precisie in plaats van terugroepen *en* uw gebruikers gebruiken de `-` operator vaak in zoekopdrachten.

Houd bij het kiezen van een **searchMode-instelling** rekening met de interactiepatronen van de gebruiker voor query's in verschillende toepassingen. Gebruikers die op zoek zijn naar informatie hebben meer kans om een operator op te nemen in een query, in tegenstelling tot e-commerce sites die meer ingebouwde navigatiestructuren hebben.

<a name="prefix-search"></a>

## <a name="prefix-search"></a>Zoeken naar voorvoegsel

De bediener is een `*`sterretje . Bijvoorbeeld, `lingui*` vindt "linguïstisch" of "linguini", het negeren van geval. 

Net als bij filters zoekt een voorvoegselquery naar een exacte overeenkomst. Als zodanig is er geen relevantie score (alle resultaten ontvangen een zoekscore van 1,0). Voorvoegselquery's kunnen traag zijn, vooral als de index groot is en het voorvoegsel uit een klein aantal tekens bestaat. 

Als u een achtervoegselquery wilt uitvoeren, overeenkomend op het laatste deel van de tekenreeks, gebruikt u een [jokerzoeking](query-lucene-syntax.md#bkmk_wildcard) en de volledige syntaxis van Lucene.

## <a name="phrase-search-"></a>Zoeken in woordgroep`"`

Een term zoeken is een query voor een of meer termen, waarbij een van de termen worden beschouwd als een overeenkomst. Een zinzoeken is een exacte `" "`zin tussen aanhalingstekens. Bijvoorbeeld, terwijl `Roach Motel` (zonder aanhalingstekens) `Roach` zou zoeken `Motel` naar documenten `"Roach Motel"` die en / of ergens in een volgorde, (met aanhalingstekens) zal alleen overeenkomen met documenten die die hele zin samen en in die volgorde (tekstanalyse is nog steeds van toepassing).

## <a name="see-also"></a>Zie ook  

+ [Queryvoorbeelden voor eenvoudig zoeken](search-query-simple-examples.md)
+ [Queryvoorbeelden voor volledige Lucene-zoekopdracht](search-query-lucene-examples.md)
+ [Zoekdocumenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Syntaxis van Lucene query](query-lucene-syntax.md)
+ [Syntaxis voor OData-expressies](query-odata-filter-orderby-syntax.md) 
