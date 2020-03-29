---
title: Wiskundige functies in Azure Cosmos DB-querytaal
description: Meer informatie over de wiskundige functies in Azure Cosmos DB om een berekening uit te voeren, op basis van invoerwaarden die als argumenten worden opgegeven, en een numerieke waarde retourneren.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a04867d356aaf2f55dbe900d2e35b42f74206851
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873264"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Wiskundige functies (Azure Cosmos DB)  

Met elke wiskundige functie wordt een berekening op basis van de opgegeven invoerwaarden uitgevoerd en een numerieke waarde geretourneerd.

U query's uitvoeren zoals het volgende voorbeeld:

```sql
    SELECT VALUE ABS(-4)
```

Het resultaat is:

```json
    [4]
```

## <a name="functions"></a>Functions

Met de volgende ondersteunde ingebouwde wiskundige functies wordt een berekening uitgevoerd, meestal op basis van invoerargumenten, en wordt een numerieke expressie retourneren.
  
||||  
|-|-|-|  
|[Abs](sql-query-abs.md)|[ACOS](sql-query-acos.md)|[ASIN](sql-query-asin.md)|  
|[ATAN](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[Plafond](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[DEGREES](sql-query-degrees.md)|  
|[Exp](sql-query-exp.md)|[Verdieping](sql-query-floor.md)|[Log](sql-query-log.md)|  
|[LOG10](sql-query-log10.md)|[PI](sql-query-pi.md)|[POWER](sql-query-power.md)|  
|[RADIANS](sql-query-radians.md)|[RAND](sql-query-rand.md)|[Ronde](sql-query-round.md)|
|[SIGN](sql-query-sign.md)|[Zonde](sql-query-sin.md)|[Sqrt](sql-query-sqrt.md)|
|[SQUARE](sql-query-square.md)|[TAN](sql-query-tan.md)|[TRUNC](sql-query-trunc.md)||  
  
Alle wiskundige functies, met uitzondering van RAND, zijn deterministische functies. Dit betekent dat ze dezelfde resultaten retourneren elke keer dat ze worden aangeroepen met een specifieke set invoerwaarden.

## <a name="next-steps"></a>Volgende stappen

- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
- [Door de gebruiker gedefinieerde functies](sql-query-udfs.md)
- [Aggregaties](sql-query-aggregates.md)
