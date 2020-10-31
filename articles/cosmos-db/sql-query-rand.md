---
title: ASELECT in Azure Cosmos DB query taal
description: Meer informatie over de functie RAND van SQL-systeem in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3e287f119a67c8bb00a309833e2ad6fe0d88975a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076092"
---
# <a name="rand-azure-cosmos-db"></a>ASELECT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een wille keurig gegenereerde numerieke waarde van [0, 1).
 
## <a name="syntax"></a>Syntax
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Retour typen

  Retourneert een numerieke expressie.

## <a name="remarks"></a>Opmerkingen

  `RAND` is een niet-deterministische functie. Herhaalde aanroepen van `RAND` worden niet dezelfde resultaten geretourneerd. Deze systeem functie maakt geen gebruik van de index.


## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt een wille keurig gegenereerde numerieke waarde geretourneerd.
  
```sql
SELECT RAND() AS rand 
```  
  
 Dit is de resultatenset.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
