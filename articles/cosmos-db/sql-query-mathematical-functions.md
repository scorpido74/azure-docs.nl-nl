---
title: Wiskundige functies in Azure Cosmos DB query taal
description: Meer informatie over de wiskundige functies in Azure Cosmos DB voor het uitvoeren van een berekening, op basis van invoer waarden die zijn opgegeven als argumenten en het retour neren van een numerieke waarde.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: bd53feb175c5be77f559a4d2e724a55e41df48eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85562825"
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

## <a name="functions"></a>Functions

De volgende ingebouwde wiskundige functies voeren een berekening uit, meestal op basis van invoer argumenten, en retour neren een numerieke expressie:
 
* [AFWEZIGHEID](sql-query-abs.md)
* [RESULTEERT](sql-query-acos.md)
* [BOOGSIN](sql-query-asin.md)
* [BOOGTAN](sql-query-atan.md)
* [ATN2](sql-query-atn2.md)
* [MAXIMUM](sql-query-ceiling.md)
* [WISSEN](sql-query-cos.md)
* [COT](sql-query-cot.md)
* [VRIJHEID](sql-query-degrees.md)
* [GELDIG](sql-query-exp.md)
* [FLOOR](sql-query-floor.md)
* [LOGBESTAND](sql-query-log.md)
* [ANDERS](sql-query-log10.md)
* [IP](sql-query-pi.md)
* [POWER](sql-query-power.md)
* [RADIALEN](sql-query-radians.md)
* [ASELECT](sql-query-rand.md)
* [ROUND](sql-query-round.md)
* [AANMELDEN](sql-query-sign.md)
* [SIN](sql-query-sin.md)
* [WORTEL](sql-query-sqrt.md)
* [SQUARE](sql-query-square.md)
* [TAN](sql-query-tan.md)
* [TRUNC](sql-query-trunc.md)

  
Alle wiskundige functies, met uitzonde ring van ASELECT, zijn deterministische functies. Dit betekent dat ze dezelfde resultaten retour neren telkens wanneer ze worden aangeroepen met een specifieke set invoer waarden.

## <a name="next-steps"></a>Volgende stappen

- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
- [Door de gebruiker gedefinieerde functies](sql-query-udfs.md)
- [Aggregaties](sql-query-aggregates.md)
