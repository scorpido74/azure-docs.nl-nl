---
title: GetCurrentDateTime in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie GetCurrentDateTime in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d50b08ab85c7e299c465c3eb6f34e867d6634006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303899"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
 Geeft als resultaat de huidige UTC -datum en -tijd als een ISO 8601-tekenreeks.
  
## <a name="syntax"></a>Syntaxis
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat de huidige UTC-datum en -tijd `YYYY-MM-DDThh:mm:ss.fffffffZ` als ISO 8601-tekenreekswaarde in de notatie waarin:
  
  |||
  |-|-|
  |Jjjj|viercijferig jaar|
  |MM|tweecijferige maand (01 = januari, enz.)|
  |Dd|tweecijferige dag van de maand (01 t/m 31)|
  |T|signifier voor begin tijdselementen|
  |hh|tweecijferig uur (00 tot en met 23)|
  |mm|tweecijferige minuten (00 tot en met 59)|
  |ss|tweecijferige seconden (00 tot en met 59)|
  |.fffffff|zevencijferige fractionele seconden|
  |Z|UTC (Coordinated Universal Time) ondertekenaar||
  
  Zie [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601) voor meer informatie over het ISO 8601-formaat

## <a name="remarks"></a>Opmerkingen

  GetCurrentDateTime() is een niet-deterministische functie. 
  
  Het geretourneerde resultaat is UTC.

  Precisie is 7 cijfers, met een nauwkeurigheid van 100 nanoseconden.

## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld ziet u hoe u de huidige UTC-datumtijd krijgen met de ingebouwde functie GetCurrentDateTime().
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Hier is een voorbeeldresultaatset.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Datum- en tijdfuncties Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
