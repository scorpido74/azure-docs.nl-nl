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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424025"
---
# <a name="cetas-with-synapse-sql"></a>CETAS met Synapse SQL

In SQL-groep of SQL on-demand (voorbeeld) u EXTERNE TABEL MAKEN ALS SELECT (CETAS) gebruiken om de volgende taken te voltooien:  

- Een externe tabel maken
- Tegelijkertijd de resultaten van een Transact-SQL SELECT-instructie exporteren naar

  - Hadoop
  - Azure Storage Blob
  - Azure Data Lake Storage Gen2

## <a name="cetas-in-sql-pool"></a>CETAS in SQL-pool

Voor SQL-groep, CETAS-gebruik en syntaxis schakelt u het artikel [EXTERNE TABEL MAKEN ALS SELECT in.](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Zie bovendien voor richtlijnen over CTAS met SQL-pool het artikel [TABEL MAKEN als SELECT.](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="cetas-in-sql-on-demand"></a>CETAS in SQL on-demand

Wanneer cetas de SQL on-demand bron gebruikt, wordt cetas gebruikt om een externe tabel te maken en queryresultaten te exporteren naar Azure Storage Blob of Azure Data Lake Storage Gen2.

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

*[ *database_name* . [ *schema_name* ] . ] | *schema_name* . ] *table_name**

De naam van één tot drie delen van de te maken tabel. Voor een externe tabel slaat SQL on-demand alleen de tabelmetagegevens op. Er worden geen werkelijke gegevens verplaatst of opgeslagen in SQL on-demand.

LOCATIE = *'path_to_folder'*

Hiermee geeft u op waar de resultaten van de select-instructie op de externe gegevensbron moeten worden geschreven. De hoofdmap is de gegevenslocatie die in de externe gegevensbron is opgegeven. LOCATIE moet naar een map wijzen en een trailing hebben /. Voorbeeld: aggregated_data/

DATA_SOURCE = *external_data_source_name*

Hiermee geeft u de naam op van het externe gegevensbronobject dat de locatie bevat waar de externe gegevens worden opgeslagen. Als u een externe gegevensbron wilt maken, gebruikt u [EXTERNE GEGEVENSBRON MAKEN (Transact-SQL).](develop-tables-external-tables.md#create-external-data-source)

FILE_FORMAT = *external_file_format_name*

Hiermee geeft u de naam op van het object externe bestandsindeling dat de indeling voor het externe gegevensbestand bevat. Als u een externe bestandsindeling wilt maken, gebruikt u [EXTERNE BESTANDSINDELING MAKEN (Transact-SQL).](develop-tables-external-tables.md#create-external-file-format) Alleen externe bestandsindelingen met FORMAT='PARQUET' worden momenteel ondersteund.

MET *<common_table_expression>*

Hiermee geeft u een tijdelijke benoemde resultaatset op, die een gemeenschappelijke tabelexpressie (CTE) wordt genoemd. Zie [WITH common_table_expression (Transact-SQL) voor](/sql/t-sql/queries/with-common-table-expression-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)meer informatie.

SELECTEER <select_criteria>

Hiermee wordt de nieuwe tabel gevuld met de resultaten van een select-instructie. *select_criteria* is de hoofdtekst van de SELECT-instructie die bepaalt welke gegevens naar de nieuwe tabel moeten worden gekopieerd. Zie [SELECT (Transact-SQL) voor](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)informatie over SELECT-instructies.

## <a name="permissions"></a>Machtigingen

U moet machtigingen hebben om mapinhoud te vermelden en naar de map LOCATIE te schrijven om CETAS te laten werken.

## <a name="examples"></a>Voorbeelden

In deze voorbeelden wordt CETAS gebruikt om de totale populatie die per jaar is samengevoegd en om te geven aan een map aggregated_data die zich in de population_ds gegevensbron bevindt.

Dit voorbeeld is gebaseerd op de referentie, gegevensbron en externe bestandsindeling die eerder zijn gemaakt. Raadpleeg het document [met externe tabellen.](develop-tables-external-tables.md) Als u queryresultaten wilt opslaan in een andere map in dezelfde gegevensbron, wijzigt u het argument LOCATIE. Als u resultaten wilt opslaan in een ander opslagaccount, maakt en gebruikt u een andere gegevensbron voor DATA_SOURCE argument.

> [!NOTE]
> De volgende voorbeelden gebruiken een openbaar Azure Open Data-opslagaccount. Het is alleen-lezen. Als u deze query's wilt uitvoeren, moet u de gegevensbron verstrekken waarvoor u schrijfmachtigingen hebt.

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

In het onderstaande voorbeeld wordt een externe tabel gebruikt als bron voor CETAS. Het is gebaseerd op de referentie, gegevensbron, externe bestandsindeling en externe tabel die eerder zijn gemaakt. Raadpleeg het document [met externe tabellen.](develop-tables-external-tables.md)

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

## <a name="supported-data-types"></a>Ondersteunde gegevenstypen

CETAS kan worden gebruikt om resultaatsets op te slaan met de volgende SQL-gegevenstypen:

- binair
- varbinary varbinary
- Char
- varchar
- date
- tijd
- datetime2
- decimal
- numeriek
- float
- real
- bigint
- int
- smallint
- tinyint
- bit

LOB's kunnen niet worden gebruikt met CETAS.

Volgende gegevenstypen kunnen niet worden gebruikt in SELECT-gedeelte van CETAS:

- Nchar
- nvarchar
- datum/tijd
- smalldatetijd
- datumtijdverschuiving
- Geld
- kleingeld
- uniqueidentifier

## <a name="next-steps"></a>Volgende stappen

U [spark-tabellen](develop-storage-files-spark-tables.md)proberen op te vragen.
