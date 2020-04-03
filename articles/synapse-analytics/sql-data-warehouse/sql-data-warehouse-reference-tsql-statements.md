---
title: Overzichten T-SQL
description: Koppelingen naar de documentatie voor T-SQL-instructies die worden ondersteund in synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 011a850a44948bf5eee28a40d59dcf7672d866e8
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586370"
---
# <a name="t-sql-statements-supported-in-synapse-sql-pool"></a>T-SQL-instructies ondersteund in Synapse SQL-pool

Koppelingen naar de documentatie voor T-SQL-instructies die worden ondersteund in synapse SQL-pool.

## <a name="data-definition-language-ddl-statements"></a>DDL-instructies (Data Definition Language)

* [DATABASE WIJZIGEN](https://msdn.microsoft.com/library/mt204042.aspx)
* [ALTER INDEX](https://msdn.microsoft.com/library/ms188388.aspx)
* [GEMATERIALISEERDE WEERGAVE WIJZIGEN](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) (voorbeeld) 
* [WIJZIGINGSPROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
* [SCHEMA WIJZIGEN](https://msdn.microsoft.com/library/ms173423.aspx)
* [ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)
* [COLUMNSTORE-INDEX MAKEN](https://msdn.microsoft.com/library/gg492153.aspx)
* [DATABASE MAKEN](https://msdn.microsoft.com/library/mt204021.aspx)
* [DATABASESCOPED-REFERENTIE MAKEN](https://msdn.microsoft.com/library/mt270260.aspx)
* [EXTERNE GEGEVENSBRON MAKEN](https://msdn.microsoft.com/library/dn935022.aspx)
* [CREATE EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/dn935026.aspx)
* [EXTERNE TABEL MAKEN](https://msdn.microsoft.com/library/dn935021.aspx)
* [MAKEN, FUNCTIE](https://msdn.microsoft.com/library/mt203952.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [GEMATERIALISEERDE WEERGAVE MAKEN ALS SELECTEREN](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (voorbeeld) 
* [PROCEDURE MAKEN](https://msdn.microsoft.com/library/ms187926.aspx)
* [SCHEMA MAKEN](https://msdn.microsoft.com/library/ms189462.aspx)
* [STATISTIEKEN MAKEN](https://msdn.microsoft.com/library/ms188038.aspx)
* [TABEL MAKEN](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [WEERGAVE MAKEN](https://msdn.microsoft.com/library/ms187956.aspx)
* [WORKLOADKLASSEMAKEN](/sql/t-sql/statements/create-workload-classifier-transact-sql)
* [EXTERNE GEGEVENSBRON NEERZETTEN](https://msdn.microsoft.com/library/mt146367.aspx)
* [EXTERNE BESTANDSINDELING NEERZETTEN](https://msdn.microsoft.com/library/mt146379.aspx)
* [EXTERNE TABEL NEERZETTEN](https://msdn.microsoft.com/library/mt130698.aspx)
* [DROP INDEX](https://msdn.microsoft.com/library/ms176118.aspx)
* [VALPROCEDURE](https://msdn.microsoft.com/library/ms174969.aspx)
* [DROP STATISTIEKEN](https://msdn.microsoft.com/library/ms175075.aspx)
* [KEUZELIJST LATEN VALLEN](https://msdn.microsoft.com/library/ms173790.aspx)
* [DROP-SCHEMA](https://msdn.microsoft.com/library/ms186751.aspx)
* [DROP WEERGAVE](https://msdn.microsoft.com/library/ms173492.aspx)
* [KLASSEKLASSER DROP-WERKBELASTING](/sql/t-sql/statements/drop-workload-classifier-transact-sql)
* [Hernoemen](https://msdn.microsoft.com/library/mt631611.aspx)
* [SET RESULT_SET_CACHING](/sql/t-sql/statements/set-result-set-caching-transact-sql) 
* [Tabel Afkappen](https://msdn.microsoft.com/library/ms177570.aspx)
* [UPDATE STATISTICS](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>DML-instructies (Data Manipulation Language)

* [Verwijderen](https://msdn.microsoft.com/library/ms189835.aspx)
* [Invoegen](https://msdn.microsoft.com/library/ms174335.aspx)
* [Update](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>Opdrachten voor databaseconsole

* [DBCC DROPCLEANBUFFERS](https://msdn.microsoft.com/library/ms187762.aspx)
* [DBCC DROPRESULTSETCACHE](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest) (Voorbeeld)
* [DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [DBCC SHRINKLOG](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)
* [DBCC SHOWRESULTCACHESPACEUSED](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql) (Voorbeeld)
* [DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>Query-instructies

* [Selecteer](https://msdn.microsoft.com/library/ms189499.aspx)
* [MET common_table_expression](https://msdn.microsoft.com/library/ms175972.aspx)
* [EXCEPT en INTERSECT](https://msdn.microsoft.com/library/ms188055.aspx)
* [Uitleggen](https://msdn.microsoft.com/library/mt631615.aspx)
* [FROM](https://msdn.microsoft.com/library/ms177634.aspx)
* [Pivot en UNPIVOT gebruiken](https://msdn.microsoft.com/library/ms177410.aspx)
* [GROEPEREN OP](https://msdn.microsoft.com/library/ms177673.aspx)
* [HAVING](https://msdn.microsoft.com/library/ms180199.aspx)
* [ORDER BY](https://msdn.microsoft.com/library/ms188385.aspx)
* [Optie](https://msdn.microsoft.com/library/ms190322.aspx)
* [UNION](https://msdn.microsoft.com/library/ms180026.aspx)
* [Waar](https://msdn.microsoft.com/library/ms188047.aspx)
* [Boven](https://msdn.microsoft.com/library/ms189463.aspx)
* [Aliasing](https://msdn.microsoft.com/library/mt631614.aspx)
* [Zoekvoorwaarde](https://msdn.microsoft.com/library/ms173545.aspx)
* [Subquery's](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>Instructies voor beveiliging

* Machtigingen: [GRANT](https://msdn.microsoft.com/library/ms187965.aspx), [DENY](https://msdn.microsoft.com/library/ms188338.aspx), [INTREKKEN](https://msdn.microsoft.com/library/ms187728.aspx)
* [TOESTEMMING WIJZIGEN](https://msdn.microsoft.com/library/ms187359.aspx)
* [WIJZIGINGSCERTIFICAAT](https://msdn.microsoft.com/library/ms189511.aspx)
* [DATABASECODERINGSSLEUTEL WIJZIGEN](https://msdn.microsoft.com/library/bb630389.aspx)
* [INLOGGEN WIJZIGEN](https://msdn.microsoft.com/library/ms189828.aspx)
* [MASTER-SLEUTEL WIJZIGEN](https://msdn.microsoft.com/library/ms186937.aspx)
* [VERANDERINGROL](https://msdn.microsoft.com/library/ms189775.aspx)
* [GEBRUIKER WIJZIGEN](https://msdn.microsoft.com/library/ms176060.aspx)
* [BACK-UPCERTIFICAAT](https://msdn.microsoft.com/library/ms178578.aspx)
* [HOOFDSLEUTEL SLUITEN](https://msdn.microsoft.com/library/ms188387.aspx)
* [CERTIFICAAT MAKEN](https://msdn.microsoft.com/library/ms187798.aspx)
* [DATABASEVERSLEUTELINGSSLEUTEL MAKEN](https://msdn.microsoft.com/library/bb677241.aspx)
* [AANMELDING MAKEN](https://msdn.microsoft.com/library/ms189751.aspx)
* [HOOFDSLEUTEL MAKEN](https://msdn.microsoft.com/library/ms174382.aspx)
* [ROL MAKEN](https://msdn.microsoft.com/library/ms187936.aspx)
* [GEBRUIKER MAKEN](https://msdn.microsoft.com/library/ms173463.aspx)
* [CERTIFICAAT DROP](https://msdn.microsoft.com/library/ms179906.aspx)
* [VERSLEUTELINGSSLEUTEL VOOR DE DROP-DATABASE](https://msdn.microsoft.com/library/bb630256.aspx)
* [INLOGGEN LATEN VALLEN](https://msdn.microsoft.com/library/ms188012.aspx)
* [DE HOOFDSLEUTEL DROP](https://msdn.microsoft.com/library/ms180071.aspx)
* [DROP ROL](https://msdn.microsoft.com/library/ms174988.aspx)
* [GEBRUIKER DROP](https://msdn.microsoft.com/library/ms189438.aspx)
* [HOOFDSLEUTEL OPENEN](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>Volgende stappen

Zie [T-SQL-taalelementen in synapse SQL-pool](sql-data-warehouse-reference-tsql-language-elements.md)en [Systeemweergaven in Synapse SQL-pool](sql-data-warehouse-reference-tsql-system-views.md)voor meer referentiegegevens.
