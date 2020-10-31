---
title: IS_DEFINED in Azure Cosmos DB query taal
description: Meer informatie over de functie IS_DEFINED van SQL-systeem in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b322eb4417e53e926d0e7cd59692383cb0e89280
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088349"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een Booleaanse waarde die aangeeft of aan de eigenschap een waarde is toegewezen.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
IS_DEFINED(<expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*expressie*  
   Is een expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een Boole-expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt gecontroleerd op de aanwezigheid van een eigenschap binnen het opgegeven JSON-document. De eerste retourneert waar, omdat "a" aanwezig is, maar de tweede retourneert onwaar omdat "b" ontbreekt.  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Dit is de resultatenset.  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Type controleren van functies Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
