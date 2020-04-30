---
title: CETAS in Synapse SQL
description: CETAS gebruiken met Synapse SQL
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 71bc20680467d270436e28190bb49db5b9313ca0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424025"
---
# <a name="cetas-with-synapse-sql"></a>CETAS met Synapse SQL

In SQL-groep of SQL-aanvraag (preview) kunt u een externe tabel maken als SELECT (CETAS) gebruiken om de volgende taken uit te voeren:  

- Een externe tabel maken
- De resultaten van een Transact-SQL-instructie SELECT parallel exporteren naar

  - Hadoop
  - Azure Storage Blob
  - Azure Data Lake Storage Gen2

## <a name="cetas-in-sql-pool"></a>CETAS in SQL-groep

Schakel voor de SQL-groep het CETAS-gebruik en de syntaxis het selectie vakje [externe tabel maken als artikel selecteren in](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) . Raadpleeg voor meer informatie over CTAS met SQL-pool het artikel [Create Table als selecteren](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) .

## <a name="cetas-in-sql-on-demand"></a>CETAS in SQL op aanvraag

Wanneer u de SQL on-demand-resource gebruikt, wordt CETAS gebruikt om een externe tabel te maken en query resultaten te exporteren naar Azure Storage Blob of Azure Data Lake Storage Gen2.

## <a name="syntax"></a>Syntaxis

```syntaxsql
CREATE EXTERNAL TABLE [ [database_name  . [ schema_name ] . ] | schema_name . ] table_name
    WITH (
        LOCATION = 'path_to_folder',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
)
    AS <select_statement>  
[;]

<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>
```

## <a name="arguments"></a>Argumenten

*[[ *database_name* . [ *SCHEMA_NAME* ]. ] | *SCHEMA_NAME* . ] *table_name**

De naam van een tot drie delen van de tabel die u wilt maken. Voor een externe tabel slaat SQL on demand alleen de meta gegevens van de tabel op. Er worden geen werkelijke gegevens verplaatst of opgeslagen in SQL op aanvraag.

LOCATION = *' path_to_folder '*

Hiermee wordt aangegeven waar de resultaten van de SELECT-instructie in de externe gegevens bron moeten worden geschreven. De hoofdmap is de gegevenslocatie die in de externe gegevensbron is opgegeven. De locatie moet verwijzen naar een map en een navolgende/hebben. Voor beeld: aggregated_data/

DATA_SOURCE = *external_data_source_name*

Hiermee geeft u de naam van het externe gegevens bron object op dat de locatie bevat waar de externe gegevens worden opgeslagen. Als u een externe gegevens bron wilt maken, gebruikt u [externe gegevens bron maken (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source).

FILE_FORMAT = *external_file_format_name*

Hiermee geeft u de naam op van het externe bestands indelings object dat de indeling van het externe gegevens bestand bevat. Als u een externe bestands indeling wilt maken, gebruikt u [externe BESTANDS indeling maken (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format). Alleen externe bestands indelingen met de indeling ' PARQUET ' worden momenteel ondersteund.

MET *<common_table_expression>*

Hiermee geeft u een tijdelijke benoemde resultatenset, ook wel een CTE (common table Expression) genoemd. Zie [met common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)voor meer informatie.

Selecteer <select_criteria>

De nieuwe tabel wordt gevuld met de resultaten van een SELECT-instructie. *select_criteria* is de hoofd tekst van de instructie SELECT die bepaalt welke gegevens naar de nieuwe tabel moeten worden gekopieerd. Zie [Select (Transact-SQL) (Engelstalig)](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)voor meer informatie over SELECT-instructies.

## <a name="permissions"></a>Machtigingen

U moet machtigingen hebben om Mapinhoud weer te geven en te schrijven naar de map locatie om CETAS te kunnen gebruiken.

## <a name="examples"></a>Voorbeelden

In deze voor beelden wordt CETAS gebruikt voor het opslaan van het totale aantal geaggregeerde populaties per jaar en status in een aggregated_data map die zich in de population_ds gegevens bron bevindt.

Dit voor beeld is afhankelijk van de referentie, de gegevens bron en de externe bestands indeling die eerder is gemaakt. Raadpleeg het document [externe tabellen](develop-tables-external-tables.md) . Als u query resultaten wilt opslaan in een andere map in dezelfde gegevens bron, wijzigt u het argument locatie. Als u resultaten wilt opslaan in een ander opslag account, moet u een andere gegevens bron maken en gebruiken voor DATA_SOURCE argument.

> [!NOTE]
> De volgende voor beelden gebruiken een openbaar Azure open data storage-account. Het is alleen-lezen. Als u deze query's wilt uitvoeren, moet u de gegevens bron opgeven waarvoor u schrijf machtigingen hebt.

```sql
-- use CETAS to export select statement with OPENROWSET result to  storage
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM
    OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=*/*.parquet',
    FORMAT='PARQUET') AS [r]
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

In het voor beeld hieronder wordt een externe tabel gebruikt als de bron voor CETAS. Dit is afhankelijk van de referentie, de gegevens bron, de externe bestands indeling en de externe tabel die eerder is gemaakt. Raadpleeg het document [externe tabellen](develop-tables-external-tables.md) .

```sql
-- use CETAS with select from external table
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM census_external_table
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>Ondersteunde gegevens typen

CETAS kan worden gebruikt om resultaten sets op te slaan met de volgende SQL-gegevens typen:

- binair
- varbinary
- char
- varchar
- date
- tijd
- datetime2
- decimal
- numeriek
- float
- werkelijk
- bigint
- int
- smallint
- tinyint
- bit

LOBs kan niet worden gebruikt met CETAS.

De volgende gegevens typen kunnen niet worden gebruikt in het gedeelte SELECT of CETAS:

- nchar
- nvarchar
- datum/tijd
- smalldatetime
- date time offset
- financieel
- smallmoney
- uniqueidentifier

## <a name="next-steps"></a>Volgende stappen

U kunt proberen een query uit te zoeken naar [Spark-tabellen](develop-storage-files-spark-tables.md).
