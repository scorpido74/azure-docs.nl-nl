---
title: PI in Azure Cosmos DB query taal
description: Meer informatie over de functie PI van SQL-systeem in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5f2aa78b866b148f59ee3dcfccd351eb51e46526
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089148"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert de constante waarde van PI.  
  
## <a name="syntax"></a>Syntax
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt de waarde van weer gegeven `PI` .  
  
```sql
SELECT PI() AS pi 
```  
  
 Dit is de resultatenset.  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
