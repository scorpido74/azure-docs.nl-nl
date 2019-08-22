---
title: OData-Azure Search selecteren
description: Naslag informatie voor de OData-taal voor Select-syntaxis in Azure Search query's.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 64e9ad75d88f595ab5def6fe8b63fee9407ae0fe
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647875"
---
# <a name="odata-select-syntax-in-azure-search"></a>OData-$select syntaxis in Azure Search

 U kunt de [OData- **$Select** para meter](query-odata-filter-orderby-syntax.md) gebruiken om te kiezen welke velden moeten worden meegenomen in de zoek resultaten van Azure Search. In dit artikel wordt de syntaxis van **$Select** uitvoerig beschreven. Zie [How to use with the Search Results in azure Search](search-pagination-page-layout.md)voor meer algemene informatie over het gebruik van **$Select** bij het presen teren van zoek resultaten.

## <a name="syntax"></a>Syntaxis

De **$Select** -para meter bepaalt welke velden voor elk document worden geretourneerd in de resultatenset van de query. De volgende EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definieert de grammatica voor de para meter **$Select** :

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Er is ook een interactief syntaxis diagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxis diagram van OData voor Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Zie de [syntaxis van de OData-expressie voor Azure Search](search-query-odata-syntax-reference.md) voor de volledige ebnf.

De para meter **$Select** is beschikbaar in twee vormen:

1. Een enkele ster (`*`), waarmee wordt aangegeven dat alle ophaalbaar velden moeten worden geretourneerd, of
1. Een door komma's gescheiden lijst met veld paden, waarmee wordt aangegeven welke velden moeten worden geretourneerd.

Wanneer u het tweede formulier gebruikt, mag u alleen ophalen bare velden in de lijst opgeven.

Als u een complex veld vermeldt zonder de subvelden expliciet op te geven, worden alle subvelden die kunnen worden opgehaald, opgenomen in de resultatenset van de query. Stel bijvoorbeeld `Address` dat uw index een veld bevat met `Street`, `City`en `Country` subvelden die allemaal kunnen worden opgehaald. Als u in `Address` **$Select**opgeeft, worden in de query resultaten alle drie subvelden weer geven.

## <a name="examples"></a>Voorbeelden

Neem de `HotelId`velden `HotelName`, en `Rating` op het hoogste niveau in de resultaten op, evenals het `City` subveld van `Address`:

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

Neem het `HotelName` veld op het hoogste niveau in de resultaten op, evenals alle subvelden van `Address`, en de `Type` `BaseRate` subvelden van elk object in de `Rooms` verzameling:

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

- [Werken met zoek resultaten in Azure Search](search-pagination-page-layout.md)
- [Overzicht van de OData-expressie taal voor Azure Search](query-odata-filter-orderby-syntax.md)
- [Verwijzing naar de syntaxis van de OData-expressie voor Azure Search](search-query-odata-syntax-reference.md)
- [Zoeken naar &#40;documenten Azure Search service rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
