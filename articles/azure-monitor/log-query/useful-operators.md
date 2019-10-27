---
title: Handige Opera tors in Azure Monitor-logboek query's | Microsoft Docs
description: Algemene functies die moeten worden gebruikt voor verschillende scenario's in Azure Monitor-logboek query's.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 022a9f638b3a7d8ae4ebeff8062f258ada7a14f8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932885"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Nuttige Opera tors in Azure Monitor-logboek query's

De onderstaande tabel bevat enkele algemene functies voor het gebruik van verschillende scenario's in Azure Monitor-logboek query's.

## <a name="useful-operators"></a>Nuttige operatoren

Category                                |Relevante analyse functie
----------------------------------------|----------------------------------------
Selectie-en kolom aliassen            |`project`, `project-away`, `extend`
Tijdelijke tabellen en constanten          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Vergelijkings-en teken reeks operators         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Veelgebruikte teken reeks functies                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Algemene wiskundige functies                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Tekst parseren                            |`extract()`, `extractjson()`, `parse`, `split()`
Uitvoer beperken                         |`take`, `limit`, `top`, `sample`
Datum functies                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Groepering en aggregatie                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Samen voegingen en samen voegingen                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Sorteren, ordenen                             |`sort`, `order` 
Dynamisch object (JSON en array)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Logische Opera tors                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Machine Learning                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>Volgende stappen

- Door loop een les over de [query's in het schrijf logboek in azure monitor](get-started-queries.md).
