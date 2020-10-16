---
title: ASIN in Azure Cosmos DB query taal
description: Meer informatie over de manier waarop de functie voor het SQL-systeem van de boog sinus (BOOGSIN) in Azure Cosmos DB de hoek in radialen retourneert, waarvan de sinus de opgegeven numerieke expressie is
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78302692"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
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
