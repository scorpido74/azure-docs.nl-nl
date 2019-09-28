---
title: LENGTE in Azure Cosmos DB query taal
description: Meer informatie over de lengte van de SQL-systeem functie in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: aa430152415b1662a73a388a03ba6d4721c730f0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349749"
---
# <a name="length-azure-cosmos-db"></a>LENGTE (Azure Cosmos DB)
 Retourneert het aantal tekens van de opgegeven tekenreeksexpressie.  
  
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
  
  Het volgende voorbeeld retourneert de lengte van een tekenreeks.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Hier volgt de resultatenset.  
  
```json
[{"len": 3}]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
