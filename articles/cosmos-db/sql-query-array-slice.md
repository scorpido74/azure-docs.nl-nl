---
title: ARRAY_SLICE in Azure Cosmos DB-querytaal
description: Meer informatie over hoe de SQL-systeemarrayfunctie array in Azure Cosmos DB een deel van een arrayexpressie retourneert
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a98cb17d22f41776ff788d12ced6aa988ad0b10e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303321"
---
# <a name="array_slice-azure-cosmos-db"></a>ARRAY_SLICE (Azure Cosmos DB)
 Retourneert een deel van een matrixexpressie.
  
## <a name="syntax"></a>Syntaxis
  
```sql
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
## <a name="arguments"></a>Argumenten
  
*arr_expr*  
   Is een array expressie.  
  
*num_expr*  
   Nulgebaseerde numerieke index waarmee de array moet worden gestart. Negatieve waarden kunnen worden gebruikt om de beginindex op te geven ten opzichte van het laatste element van de array, d.w.z. -1 verwijst naar het laatste element in de array.  

*num_expr* Optionele numerieke expressie die het maximumaantal elementen in de resulterende array instelt.    

## <a name="return-types"></a>Retourtypen
  
  Retourneert een matrixexpressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld ziet u hoe `ARRAY_SLICE`u verschillende segmenten van een array krijgen met behulp van.  
  
```sql
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Arrayfuncties Azure Cosmos DB](sql-query-array-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
