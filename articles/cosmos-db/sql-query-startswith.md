---
title: STARTSWITH in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie STARTSWITH in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9ed49c067946186f8b79f67bad0a460113eacb73
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78295705"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
 Retourneert een Booleaanse waarde die aangeeft of de eerste expressie tekenreeks begint met de tweede.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
STARTSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr1*  
   Is een teken reeks expressie.
  
*str_expr2*  
   Is een teken reeks expressie die moet worden vergeleken met het begin van *str_expr1*.

## <a name="return-types"></a>Retour typen
  
  Retourneert een Booleaanse expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  Het volgende voorbeeld wordt als de tekenreeks "abc" met "b begint" en "a".  
  
```sql
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Hier volgt de resultatenset.  
  
```json
[{"s1": false, "s2": true}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
