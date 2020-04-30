---
title: Externe tabellen gebruiken met Synapse SQL
description: Gegevens bestanden lezen of schrijven met Synapse SQL
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 4d13d15fe950c89687acfca355d4ed183756536a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81423976"
---
# <a name="use-external-tables-with-synapse-sql"></a>Externe tabellen gebruiken met Synapse SQL

Een externe tabel verwijst naar gegevens die zich bevinden in Hadoop, Azure Storage BLOB of Azure Data Lake Storage. Externe tabellen worden gebruikt voor het lezen van gegevens uit bestanden of het schrijven van gegevens naar bestanden in Azure Storage. Met Synapse SQL kunt u externe tabellen gebruiken om gegevens te lezen en schrijven naar SQL-groep of SQL-op-aanvraag (preview).

## <a name="external-tables-in-sql-pool"></a>Externe tabellen in SQL-groep

In SQL-groep kunt u een externe tabel gebruiken voor het volgende:

- Query's uitvoeren op Azure Blob Storage en Azure Data Lake Gen2 met Transact-SQL-instructies.
- Importeer en sla gegevens op uit Azure Blob Storage en Azure Data Lake Storage in de SQL-groep.

Bij gebruik in combi natie met de [Create Table als Select](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) -instructie selecteert u in een externe tabel gegevens in een tabel in de SQL-groep. In aanvulling op de [instructie Copy](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)zijn externe tabellen handig voor het laden van gegevens. Zie [poly Base gebruiken voor het laden van gegevens uit Azure Blob Storage](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor een zelf studie die u kunt laden.

## <a name="external-tables-in-sql-on-demand-preview"></a>Externe tabellen in SQL on-demand (preview-versie)

Voor SQL on-demand gebruikt u een externe tabel voor het volgende:

- Query's uitvoeren op gegevens in Azure Blob Storage of Azure Data Lake Storage met Transact-SQL-instructies
- Sla SQL op aanvraag-query resultaten op in bestanden in Azure Blob Storage of Azure Data Lake Storage met behulp van [CETAS](develop-tables-cetas.md).

U kunt via de volgende stappen externe tabellen maken met behulp van SQL op aanvraag:

1. EXTERNE GEGEVENS BRON MAKEN
2. CREATE EXTERNAL FILE FORMAT
3. EXTERNE TABEL MAKEN

## <a name="create-external-data-source"></a>EXTERNE GEGEVENS BRON MAKEN

Externe gegevens bronnen worden gebruikt om verbinding te maken met opslag accounts. De volledige documentatie wordt [hier](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)beschreven.

## <a name="syntax-for-create-external-data-source"></a>De syntaxis voor het maken van een externe gegevens bron

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>' )
[;]
```

## <a name="arguments-for-create-external-data-source"></a>Argumenten voor het maken van externe gegevens bron

data_source_name: Hiermee geeft u de door de gebruiker gedefinieerde naam voor de gegevens bron op. De naam moet uniek zijn binnen de data base.

LOCATIE = `'<prefix>://<path>'` -levert het verbindings protocol en het pad naar de externe gegevens bron. Het pad kan een container bevatten in de vorm van `'<prefix>://<path>/container'`en een map in de vorm van `'<prefix>://<path>/container/folder'`.

| Externe gegevens bron        | Locatie voorvoegsel | Pad naar locatie                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Blob Storage          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Azure Data Lake Store gen 1 | `adl`           | `<storage_account>.azuredatalake.net`                 |
| Azure Data Lake Store gen 2 | `abfs[s]`       | `<container>@<storage_account>.dfs.core.windows.net`  |

## <a name="example-for-create-external-data-source"></a>Voor beeld voor het maken van een externe gegevens bron

In het volgende voor beeld wordt een externe gegevens bron gemaakt voor Azure Data Lake Gen2 die verwijst naar de data set New York:

```sql
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://newyorktaxidataset.azuredatalakestore.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Hiermee maakt u een object voor externe bestands indeling dat externe gegevens definieert die zijn opgeslagen in Azure Blob Storage of Azure Data Lake Storage. Het maken van een externe bestands indeling is een vereiste voor het maken van een externe tabel. De volledige documentatie is [hier](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Door een externe bestands indeling te maken, geeft u de werkelijke indeling op van de gegevens waarnaar wordt verwezen door een externe tabel.

## <a name="syntax-for-create-external-file-format"></a>De syntaxis voor het maken van een externe BESTANDS indeling

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
}
```

## <a name="arguments-for-create-external-file-format"></a>Argumenten voor het maken van een externe BESTANDS indeling

file_format_name: Hiermee geeft u een naam op voor de externe bestands indeling.

FORMAT_TYPE = [PARQUET | DELIMITEDTEXT]-Hiermee geeft u de indeling van de externe gegevens op.

- PARQUET: Hiermee geeft u een Parquet-indeling.
- DELIMITEDTEXT: Hiermee geeft u een tekst indeling met kolom scheidings tekens, ook wel veld scheidings genoemd.

FIELD_TERMINATOR = *field_terminator* -is alleen van toepassing op tekst bestanden met scheidings tekens. In het veld Terminator worden een of meer tekens opgegeven die het einde van elk veld (kolom) in het door tekst gescheiden bestand markeren. De standaard waarde is het sluis teken (ꞌ | ꞌ).

Voorbeelden:

- FIELD_TERMINATOR = |
- FIELD_TERMINATOR = ' '
- FIELD_TERMINATOR = ꞌ \ t ꞌ

STRING_DELIMITER = *string_delimiter* : Hiermee geeft u de veld Terminator op voor gegevens van het type teken reeks in het bestand met tekst gescheiden waarden. De teken reeks wordt een of meer tekens met een lengte en is inge sloten met enkele citaten. De standaard waarde is een lege teken reeks ("").

Voorbeelden:

- STRING_DELIMITER = ' "'
- STRING_DELIMITER = ' * '
- STRING_DELIMITER = ꞌ, ꞌ

FIRST_ROW = *First_row_int* -geeft het rijnummer op dat het eerst wordt gelezen en is van toepassing op alle bestanden. Als u de waarde instelt op twee, wordt de eerste rij in elk bestand (koprij) overgeslagen wanneer de gegevens worden geladen. Rijen worden overgeslagen op basis van het bestaan van rij-afsluitingen (/r/n,/r,/n).

USE_TYPE_DEFAULT = {TRUE | **False** } -Hiermee geeft u op hoe ontbrekende waarden in tekst bestanden met scheidings tekens moeten worden verwerkt bij het ophalen van gegevens uit het tekst bestand.

WAAR: als u gegevens ophaalt uit het tekst bestand, slaat u elke ontbrekende waarde op met behulp van het gegevens type van de standaard waarde voor de overeenkomende kolom in de definitie van de externe tabel. Vervang bijvoorbeeld een ontbrekende waarde door:

- 0 als de kolom is gedefinieerd als een numerieke kolom. Decimale kolommen worden niet ondersteund en veroorzaken een fout.
- Lege teken reeks ("") als de kolom een teken reeks kolom is.
- 1900-01-01 als de kolom een datum kolom is.

Onwaar: Sla alle ontbrekende waarden op als NULL. NULL-waarden die zijn opgeslagen met behulp van het woord NULL in het tekst bestand met scheidings tekens, worden geïmporteerd als de teken reeks ' NULL '.

Encoding = {' UTF8 ' | ' UTF16 '}-SQL on-demand kan UTF8-en UTF16-gecodeerde tekst bestanden met scheidings tekens lezen.

DATA_COMPRESSION = *data_compression_method* -dit argument geeft de gegevens compressie methode voor de externe gegevens aan. Bij het lezen van externe tabellen wordt deze genegeerd. Deze wordt alleen gebruikt bij het schrijven naar externe tabellen met behulp van [CETAS](develop-tables-cetas.md).

Het PARQUET-bestands indelings type ondersteunt de volgende compressie methoden:

- DATA_COMPRESSION = ' org. apache. Hadoop. io. compress. GzipCodec '
- DATA_COMPRESSION = ' org. apache. Hadoop. io. compress. SnappyCodec '

## <a name="example-for-create-external-file-format"></a>Voor beeld voor het maken van een externe BESTANDS indeling

In het volgende voor beeld wordt een externe bestands indeling voor de telling van bestanden gemaakt:

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>EXTERNE TABEL MAKEN

Met de opdracht externe tabel maken maakt u een externe tabel voor Synapse SQL om toegang te krijgen tot gegevens die zijn opgeslagen in Azure Blob Storage of Azure Data Lake Storage. 

## <a name="syntax-for-create-external-table"></a>Syntaxis voor het maken van een externe tabel

```syntaxsql
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )  
    WITH (
        LOCATION = 'folder_or_filepath',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name
    )  
[;]  

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]
```

## <a name="arguments-create-external-table"></a>Argumenten externe tabel maken

*{database_name. schema_name. table_name | schema_name. table_name | table_name}*

De naam van een tot drie delen van de tabel die u wilt maken. Voor een externe tabel slaat SQL on demand alleen de meta gegevens van de tabel op. Er worden geen werkelijke gegevens verplaatst of opgeslagen in SQL op aanvraag.

<column_definition>,... *n* ]

Het maken van een externe tabel ondersteunt de mogelijkheid om kolom naam, gegevens type, null-waarde en sortering te configureren. U kunt de standaard beperking niet gebruiken voor externe tabellen.

>[!IMPORTANT]
>De kolomdefinities, inclusief de gegevenstypen en het aantal kolommen, moeten overeenkomen met de gegevens in de externe bestanden. Als er sprake is van een afwijking worden de bestandsrijen geweigerd wanneer er een query voor de daadwerkelijke gegevens wordt uitgevoerd.

Bij het lezen van Parquet-bestanden kunt u alleen de kolommen opgeven die u wilt lezen en de rest overs Laan.

LOCATION = '*folder_or_filepath*'

Hiermee geeft u de map of het bestandspad en de bestands naam op voor de werkelijke gegevens in Azure Blob Storage. De locatie wordt gestart vanaf de hoofdmap. De hoofdmap is de gegevenslocatie die in de externe gegevensbron is opgegeven.

Als u een maplocatie opgeeft, wordt een SQL op aanvraag-query geselecteerd in de externe tabel en worden de bestanden opgehaald uit de map.

> [!NOTE]
> In tegens telling tot Hadoop en poly base retour neren SQL on-demand geen submappen. Het retourneert bestanden waarvoor de bestands naam begint met een onderstreping (_) of een punt (.).

Als locatie = '/webdata/', een SQL op aanvraag-query, wordt in dit voor beeld rijen geretourneerd van mydata. txt en _hidden. txt. Het retourneert mydata2. txt en mydata3. txt niet omdat ze zich in een submap bevinden.

![Recursieve gegevens voor externe tabellen](./media/develop-tables-external-tables/folder-traversal.png)

DATA_SOURCE = *external_data_source_name* : Hiermee geeft u de naam op van de externe gegevens bron die de locatie van de externe gegevens bevat. Gebruik [externe gegevens bron maken](#create-external-data-source)om een externe gegevens bron te maken.

FILE_FORMAT = *external_file_format_name* -Hiermee geeft u de naam op van het externe bestands indelings object waarin het bestands type en de compressie methode voor de externe gegevens worden opgeslagen. Als u een externe bestands indeling wilt maken, gebruikt u [externe BESTANDS indeling maken](#create-external-file-format).

## <a name="permissions-create-external-table"></a>Machtigingen voor het maken van een externe tabel

Als u een externe tabel wilt selecteren, moet u over de juiste referenties beschikken met de machtigingen lijst en lezen.

## <a name="example-create-external-table"></a>Voor beeld van een externe tabel maken

In het volgende voor beeld wordt een externe tabel gemaakt. De eerste rij wordt geretourneerd:

```sql
CREATE EXTERNAL TABLE census_external_table
(
    decennialTime varchar(20),
    stateName varchar(100),
    countyName varchar(100),
    population int,
    race varchar(50),
    sex    varchar(10),
    minAge int,
    maxAge int
)  
WITH (
    LOCATION = '/parquet/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)
GO

SELECT TOP 1 * FROM census_external_table
```

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Externe tabellen maken en opvragen van een bestand in Azure Data Lake

Met behulp van Data Lake-functies kunt u een externe tabel nu maken en een query uitvoeren met behulp van SQL-groep of SQL on-demand met een eenvoudige klik met de rechter muisknop op het bestand.

## <a name="prerequisites"></a>Vereisten

- U moet toegang hebben tot de werk ruimte met ten minste de Access-rol Storage BLOB data contributor ARM voor het ADLS Gen2-account

- U moet ten minste [machtigingen hebben voor het maken](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#permissions-2) en opvragen van externe tabellen op de SQL-groep of SQL-od

- De gekoppelde service die is gekoppeld aan het ADLS Gen2 account **moet toegang hebben tot het bestand**. Als het verificatie mechanisme van de gekoppelde service bijvoorbeeld beheerde identiteit is, moet de beheerde identiteit van de werk ruimte ten minste machtigingen voor de opslag-BLOB-lezer hebben voor het opslag account

Selecteer in het deel venster gegevens het bestand waarin u de externe tabel wilt maken:
> [!div class="mx-imgBorder"]
>![externaltable1](./media/develop-tables-external-tables/external-table-1.png)

Er wordt een dialoog venster geopend. Selecteer SQL-groep of SQL on-demand, geef een naam op voor de tabel en selecteer script openen:

> [!div class="mx-imgBorder"]
>![externaltable2](./media/develop-tables-external-tables/external-table-2.png)

Het SQL-script wordt automatisch gegenereerd, waarbij het schema wordt afgeleid van het bestand:
> [!div class="mx-imgBorder"]
>![externaltable3](./media/develop-tables-external-tables/external-table-3.png)

Voer het script uit. Het script voert automatisch een SELECT Top 100 * uit.:
> [!div class="mx-imgBorder"]
>![externaltable4](./media/develop-tables-external-tables/external-table-4.png)

De externe tabel wordt nu gemaakt, zodat de gebruiker de inhoud van deze externe tabel direct kan doorzoeken via het deel venster gegevens:
> [!div class="mx-imgBorder"]
>![externaltable5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg het artikel [CETAS](develop-tables-cetas.md) voor informatie over het opslaan van de query resultaten naar een externe tabel in azure Storage. U kunt ook beginnen met het uitvoeren van query's op [Spark-tabellen](develop-storage-files-spark-tables.md).
