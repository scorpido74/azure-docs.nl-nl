---
title: Verwijzing oData selecteren
titleSuffix: Azure Cognitive Search
description: Syntaxis en taalverwijzing voor expliciete selectie van velden die moeten worden weergegeven in de zoekresultaten van Azure Cognitive Search-query's.
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
ms.openlocfilehash: 64f15bf3d262249cdda2760c7ddf768be2590419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113097"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>OData $select syntaxis in Azure Cognitive Search

 U de [parameter OData **$select** ](query-odata-filter-orderby-syntax.md) gebruiken om te kiezen welke velden u wilt opnemen in zoekresultaten van Azure Cognitive Search. In dit artikel worden de syntaxis van **$select** in detail beschreven. Zie [Hoe u met zoekresultaten werken in Azure Cognitive Search](search-pagination-page-layout.md)voor meer algemene informatie over het gebruik van **$select** bij het presenteren van zoekresultaten.

## <a name="syntax"></a>Syntaxis

De **parameter $select** bepaalt welke velden voor elk document worden geretourneerd in de queryresultaatset. In het volgende EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) wordt de grammatica voor de **parameter $select** gedefinieerd:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Er is ook een interactief syntaxisdiagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxisdiagram OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Zie [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige EBNF.

De **parameter $select** komt in twee vormen:

1. Een enkele`*`ster ( ), die aangeeft dat alle opvraagbare velden moeten worden geretourneerd, of
1. Een door komma's gescheiden lijst met veldpaden, die bepalen welke velden moeten worden geretourneerd.

Wanneer u het tweede formulier gebruikt, u alleen ophaalbare velden in de lijst opgeven.

Als u een complex veld aangeeft zonder de subvelden expliciet op te geven, worden alle opvraagbare subvelden opgenomen in de queryresultatenset. Stel dat uw index `Address` een `Street`veld `City`heeft `Country` met , en subvelden die allemaal kunnen worden opgehaald. Als u `Address` in **$select**opgeeft, bevatten de queryresultaten alle drie de subvelden.

## <a name="examples"></a>Voorbeelden

Neem `HotelId`de `HotelName`velden `Rating` op het hoogste niveau op in `City` de resultaten, evenals het subveld van `Address`:

    $select=HotelId, HotelName, Rating, Address/City

Een voorbeeldresultaat kan er als volgt uitzien:

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

Neem `HotelName` het veld op het hoogste niveau op in `Address`de resultaten, `BaseRate` evenals alle subvelden `Rooms` van , en de `Type` en subvelden van elk object in de verzameling:

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Een voorbeeldresultaat kan er als volgt uitzien:

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen  

- [Werken met zoekresultaten in Azure Cognitive Search](search-pagination-page-layout.md)
- [Overzicht van OData-expressietaal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoekdocumenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
