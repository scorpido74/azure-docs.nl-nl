---
title: ARRAY_CONTAINS in Azure Cosmos DB-querytaal
description: Meer informatie over hoe de functie Array Contains SQL-systeem in Azure Cosmos DB een Booleaanse retourneert die aangeeft of de array de opgegeven waarde bevat
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 47fe20888aa546e414b268b30c2e03580750a040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303474"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
Retourneert een Booleaanse waarde die aangeeft of de matrix de opgegeven waarde bevat. U controleren op een gedeeltelijke of volledige overeenkomst van een object met behulp van een booleaanse expressie binnen de opdracht. 

## <a name="syntax"></a>Syntaxis
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argumenten
  
*arr_expr*  
   Moet de arrayexpressie worden doorzocht.  
  
*Expr*  
   Is de uitdrukking te vinden.  

*bool_expr*  
   Is een booleaanse uitdrukking. Als het wordt geëvalueerd op 'waar' en als de opgegeven zoekwaarde een object is, controleert de opdracht op een gedeeltelijke overeenkomst (het zoekobject is een subset van een van de objecten). Als het wordt geëvalueerd als 'false', controleert de opdracht op een volledige overeenkomst van alle objecten binnen de array. De standaardwaarde als deze niet is opgegeven, is onjuist. 
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een Booleaanse waarde.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld hoe u `ARRAY_CONTAINS`kunt controleren of u lid bent in een array met behulp van .  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"b1": true, "b2": false}]  
```  

In het volgende voorbeeld hoe u controleert op een gedeeltelijke overeenkomst van een JSON in een array met ARRAY_CONTAINS.  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}]
```

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie zal profiteren van een [bereikindex](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Arrayfuncties Azure Cosmos DB](sql-query-array-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
