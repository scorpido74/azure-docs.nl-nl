---
title: COS in Azure Cosmos DB-querytaal
description: Meer informatie over hoe het COSINE SQL-systeem de functie van het Cosine (COS) SQL-systeem in Azure Cosmos DB retourneert als de trigonometrische cosine van de opgegeven hoek in radialen, in de opgegeven expressie
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40d277ff38691e2cb74bd4d5d78a666c304acfcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304018"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 Retourneert de trigonometrische cosinus van de opgegeven hoek, in radialen, in de opgegeven expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende `COS` voorbeeld wordt de opgegeven hoek berekend.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
