---
title: REVERSE in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie REVERSE in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a22e1c8a5f4350bd2f966ee48f96368c648a4a1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302165"
---
# <a name="reverse-azure-cosmos-db"></a>REVERSE (Azure Cosmos DB)
 Retourneert een tekenreekswaarde in de omgekeerde volgorde.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een tekenreeksexpressie.  
  
## <a name="return-types"></a>Retourtypen
  
  Retourneert een tekenreeksexpressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld `REVERSE` ziet u hoe u deze in een query gebruiken.  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Tekenreeksfuncties Azure Cosmos DB](sql-query-string-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
