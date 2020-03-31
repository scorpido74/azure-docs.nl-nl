---
title: LAGER in Azure Cosmos DB-querytaal
description: Meer informatie over de functie LOWER SQL-systeem in Azure Cosmos DB om een tekenreeksexpressie te retourneren nadat u hoofdlettergegevens hebt geconverteerd naar kleine letters
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302267"
---
# <a name="lower-azure-cosmos-db"></a>LAGER (Azure Cosmos DB)
 Retourneert een tekenreeksexpressie na het converteren van tekens in hoofdletters naar kleine letters.  

De functie LOWER-systeem maakt geen gebruik van de index. Als u van plan bent om frequente gevallen ongevoelige vergelijkingen te doen, kan de lagere systeemfunctie een aanzienlijke hoeveelheid RU's verbruiken. Als dit het geval is, in plaats van het gebruik van de lagere systeemfunctie om gegevens te normaliseren elke keer voor vergelijkingen, u de behuizing normaliseren bij het inbrengen. Dan wordt een query zoals SELECT * FROM c WHERE LOWER(c.name) = 'bob' gewoon SELECT * FROM c WHERE c.name = 'bob'.

## <a name="syntax"></a>Syntaxis
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een tekenreeksexpressie.  
  
## <a name="return-types"></a>Retourtypen
  
  Retourneert een tekenreeksexpressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld `LOWER` ziet u hoe u deze in een query gebruiken.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Tekenreeksfuncties Azure Cosmos DB](sql-query-string-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
