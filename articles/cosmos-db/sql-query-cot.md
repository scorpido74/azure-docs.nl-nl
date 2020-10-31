---
title: COT in Azure Cosmos DB-query taal
description: Meer informatie over hoe de COT-functie (cotangens) in Azure Cosmos DB de trigonometrische cotangens van de opgegeven hoek in radialen retourneert in de opgegeven numerieke expressie
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 862899b6abd7f6d10da0c3b01205457553bad903
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095857"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert de trigonometrische cotangens van de opgegeven hoek, in radialen, in de opgegeven expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt de `COT` van de opgegeven hoek berekend.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Dit is de resultatenset.  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
