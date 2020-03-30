---
title: Syntaxisverwijzing oData-expressie
titleSuffix: Azure Cognitive Search
description: Formele grammatica- en syntaxisspecificatie voor OData-expressies in Azure Cognitive Search-query's.
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
ms.openlocfilehash: f3422fd10e062ae87bc165491e0d01ac2b4943d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793239"
---
# <a name="odata-expression-syntax-reference-for-azure-cognitive-search"></a>Syntaxisverwijzing oData-expressie voor Azure Cognitive Search

Azure Cognitive Search gebruikt [OData-expressies](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) als parameters in de API. Meestal worden OData-expressies `$orderby` gebruikt `$filter` voor de parameters en parameters. Deze expressies kunnen complex zijn en meerdere clausules, functies en operatoren bevatten. Zelfs eenvoudige OData-expressies zoals eigenschapspaden worden echter in veel delen van de Azure Cognitive Search REST API gebruikt. Padexpressies worden bijvoorbeeld gebruikt om te verwijzen naar subvelden van complexe velden overal in de API, zoals bij `$select` het weergeven van subvelden in een [suggestie,](index-add-suggesters.md)een [scoringsfunctie,](index-add-scoring-profiles.md)de parameter of zelfs [zoeken in Lucene-query's](query-lucene-syntax.md).

In dit artikel worden al deze vormen van OData-expressies beschreven met behulp van een formele grammatica. Er is ook een [interactief diagram](#syntax-diagram) om visueel te helpen de grammatica te verkennen.

## <a name="formal-grammar"></a>Formele grammatica

We kunnen de subset van de OData-taal die wordt ondersteund door Azure Cognitive Search beschrijven met behulp van een EBNF -grammatica[(Extended Backus-Naur Form).](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) Regels worden 'top-down' weergegeven, te beginnen met de meest complexe expressies, en ze op te splitsen in primitievere expressies. Bovenaan staan de grammaticaregels die overeenkomen met specifieke parameters van de Azure Cognitive Search REST API:

- [`$filter`](search-query-odata-filter.md), gedefinieerd door `filter_expression` de regel.
- [`$orderby`](search-query-odata-orderby.md), gedefinieerd door `order_by_expression` de regel.
- [`$select`](search-query-odata-select.md), gedefinieerd door `select_expression` de regel.
- Veldpaden, gedefinieerd door `field_path` de regel. Veldpaden worden in de hele API gebruikt. Ze kunnen verwijzen naar velden op het hoogste niveau van een index of subvelden met een of meer [complexe veldvoorouders.](search-howto-complex-data-types.md)

Na de EBNF is een browsable [syntaxdiagram](https://en.wikipedia.org/wiki/Syntax_diagram) waarmee u interactief de grammatica en de relaties tussen de regels verkennen.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
/* Top-level rules */

filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*


/* Shared base rules */

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*


/* Rules for $orderby */

order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'


/* Rules for $filter */

boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path

collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression

logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression

comparison_expression ::= 
    variable_or_function comparison_operator constant | 
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'


/* Rules for constants and literals */

constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'


/* Rules for functions */

function_call ::=
    geo_distance_call |
    boolean_function_call

geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

boolean_function_call ::=
    geo_intersects_call |
    search_in_call |
    search_is_match_call

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*

search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'

/* Note that it is illegal to call search.ismatch or search.ismatchscoring
from inside a lambda expression. */
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

## <a name="syntax-diagram"></a>Syntaxisdiagram

Als u de OData-taalgrammatica die wordt ondersteund door Azure Cognitive Search visueel wilt verkennen, probeert u het interactieve syntaxisdiagram:

> [!div class="nextstepaction"]
> [Syntaxisdiagram OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>Zie ook  

- [Filters in Azure Cognitive Search](search-filters.md)
- [Zoekdocumenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Syntaxis van Lucene query](query-lucene-syntax.md)
- [Eenvoudige querysyntaxis in Azure Cognitive Search](query-simple-syntax.md)
