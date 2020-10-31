---
title: ASIN in Azure Cosmos DB query taal
description: Meer informatie over de manier waarop de functie voor het SQL-systeem van de boog sinus (BOOGSIN) in Azure Cosmos DB de hoek in radialen retourneert, waarvan de sinus de opgegeven numerieke expressie is
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f2a3e87f464c2b5d0365785fcd7b81623ef09ef
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089250"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert de hoek, in radialen, waarvan de sinus de opgegeven numerieke expressie is. Dit wordt ook wel een boog sinus genoemd.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt de waarde `ASIN` -1 geretourneerd.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Dit is de resultatenset.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
