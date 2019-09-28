---
title: Wiskundige functies in Azure Cosmos DB query taal
description: Meer informatie over wiskundige SQL-systeem functies in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fe4a84db3f2cbcfc2d9841caf520404afec2a297
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349673"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Wiskundige functies (Azure Cosmos DB)  

Met elke wiskundige functie wordt een berekening op basis van de opgegeven invoerwaarden uitgevoerd en een numerieke waarde geretourneerd.

U kunt query's uitvoeren zoals in het volgende voor beeld:

```sql
    SELECT VALUE ABS(-4)
```

Het resultaat is:

```json
    [4]
```

## <a name="functions"></a>Functies

De volgende ingebouwde wiskundige functies voeren een berekening uit, meestal op basis van invoer argumenten, en retour neren een numerieke expressie.
  
||||  
|-|-|-|  
|[ABS](sql-query-abs.md)|[ACOS](sql-query-acos.md)|[ASIN](sql-query-asin.md)|  
|[ATAN](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[MAXIMUM](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[DEGREES](sql-query-degrees.md)|  
|[EXP](sql-query-exp.md)|[FLOOR](sql-query-floor.md)|[LOGBOEK](sql-query-log.md)|  
|[LOG10](sql-query-log10.md)|[PI](sql-query-pi.md)|[ENERGIEBEHEER](sql-query-power.md)|  
|[RADIANS](sql-query-radians.md)|[ASELECT](sql-query-rand.md)|[AFRONDEN](sql-query-round.md)|
|[SIGN](sql-query-sign.md)|[SIN](sql-query-sin.md)|[SQRT](sql-query-sqrt.md)|
|[SQUARE](sql-query-square.md)|[TAN](sql-query-tan.md)|[TRUNC](sql-query-trunc.md)||  
  
Alle wiskundige functies, met uitzonde ring van ASELECT, zijn deterministische functies. Dit betekent dat ze dezelfde resultaten retour neren telkens wanneer ze worden aangeroepen met een specifieke set invoer waarden.

## <a name="next-steps"></a>Volgende stappen

- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
- [Door de gebruiker gedefinieerde functies](sql-query-udfs.md)
- [Aggregates](sql-query-aggregates.md)
