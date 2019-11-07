---
title: Overzichten T-SQL
description: Koppelingen naar de documentatie voor T-SQL-instructies die worden ondersteund in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 05/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 861675941693697a20da189edebe33d91918782c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685513"
---
# <a name="t-sql-statements-supported-in-azure-sql-data-warehouse"></a>T-SQL-instructies die worden ondersteund in Azure SQL Data Warehouse
Koppelingen naar de documentatie voor T-SQL-instructies die worden ondersteund in Azure SQL Data Warehouse.

## <a name="data-definition-language-ddl-statements"></a>DDL-instructies (Data Definition Language)
* [ALTER DATABASE](https://msdn.microsoft.com/library/mt204042.aspx)
* [INDEX WIJZIGEN](https://msdn.microsoft.com/library/ms188388.aspx)
* [Gerealiseerde weer gave wijzigen](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) (preview-versie) 
* [ALTER-PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
* [SCHEMA WIJZIGEN](https://msdn.microsoft.com/library/ms173423.aspx)
* [ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)
* [COLUMN STORE-INDEX MAKEN](https://msdn.microsoft.com/library/gg492153.aspx)
* [CREATE DATABASE](https://msdn.microsoft.com/library/mt204021.aspx)
* [DATA BASE-SCOPED REFERENTIE MAKEN](https://msdn.microsoft.com/library/mt270260.aspx)
* [EXTERNE GEGEVENS BRON MAKEN](https://msdn.microsoft.com/library/dn935022.aspx)
* [CREATE EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/dn935026.aspx)
* [EXTERNE TABEL MAKEN](https://msdn.microsoft.com/library/dn935021.aspx)
* [FUNCTIE MAKEN](https://msdn.microsoft.com/library/mt203952.aspx)
* [INDEX MAKEN](https://msdn.microsoft.com/library/ms188783.aspx)
* [Gerealiseerde weer gave maken als selecteren](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (preview-versie) 
* [PROCEDURE MAKEN](https://msdn.microsoft.com/library/ms187926.aspx)
* [SCHEMA MAKEN](https://msdn.microsoft.com/library/ms189462.aspx)
* [CREATE STATISTICS](https://msdn.microsoft.com/library/ms188038.aspx)
* [CREATE TABLE](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [WEER GAVE MAKEN](https://msdn.microsoft.com/library/ms187956.aspx)
* [CLASSIFICATIE VAN WERK BELASTING MAKEN](/sql/t-sql/statements/create-workload-classifier-transact-sql)
* [EXTERNE GEGEVENS BRON VERWIJDEREN](https://msdn.microsoft.com/library/mt146367.aspx)
* [EXTERNE BESTANDS INDELING VERWIJDEREN](https://msdn.microsoft.com/library/mt146379.aspx)
* [EXTERNE TABEL VERWIJDEREN](https://msdn.microsoft.com/library/mt130698.aspx)
* [DROP INDEX](https://msdn.microsoft.com/library/ms176118.aspx)
* [PROCEDURE VOOR NEERZETTEN](https://msdn.microsoft.com/library/ms174969.aspx)
* [STATISTIEKEN NEERZETTEN](https://msdn.microsoft.com/library/ms175075.aspx)
* [TABEL NEERZETTEN](https://msdn.microsoft.com/library/ms173790.aspx)
* [SCHEMA VERWIJDEREN](https://msdn.microsoft.com/library/ms186751.aspx)
* [DROP-WEER GAVE](https://msdn.microsoft.com/library/ms173492.aspx)
* [CLASSIFICATIE VAN VERVOLG WORKLOAD](/sql/t-sql/statements/drop-workload-classifier-transact-sql)
* [DOMEINNAAM](https://msdn.microsoft.com/library/mt631611.aspx)
* [RESULT_SET_CACHING INSTELLEN](/sql/t-sql/statements/set-result-set-caching-transact-sql) 
* [TRUNCATE TABLE](https://msdn.microsoft.com/library/ms177570.aspx)
* [UPDATE STATISTICS](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>DML-instructies (data manipulatie Language)
* [DELETE](https://msdn.microsoft.com/library/ms189835.aspx)
* [INSERT](https://msdn.microsoft.com/library/ms174335.aspx)
* [BIJWERK](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>Database console-opdrachten
* [DBCC DROPCLEANBUFFERS](https://msdn.microsoft.com/library/ms187762.aspx)
* [DBCC DROPRESULTSETCACHE](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest) (preview-versie)
* [DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [DBCC SHRINKLOG](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)
* [DBCC SHOWRESULTCACHESPACEUSED](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql) (preview-versie)
* [DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>Query-instructies
* [UITGESCHAKELD](https://msdn.microsoft.com/library/ms189499.aspx)
* [MET common_table_expression](https://msdn.microsoft.com/library/ms175972.aspx)
* [BEHALVE en Intersect](https://msdn.microsoft.com/library/ms188055.aspx)
* [BESPREKEN](https://msdn.microsoft.com/library/mt631615.aspx)
* [Van](https://msdn.microsoft.com/library/ms177634.aspx)
* [DRAAIEN en depivot gebruiken](https://msdn.microsoft.com/library/ms177410.aspx)
* [GROEPEREN OP](https://msdn.microsoft.com/library/ms177673.aspx)
* [DIE](https://msdn.microsoft.com/library/ms180199.aspx)
* [SORTEREN OP](https://msdn.microsoft.com/library/ms188385.aspx)
* [OPTION](https://msdn.microsoft.com/library/ms190322.aspx)
* [RÉUNION](https://msdn.microsoft.com/library/ms180026.aspx)
* [POSITIE](https://msdn.microsoft.com/library/ms188047.aspx)
* [Boven](https://msdn.microsoft.com/library/ms189463.aspx)
* [Antialiasing](https://msdn.microsoft.com/library/mt631614.aspx)
* [Zoek voorwaarde](https://msdn.microsoft.com/library/ms173545.aspx)
* [Subquery's](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>Instructies voor beveiliging
* Machtigingen: [verlenen](https://msdn.microsoft.com/library/ms187965.aspx), [weigeren](https://msdn.microsoft.com/library/ms188338.aspx), [intrekken](https://msdn.microsoft.com/library/ms187728.aspx)
* [AUTORISATIE WIJZIGEN](https://msdn.microsoft.com/library/ms187359.aspx)
* [CERTIFICAAT WIJZIGEN](https://msdn.microsoft.com/library/ms189511.aspx)
* [ALTER DATA BASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb630389.aspx)
* [AANMELDING WIJZIGEN](https://msdn.microsoft.com/library/ms189828.aspx)
* [HOOFD SLEUTEL WIJZIGEN](https://msdn.microsoft.com/library/ms186937.aspx)
* [ROL WIJZIGEN](https://msdn.microsoft.com/library/ms189775.aspx)
* [GEBRUIKER WIJZIGEN](https://msdn.microsoft.com/library/ms176060.aspx)
* [BACK-UPCERTIFICAAT](https://msdn.microsoft.com/library/ms178578.aspx)
* [HOOFD SLEUTEL SLUITEN](https://msdn.microsoft.com/library/ms188387.aspx)
* [CERTIFICAAT MAKEN](https://msdn.microsoft.com/library/ms187798.aspx)
* [DATABASE VERSLEUTELINGS SLEUTEL MAKEN](https://msdn.microsoft.com/library/bb677241.aspx)
* [AANMELDING MAKEN](https://msdn.microsoft.com/library/ms189751.aspx)
* [HOOFD SLEUTEL MAKEN](https://msdn.microsoft.com/library/ms174382.aspx)
* [ROL MAKEN](https://msdn.microsoft.com/library/ms187936.aspx)
* [GEBRUIKER MAKEN](https://msdn.microsoft.com/library/ms173463.aspx)
* [CERTIFICAAT VERWIJDEREN](https://msdn.microsoft.com/library/ms179906.aspx)
* [VERSLEUTELINGS SLEUTEL VAN DATA BASE VERWIJDEREN](https://msdn.microsoft.com/library/bb630256.aspx)
* [AANMELDING VERWIJDEREN](https://msdn.microsoft.com/library/ms188012.aspx)
* [HOOFD SLEUTEL VERWIJDEREN](https://msdn.microsoft.com/library/ms180071.aspx)
* [ROL NEERZETTEN](https://msdn.microsoft.com/library/ms174988.aspx)
* [GEBRUIKER WEGHALEN](https://msdn.microsoft.com/library/ms189438.aspx)
* [HOOFD SLEUTEL OPENEN](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over [T-SQL-taal elementen in Azure SQL Data Warehouse](sql-data-warehouse-reference-tsql-language-elements.md)en [systeem weergaven in Azure SQL Data Warehouse](sql-data-warehouse-reference-tsql-system-views.md).
