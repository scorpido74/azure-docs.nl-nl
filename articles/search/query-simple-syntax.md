---
title: Vereenvoudigde querysyntaxis
titleSuffix: Azure Cognitive Search
description: Referentie voor de eenvoudige querysyntaxis die wordt gebruikt voor zoekopdrachten met volledige tekst in Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/03/2020
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
ms.openlocfilehash: 3d5a4ddf863115747c27efbca1808d51444aac8c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656169"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Eenvoudige querysyntaxis in Azure Cognitive Search

Azure Cognitive Search implementeert twee op Lucene gebaseerde querytalen: [Simple Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) en de [Lucene Query Parser.](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) In Azure Cognitive Search worden de opties voor fuzzy/slop uitgesloten van de eenvoudige querysyntaxis.  

> [!NOTE]
> De syntaxis van eenvoudige query's wordt gebruikt voor queryexpressies die zijn doorgegeven in de **zoekparameter** van de API [voor zoekdocumenten,](https://docs.microsoft.com/rest/api/searchservice/search-documents) en niet te verwarren met de [syntaxis van OData](query-odata-filter-orderby-syntax.md) die wordt gebruikt voor de [$filter](search-filters.md) parameter van die API. Deze verschillende syntaxissen hebben hun eigen regels voor het construeren van query's, ontsnappen aan tekenreeksen, enzovoort.
>
> Azure Cognitive Search biedt een alternatieve [volledige Lucene-querysyntaxis](query-lucene-syntax.md) voor complexere query's in de **zoekparameter.** Zie Hoe zoeken in volledige tekst werkt in [Azure Cognitive Search](search-lucene-query-architecture.md)voor meer informatie over queryparsing-architectuur en voordelen van elke syntaxis.

## <a name="how-to-invoke-simple-parsing"></a>Hoe eenvoudige parsing aan te roepen

Eenvoudige syntaxis is de standaard. Aanroepen is alleen nodig als u de syntaxis van volledig naar eenvoudig zet. Als u de syntaxis `queryType` expliciet wilt instellen, gebruikt u de zoekparameter. Geldige waarden `simple|full`zijn `simple` , met `full` als standaard, en voor Lucene. 

## <a name="query-behavior-anomalies"></a>Afwijkingen van querygedrag

Elke tekst met een of meer termen wordt beschouwd als een geldig uitgangspunt voor queryuitvoering. Azure Cognitive Search komt overeen met documenten die een of alle termen bevatten, inclusief eventuele variaties die tijdens de analyse van de tekst zijn gevonden. 

Zo eenvoudig als dit klinkt, is er een aspect van query-uitvoering in Azure Cognitive Search dat onverwachte resultaten *kan* opleveren, waardoor de zoekresultaten worden verhoogd in plaats van te dalen naarmate er meer termen en operators aan de invoertekenreeks worden toegevoegd. Of deze uitbreiding daadwerkelijk plaatsvindt, hangt af van de `searchMode` opname van een OPERATOR NOT, gecombineerd met een parameterinstelling die bepaalt hoe NIET wordt geïnterpreteerd in termen van EN of OF gedragingen. Gezien de `searchMode=Any`standaardinstelling en een OPERATOR NOT wordt de bewerking berekend `"New York" NOT Seattle` als een OR-actie, zodat alle steden die niet in Seattle zijn, worden geretourneerd.  

Meestal ziet u dit gedrag eerder in gebruikersinteractiepatronen voor toepassingen die via inhoud zoeken, waarbij gebruikers eerder een operator in een query opnemen, in tegenstelling tot e-commercesites met meer ingebouwde navigatiestructuren. Zie [GEEN operator voor](#not-operator)meer informatie . 

## <a name="boolean-operators-and-or-not"></a>Booleaanse operatoren (EN, OF, NIET) 

U operators insluiten in een querytekenreeks om een uitgebreide set criteria op te bouwen waartegen overeenkomende documenten worden gevonden. 

### <a name="and-operator-"></a>EN operator`+`

De AND operator is een pluspunt. Zoek bijvoorbeeld `wifi+luxury` naar documenten die `wifi` beide `luxury`bevatten en .

### <a name="or-operator-"></a>OR-operator`|`

De OR-operator is een verticaal balk- of pijpteken. Zoek bijvoorbeeld `wifi | luxury` naar documenten die `wifi` een `luxury` of beide bevatten.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NIET operator`-`

De OPERATOR NOT is een minteken. Zoek bijvoorbeeld `wifi –luxury` naar documenten die `wifi` de term hebben en/of niet hebben `luxury` `searchMode`(en/of wordt gecontroleerd door ).

> [!NOTE]  
>  De `searchMode` optie bepaalt of een term met de OPERATOR NOT ANDed of ORed `+` is `|` met de andere termen in de query zonder een of operator. Terugroepactie `searchMode` die kan worden `any` ingesteld op `all`(standaard) of . Als u `any`query's gebruikt, wordt het terugroepen van `-` query's verhoogd door meer resultaten op te nemen en wordt deze standaard geïnterpreteerd als "OF niet". Komt bijvoorbeeld `wifi -luxury` overeen met documenten `wifi` die de term bevatten `luxury`of documenten die de term niet bevatten. Als u `all`deze gebruikt, zal het de precisie van query's verhogen door minder resultaten op te nemen, en standaard - wordt geïnterpreteerd als "EN NIET". Bijvoorbeeld, `wifi -luxury` zal overeenkomen met documenten `wifi` die de term bevatten en bevatten niet de term "luxe". Dit is aantoonbaar een meer `-` intuïtief gedrag voor de operator. Daarom moet u `searchMode=all` overwegen `searchMode=any` om zoekopdrachten te optimaliseren voor precisie in plaats van terugroepen *en* uw gebruikers gebruiken de `-` operator vaak in zoekopdrachten.

<a name="prefix-search"></a>

## <a name="suffix--operator-for-prefix-search"></a>Achtervoegseloperator `*` voor zoeken naar voorvoegsel

De bediener is een `*`sterretje . Zoek bijvoorbeeld `cap*` naar documenten met een term `cap`die begint met het negeren van aanvragen. 

Net als bij filters zoekt een voorvoegselquery naar een exacte overeenkomst. Als zodanig is er geen relevantie score (alle resultaten ontvangen een zoekscore van 1,0). Voorvoegselquery's kunnen traag zijn, vooral als de index groot is en het voorvoegsel uit een klein aantal tekens bestaat. 

Als u een achtervoegselquery wilt uitvoeren, overeenkomend op het laatste deel van de tekenreeks, gebruikt u een [jokerzoeking](query-lucene-syntax.md#bkmk_wildcard) en de volledige syntaxis van Lucene.

## <a name="phrase-search-operator"></a>Zoekoperator voor woordtermen

De woordgroep operator omsluit `" "`een zin tussen aanhalingstekens . Bijvoorbeeld, terwijl `Roach Motel` (zonder aanhalingstekens) `Roach` zou zoeken `Motel` naar documenten `"Roach Motel"` die en / of ergens in een volgorde, (met aanhalingstekens) zal alleen overeenkomen met documenten die die hele zin samen en in die volgorde (tekstanalyse is nog steeds van toepassing).

## <a name="precedence-operator"></a>Voorrangsoperator

De voorrangsoperator omsluit de tekenreeks `( )`tussen haakjes . Zoek bijvoorbeeld `motel+(wifi | luxury)` naar documenten met de motelterm `wifi` `luxury` en een of (of beide).  

## <a name="escaping-search-operators"></a>Ontsnappende zoekoperators  

 Om de bovenstaande symbolen te gebruiken als daadwerkelijk onderdeel van de zoektekst, moeten ze worden ontsnapt door ze vooraf te maken met een backslash. Bijvoorbeeld, `luxury\+hotel` zal resulteren in `luxury+hotel`de term . Om het voor de meer typische gevallen eenvoudig te maken, zijn er twee uitzonderingen op deze regel waarbij ontsnappen niet nodig is:  

- De NIET `-` operator hoeft alleen te worden ontsnapt als het het eerste teken na witruimte, niet als het in het midden van een termijn. Bijvoorbeeld, `wi-fi` is een enkele term; overwegende dat GUIDs `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`(zoals) als één token worden behandeld.
- De achtervoegsel operator `*` hoeft alleen te worden ontsnapt als het het laatste teken voor witruimte, niet als het in het midden van een termijn. Wordt bijvoorbeeld `wi*fi` behandeld als één token.

> [!NOTE]  
>  Hoewel ontsnappen tokens bij elkaar houdt, kan tekstanalyse ze opsplitsen, afhankelijk van de analysemodus. Zie [Taalondersteuning &#40;Azure Cognitive Search REST API&#41;](index-add-language-analyzers.md) voor meer informatie.  

## <a name="see-also"></a>Zie ook  

+ [Zoekdocumenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Syntaxis van Lucene query](query-lucene-syntax.md)
+ [Syntaxis voor OData-expressies](query-odata-filter-orderby-syntax.md) 
