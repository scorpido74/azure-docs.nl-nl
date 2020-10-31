---
title: REPLICEREN in Azure Cosmos DB query taal
description: Meer informatie over de functie voor het REPLICEREN van SQL-functies in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8b1fc1cb28b62a388df53238df58420bd3317a8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082858"
---
# <a name="replicate-azure-cosmos-db"></a>REPLICEREN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Herhaalt een tekenreekswaarde een opgegeven aantal keren.
  
## <a name="syntax"></a>Syntaxis
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie.
  
*num_expr*  
   Is een numerieke expressie. Als *num_expr* negatief of niet eindig is, is het resultaat niet gedefinieerd.
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een teken reeks expressie.
  
## <a name="remarks"></a>Opmerkingen

  De maximale lengte van het resultaat is 10.000 tekens, dat wil zeggen (length ( *str_expr* ) *  *num_expr* ) <= 10.000. Deze systeem functie maakt geen gebruik van de index.

## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe u kunt gebruiken `REPLICATE` in een query.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Dit is de resultatenset.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
