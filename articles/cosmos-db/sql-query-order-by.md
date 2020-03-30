---
title: ORDER BY-clausule in Azure Cosmos DB
description: Meer informatie over SQL ORDER BY-clausule voor Azure Cosmos DB. Sql gebruiken als json-querytaal van Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: tisande
ms.openlocfilehash: b88184be39a41ec42f8fb304a7511073f645f1cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188734"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>ORDER BY-clausule in Azure Cosmos DB

De optionele orderdoor-clausule geeft de sorteervolgorde op voor resultaten die door de query worden geretourneerd.

## <a name="syntax"></a>Syntaxis
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumenten
  
- `<sort_specification>`  
  
   Hiermee geeft u een eigenschap of expressie op waarop de queryresultatenset moet worden gesorteerd. Een sorteerkolom kan worden opgegeven als een naam of eigenschapsalias.  
  
   Er kunnen meerdere eigenschappen worden opgegeven. Eigendomsnamen moeten uniek zijn. De volgorde van de sorteereigenschappen in de component ORDER BY definieert de organisatie van de gesorteerde resultaatset. Dat wil zeggen, het resultaat set wordt gesorteerd op de eerste eigenschap en vervolgens dat geordende lijst wordt gesorteerd op de tweede eigenschap, enzovoort.  
  
   De eigendomsnamen waarnaar wordt verwezen in de ORDER BY-clausule moeten overeenkomen met een eigenschap in de selecte lijst of met een eigenschap die is gedefinieerd in de verzameling die is gespecificeerd in de FROM-clausule zonder dubbelzinnigheden.  
  
- `<sort_expression>`  
  
   Hiermee geeft u een of meer eigenschappen of expressies op waarop de queryresultatenset moet worden gesorteerd.  
  
- `<scalar_expression>`  
  
   Zie de [scalaire expressiesectie](sql-query-scalar-expressions.md) voor meer informatie.  
  
- `ASC | DESC`  
  
   Hiermee geeft u op dat de waarden in de opgegeven kolom in oplopende of dalende volgorde moeten worden gesorteerd. ASC sorteert van de laagste waarde naar de hoogste waarde. DESC sorteert van hoogste waarde naar laagste waarde. ASC is de standaardsorteervolgorde. Null-waarden worden behandeld als de laagst mogelijke waarden.  
  
## <a name="remarks"></a>Opmerkingen  
  
   De `ORDER BY` clausule schrijft voor dat het indexeringsbeleid een index bevat voor de velden die worden gesorteerd. De runtime azure cosmos DB-query ondersteunt sorteren op basis van een eigenschapsnaam en niet tegen berekende eigenschappen. Azure Cosmos DB `ORDER BY` ondersteunt meerdere eigenschappen. Als u een query met meerdere eigenschappen VAN ORDER BY wilt uitvoeren, moet u een [samengestelde index](index-policy.md#composite-indexes) definiëren op de velden die worden gesorteerd.

> [!Note]
> Als de eigenschappen die worden gesorteerd mogelijk niet zijn gedefinieerd voor sommige documenten en u deze wilt ophalen in een query ORDER BY, moet u dit pad expliciet in de index opnemen. Het standaardindexeringsbeleid staat niet toe dat de documenten worden opgehaald waarvan de eigenschap sortering niet is gedefinieerd. [Bekijk voorbeeldquery's op documenten met enkele ontbrekende velden](#documents-with-missing-fields).

## <a name="examples"></a>Voorbeelden

Hier vindt u bijvoorbeeld een query die gezinnen ophaalt in oplopende volgorde van de naam van de inwonervan de stad:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

U ziet deze uitvoer:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Met de volgende `id`query wordt familie opgehaald in volgorde van de datum waarop het artikel is gemaakt. Item `creationDate` is een getal dat de *tijdtijd*vertegenwoordigt , of verstreken tijd sinds 1 januari 1970 in seconden.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

U ziet deze uitvoer:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Bovendien u op meerdere eigenschappen bestellen. Voor een query die op meerdere eigenschappen wordt georderd, is een [samengestelde index](index-policy.md#composite-indexes)vereist. Laten we nu eens naar deze query kijken:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Met deze query `id` wordt het gezin opgehaald in oplopende volgorde van de plaatsnaam. Als meerdere items dezelfde plaatsnaam hebben, wordt `creationDate` de query op de in aflopende volgorde besteld.

## <a name="documents-with-missing-fields"></a>Documenten met ontbrekende velden

Query's `ORDER BY` met die worden uitgevoerd tegen containers met het standaard indexeringsbeleid, geven geen documenten terug wanneer de eigenschap sortering niet is gedefinieerd. Als u documenten wilt opnemen waarvan de eigenschap sortering niet is gedefinieerd, moet u deze eigenschap expliciet opnemen in het indexeringsbeleid.

Hier is bijvoorbeeld een container met een indexeringsbeleid dat geen `"/*"`paden bevat naast:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Als u een query `lastName` uitvoert `Order By` die in de clausule is `lastName` opgenomen, bevatten de resultaten alleen documenten waarop een eigenschap is gedefinieerd. We hebben geen expliciet opgenomen `lastName` pad gedefinieerd voor `lastName` documenten zonder een wordt niet weergegeven in de queryresultaten.

Hier is een query `lastName` die sorteert op twee documenten, waarvan er een niet is `lastName` gedefinieerd:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

De resultaten bevatten alleen het document `lastName`met een gedefinieerd document:

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Als we het indexeringsbeleid van de container bijwerken `lastName`om expliciet een pad voor op te nemen, nemen we documenten op met een niet-gedefinieerde sorteereigenschap in de queryresultaten. U moet expliciet het pad definiëren dat moet leiden naar deze scalaire waarde (en niet daarbuiten). U moet `?` het teken in uw paddefinitie gebruiken in het indexeringsbeleid om ervoor te zorgen dat u de eigenschap `lastName` expliciet indexeert en geen extra geneste paden daarbuiten.

Hier vindt u een voorbeeldindexeringsbeleid waarmee u documenten `lastName` met een niet-gedefinieerde functie laten weergeven in de queryresultaten:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Als u dezelfde query opnieuw uitvoert, `lastName` worden documenten die ontbreken als eerste weergegeven in de queryresultaten:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

U ziet deze uitvoer:

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

Als u de sorteervolgorde wijzigt `DESC` `lastName` in , worden documenten die ontbreken als laatste weergegeven in de queryresultaten:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

U ziet deze uitvoer:

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

## <a name="next-steps"></a>Volgende stappen

- [Slag](sql-query-getting-started.md)
- [Indexeringsbeleid in Azure Cosmos DB](index-policy.md)
- [COMPENSATIELIMIETclausule](sql-query-offset-limit.md)
