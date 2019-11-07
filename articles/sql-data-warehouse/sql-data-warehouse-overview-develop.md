---
title: Resources voor het ontwikkelen van een Data Warehouse in azure
description: Ontwikkel concepten, ontwerp beslissingen, aanbevelingen en codeer technieken voor SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: a6cb7937bdd5dea9eb1a48b2b350db9077431fe0
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645620"
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Ontwerp beslissingen en coderings technieken voor SQL Data Warehouse
Bekijk deze ontwikkel artikelen om meer inzicht te krijgen in belang rijke beslissingen, aanbevelingen en coderings technieken voor SQL Data Warehouse.

## <a name="key-design-decisions"></a>Voornaamste ontwerp beslissingen
De volgende artikelen markeren concepten en ontwerp beslissingen voor het ontwikkelen van een gedistribueerd data warehouse met behulp van SQL Data Warehouse:

* [inbel][connections]
* [gelijktijdigheid][concurrency]
* [transacties][transactions]
* [door de gebruiker gedefinieerde schema's][user-defined schemas]
* [tabel distributie][table distribution]
* [tabel indexen][table indexes]
* [tabel partities][table partitions]
* [CTAS][CTAS]
* [statistieken][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Aanbevelingen voor ontwikkeling en code ring
Deze artikelen markeren specifieke coderings technieken, tips en aanbevelingen voor het ontwikkelen van uw SQL Data Warehouse:

* [opgeslagen procedures][stored procedures]
* [Labels][labels]
* [Weergaven][views]
* [tijdelijke tabellen][temporary tables]
* [dynamische SQL][dynamic SQL]
* [lussen][looping]
* [groeperen op Opties][group by options]
* [variabele toewijzing][variable assignment]

## <a name="next-steps"></a>Volgende stappen
Zie [SQL Data Warehouse T-SQL-instructies](sql-data-warehouse-reference-tsql-statements.md)voor meer informatie.

<!--Image references-->

<!--Article references-->
[concurrency]: ./resource-classes-for-workload-management.md
[connections]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamic SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by options]: ./sql-data-warehouse-develop-group-by-options.md
[labels]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[statistics]: ./sql-data-warehouse-tables-statistics.md
[stored procedures]: ./sql-data-warehouse-develop-stored-procedures.md
[table distribution]: ./sql-data-warehouse-tables-distribute.md
[table indexes]: ./sql-data-warehouse-tables-index.md
[table partitions]: ./sql-data-warehouse-tables-partition.md
[temporary tables]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[user-defined schemas]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[views]: ./sql-data-warehouse-develop-views.md


<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
