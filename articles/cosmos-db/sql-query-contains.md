---
title: CONTAINS in Azure Cosmos DB-querytaal
description: Meer informatie over hoe de functie SQL-systeem bevat in Azure Cosmos DB een Booleaanse retourneert die aangeeft of de eerste tekenreeksexpressie de tweede
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0c25b63fb6a7bf42bd2ec5b9503cac2cce7583f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302590"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINS (Azure Cosmos DB)
 Retourneert een Booleaanse waarde die aangeeft of de eerste tekenreeksexpressie de tweede bevat.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr1*  
   Moet de tekenreeksexpressie worden doorzocht.  
  
*str_expr2*  
   Is de tekenreeksexpressie die u moet vinden.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een Booleaanse expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld wordt gecontroleerd of "abc" "ab" bevat en of "abc" "d" bevat.  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Tekenreeksfuncties Azure Cosmos DB](sql-query-string-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
