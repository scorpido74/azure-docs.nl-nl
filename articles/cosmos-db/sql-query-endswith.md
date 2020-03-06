---
title: ENDSWITH in Azure Cosmos DB-query taal
description: Meer informatie over de functie ENDSWITH SQL System in Azure Cosmos DB om een Booleaanse waarde te retour neren die aangeeft of de eerste teken reeks expressie eindigt met de tweede
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 37c5a8b3c44c5ac46b837e4d851d22f85aeaf39c
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78299445"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
 Retourneert een Booleaanse waarde die aangeeft of de eerste expressie tekenreeks eindigt met de tweede.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr1*  
   Is een teken reeks expressie.  
  
*str_expr2*  
   Is een teken reeks expressie die moet worden vergeleken met het einde van *str_expr1*.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een Booleaanse expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  Het volgende voorbeeld retourneert dat de "abc" eindigt op "b" en 'bc'.  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Hier volgt de resultatenset.  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
