---
title: Referentie van oData-verzamelingsoperator
titleSuffix: Azure Cognitive Search
description: Wanneer u filterexpressies maakt in Azure Cognitive Search-query's, gebruikt u 'alle' en 'alle' operatoren in lambda-expressies wanneer het filter zich in een verzameling of complex verzamelingsveld bevindt.
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
ms.openlocfilehash: 54ddc8222816831b5b436297bbb1b40d03230f0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113231"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>OData-verzamelingsoperatoren in `any` Azure Cognitive Search - en`all`

Bij het schrijven van een [OData-filterexpressie](query-odata-filter-orderby-syntax.md) die u wilt gebruiken met Azure Cognitive Search, is het vaak handig om te filteren op verzamelingsvelden. U dit `any` bereiken `all` met behulp van de en exploitanten.

## <a name="syntax"></a>Syntaxis

Met de volgende EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) `any` wordt `all`de grammatica gedefinieerd van een OData-expressie die gebruik maakt of .

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Er is ook een interactief syntaxisdiagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxisdiagram OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Zie [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige EBNF.

Er zijn drie expressievormen die verzamelingen filteren.

- De eerste twee herhalen over een verzamelveld en passen een predicaat toe in de vorm van een lambda-uitdrukking op elk element van de collectie.
  - Een expressie `all` `true` met retouren als het predicaat geldt voor elk element van de verzameling.
  - Een expressie `any` `true` met retouren als het predicaat geldt voor ten minste één element van de verzameling.
- De derde vorm van `any` collectiefilter gebruikt zonder lambda-expressie om te testen of een verzamelveld leeg is. Als de verzameling elementen bevat, wordt deze geretourneerd. `true` Als de verzameling leeg `false`is, wordt deze geretourneerd.

Een **lambdauitdrukking** in een inzamelingsfilter is als het lichaam van een lijn in een programmeertaal. Het definieert een variabele, genaamd het **bereik variabele**, dat het huidige element van de collectie houdt tijdens iteratie. Het definieert ook een andere booleaanse expressie die de filtercriteria is die van toepassing zijn op de bereikvariabele voor elk element van de verzameling.

## <a name="examples"></a>Voorbeelden

Overeenkomen met `tags` documenten waarvan het veld precies de tekenreeks "wifi" bevat:

    tags/any(t: t eq 'wifi')

Overeenkomen met documenten waarin `ratings` elk element van het veld tussen 3 en 5 valt, inclusief:

    ratings/all(r: r ge 3 and r le 5)

Documenten overeenkomen waarbij een van de `locations` geocoördinaten in het veld zich binnen de opgegeven veelhoek bevindt:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Documenten overeenkomen `rooms` waar het veld leeg is:

    not rooms/any()

Overeenkomen met documenten waar `rooms/amenities` voor alle kamers, `rooms/baseRate` het veld bevat "tv" en is minder dan 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Beperkingen

Niet elk kenmerk van filterexpressies is beschikbaar in het lichaam van een lambda-expressie. De beperkingen verschillen afhankelijk van het gegevenstype van het verzamelingsveld dat u wilt filteren. In de volgende tabel worden de beperkingen samengevat.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Zie [Filters voor het oplossen van problemen oplossen in Azure Cognitive Search](search-query-troubleshoot-collection-filters.md)voor meer informatie over deze beperkingen en voorbeelden. Zie [Verzamelingsfilters in Azure Cognitive Search begrijpen](search-query-understand-collection-filters.md)voor meer diepgaande informatie over waarom deze beperkingen bestaan.

## <a name="next-steps"></a>Volgende stappen  

- [Filters in Azure Cognitive Search](search-filters.md)
- [Overzicht van OData-expressietaal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoekdocumenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
