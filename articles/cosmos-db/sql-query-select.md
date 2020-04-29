---
title: SELECT-component in Azure Cosmos DB
description: Meer informatie over de SQL SELECT-component voor Azure Cosmos DB. Gebruik SQL als Azure Cosmos DB JSON-query taal.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 013ebdcdbac41825c10a1362f73ab4c94052400d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77469932"
---
# <a name="select-clause-in-azure-cosmos-db"></a>SELECT-component in Azure Cosmos DB

Elke query bestaat uit een SELECT-component en optionele [from](sql-query-from.md) -en [where](sql-query-where.md) -componenten, per ANSI SQL-standaards. Normaal gesp roken wordt de bron in de component FROM genummerd en de WHERE-component past een filter op de bron toe om een subset van JSON-items op te halen. De SELECT-component vervolgens projecteert de aangevraagde JSON-waarden in de selectie lijst.

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

  Eigenschappen of waarde die voor de resultatenset moet worden geselecteerd.  
  
- `'*'`  

  Geeft aan dat de waarde moet worden opgehaald zonder dat er wijzigingen worden aangebracht. Met name als de verwerkte waarde een object is, worden alle eigenschappen opgehaald.  
  
- `<object_property_list>`  
  
  Hiermee geeft u de lijst met eigenschappen op die moeten worden opgehaald. Elke geretourneerde waarde is een object met de opgegeven eigenschappen.  
  
- `VALUE`  

  Hiermee geeft u op dat de JSON-waarde moet worden opgehaald in plaats van het volledige JSON-object. Dit, in tegens telling tot `<property_list>` de geprojecteerde waarde in een object.  
 
- `DISTINCT`
  
  Hiermee geeft u op dat dubbele waarden van geprojecteerde eigenschappen moeten worden verwijderd.  

- `<scalar_expression>`  

  Expressie die de waarde vertegenwoordigt die moet worden berekend. Zie de sectie [scalaire expressies](sql-query-scalar-expressions.md) voor meer informatie.  

## <a name="remarks"></a>Opmerkingen

De `SELECT *` syntaxis is alleen geldig als from-component precies één alias heeft gedeclareerd. `SELECT *`biedt een identiteits projectie. Dit kan nuttig zijn als er geen projectie nodig is. SELECT * is alleen geldig als de component FROM is opgegeven en slechts één invoer bron heeft geïntroduceerd.  
  
Beide `SELECT <select_list>` en `SELECT *` zijn ' syntactische ' en kunnen ook worden aangegeven met BEhulp van eenvoudige SELECT-instructies, zoals hieronder wordt weer gegeven.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   is gelijk aan:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   is gelijk aan:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Voorbeelden

In het volgende voor beeld van `address` een `Families` Select `id` - `AndersenFamily`query wordt een resultaat van de overeenkomst geretourneerd:

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
U kunt eigenschappen openen met de operator voor de geciteerde eigenschap []. Zo is `SELECT c.grade` bijvoorbeeld het equivalent van `SELECT c["grade"]`. Deze syntaxis is handig als u een eigenschap wilt escapepen die spaties, speciale tekens bevat of dezelfde naam heeft als een SQL-tref woord of gereserveerd woord.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Geneste eigenschappen

In het volgende voor beeld worden twee geneste eigenschappen geprojecteerd, `f.address.state` en. `f.address.city`

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

Projectie biedt ook ondersteuning voor JSON-expressies, zoals wordt weer gegeven in het volgende voor beeld:

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

In het voor gaande voor beeld moet de SELECT-component een JSON-object maken en aangezien het voor beeld geen sleutel bevat, gebruikt de component de impliciete `$1`argument naam van de variabele. Met de volgende query worden twee impliciete argument `$1` variabelen `$2`geretourneerd: en.

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
## <a name="reserved-keywords-and-special-characters"></a>Gereserveerde tref woorden en speciale tekens

Als uw gegevens eigenschappen bevatten met dezelfde namen als gereserveerde tref woorden, zoals ' order ' of ' groep ', hebben de query's voor deze documenten een syntaxis fout. U moet expliciet de eigenschap in `[]` het teken toevoegen om de query uit te voeren.

Hier ziet u bijvoorbeeld een document met een eigenschap met de `order` naam en een `price($)` eigenschap die speciale tekens bevat:

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

Als u een query uitvoert die de `order` eigenschap of `price($)` eigenschap bevat, wordt er een syntaxis fout weer gegeven.

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

U moet dezelfde query's opnieuw schrijven zoals hieronder wordt beschreven:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag](sql-query-getting-started.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE-component](sql-query-where.md)
