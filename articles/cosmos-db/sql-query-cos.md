---
title: CO'S in Azure Cosmos DB-query taal
description: Meer informatie over hoe de functie van cosinus (COS) SQL-systeem in Azure Cosmos DB de trigonometrische cosinus van de opgegeven hoek in radialen retourneert in de opgegeven expressie
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a5f60b9f321b7d1ef75a1927310c788ff3ee3270
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100844"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert de trigonometrische cosinus van de opgegeven hoek, in radialen, in de opgegeven expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt de `COS` van de opgegeven hoek berekend.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Dit is de resultatenset.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
