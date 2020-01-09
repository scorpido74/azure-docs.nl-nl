---
title: PLAFOND in Azure Cosmos DB query taal
description: Meer informatie over hoe de SQL-functie CEILING in Azure Cosmos DB de kleinste gehele waarde retourneert die groter is dan, of gelijk is aan, de opgegeven numerieke expressie.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2da7820a6c9f1f90585b4deb605bb99c7580b0e5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444809"
---
# <a name="ceiling-azure-cosmos-db"></a>PLAFOND (Azure Cosmos DB)
 Retourneert het kleinste gehele getal dat groter is dan of gelijk is aan de opgegeven numerieke expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
CEILING (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Een numerieke expressie is.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld worden positieve numerieke, negatieve waarden en nulwaarden weer gegeven met de functie `CEILING`.  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Hier volgt de resultatenset.  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
