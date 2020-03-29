---
title: SQL naar Azure Monitor-logboekqueryspiekblad | Microsoft Documenten
description: Help voor gebruikers die bekend zijn met SQL bij het schrijven van logquery's in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: c76ab145fd2fdd077075b345ecac9c6a473f2369
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75365186"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>SQL naar Azure Monitor-logboekqueryspiekblad 

In de onderstaande tabel kunnen gebruikers die bekend zijn met SQL de Kusto-querytaal leren om logboekquery's in Azure Monitor te schrijven. Bekijk de opdracht T-SQL voor het oplossen van veelvoorkomende scenario's en het equivalent in een Azure Monitor-logboekquery.

## <a name="sql-to-azure-monitor"></a>SQL naar Azure-monitor

Beschrijving                             |SQL-query                                                                                          |Azure Monitor-logboekquery
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Alle gegevens uit een tabel selecteren            |`SELECT * FROM dependencies`                                                                       |<code>dependencies</code>
Specifieke kolommen uit een tabel selecteren    |`SELECT name, resultCode FROM dependencies`                                                        |<code>dependencies <br>&#124; project name, resultCode</code>
100 records uit een tabel selecteren         |`SELECT TOP 100 * FROM dependencies`                                                               |<code>dependencies <br>&#124; take 100</code>
Evaluatie null                         |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL`                                          |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
String vergelijking: gelijkheid             |`SELECT * FROM dependencies WHERE name = "abcde"`                                                  |<code>dependencies <br>&#124; where name == "abcde"</code>
Tekenreeksvergelijking: subtekenreeks            |`SELECT * FROM dependencies WHERE name like "%bcd%"`                                                   |<code>dependencies <br>&#124; where name contains "bcd"</code>
Tekenreeksvergelijking: wildcard             |`SELECT * FROM dependencies WHERE name like "abc%"`                                                |<code>dependencies <br>&#124; where name startswith "abc"</code>
Datumvergelijking: laatste 1 dag             |`SELECT * FROM dependencies WHERE timestamp > getdate()-1`                                         |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Datumvergelijking: datumbereik             |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'`                 |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Booleaanse vergelijking                      |`SELECT * FROM dependencies WHERE !(success)`                                                      |<code>dependencies <br>&#124; where success == "False" </code>
Sorteren                                    |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc`                                  |<code>dependencies <br>&#124; order by timestamp asc </code>
Distinct                                |`SELECT DISTINCT name, type  FROM dependencies`                                                    |<code>dependencies <br>&#124; summarize by name, type </code>
Groepering, Aggregatie                   |`SELECT name, AVG(duration) FROM dependencies GROUP BY name`                                       |<code>dependencies <br>&#124; summarize avg(duration) by name </code>
Kolomaliassen, Uitbreiden                  |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name`             |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
Top n records op maat                |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc`        |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
Union                                   |`SELECT * FROM dependencies UNION SELECT * FROM exceptions`                                        |<code>union dependencies, exceptions</code>
Unie: met voorwaarden                  |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions WHERE value < 5`                |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Koppelen                                    |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>Volgende stappen

- Ga door de lessen over [het schrijven van logquery's in Azure Monitor](get-started-queries.md).
