---
title: StringEquals in Azure Cosmos DB-query taal
description: Meer informatie over hoe de StringEquals-functie van SQL System in Azure Cosmos DB een Booleaanse waarde retourneert die aangeeft of de eerste teken reeks expressie overeenkomt met de seconde
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 57d124421082e1c38fab4d982687a8e6c970505e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83853797"
---
# <a name="stringequals-azure-cosmos-db"></a>STRINGEQUALS (Azure Cosmos DB)

 Retourneert een Booleaanse waarde die aangeeft of de eerste teken reeks expressie overeenkomt met de seconde.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
STRINGEQUALS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr1*  
   Is de eerste teken reeks expressie die u wilt vergelijken.  
  
*str_expr2*  
   Is de tweede teken reeks expressie die u wilt vergelijken.  

*bool_expr* Optionele waarde voor het negeren van case. Als deze eigenschap is ingesteld op True, wordt in StringEquals een hoofdletter gevoelige zoek opdracht uitgevoerd. Indien niet opgegeven, is deze waarde false.
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een Boole-expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt gecontroleerd of "ABC" overeenkomt met "ABC" en als "ABC" overeenkomt met "ABC".  
  
```sql
SELECT STRINGEQUALS("abc", "abc", false) AS c1, STRINGEQUALS("abc", "ABC", false) AS c2,  STRINGEQUALS("abc", "ABC", true) AS c3
```  
  
 Dit is de resultatenset.  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
