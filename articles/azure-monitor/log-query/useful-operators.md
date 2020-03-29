---
title: Nuttige operatoren in Azure Monitor-logboekquery's | Microsoft Documenten
description: Algemene functies die u gebruiken voor verschillende scenario's in azure monitorlogboekquery's.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: ff63b9b7027e99c70971230936ed98186c2208e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75397708"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Nuttige operatoren in Azure Monitor-logboekquery's

De onderstaande tabel biedt een aantal algemene functies die u gebruiken voor verschillende scenario's in azure monitorlogboekquery's.

## <a name="useful-operators"></a>Nuttige operatoren

Categorie                                |Relevante analysefunctie
----------------------------------------|----------------------------------------
Aliassen selecteren en kolom            |`project`, `project-away`, `extend`
Tijdelijke tabellen en constanten          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Vergelijkings- en tekenreeksoperatoren         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Algemene tekenreeksfuncties                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Algemene wiskundige functies                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Tekst ontkadern                            |`extract()`, `extractjson()`, `parse`, `split()`
Beperking van de uitvoer                         |`take`, `limit`, `top`, `sample`
Datumfuncties                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Groepering en aggregatie                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Joins en Vakbonden                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Sorteervolgorde                             |`sort`, `order` 
Dynamisch object (JSON en array)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Logische operators                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Machine learning                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>Volgende stappen

- Ga door een les over de [schrijflogboekquery's in Azure Monitor](get-started-queries.md).
