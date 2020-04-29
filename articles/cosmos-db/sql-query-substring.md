---
title: Subtekenreeks in Azure Cosmos DB query taal
description: Meer informatie over de subtekenreeks voor de SQL-systeemfunctie in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78303695"
---
# <a name="substring-azure-cosmos-db"></a>Subtekenreeks (Azure Cosmos DB)
 Retourneert een deel van een teken reeks expressie, beginnend bij het opgegeven teken op nul gebaseerde positie en gaat verder met de opgegeven lengte of naar het einde van de teken reeks.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie.
  
*num_expr1*  
   Is een numerieke expressie om het begin teken aan te duiden. De waarde 0 is het eerste teken van *str_expr*.
  
*num_expr2*  
   Is een numerieke expressie waarmee het maximum aantal tekens van *str_expr* wordt aangegeven dat moet worden geretourneerd. Een waarde van 0 of minder resulteert in een lege teken reeks.

## <a name="return-types"></a>Retour typen
  
  Retourneert een teken reeks expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt de subtekenreeks van "ABC" geretourneerd, beginnend bij 1 en voor een lengte van 1 teken.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Dit is de resultatenset.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy) als de begin positie `0`is.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
