---
title: SELECT-component in Azure Cosmos DB
description: Meer informatie over SQL SELECT-component voor Azure Cosmos DB. Sql gebruiken als json-querytaal van Azure Cosmos DB.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 013ebdcdbac41825c10a1362f73ab4c94052400d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469932"
---
# <a name="select-clause-in-azure-cosmos-db"></a>SELECT-component in Azure Cosmos DB

Elke query bestaat uit een SELECT-clausule en optionele [FROM-](sql-query-from.md) en [WHERE-clausules,](sql-query-where.md) per ANSI SQL-standaarden. Doorgaans wordt de bron in de FROM-component opgesomd en wordt de WHERE-component een filter op de bron toepast om een subset van JSON-items op te halen. De SELECT-component projecteert vervolgens de gevraagde JSON-waarden in de selecte lijst.

## <a name="syntax"></a>Syntaxis

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | [DISTINCT] <object_property_list>   
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
## <a name="arguments"></a>Argumenten
  
- `<select_specification>`  

  Eigenschappen of waarde die moeten worden geselecteerd voor de resultaatset.  
  
- `'*'`  

  Hiermee geeft u op dat de waarde moet worden opgehaald zonder wijzigingen aan te brengen. Specifiek als de verwerkte waarde een object is, worden alle eigenschappen opgehaald.  
  
- `<object_property_list>`  
  
  Hiermee geeft u de lijst op met eigenschappen die moeten worden opgehaald. Elke geretourneerde waarde is een object met de opgegeven eigenschappen.  
  
- `VALUE`  

  Hiermee geeft u op dat de JSON-waarde moet worden opgehaald in plaats van het volledige JSON-object. Dit, `<property_list>` in tegenstelling tot niet wrap de geprojecteerde waarde in een object.  
 
- `DISTINCT`
  
  Hiermee geeft u op dat duplicaten van geprojecteerde eigenschappen moeten worden verwijderd.  

- `<scalar_expression>`  

  Expressie die de te berekenen waarde vertegenwoordigt. Zie [scalaire expressies](sql-query-scalar-expressions.md) sectie voor meer informatie.  

## <a name="remarks"></a>Opmerkingen

De `SELECT *` syntaxis is alleen geldig als DE-component precies één alias heeft gedeclareerd. `SELECT *`geeft een identiteitsprojectie, die nuttig kan zijn als er geen projectie nodig is. SELECT * is alleen geldig als DE-clausule is opgegeven en slechts één invoerbron wordt geïntroduceerd.  
  
Beide `SELECT <select_list>` `SELECT *` en zijn "syntactische suiker" en kan alternatief worden uitgedrukt met behulp van eenvoudige SELECT verklaringen zoals hieronder weergegeven.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   is gelijk aan:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   is gelijk aan:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Voorbeelden

Het volgende voorbeeld `address` van `Families` `id` SELECT-query retourneert van de volgende overeenkomsten: `AndersenFamily`

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

U ziet deze uitvoer:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="quoted-property-accessor"></a>Accessor van eigenschap tussen aanhalingstekens
U hebt toegang tot eigenschappen via de vermelde vastgoedbeheerder []. Zo is `SELECT c.grade` bijvoorbeeld het equivalent van `SELECT c["grade"]`. Deze syntaxis is handig om te ontsnappen aan een eigenschap die spaties, speciale tekens of dezelfde naam heeft als een SQL-trefwoord of gereserveerd woord.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Geneste eigenschappen

In het volgende voorbeeld worden `f.address.state` `f.address.city`twee geneste eigenschappen en .

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

U ziet deze uitvoer:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```
### <a name="json-expressions"></a>JSON-expressies

Projectie ondersteunt ook JSON-expressies, zoals in het volgende voorbeeld wordt weergegeven:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

U ziet deze uitvoer:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

In het voorgaande voorbeeld moet de SELECT-component een JSON-object maken en aangezien de steekproef `$1`geen sleutel bevat, gebruikt de component de impliciete argumentvariabelenaam . Met de volgende query worden `$1` twee `$2`impliciete argumentvariabelen geretourneerd: en .

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

U ziet deze uitvoer:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```
## <a name="reserved-keywords-and-special-characters"></a>Gereserveerde trefwoorden en speciale tekens

Als uw gegevens eigenschappen bevatten met dezelfde namen als gereserveerde zoekwoorden zoals 'volgorde' of 'Groeperen', leiden de query's tegen deze documenten tot syntaxisfouten. U moet de eigenschap `[]` expliciet in teken opnemen om de query met succes uit te voeren.

Hier vindt u bijvoorbeeld een document `order` met een `price($)` eigenschap met de naam en een eigenschap met speciale tekens:

```json
{
  "id": "AndersenFamily",
  "order": [
     {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
     }
  ],
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Als u een query uitvoert `order` die `price($)` de eigenschap of eigenschap bevat, ontvangt u een syntaxisfout.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```
```sql
SELECT * FROM c where c.order.price($) > 50
```
Het resultaat is:

`
Syntax error, incorrect syntax near 'order'
`

U moet dezelfde query's opnieuw schrijven als hieronder:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="next-steps"></a>Volgende stappen

- [Slag](sql-query-getting-started.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE-clausule](sql-query-where.md)
