---
title: StringToObject in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie StringToObject in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c3e61d1efe20910d84ef4ff583d74982b3ea9f3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296378"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
 Retourneert expressie die naar een object is vertaald. Als expressie niet kan worden vertaald, wordt niet gedefinieerd geretourneerd.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een tekenreeksexpressie die moet worden ontleed als een JSON-objectexpressie. Houd er rekening mee dat geneste tekenreekswaarden moeten worden geschreven met dubbele aanhalingstekens om geldig te zijn. Zie [json.org](https://json.org/)  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een objectexpressie of niet gedefinieerd.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende `StringToObject` voorbeeld ziet u hoe zich zich gedraagt voor verschillende typen. 
  
 Hieronder volgen voorbeelden met geldige invoer.

```sql
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Hier is het resultaat ingesteld.

```json
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 Hieronder volgen voorbeelden met ongeldige invoer.
Hoewel ze geldig zijn binnen een query, zullen ze niet ontlopen tot geldige objecten. Tekenreeksen binnen de tekenreeks van het\\object\\moeten\\worden\\ontsnapt "{ "a ": "str "}" of het omringende citaat moet single zijn "{"a": "str"}".

Enkele aanhalingstekens rond eigenschapnamen zijn niet geldig JSON.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Hier is het resultaat ingesteld.

```json
[{}]
```  

Eigendomsnamen zonder omliggende offertes zijn niet geldig JSON.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Hier is het resultaat ingesteld.

```json
[{}]
``` 

Hieronder volgen voorbeelden met ongeldige invoer.

 De doorgegeven expressie wordt ontleed als een JSON-object; deze ingangen evalueren niet om object te typen en keren dus ongedefinieerd terug.

```sql
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 Hier is het resultaat ingesteld.

```json
[{}]
```

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Tekenreeksfuncties Azure Cosmos DB](sql-query-string-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
