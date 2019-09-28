---
title: Systeem functies in Azure Cosmos DB query taal
description: Meer informatie over SQL-systeem functies in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 98a087f02035545b828cfcf11c58d03d02a5e76a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349170"
---
# <a name="system-functions-azure-cosmos-db"></a>Systeem functies (Azure Cosmos DB)

 Cosmos DB biedt veel ingebouwde SQL-functies. De categorieën ingebouwde functies worden hieronder vermeld.  
  
|Functiegroep|Description|Bewerkingen|  
|--------------|-----------------|-----------------| 
|[Matrixfuncties](sql-query-array-functions.md)|De matrixfuncties uitvoeren van een bewerking op een matrix invoerwaarde en retourneren numerieke, Booleaanse waarde of Matrixwaarde. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md), [ARRAY_SLICE](sql-query-array-slice.md) |
|[Datum-en tijd functies](sql-query-date-time-functions.md)|Met de functies datum en tijd kunt u de huidige UTC-datum en-tijd in twee vormen ophalen. een numerieke tijds tempel waarvan de waarde de UNIX-epoche is in milliseconden of als een teken reeks die voldoet aan de ISO 8601-indeling. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md) |
|[Wiskundige functies](sql-query-mathematical-functions.md)|Wiskundige functies elke uitvoeren van een berekening, meestal op basis van de invoerwaarden die zijn opgegeven als argumenten en retourneert een numerieke waarde. | [ABS](sql-query-abs.md), [BOOGCOS](sql-query-acos.md), [BOOGSIN](sql-query-asin.md), [BOOGTAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [PLAFOND](sql-query-ceiling.md), [COS](sql-query-cos.md), [COT](sql-query-cot.md), [GRADEN](sql-query-degrees.md), [EXP](sql-query-exp.md), [VLOER](sql-query-floor.md), [LOGBOEK](sql-query-log.md), [LOG10](sql-query-log10.md), [PI](sql-query-pi.md), [KRACHT](sql-query-power.md), [RADIALEN](sql-query-radians.md), [ASELECT](sql-query-rand.md), [ROND](sql-query-round.md), [ONDERTEKENEN](sql-query-sign.md), [SIN](sql-query-sin.md), [WORTEL](sql-query-sqrt.md), [VIER KANT](sql-query-square.md), [TAN](sql-query-tan.md), [TRUNC](sql-query-trunc.md) |
|[Ruimtelijke-functies](sql-query-spatial-functions.md)|De ruimtelijke functies uitvoeren van een bewerking op een invoerwaarde ruimtelijke index en een numerieke of Booleaanse waarde retourneren. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md), [ST_WITHIN](sql-query-st-within.md) |
|[Tekenreeksfuncties](sql-query-string-functions.md)|De tekenreeks-functies uitvoeren van een bewerking op een tekenreekswaarde voor invoer en retourneert een tekenreeks, een numerieke of Booleaanse waarde. | [concat](sql-query-concat.md), [bevat](sql-query-contains.md), [ENDSWITH](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [Left](sql-query-left.md), [LENGTH](sql-query-length.md), [Lower](sql-query-lower.md), [LTrim](sql-query-ltrim.md), [replace](sql-query-replace.md), [replica](sql-query-replicate.md), [Reverse](sql-query-reverse.md), [Right](sql-query-right.md), [RTrim](sql-query-rtrim.md), [ STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [StringToObject](sql-query-stringtoobject.md), [subtekenreeks](sql-query-substring.md), [toString](sql-query-tostring.md), [Trim](sql-query-trim.md), [Upper](sql-query-upper.md) |
|[Controle van functies van het type](sql-query-type-checking-functions.md)|Met de typecontrolefuncties kunt u het type van een expressie in SQL-query's controleren. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md), [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Ingebouwde versus door de gebruiker gedefinieerde functies (Udf's)

Als u momenteel een door de gebruiker gedefinieerde functie (UDF) gebruikt waarvoor een ingebouwde functie nu beschikbaar is, wordt de bijbehorende ingebouwde functie sneller uitgevoerd en efficiënter.

## <a name="built-in-versus-ansi-sql-functions"></a>Ingebouwde versus ANSI SQL-functies

Het belangrijkste verschil tussen Cosmos DB functies en ANSI SQL-functies is dat Cosmos DB-functies zijn ontworpen om goed te werken met schemaloze en gemengde schema gegevens. Als er bijvoorbeeld een eigenschap ontbreekt of een niet-numerieke waarde heeft, zoals `unknown`, wordt het item overgeslagen in plaats van dat er een fout wordt geretourneerd.

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Cosmos DB](introduction.md)
- [Matrixfuncties](sql-query-array-functions.md)
- [Datum-en tijd functies](sql-query-date-time-functions.md)
- [Wiskundige functies](sql-query-mathematical-functions.md)
- [Ruimtelijke-functies](sql-query-spatial-functions.md)
- [Tekenreeksfuncties](sql-query-string-functions.md)
- [Controle van functies van het type](sql-query-type-checking-functions.md)
- [Door de gebruiker gedefinieerde functies](sql-query-udfs.md)
- [Aggregates](sql-query-aggregates.md)
