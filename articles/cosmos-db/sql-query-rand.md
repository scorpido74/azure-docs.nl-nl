---
title: ASELECT in Azure Cosmos DB query taal
description: Meer informatie over de functie RAND van SQL-systeem in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e44878b6d65725f08aeca4eb07088315ae2bb78a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302216"
---
# <a name="rand-azure-cosmos-db"></a>ASELECT (Azure Cosmos DB)
 Retourneert een wille keurig gegenereerde numerieke waarde van [0, 1).
 
## <a name="syntax"></a>Syntax
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Retour typen

  Retourneert een numerieke expressie.

## <a name="remarks"></a>Opmerkingen

  `RAND`is een niet-deterministische functie. Herhaalde aanroepen van `RAND` worden niet dezelfde resultaten geretourneerd.

## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt een wille keurig gegenereerde numerieke waarde geretourneerd.
  
```sql
SELECT RAND() AS rand 
```  
  
 Dit is de resultatenset.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
