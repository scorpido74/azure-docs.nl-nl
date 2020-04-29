---
title: ORDER BY-component in Azure Cosmos DB
description: Meer informatie over de component SQL ORDER BY voor Azure Cosmos DB. Gebruik SQL als Azure Cosmos DB JSON-query taal.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: tisande
ms.openlocfilehash: 70702ee4a77e8b3c46de4354f3394bca4080d837
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641399"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>ORDER BY-component in Azure Cosmos DB

De optionele `ORDER BY` component specificeert de sorteer volgorde voor de resultaten die door de query worden geretourneerd.

## <a name="syntax"></a>Syntaxis
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumenten
  
- `<sort_specification>`  
  
   Hiermee geeft u een eigenschap of expressie op waarop de resultatenset van de query moet worden gesorteerd. Een sorteer kolom kan worden opgegeven als een naam of eigenschaps alias.  
  
   Er kunnen meerdere eigenschappen worden opgegeven. Eigenschaps namen moeten uniek zijn. De volg orde van de sorteer eigenschappen in `ORDER BY` de component definieert de organisatie van de gesorteerde resultatenset. Dat wil zeggen dat de resultatenset wordt gesorteerd op de eerste eigenschap en dat de geordende lijst wordt gesorteerd op de tweede eigenschap, enzovoort.  
  
   De eigenschaps namen waarnaar in de `ORDER BY` component wordt verwezen, moeten overeenkomen met een eigenschap in de SELECT-lijst of een eigenschap die is gedefinieerd in `FROM` de verzameling die in de component is opgegeven, zonder ambiguïteit.  
  
- `<sort_expression>`  
  
   Hiermee geeft u een of meer eigenschappen of expressies op waarop de resultatenset van de query moet worden gesorteerd.  
  
- `<scalar_expression>`  
  
   Zie de sectie [scalaire expressies](sql-query-scalar-expressions.md) voor meer informatie.  
  
- `ASC | DESC`  
  
   Geeft aan dat de waarden in de opgegeven kolom in oplopende of aflopende volg orde moeten worden gesorteerd. `ASC`Hiermee wordt van de laagste waarde naar de hoogste waarde gesorteerd. `DESC`Hiermee wordt van de hoogste waarde naar de laagste waarde gesorteerd. `ASC`is de standaard sorteer volgorde. Null-waarden worden beschouwd als de laagst mogelijke waarden.  
  
## <a name="remarks"></a>Opmerkingen  
  
   De `ORDER BY` component vereist dat het indexerings beleid een index bevat voor de gesorteerde velden. De runtime van Azure Cosmos DB query's ondersteunt sorteren op basis van een eigenschaps naam en niet op basis van berekende eigenschappen. Azure Cosmos DB ondersteunt meerdere `ORDER BY` eigenschappen. Als u een query met meerdere ORDER BY-eigenschappen wilt uitvoeren, moet u een [samengestelde index](index-policy.md#composite-indexes) definiëren voor de gesorteerde velden.

> [!Note]
> Als de eigenschappen die worden gesorteerd mogelijk niet zijn gedefinieerd voor sommige documenten en u deze wilt ophalen in een ORDER BY-query, moet u dit pad expliciet in de index toevoegen. Het standaard indexerings beleid staat niet toe dat de documenten worden opgehaald waarvoor de sorteer eigenschap niet is gedefinieerd. [Bekijk voorbeeld query's op documenten met een aantal ontbrekende velden](#documents-with-missing-fields).

## <a name="examples"></a>Voorbeelden

Dit is bijvoorbeeld een query waarmee families worden opgehaald in oplopende volg orde van de naam van de residente stad:

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

Met de volgende query worden `id`familie s opgehaald op volg orde van de aanmaak datum van het item. Item `creationDate` is een getal dat de *epoche-tijd*of verstreken tijd sinds 1 januari 1970 in seconden aangeeft.

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

Daarnaast kunt u op meerdere eigenschappen sorteren. Een query die door meerdere eigenschappen moet worden gesorteerd, vereist een [samengestelde index](index-policy.md#composite-indexes). Laten we nu eens naar deze query kijken:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Met deze query wordt de `id` familie opgehaald in oplopende volg orde van de plaatsnaam. Als meerdere items dezelfde plaatsnamen hebben, wordt de query gesorteerd op de `creationDate` in aflopende volg orde.

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

Als u een query uitvoert die in `lastName` de `Order By` -component is opgenomen, bevatten de resultaten alleen documenten waarvoor een `lastName` eigenschap is gedefinieerd. Er is geen expliciet opgenomen pad gedefinieerd `lastName` , zodat documenten zonder een `lastName` niet worden weer gegeven in de query resultaten.

Hier volgt een query die `lastName` op twee documenten wordt gesorteerd, waarvan er een is waarvoor geen `lastName` gedefinieerde:

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

Als we het indexerings beleid van de container bijwerken om expliciet een pad `lastName`op te nemen, zullen we documenten met een niet-gedefinieerde Sorteer eigenschap in de query resultaten bevatten. U moet het pad expliciet definiëren om te leiden naar deze scalaire waarde (en niet verder). Gebruik het `?` teken in de definitie van het pad in het indexerings beleid om ervoor te zorgen dat u de `lastName` eigenschap expliciet indexeert en geen extra geneste paden overschrijdt. Als uw `Order By` query een [samengestelde index](index-policy.md#composite-indexes)gebruikt, bevatten de resultaten altijd documenten met een niet-gedefinieerde Sorteer eigenschap in de query resultaten.

Hier volgt een voor beeld van een indexerings beleid waarmee u documenten met een niet- `lastName` gedefinieerd resultaat kunt weer geven in de query resultaten:

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

Als u dezelfde query opnieuw uitvoert, worden de ontbrekende `lastName` documenten eerst weer gegeven in de query resultaten:

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

Als u de sorteer volgorde wijzigt in `DESC`, worden de ontbrekende `lastName` documenten als laatste weer gegeven in de query resultaten:

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

- [Aan de slag](sql-query-getting-started.md)
- [Indexeringsbeleid in Azure Cosmos DB](index-policy.md)
- [Component OFFSET limiet](sql-query-offset-limit.md)
