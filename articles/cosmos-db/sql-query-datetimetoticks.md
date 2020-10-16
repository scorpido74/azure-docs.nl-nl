---
title: DateTimeToTicks in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie DateTimeToTicks in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2e2c9e8f2bf0d4760bf030fb19a90737cdb54525
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88605132"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB)

Hiermee wordt de opgegeven datum/tijd geconverteerd naar Ticks. Eén tick vertegenwoordigt 100 nano seconden of 1 10-millionth van een seconde. 

## <a name="syntax"></a>Syntaxis
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>Argumenten
  
*Datum/tijd*  
   UTC-datum en-tijd, ISO 8601-teken reeks waarde in de notatie `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Retour typen

Retourneert een ondertekende numerieke waarde, het huidige aantal 100-nano seconden Ticks dat is verstreken sinds de UNIX-epoche. Met andere woorden, DateTimeToTicks retourneert het aantal 100-nano seconden-tikken dat sinds 00:00:00 donderdag, 1 januari 1970, is verstreken.

## <a name="remarks"></a>Opmerkingen

DateTimeDateTimeToTicks wordt geretourneerd `undefined` als de datum/tijd geen geldige ISO 8601-datum is

Deze systeem functie maakt geen gebruik van de index.

## <a name="examples"></a>Voorbeelden

Hier volgt een voor beeld waarin het aantal maten wordt geretourneerd:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05.6789123Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342456789124
    }
]
```

Hier volgt een voor beeld waarin het aantal maat streepjes wordt geretourneerd zonder het aantal seconden op te geven:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342450000000
    }
]
```

## <a name="next-steps"></a>Volgende stappen

- [Datum-en tijd functies Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
