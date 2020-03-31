---
title: StringToArray in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie StringToArray in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 18acbd94fa3d717fc20b9e1020b9bf7c6db7744d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302913"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
 Retourneert expressie die is vertaald naar een array. Als expressie niet kan worden vertaald, wordt niet gedefinieerd geretourneerd.  
  
## <a name="syntax"></a>Syntaxis
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een tekenreeksexpressie die moet worden ontleed als een JSON Array-expressie. 
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een matrixexpressie of niet gedefinieerd. 
  
## <a name="remarks"></a>Opmerkingen
  Geneste tekenreekswaarden moeten worden geschreven met dubbele aanhalingstekens om geldige JSON te zijn. Zie [json.org](https://json.org/)
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende `StringToArray` voorbeeld ziet u hoe zich zich gedraagt voor verschillende typen. 
  
 Hieronder volgen voorbeelden met geldige invoer.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Hier is het resultaat ingesteld.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Het volgende is een voorbeeld van ongeldige invoer. 
   
 Enkele aanhalingstekens binnen de array zijn niet geldig JSON.
Hoewel ze geldig zijn binnen een query, zullen ze niet ontlopen naar geldige arrays. Tekenreeksen binnen de arraytekenreeks moeten\\\\worden ontsnapt aan "[ " "]" of het omringende citaat moet alleenstaand zijn "[""]'.

```sql
SELECT
    StringToArray("['5','6','7']")
```

Hier is het resultaat ingesteld.

```json
[{}]
```

Hieronder volgen voorbeelden van ongeldige invoer.
   
 De doorgegeven expressie wordt ontleed als een JSON-array; het volgende niet evalueren om array te typen en dus ongedefinieerd terug te keren.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
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
