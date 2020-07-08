---
title: TAN in Azure Cosmos DB query taal
description: Meer informatie over de functie TAN in Azure Cosmos DB van SQL-systeem.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9d7187ba116067445e835769fc33aa70677ef80b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78301978"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
 Retourneert de tangens van de opgegeven hoek in radialen, in de opgegeven expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt de tangens van PI ()/2 berekend.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 Dit is de resultatenset.  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
