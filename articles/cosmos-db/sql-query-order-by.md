---
title: ORDER BY-component in Azure Cosmos DB
description: Meer informatie over de component SQL ORDER BY voor Azure Cosmos DB. Gebruik SQL als Azure Cosmos DB JSON-query taal.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 14f61d14b59dca4bcf2e0f4b93e918f101a61833
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326836"
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
  
   Hiermee geeft u een eigenschap of expressie op waarop de resultatenset van de query moet worden gesorteerd. Een sorteer kolom kan worden opgegeven als een naam of eigenschaps alias.  
  
   Er kunnen meerdere eigenschappen worden opgegeven. Eigenschaps namen moeten uniek zijn. De volg orde van de sorteer eigenschappen in de ORDER BY-component definieert de organisatie van de gesorteerde resultatenset. Dat wil zeggen dat de resultatenset wordt gesorteerd op de eerste eigenschap en dat de geordende lijst wordt gesorteerd op de tweede eigenschap, enzovoort.  
  
   De namen van de eigenschappen waarnaar wordt verwezen in de component ORDER BY moeten overeenkomen met een eigenschap in de SELECT-lijst of een eigenschap die is gedefinieerd in de verzameling die is opgegeven in de component FROM, zonder dubbel zinnigheid.  
  
- `<sort_expression>`  
  
   Hiermee geeft u een of meer eigenschappen of expressies op waarop de resultatenset van de query moet worden gesorteerd.  
  
- `<scalar_expression>`  
  
   Zie de sectie [scalaire expressies](sql-query-scalar-expressions.md) voor meer informatie.  
  
- `ASC | DESC`  
  
   Geeft aan dat de waarden in de opgegeven kolom in oplopende of aflopende volg orde moeten worden gesorteerd. ASC sorteert van de laagste waarde naar de hoogste waarde. DESC sorteert van de hoogste waarde naar de laagste waarde. ASC is de standaard sorteer volgorde. Null-waarden worden beschouwd als de laagst mogelijke waarden.  
  
## <a name="remarks"></a>Opmerkingen  
  
   De ORDER BY-component vereist dat het indexerings beleid een index bevat voor de gesorteerde velden. De runtime van Azure Cosmos DB query's ondersteunt sorteren op basis van een eigenschaps naam en niet op basis van berekende eigenschappen. Azure Cosmos DB ondersteunt meerdere ORDER BY-eigenschappen. Als u een query met meerdere ORDER BY-eigenschappen wilt uitvoeren, moet u een [samengestelde index](index-policy.md#composite-indexes) definiëren voor de gesorteerde velden.

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

Met de volgende query worden familie `id`s opgehaald om de aanmaak datum van het item op te halen. Item `creationDate` is een getal dat de *epoche-tijd*of verstreken tijd sinds 1 januari 1970 in seconden aangeeft.

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

Met deze query haalt u de familie `id` op in oplopende volg orde van de naam van de stad. Als meerdere items dezelfde plaats naam hebben, wordt de query gesorteerd op de `creationDate` in aflopende volg orde.

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag](sql-query-getting-started.md)
- [SELECT-component](sql-query-select.md)
- [Component OFFSET limiet](sql-query-offset-limit.md)
