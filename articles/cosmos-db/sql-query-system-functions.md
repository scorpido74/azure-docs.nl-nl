---
title: Systeemfuncties in Azure Cosmos DB-querytaal
description: Meer informatie over ingebouwde en door de gebruiker gedefinieerde SQL-systeemfuncties in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f41adbb726313ef095084d079dc7852736e0c06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870527"
---
# <a name="system-functions-azure-cosmos-db"></a>Systeemfuncties (Azure Cosmos DB)

 Cosmos DB biedt veel ingebouwde SQL-functies. De categorieën van ingebouwde functies staan hieronder vermeld.  
  
|Functiegroep|Beschrijving|Bewerkingen|  
|--------------|-----------------|-----------------| 
|[Arrayfuncties](sql-query-array-functions.md)|De arrayfuncties voeren een bewerking uit op een arrayinvoerwaarde en geven numerieke, Booleaanse of matrixwaarde weer. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md), [ARRAY_SLICE](sql-query-array-slice.md) |
|[Datum- en tijdfuncties](sql-query-date-time-functions.md)|Met de datum- en tijdfuncties u de huidige UTC-datum en -tijd in twee vormen krijgen. een numerieke tijdstempel waarvan de waarde het Unix-tijdperk in milliseconden of als een tekenreeks is die voldoet aan de ISO 8601-indeling. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md) |
|[Wiskundige functies](sql-query-mathematical-functions.md)|De wiskundige functies voeren elk een berekening uit, meestal op basis van invoerwaarden die als argumenten worden opgegeven, en geven een numerieke waarde terug. | [ABS](sql-query-abs.md), [ACOS](sql-query-acos.md), [ASIN](sql-query-asin.md), [ATAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [PLAFOND](sql-query-ceiling.md), [COS](sql-query-cos.md), [COT](sql-query-cot.md), [DEGREES](sql-query-degrees.md), [EXP](sql-query-floor.md), [LOG](sql-query-log.md), LOG , [LOG10](sql-query-log10.md), [PI](sql-query-pi.md), [EXP](sql-query-exp.md) [POWER](sql-query-power.md), RADIAL [,](sql-query-radians.md) [RAND](sql-query-rand.md), ROUND , [SIGN](sql-query-sign.md), [SIN](sql-query-sin.md), [SQRT](sql-query-sqrt.md), [Square](sql-query-square.md), [Tan](sql-query-tan.md), [TRUNC](sql-query-trunc.md) [ROUND](sql-query-round.md) |
|[Ruimtelijke functies](sql-query-spatial-functions.md)|De ruimtelijke functies voeren een bewerking uit op een inputwaarde van een ruimtelijk object en geven een numerieke of Booleaanse waarde weer. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID ST_ISVALIDDETAILED](sql-query-st-isvalid.md), [ST_WITHIN](sql-query-st-within.md) [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md) |
|[Tekenreeksfuncties](sql-query-string-functions.md)|De tekenreeksfuncties voeren een bewerking uit op een tekenreeksinvoerwaarde en retourneren een tekenreeks, numerieke of Booleaanse waarde. | [CONCAT](sql-query-concat.md), [BEVAT](sql-query-contains.md), [ENDSWITH](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [Left](sql-query-left.md), [Length](sql-query-length.md), [Lower](sql-query-lower.md), [LTRIM](sql-query-ltrim.md), [REPLACE](sql-query-replace.md), [REVERSE](sql-query-replicate.md), [REVERSE](sql-query-reverse.md), [RTRIM](sql-query-rtrim.md), [STARTSWITH](sql-query-startswith.md), [StringToarray](sql-query-stringtoarray.md), [Stringtoboolean](sql-query-stringtoboolean.md), [Stringtonull](sql-query-stringtonull.md), [StringtoNumber](sql-query-stringtonumber.md), [StringtoObject](sql-query-stringtoobject.md), [SUBSTRING](sql-query-substring.md), [TOString](sql-query-tostring.md), [TRIM](sql-query-trim.md), [UPPER](sql-query-upper.md) [RIGHT](sql-query-right.md) |
|[Functies voor controle van het type](sql-query-type-checking-functions.md)|Met de typecontrolefuncties kunt u het type van een expressie in SQL-query's controleren. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT IS_PRIMITIVE](sql-query-is-object.md), [IS_STRING](sql-query-is-primitive.md) [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Ingebouwde versus door de gebruiker gedefinieerde functies (UDF's)

Als u momenteel een door de gebruiker gedefinieerde functie (UDF) gebruikt waarvoor nu een ingebouwde functie beschikbaar is, is de bijbehorende ingebouwde functie sneller uitgevoerd en efficiënter.

## <a name="built-in-versus-ansi-sql-functions"></a>Ingebouwde versus ANSI SQL-functies

Het belangrijkste verschil tussen Cosmos DB-functies en ANSI SQL-functies is dat Cosmos DB-functies zijn ontworpen om goed te werken met schemaloze en gemengde schemagegevens. Als een eigenschap bijvoorbeeld ontbreekt of een niet-numerieke waarde heeft, zoals `unknown`, wordt het item overgeslagen in plaats van een fout terug te sturen.

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Cosmos DB](introduction.md)
- [Arrayfuncties](sql-query-array-functions.md)
- [Datum- en tijdfuncties](sql-query-date-time-functions.md)
- [Wiskundige functies](sql-query-mathematical-functions.md)
- [Ruimtelijke functies](sql-query-spatial-functions.md)
- [Tekenreeksfuncties](sql-query-string-functions.md)
- [Functies voor controle van het type](sql-query-type-checking-functions.md)
- [Door de gebruiker gedefinieerde functies](sql-query-udfs.md)
- [Aggregaties](sql-query-aggregates.md)
