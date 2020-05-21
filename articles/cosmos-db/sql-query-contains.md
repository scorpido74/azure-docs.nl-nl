---
title: BEVAT in Azure Cosmos DB query taal
description: Meer informatie over hoe de CONTAINs-functie voor SQL-systeem in Azure Cosmos DB een Booleaanse waarde retourneert die aangeeft of de eerste teken reeks expressie de tweede bevat
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0de34e6e0e238887b8f75ae2397e9e650eaac340
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83711700"
---
# <a name="contains-azure-cosmos-db"></a>BEVAT (Azure Cosmos DB)

 Retourneert een Booleaanse waarde die aangeeft of de eerste tekenreeksexpressie de tweede bevat.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr1*  
   Is de teken reeks expressie die moet worden doorzocht.  
  
*str_expr2*  
   Is de teken reeks expressie die u wilt zoeken.  

*bool_expr* Optionele waarde voor het negeren van case. Als deze waarde is ingesteld op True, wordt een niet-hoofdletter gevoelige zoek actie uitgevoerd. Indien niet opgegeven, is deze waarde false.
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een Boole-expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt gecontroleerd of "ABC" bevat "AB" en "ABC" bevat "d".  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2
```  
  
 Dit is de resultatenset.  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
