---
title: Afronden in Azure Cosmos DB query taal
description: Meer informatie over de functie voor het afronden van SQL-systemen in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 41b1372cd5165b3548a4e574e7eb037111188bac
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341549"
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
