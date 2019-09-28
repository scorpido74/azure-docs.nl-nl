---
title: LAGER in Azure Cosmos DB query taal
description: Meer informatie over de functie voor SQL-systeem functies lager in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3a9c122ef65772458b832d3b1651e7e63e42985e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349700"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
 Retourneert een tekenreeksexpressie na het converteren van tekens in hoofdletters naar kleine letters.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een tekenreeksexpressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe u `LOWER` gebruikt in een query.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Hier volgt de resultatenset.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
