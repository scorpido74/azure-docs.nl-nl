---
title: GetCurrentDateTime in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie GetCurrentDateTime in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8a2c3dcd3c8ca6dc9d751e50a7862fe98e6de510
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351020"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
 Retourneert de huidige UTC-datum en-tijd (Coordinated Universal Time) als een ISO 8601-teken reeks.
  
## <a name="syntax"></a>Syntaxis
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>Retour typen
  
  Retourneert de huidige UTC-datum en-8601 tijd in de notatie `YYYY-MM-DDThh:mm:ss.sssZ`, waarbij:
  
  |||
  |-|-|
  |DD|jaar met vier cijfers|
  |MM|maand van twee cijfers (01 = januari, etc.)|
  |DD|2-cijferige dag van de maand (01 tot en met 31)|
  |D|de aanzienlijke voor het begin van de tijd elementen|
  |UU|twee cijfers per uur (00 tot en met 23)|
  |mm|twee cijfer minuten (00 tot en met 59)|
  |SS|twee cijfer seconden (00 tot en met 59)|
  |.sss|drie cijfers van decimale delen van een seconde|
  |Z|UTC (Coordinated Universal Time)||
  
  Zie [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601) voor meer informatie over de ISO 8601-indeling.

## <a name="remarks"></a>Opmerkingen

  GetCurrentDateTime () is een niet-deterministische functie. 
  
  Het geretourneerde resultaat is UTC.

## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe u de huidige UTC-datum tijd kunt ophalen met behulp van de ingebouwde functie GetCurrentDateTime ().
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Hier volgt een voor beeld van een resultatenset.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Datum-en tijd functies Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
