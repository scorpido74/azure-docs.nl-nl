---
title: STARTSWITH in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie STARTSWITH in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e915ea4be058f805e938ec8526ca0ee40d556271
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715270"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)

 Retourneert een Booleaanse waarde die aangeeft of de eerste teken reeks expressie begint met de tweede.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
STARTSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr1*  
   Is een teken reeks expressie.
  
*str_expr2*  
   Is een teken reeks expressie die moet worden vergeleken met het begin van *str_expr1*.

*bool_expr* Optionele waarde voor het negeren van case. Als deze eigenschap is ingesteld op True, wordt in STARTSWITH een hoofdletter gevoelige zoek opdracht uitgevoerd. Indien niet opgegeven, is deze waarde false.

## <a name="return-types"></a>Retour typen
  
  Retourneert een Boole-expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt gecontroleerd of de teken reeks "ABC" begint met "b" en "a".  
  
```sql
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Dit is de resultatenset.  
  
```json
[{"s1": false, "s2": true}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
