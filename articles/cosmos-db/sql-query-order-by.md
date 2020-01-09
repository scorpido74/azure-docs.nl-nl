---
title: ORDER BY-component in Azure Cosmos DB
description: Meer informatie over de component SQL ORDER BY voor Azure Cosmos DB. Gebruik SQL als Azure Cosmos DB JSON-query taal.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 5cae2bdd7d1f2f26e626c81ea95d2cee3cc8ae13
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444796"
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
  
   Zie de [scalaire expressies](sql-query-scalar-expressions.md) sectie voor meer informatie.  
  
- `ASC | DESC`  
  
   Hiermee geeft u op dat de waarden in de opgegeven kolom moeten worden gesorteerd in oplopende of aflopende volgorde. ASC sorteren van de laagste waarde naar hoogste waarde. DESC sorteren van hoogste waarde naar laagste waarde. ASC is de standaardsorteervolgorde. Null-waarden worden behandeld als de laagste mogelijke waarden.  
  
## <a name="remarks"></a>Opmerkingen  
  
   De ORDER BY-component vereist dat het indexerings beleid een index bevat voor de gesorteerde velden. De runtime van Azure Cosmos DB query's ondersteunt sorteren op basis van een eigenschaps naam en niet op basis van berekende eigenschappen. Azure Cosmos DB ondersteunt meerdere ORDER BY-eigenschappen. Als u een query met meerdere ORDER BY-eigenschappen wilt uitvoeren, moet u een [samengestelde index](index-policy.md#composite-indexes) definiëren voor de gesorteerde velden.
   
> [!Note] 
> Als u de .NET SDK 3.4.0 of hoger gebruikt, moet u expliciet een index voor deze eigenschappen maken als de gesorteerde eigenschappen voor sommige documenten niet kunnen worden gedefinieerd. Het standaard indexerings beleid staat niet toe dat de documenten worden opgehaald waarvoor de sorteer eigenschap niet is gedefinieerd.

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

Met de volgende query worden familie `id`s opgehaald op volg orde van de aanmaak datum van het item. Item `creationDate` is een getal dat de *epoche-tijd*of verstreken tijd sinds 1 januari 1970 in seconden aangeeft.

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

Met deze query haalt u de familie `id` op in oplopende volg orde van de plaatsnaam. Als meerdere items dezelfde plaats naam hebben, wordt de query gesorteerd op de `creationDate` in aflopende volg orde.

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag](sql-query-getting-started.md)
- [SELECT-component](sql-query-select.md)
- [Component OFFSET limiet](sql-query-offset-limit.md)
