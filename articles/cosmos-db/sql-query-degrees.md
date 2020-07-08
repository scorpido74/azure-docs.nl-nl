---
title: GRADEN in Azure Cosmos DB query taal
description: Meer informatie over de functie voor het SQL-systeem van graden in Azure Cosmos DB om de bijbehorende hoek in graden te retour neren voor een hoek opgegeven in radialen
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d175ba53a71998fc8e7812a1b761f9cd264c38a9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78299467"
---
# <a name="degrees-azure-cosmos-db"></a>GRADEN (Azure Cosmos DB)
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
