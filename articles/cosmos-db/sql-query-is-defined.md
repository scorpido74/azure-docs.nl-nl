---
title: IS_DEFINED in Azure Cosmos DB query taal
description: Meer informatie over de functie IS_DEFINED van SQL-systeem in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a5fcaf52d8e9e6b942a95f0b0c43f3f654c5d5d2
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303848"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED (Azure Cosmos DB)
 Retourneert een Booleaanse waarde die aangeeft of aan de eigenschap een waarde is toegewezen.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
IS_DEFINED(<expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*expressie*  
   Is een expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een Booleaanse expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  Het volgende voorbeeld wordt gecontroleerd op de aanwezigheid van een eigenschap binnen het opgegeven JSON-document. De eerste retourneert ' True ', omdat "a" aanwezig is, maar de tweede ' false ' retourneert omdat "b" afwezig is.  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Hier volgt de resultatenset.  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Type controleren van functies Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
