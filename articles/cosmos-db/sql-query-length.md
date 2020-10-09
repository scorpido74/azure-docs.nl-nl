---
title: LENGTE in Azure Cosmos DB query taal
description: Meer informatie over de lengte van de SQL-systeem functie in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e020555b0c706b5577bd20ac9bd537604d43ba3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78303712"
---
# <a name="length-azure-cosmos-db"></a>LENGTE (Azure Cosmos DB)
 Retourneert het aantal tekens van de opgegeven teken reeks expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is de teken reeks expressie die moet worden geëvalueerd.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt de lengte van een teken reeks geretourneerd.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Dit is de resultatenset.  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
