---
title: COT in Azure Cosmos DB-querytaal
description: Meer informatie over hoe de COtangent(COT)SQL-systeemfunctie in Azure Cosmos DB de trigonometrische cotangens van de opgegeven hoek in radialen retourneert in de opgegeven numerieke expressie
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25c907644f58ee40ea08e5636d68dc0e84564a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299484"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
 Retourneert de trigonometrische cotangens van de opgegeven hoek, in radialen, in de opgegeven expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende `COT` voorbeeld wordt de opgegeven hoek berekend.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
