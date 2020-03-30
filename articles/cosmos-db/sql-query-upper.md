---
title: UPPER in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie UPPER in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303967"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
 Retourneert een tekenreeksexpressie na het converteren van tekens in kleine letters naar hoofdletters.  

De functie BOVEN-systeem maakt geen gebruik van de index. Als u van plan bent om frequente gevallen ongevoelige vergelijkingen te doen, kan de upper-systeemfunctie een aanzienlijke hoeveelheid RU's verbruiken. Als dit het geval is, u in plaats van de functie UPPER-systeem elke keer gegevens te normaliseren voor vergelijkingen, de behuizing normaliseren bij het inbrengen. Dan wordt een query zoals SELECT * FROM c WHERE UPPER(c.name) = 'BOB' gewoon SELECT * FROM c WHERE c.name = 'BOB'.

## <a name="syntax"></a>Syntaxis
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een tekenreeksexpressie.  
  
## <a name="return-types"></a>Retourtypen
  
  Retourneert een tekenreeksexpressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld `UPPER` ziet u hoe u deze in een query gebruiken  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Tekenreeksfuncties Azure Cosmos DB](sql-query-string-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
