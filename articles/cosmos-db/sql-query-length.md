---
title: LENGTE in Azure Cosmos DB query taal
description: Meer informatie over de lengte van de SQL-systeem functie in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0c7766ac81df02755fad741c6fa733ba3163baff
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083648"
---
# <a name="length-azure-cosmos-db"></a>LENGTE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
