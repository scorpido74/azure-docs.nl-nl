---
title: INDEX_OF in Azure Cosmos DB-querytaal
description: Meer informatie over sql-systeemfunctie INDEX_OF in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 50e489fdf57398d486b07944782ecbb3fd1d6a43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71350985"
---
# <a name="index_of-azure-cosmos-db"></a>INDEX_OF (Azure Cosmos DB)
 Retourneert de beginpositie van het eerste exemplaar van de tweede tekenreeksexpressie binnen de eerste opgegeven tekenreeksexpressie, of -1 als de tekenreeks niet is gevonden.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
INDEX_OF(<str_expr1>, <str_expr2> [, <numeric_expr>])  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr1*  
   Moet de tekenreeksexpressie worden doorzocht.  
  
*str_expr2*  
   Is de tekenreeksexpressie om naar te zoeken.  

*numeric_expr* Optionele numerieke expressie die de positie instelt die de zoekopdracht start. De eerste positie in *str_expr1* is 0. 
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld wordt de index van verschillende subtekenreeksen in "abc" geretourneerd.  
  
```sql
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"i1": 0, "i2": 1, "i3": -1}]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Tekenreeksfuncties Azure Cosmos DB](sql-query-string-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
