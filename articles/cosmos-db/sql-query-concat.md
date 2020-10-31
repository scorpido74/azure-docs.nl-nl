---
title: CONCAt in Azure Cosmos DB query taal
description: Meer informatie over hoe de functie CONCAt SQL-systeem in Azure Cosmos DB een teken reeks retourneert die het resultaat is van het samen voegen van twee of meer teken reeks waarden
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0b3401e8de4987670f74a02286cb66eb7a9dfb29
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081617"
---
# <a name="concat-azure-cosmos-db"></a>CONCAt (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een tekenreeks die het resultaat is van het samenvoegen van twee of meer tekenreekswaarden.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie die moet worden samengevoegd met de andere waarden. `CONCAT`Voor de functie zijn ten minste *twee str_expr* argumenten vereist.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een teken reeks expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt de aaneengeschakelde teken reeks van de opgegeven waarden geretourneerd.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Dit is de resultatenset.  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
