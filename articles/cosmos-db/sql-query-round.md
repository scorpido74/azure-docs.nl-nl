---
title: Afronden in Azure Cosmos DB query taal
description: Meer informatie over de functie voor het afronden van SQL-systemen in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1e2447c713ff418f8d5ccc0e6bc64bf81f000524
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082722"
---
# <a name="round-azure-cosmos-db"></a>ROUND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een numerieke waarde, afgerond naar het dichtstbijzijnde gehele getal.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="remarks"></a>Opmerkingen
  
  De Afrondings bewerking wordt uitgevoerd op basis van het middel punt afronding van nul. Als de invoer een numerieke expressie is die precies tussen twee gehele getallen ligt, is het resultaat de dichtstbijzijnde gehele waarde van nul. Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy).
  
  |<numeric_expr>|Impliceer|
  |-|-|
  |-6,5000|-7|
  |-0,5|-1|
  |0.5|1|
  |6,5000|7||
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld worden de volgende positieve en negatieve getallen afgerond op het dichtstbijzijnde gehele getal.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Dit is de resultatenset.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
