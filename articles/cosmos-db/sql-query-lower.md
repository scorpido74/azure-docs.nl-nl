---
title: LAGER in Azure Cosmos DB query taal
description: Meer informatie over de lagere SQL-systeem functie in Azure Cosmos DB om een teken reeks expressie te retour neren na het omzetten van een hoofd letter-teken in kleine letters
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f7cd864d90b8ab17a180a00a7a6d4bb683da9be
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873298"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
 Retourneert een tekenreeksexpressie na het converteren van tekens in hoofdletters naar kleine letters.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een tekenreeksexpressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe u `LOWER` gebruikt in een query.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Hier volgt de resultatenset.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
