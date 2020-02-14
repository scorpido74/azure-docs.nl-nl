---
title: ORDER BY-component in Azure Cosmos DB
description: Meer informatie over de component SQL ORDER BY voor Azure Cosmos DB. Gebruik SQL als Azure Cosmos DB JSON-query taal.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: tisande
ms.openlocfilehash: b88184be39a41ec42f8fb304a7511073f645f1cb
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188734"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>ORDER BY-component in Azure Cosmos DB

De optionele ORDER BY-component geeft de sorteer volgorde op voor de resultaten die door de query worden geretourneerd.

## <a name="syntax"></a>Syntaxis
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumenten
  
- `<sort_specification>`  
  
   Hiermee geeft u een eigenschap of een expressie waarop u wilt sorteren van de queryresultaatset. Een sorteer kolom kan worden opgegeven als een naam of eigenschaps alias.  
  
   Er kunnen meerdere eigenschappen worden opgegeven. Eigenschaps namen moeten uniek zijn. De volg orde van de sorteer eigenschappen in de ORDER BY-component definieert de organisatie van de gesorteerde resultatenset. Dat wil zeggen, de resultatenset is gesorteerd op de eerste eigenschap en vervolgens die geordende lijst is gesorteerd op de tweede eigenschap, enzovoort.  
  
   De namen van de eigenschappen waarnaar wordt verwezen in de component ORDER BY moeten overeenkomen met een eigenschap in de SELECT-lijst of een eigenschap die is gedefinieerd in de verzameling die is opgegeven in de component FROM, zonder dubbel zinnigheid.  
  
- `<sort_expression>`  
  
   Hiermee geeft u een of meer eigenschappen of expressies op waarop de resultatenset van de query moet worden gesorteerd.  
  
- `<scalar_expression>`  
  
   Zie de sectie [scalaire expressies](sql-query-scalar-expressions.md) voor meer informatie.  
  
- `ASC | DESC`  
  
   Hiermee geeft u op dat de waarden in de opgegeven kolom moeten worden gesorteerd in oplopende of aflopende volgorde. ASC sorteren van de laagste waarde naar hoogste waarde. DESC sorteren van hoogste waarde naar laagste waarde. ASC is de standaardsorteervolgorde. Null-waarden worden behandeld als de laagste mogelijke waarden.  
  
## <a name="remarks"></a>Opmerkingen  
  
   De `ORDER BY`-component vereist dat het indexerings beleid een index bevat voor de gesorteerde velden. De runtime van Azure Cosmos DB query's ondersteunt sorteren op basis van een eigenschaps naam en niet op basis van berekende eigenschappen. Azure Cosmos DB ondersteunt meerdere `ORDER BY`-eigenschappen. Als u een query met meerdere ORDER BY-eigenschappen wilt uitvoeren, moet u een [samengestelde index](index-policy.md#composite-indexes) definiëren voor de gesorteerde velden.

> [!Note]
> Als de eigenschappen die worden gesorteerd mogelijk niet zijn gedefinieerd voor sommige documenten en u deze wilt ophalen in een ORDER BY-query, moet u dit pad expliciet in de index toevoegen. Het standaard indexerings beleid staat niet toe dat de documenten worden opgehaald waarvoor de sorteer eigenschap niet is gedefinieerd. [Bekijk voorbeeld query's op documenten met een aantal ontbrekende velden](#documents-with-missing-fields).

## <a name="examples"></a>Voorbeelden

Dit is bijvoorbeeld een query waarmee families worden opgehaald in oplopende volg orde van de naam van de residente stad:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

De resultaten zijn:

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

Met de volgende query worden familie `id`s opgehaald op volg orde van de aanmaak datum van het item. Item `creationDate` is een getal dat de *epoche-tijd*of verstreken tijd sinds 1 januari 1970 in seconden aangeeft.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

De resultaten zijn:

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

Daarnaast kunt u op meerdere eigenschappen sorteren. Een query die door meerdere eigenschappen moet worden gesorteerd, vereist een [samengestelde index](index-policy.md#composite-indexes). Bekijk de volgende query:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Met deze query haalt u de familie `id` op in oplopende volg orde van de plaatsnaam. Als meerdere items dezelfde plaats naam hebben, wordt de query gesorteerd op de `creationDate` in aflopende volg orde.

## <a name="documents-with-missing-fields"></a>Documenten met ontbrekende velden

Query's met `ORDER BY` die worden uitgevoerd op containers met het standaard indexerings beleid, retour neren geen documenten waarin de sorteer eigenschap niet is gedefinieerd. Als u documenten wilt toevoegen waarin de sorteer eigenschap niet is gedefinieerd, moet u deze eigenschap expliciet in het indexerings beleid toevoegen.

Dit is bijvoorbeeld een container met een indexerings beleid dat geen expliciete paden bevat naast `"/*"`:

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

Als u een query uitvoert die `lastName` bevat in de `Order By`-component, bevatten de resultaten alleen documenten waarvoor een `lastName` eigenschap is gedefinieerd. Er is geen expliciet opgenomen pad gedefinieerd voor `lastName`, zodat documenten zonder een `lastName` niet worden weer gegeven in de query resultaten.

Hier volgt een query die door `lastName` op twee documenten wordt gesorteerd, waarvan er een `lastName` is gedefinieerd:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

De resultaten omvatten alleen het document met een gedefinieerde `lastName`:

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Als we het indexerings beleid van de container bijwerken om expliciet een pad op te nemen voor `lastName`, zullen we documenten met een niet-gedefinieerde Sorteer eigenschap in de query resultaten bevatten. U moet het pad expliciet definiëren om te leiden naar deze scalaire waarde (en niet verder). Gebruik het `?` teken in uw pad definitie in het indexerings beleid om ervoor te zorgen dat u de eigenschap `lastName` expliciet indexeert en geen extra geneste paden overschrijdt.

Hier volgt een voor beeld van een indexerings beleid waarmee u documenten met een niet-gedefinieerde `lastName` in de query resultaten kunt weer geven:

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

Als u dezelfde query opnieuw uitvoert, worden documenten die ontbreken `lastName` eerst weer gegeven in de query resultaten:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

De resultaten zijn:

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

Als u de sorteer volgorde wijzigt in `DESC`, worden documenten die ontbreken `lastName` als laatste weer gegeven in de query resultaten:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

De resultaten zijn:

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

- [Aan de slag](sql-query-getting-started.md)
- [Indexeringsbeleid in Azure Cosmos DB](index-policy.md)
- [Component OFFSET limiet](sql-query-offset-limit.md)
