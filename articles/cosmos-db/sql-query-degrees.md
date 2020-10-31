---
title: GRADEN in Azure Cosmos DB query taal
description: Meer informatie over de functie voor het SQL-systeem van graden in Azure Cosmos DB om de bijbehorende hoek in graden te retour neren voor een hoek opgegeven in radialen
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 991d83a329603342975a8186fe704afc53813e08
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095761"
---
# <a name="degrees-azure-cosmos-db"></a>GRADEN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert de overeenkomende hoek in graden voor een hoek die is opgegeven in radialen.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt het aantal graden in een hoek van PI/2 radialen geretourneerd.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Dit is de resultatenset.  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
