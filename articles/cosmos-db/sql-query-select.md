---
title: SELECT-component in Azure Cosmos DB
description: Meer informatie over de SQL SELECT-component voor Azure Cosmos DB. Gebruik SQL als Azure Cosmos DB JSON-query taal.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: d34b1c39d9789409dc365cd4cf07fdc3d5a780fd
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003528"
---
# <a name="select-clause"></a>SELECT-component

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

  De eigenschappen of de waarde die moet worden geselecteerd voor de resultatenset.  
  
- `'*'`  

  Hiermee geeft u op dat de waarde zonder wijzigingen moet worden opgehaald. Specifiek als de verwerkte waarde een object is, worden alle eigenschappen worden opgehaald.  
  
- `<object_property_list>`  
  
  Hiermee geeft u de lijst met eigenschappen die moeten worden opgehaald. Elke geretourneerde waarde is een object met de eigenschappen die zijn opgegeven.  
  
- `VALUE`  

  Hiermee geeft u op dat de JSON-waarde moet worden opgehaald in plaats van de volledige JSON-object. Dit, in tegenstelling tot `<property_list>` loopt niet de verwachte waarde in een object.  
 
- `DISTINCT`
  
  Hiermee geeft u op dat dubbele waarden van geprojecteerde eigenschappen moeten worden verwijderd.  

- `<scalar_expression>`  

  Expressie voor de waarde die moet worden berekend. Zie [scalaire expressies](sql-query-scalar-expressions.md) sectie voor meer informatie.  

## <a name="remarks"></a>Opmerkingen

De `SELECT *` syntaxis is alleen geldig als FROM-component precies één alias is gedeclareerd. `SELECT *` biedt een identity-projectie handig is als er geen projectie is vereist. Selecteer * is alleen geldig als FROM-component is opgegeven en slechts één invoer bron geïntroduceerd.  
  
Beide `SELECT <select_list>` en `SELECT *` 'syntactische suiker' zijn en kan ook worden uitgedrukt met behulp van eenvoudige SELECT-instructies zoals hieronder wordt weergegeven.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   is gelijk aan:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   is gelijk aan:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Voorbeelden

In het volgende voor beeld van `address` een select- `AndersenFamily`query wordt een resultaat van `Families` de overeenkomstgeretourneerd:`id`

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

De resultaten zijn:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="quoted-property-accessor"></a>Accessor van de geciteerde eigenschap
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

De resultaten zijn:

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

De resultaten zijn:

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

De resultaten zijn:

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

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag](sql-query-getting-started.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE-component](sql-query-where.md)