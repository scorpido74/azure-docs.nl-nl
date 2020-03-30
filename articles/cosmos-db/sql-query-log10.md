---
title: LOG10 in Azure Cosmos DB-querytaal
description: Meer informatie over de FUNCTIE LOG10 SQL-systeem in Azure Cosmos DB om de base-10 logaritme van de opgegeven numerieke expressie terug te sturen
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f47200f6978d91f46c010640bb9c2bb26e9b7d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302488"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
 Geeft als resultaat de logaritme basis-10 van de opgegeven numerieke expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expression*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke expressie.  
  
## <a name="remarks"></a>Opmerkingen
  
  De log10- en POWER-functies zijn omgekeerd met elkaar verbonden. Bijvoorbeeld 10 ^ LOG10(n) = n.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld wordt een variabele gedeclareerd en wordt de LOG10-waarde van de opgegeven variabele (100) geretourneerd.  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{log10: 2}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
