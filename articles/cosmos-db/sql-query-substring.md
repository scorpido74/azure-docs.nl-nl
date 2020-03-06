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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303695"
---
# <a name="substring-azure-cosmos-db"></a>Subtekenreeks (Azure Cosmos DB)
 Onderdeel van een tekenreeksexpressie vanaf de op nul gebaseerde positie van het opgegeven teken geretourneerd en blijft aan de opgegeven lengte of aan het einde van de tekenreeks.  
  
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
  
  Retourneert een tekenreeksexpressie.  
  
## <a name="examples"></a>Voorbeelden
  
  Het volgende voorbeeld retourneert de subtekenreeks van "abc" beginnen op 1 en een lengte van 1 teken bevatten.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Hier volgt de resultatenset.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy) als de begin positie `0`is.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
