---
title: ENDSWITH in Azure Cosmos DB-query taal
description: Meer informatie over de functie ENDSWITH SQL System in Azure Cosmos DB om een Booleaanse waarde te retour neren die aangeeft of de eerste teken reeks expressie eindigt met de tweede
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d7e7f3e33389d4a201ec3281829cb9f0415978e6
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83713553"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)

 Retourneert een Booleaanse waarde die aangeeft of de eerste teken reeks expressie eindigt op de tweede.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr1*  
   Is een teken reeks expressie.  
  
*str_expr2*  
   Is een teken reeks expressie die moet worden vergeleken met het einde van *str_expr1*.

*bool_expr* Optionele waarde voor het negeren van case. Als deze eigenschap is ingesteld op True, wordt in ENDSWITH een hoofdletter gevoelige zoek opdracht uitgevoerd. Indien niet opgegeven, is deze waarde false.
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een Boole-expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt de ' ABC ' eindigt op ' b ' en ' BC '.  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Dit is de resultatenset.  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
