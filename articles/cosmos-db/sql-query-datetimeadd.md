---
title: DateTimeAdd in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie DateTimeAdd in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 0b2741a3d2b013ba7bd97038eb4ba4512f36af11
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261825"
---
# <a name="datetimeadd-azure-cosmos-db"></a>DateTimeAdd (Azure Cosmos DB)

Retourneert een datum-teken reeks waarde die het resultaat is van het toevoegen van een opgegeven numerieke waarde (als een ondertekend geheel getal) aan een opgegeven DateTime-teken reeks  
  
## <a name="syntax"></a>Syntaxis
  
```sql
DateTimeAdd (<DateTimePart> , <numeric_expr> ,<DateTime>)
```

## <a name="arguments"></a>Argumenten
  
*DateTimePart*  
   Het deel van de datum waarop DateTimeAdd een geheel getal toevoegt. Deze tabel bevat alle geldige DateTimePart-argumenten:

| DateTimePart | afkortingen        |
| ------------ | -------------------- |
| Jaar         | "Year", "yyyy", "JJ" |
| Maand        | "month", "mm", "m"   |
| Dag          | "dag", "dd", "d"     |
| Uur         | "uur", "uu"         |
| Minuut       | ' minuut ', ' mi ', ' n '  |
| Tweede       | "seconde", "SS", "s"  |
| Milliseconde  | ' milliseconde ', ' MS '  |
| Wacht  | "micro seconde", "mcs" |
| Nano seconden   | "nano seconden", "ns"   |

*numeric_expr*  
   Is een ondertekende integer-waarde die wordt toegevoegd aan de DateTimePart van de opgegeven datum/tijd

*Datum/tijd*  
   UTC-datum en-tijd, ISO 8601-teken reeks waarde in de notatie `YYYY-MM-DDThh:mm:ss.fffffffZ` waarin:
  
  |Indeling|Beschrijving|
  |-|-|
  |DD|jaar met vier cijfers|
  |MM|maand van twee cijfers (01 = januari, etc.)|
  |AFSCHRIJVING|2-cijferige dag van de maand (01 tot en met 31)|
  |T|de aanzienlijke voor het begin van de tijd elementen|
  |hh|twee cijfers per uur (00 tot en met 23)|
  |mm|twee cijfers minuten (00 tot en met 59)|
  |ss|seconden van twee cijfers (00 tot en met 59)|
  |.fffffff|aantal seconden van zeven cijfers|
  |Z|UTC (Coordinated Universal Time)||
  
  Zie [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601) voor meer informatie over de ISO 8601-indeling.

## <a name="return-types"></a>Retour typen

Retourneert een UTC-datum en-8601 tijd teken reeks waarde in de notatie `YYYY-MM-DDThh:mm:ss.fffffffZ` waarin:
  
  |Indeling|Beschrijving|
  |-|-|
  |DD|jaar met vier cijfers|
  |MM|maand van twee cijfers (01 = januari, etc.)|
  |AFSCHRIJVING|2-cijferige dag van de maand (01 tot en met 31)|
  |T|de aanzienlijke voor het begin van de tijd elementen|
  |hh|twee cijfers per uur (00 tot en met 23)|
  |mm|twee cijfers minuten (00 tot en met 59)|
  |ss|seconden van twee cijfers (00 tot en met 59)|
  |.fffffff|aantal seconden van zeven cijfers|
  |Z|UTC (Coordinated Universal Time)||

## <a name="remarks"></a>Opmerkingen

DateTimeAdd wordt `undefined` om de volgende redenen geretourneerd:

- De opgegeven DateTimePart-waarde is ongeldig
- De opgegeven numeric_expr is geen geldig geheel getal
- De datum/tijd in het argument of het resultaat is geen geldige ISO 8601-datum/tijd.

## <a name="examples"></a>Voorbeelden
  
In het volgende voor beeld wordt 1 maand toegevoegd aan de datum/tijd:`2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("mm", 1, "2020-07-09T23:20:13.4575530Z") AS OneMonthLater
```

```json
[
    {
        "OneMonthLater": "2020-08-09T23:20:13.4575530Z"
    }
]
```  

In het volgende voor beeld worden twee uur afgetrokken van de datum/tijd:`2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("hh", -2, "2020-07-09T23:20:13.4575530Z") AS TwoHoursEarlier
```

```json
[
    {
        "TwoHoursEarlier": "2020-07-09T21:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>Volgende stappen

- [Datum-en tijd functies Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
