---
title: Datum-en tijd functies in Azure Cosmos DB query taal
description: Meer informatie over de functies datum en tijd van SQL-systeem in Azure Cosmos DB voor het uitvoeren van DateTime-en time stamp-bewerkingen.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e3666f58b12855c19dd9b8ecf5519ab772c49743
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246934"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Datum-en tijd functies (Azure Cosmos DB)

Met de functies datum en tijd kunt u datum-en tijds tempel bewerkingen uitvoeren in Azure Cosmos DB.

## <a name="functions-to-obtain-the-date-and-time"></a>Functies voor het verkrijgen van de datum en tijd

Met de volgende scalaire functies kunt u de huidige UTC-datum en-tijd in twee formulieren ophalen: een teken reeks die voldoet aan de ISO 8601-notatie of een numerieke tijds tempel waarvan de waarde de UNIX-epoche is in milliseconden:

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)

## <a name="functions-to-work-with-datetime-values"></a>Functies voor het werken met datum/tijd-waarden

Met de volgende functies kunt u eenvoudig datum-en tijd waarden bewerken:

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)

## <a name="next-steps"></a>Volgende stappen

- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
- [Door de gebruiker gedefinieerde functies](sql-query-udfs.md)
- [Aggregaties](sql-query-aggregates.md)
