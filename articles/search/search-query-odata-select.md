---
title: Verwijzing naar OData selecteren
titleSuffix: Azure Cognitive Search
description: De syntaxis en de taal verwijzing voor een expliciete selectie van velden die moeten worden geretourneerd in de zoek resultaten van Azure Cognitive Search query's.
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
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113097"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>OData-$select syntaxis in azure Cognitive Search

 U kunt de [OData- **$Select** para meter](query-odata-filter-orderby-syntax.md) gebruiken om te kiezen welke velden u wilt toevoegen aan de zoek resultaten van Azure Cognitive Search. In dit artikel wordt de syntaxis van **$Select** uitvoerig beschreven. Zie [How to work with Search Results in Azure Cognitive Search](search-pagination-page-layout.md)voor meer algemene informatie over het gebruik van **$Select** bij het presen teren van zoek resultaten.

## <a name="syntax"></a>Syntaxis

De **$Select** -para meter bepaalt welke velden voor elk document worden geretourneerd in de resultatenset van de query. De volgende EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definieert de grammatica voor de para meter **$Select** :

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Er is ook een interactief syntaxis diagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxis diagram van OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Zie [OData-expressie syntaxis referentie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige ebnf.

De para meter **$Select** is beschikbaar in twee vormen:

1. Eén ster (`*`), waarmee wordt aangegeven dat alle ophaalbaar velden moeten worden geretourneerd, of
1. Een door komma's gescheiden lijst met veld paden, waarmee wordt aangegeven welke velden moeten worden geretourneerd.

Wanneer u het tweede formulier gebruikt, mag u alleen ophalen bare velden in de lijst opgeven.

Als u een complex veld vermeldt zonder de subvelden expliciet op te geven, worden alle subvelden die kunnen worden opgehaald, opgenomen in de resultatenset van de query. Stel bijvoorbeeld dat uw index een `Address` veld bevat met `Street`, `City`en `Country` subvelden die allemaal kunnen worden opgehaald. Als u `Address` opgeeft in **$Select**, bevatten de query resultaten alle drie de subvelden.

## <a name="examples"></a>Voorbeelden

Neem de `HotelId`, `HotelName`en `Rating` velden op het hoogste niveau in de resultaten op, evenals het subveld `City` van `Address`:

    $select=HotelId, HotelName, Rating, Address/City

Een voor beeld van een resultaat kan er als volgt uitzien:

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

Neem het `HotelName` veld op het hoogste niveau in de resultaten op, evenals alle subvelden van `Address`en de subvelden `Type` en `BaseRate` van elk object in de `Rooms` verzameling:

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Een voor beeld van een resultaat kan er als volgt uitzien:

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

- [Werken met zoek resultaten in azure Cognitive Search](search-pagination-page-layout.md)
- [Overzicht van de OData-expressie taal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Naslag informatie voor de syntaxis van OData-expressies voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoeken in &#40;documenten Azure Cognitive Search rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
