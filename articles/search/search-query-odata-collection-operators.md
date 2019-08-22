---
title: Referentie voor OData-verzamelings operator-Azure Search
description: OData-verzamelings operators, alle en alle en Lambda-expressies in Azure Search query's.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: e057d0b57162d10aab13d8b1f77e0eaddca2ec2a
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647639"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>OData-verzamelings operators `any` in azure Search-en`all`

Wanneer u een [OData-filter expressie](query-odata-filter-orderby-syntax.md) schrijft om te gebruiken met Azure Search, is het vaak handig om te filteren op verzamelings velden. U kunt dit doen met behulp `all` van de `any` Opera tors en.

## <a name="syntax"></a>Syntaxis

De volgende ebnf ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definieert de grammatica van een OData-expressie die `any` gebruikmaakt `all`van of.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Er is ook een interactief syntaxis diagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxis diagram van OData voor Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Zie de [syntaxis van de OData-expressie voor Azure Search](search-query-odata-syntax-reference.md) voor de volledige ebnf.

Er zijn drie soorten expressies waarmee verzamelingen worden gefilterd.

- De eerste twee herhaalde iteratie van een verzamelings veld, waarbij een predikaat wordt toegepast dat is opgegeven in de vorm van een lambda-expressie voor elk element van de verzameling.
  - Een expressie die `all` gebruikmaakt `true` van retourneert als het predicaat True is voor elk element van de verzameling.
  - Een expressie die `any` gebruikmaakt `true` van retourneert als het predicaat True is voor ten minste één element van de verzameling.
- Het derde formulier van het verzamelings `any` filter gebruikt zonder een lambda-expressie om te testen of een veld in de verzameling leeg is. Als de verzameling elementen bevat, wordt deze geretourneerd `true`. Als de verzameling leeg is, wordt geretourneerd `false`.

Een **lambda-expressie** in een verzamelings filter is net als de hoofd tekst van een lus in een programmeer taal. Hiermee wordt een variabele gedefinieerd, de **variabele Range**genoemd, die het huidige element van de verzameling tijdens iteratie bevat. Er wordt ook een andere booleaanse expressie gedefinieerd die de filter criteria is die moeten worden toegepast op de bereik variabele voor elk element van de verzameling.

## <a name="examples"></a>Voorbeelden

Overeenkomende documenten waarvan `tags` het veld precies de teken reeks ' WiFi ' bevat:

    tags/any(t: t eq 'wifi')

Vergelijkt documenten waarbij elk element `ratings` van het veld tussen 3 en 5 ligt:

    ratings/all(r: r ge 3 and r le 5)

Vergelijkt documenten waarbij een van de geo- `locations` coördinaten in het veld binnen de opgegeven veelhoek ligt:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Overeenkomende documenten waarbij het `rooms` veld leeg is:

    not rooms/any()

Documenten die voor alle ruimten overeenkomen, `rooms/amenities` het veld bevat ' TV ' `rooms/baseRate` en is kleiner dan 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Beperkingen

Niet elke functie van filter expressies is beschikbaar in de hoofd tekst van een lambda-expressie. De beperkingen variëren, afhankelijk van het gegevens type van het verzamelings veld dat u wilt filteren. De volgende tabel geeft een overzicht van de beperkingen.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Zie [problemen met verzamelings filters in azure Search oplossen](search-query-troubleshoot-collection-filters.md)voor meer informatie over deze beperkingen en voor beelden. Zie informatie over het [verzamelen van filters in azure Search](search-query-understand-collection-filters.md)voor meer gedetailleerde informatie over de oorzaak van deze beperkingen.

## <a name="next-steps"></a>Volgende stappen  

- [Filters in Azure Search](search-filters.md)
- [Overzicht van de OData-expressie taal voor Azure Search](query-odata-filter-orderby-syntax.md)
- [Verwijzing naar de syntaxis van de OData-expressie voor Azure Search](search-query-odata-syntax-reference.md)
- [Zoeken naar &#40;documenten Azure Search service rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
