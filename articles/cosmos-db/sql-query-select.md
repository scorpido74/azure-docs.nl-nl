---
title: SELECT-component in Azure Cosmos DB
description: Meer informatie over de SQL SELECT-component voor Azure Cosmos DB. Gebruik SQL als Azure Cosmos DB JSON-query taal.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: b90fc6f1f50ec2ea75619188cca36f78061f28df
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326790"
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

  Hiermee geeft u op dat de JSON-waarde moet worden opgehaald in plaats van het volledige JSON-object. Dit betekent dat, in tegens telling tot `<property_list>`, de geschatte waarde in een object niet verloopt.  
 
- `DISTINCT`
  
  Hiermee geeft u op dat dubbele waarden van geprojecteerde eigenschappen moeten worden verwijderd.  

- `<scalar_expression>`  

  Expressie die de waarde vertegenwoordigt die moet worden berekend. Zie de sectie [scalaire expressies](sql-query-scalar-expressions.md) voor meer informatie.  

## <a name="remarks"></a>Opmerkingen

De syntaxis van de `SELECT *` is alleen geldig als FROM-component precies één alias heeft gedeclareerd. `SELECT *` biedt een identiteits projectie. Dit kan nuttig zijn als er geen projectie nodig is. SELECT * is alleen geldig als de component FROM is opgegeven en slechts één invoer bron heeft geïntroduceerd.  
  
Zowel `SELECT <select_list>` als `SELECT *` zijn ' syntactische suiker ' en kunnen ook worden aangegeven met behulp van eenvoudige SELECT-instructies, zoals hieronder wordt weer gegeven.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   is gelijk aan:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   is gelijk aan:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Voorbeelden

In het volgende voor beeld van een SELECT-query wordt `address` geretourneerd van `Families` waarvan de `id` overeenkomt met `AndersenFamily`:

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

In het volgende voor beeld worden twee geneste eigenschappen, `f.address.state` en `f.address.city`, geprojecteerd.

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

In het voor gaande voor beeld moet de component SELECT een JSON-object maken en aangezien het voor beeld geen sleutel bevat, gebruikt de component de variabele naam van het impliciete argument `$1`. Met de volgende query worden twee impliciete argument variabelen geretourneerd: `$1` en `$2`.

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