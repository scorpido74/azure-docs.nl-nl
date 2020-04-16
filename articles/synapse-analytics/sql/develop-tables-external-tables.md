---
title: Externe tabellen gebruiken met Synapse SQL
description: Gegevensbestanden lezen of schrijven met Synapse SQL
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 4d13d15fe950c89687acfca355d4ed183756536a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423976"
---
# <a name="use-external-tables-with-synapse-sql"></a>Externe tabellen gebruiken met Synapse SQL

Een externe tabel verwijst naar gegevens in Hadoop, Azure Storage blob of Azure Data Lake Storage. Externe tabellen worden gebruikt om gegevens uit bestanden te lezen of gegevens naar bestanden in Azure Storage te schrijven. Met Synapse SQL u externe tabellen gebruiken om gegevens te lezen en te schrijven naar SQL-pool of SQL on-demand (preview).

## <a name="external-tables-in-sql-pool"></a>Externe tabellen in SQL-groep

In SQL-groep u een externe tabel gebruiken om:

- Query Azure Blob Storage en Azure Data Lake Gen2 met Transact-SQL-instructies.
- Importeer en sla gegevens uit Azure Blob Storage en Azure Data Lake Storage in SQL-groep.

Wanneer u deze gebruikt in combinatie met de instructie [TABEL MAKEN ALS SELECT,](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) importeert het selecteren uit een externe tabel gegevens in een tabel in de SQL-groep. Naast de [instructie COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)zijn externe tabellen handig voor het laden van gegevens. Zie [PolyBase gebruiken om gegevens uit Azure Blob Storage te laden](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor een zelfstudie voor het laden.

## <a name="external-tables-in-sql-on-demand-preview"></a>Externe tabellen in SQL on-demand (voorbeeld)

Voor SQL on-demand gebruikt u een externe tabel om:

- Querygegevens in Azure Blob Storage of Azure Data Lake Storage met Transact-SQL-instructies
- Sla SQL-queryresultaten op aanvraag op bestanden in Azure Blob Storage of Azure Data Lake Storage met [CETAS](develop-tables-cetas.md).

U externe tabellen maken met SQL on-demand via de volgende stappen:

1. EXTERNE GEGEVENSBRON MAKEN
2. CREATE EXTERNAL FILE FORMAT
3. EXTERNE TABEL MAKEN

## <a name="create-external-data-source"></a>EXTERNE GEGEVENSBRON MAKEN

Externe gegevensbronnen worden gebruikt om verbinding te maken met opslagaccounts. De volledige documentatie wordt [hier](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)beschreven.

## <a name="syntax-for-create-external-data-source"></a>Syntaxis voor EXTERNE GEGEVENSBRON MAKEN

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>' )
[;]
```

## <a name="arguments-for-create-external-data-source"></a>Argumenten voor HET MAKEN van externe gegevensbron

data_source_name -Hiermee geeft u de door de gebruiker gedefinieerde naam voor de gegevensbron op. De naam moet uniek zijn in de database.

LOCATIE `'<prefix>://<path>'` = - Biedt het connectiviteitsprotocol en het pad naar de externe gegevensbron. Het pad kan een container `'<prefix>://<path>/container'`in de vorm van `'<prefix>://<path>/container/folder'`, en een map in de vorm van .

| Externe gegevensbron        | Locatievoorvoegsel | Locatiepad                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Blob Storage          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Azure Data Lake Store Gen 1 | `adl`           | `<storage_account>.azuredatalake.net`                 |
| Azure Data Lake Store Gen 2 | `abfs[s]`       | `<container>@<storage_account>.dfs.core.windows.net`  |

## <a name="example-for-create-external-data-source"></a>Voorbeeld voor MAKEN EXTERNE GEGEVENSBRON

In het volgende voorbeeld wordt een externe gegevensbron gemaakt voor Azure Data Lake Gen2 die naar de gegevensset van New York wijst:

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

Hiermee maakt u een extern bestandsindelingsobject dat externe gegevens definieert die zijn opgeslagen in Azure Blob Storage of Azure Data Lake Storage. Het maken van een externe bestandsindeling is een vereiste voor het maken van een externe tabel. De volledige documentatie is [hier](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Door een externe bestandsindeling te maken, geeft u de werkelijke indeling op van de gegevens waarnaar wordt verwezen door een externe tabel.

## <a name="syntax-for-create-external-file-format"></a>Syntaxis voor EXTERNE BESTANDSNOTATIE MAKEN

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

## <a name="arguments-for-create-external-file-format"></a>Argumenten voor EXTERNE BESTANDSNOTATIE MAKEN

file_format_name- Hiermee geeft u een naam op voor de externe bestandsindeling.

FORMAT_TYPE = [ PARKET | DELIMITEDTEXT]- Hiermee geeft u de opmaak van de externe gegevens op.

- PARQUET - Geeft een parketformaat op.
- DELIMITEDTEXT - Hiermee geeft u een tekstnotatie op met kolomscheidingstekens, ook wel veldterminators genoemd.

FIELD_TERMINATOR = *field_terminator* - Geldt alleen voor afgebakende tekstbestanden. De veldbegeindiger geeft een of meer tekens op die het einde van elk veld (kolom) markeren in het met tekst afgebakende bestand. De standaardinstelling is het pijpteken (|?

Voorbeelden:

- FIELD_TERMINATOR = "|"
- FIELD_TERMINATOR = '
- FIELD_TERMINATOR =

STRING_DELIMITER = *string_delimiter* - Hiermee geeft u de veldbegeindiger op voor gegevens van teksttekenreeks in het tekst-afgebakende bestand. De tekenreeksscheidingskiezer is een of meer tekens in lengte en is ingesloten met enkele aanhalingstekens. De standaardinstelling is de lege tekenreeks ("").

Voorbeelden:

- STRING_DELIMITER = '""
- STRING_DELIMITER = '*'
- STRING_DELIMITER =

FIRST_ROW = *First_row_int* - Hiermee geeft u het rijnummer op dat als eerste wordt gelezen en geldt voor alle bestanden. Als u de waarde instelt op twee, wordt de eerste rij in elk bestand (koptekstrij) overgeslagen wanneer de gegevens worden geladen. Rijen worden overgeslagen op basis van het bestaan van rijterminators (/r/n, /r, /n).

USE_TYPE_DEFAULT = { WAAR | **VALSE** } - Hiermee geeft u op hoe ontbrekende waarden in afgebakende tekstbestanden moeten worden verwerkt bij het ophalen van gegevens uit het tekstbestand.

WAAR - Als u gegevens uit het tekstbestand ophaalt, slaat u elke ontbrekende waarde op met behulp van het gegevenstype van de standaardwaarde voor de overeenkomstige kolom in de externe tabeldefinitie. Vervang bijvoorbeeld een ontbrekende waarde door:

- 0 als de kolom wordt gedefinieerd als een numerieke kolom. Decimale kolommen worden niet ondersteund en veroorzaken een fout.
- Lege tekenreeks ("") als de kolom een tekenreekskolom is.
- 1900-01-01 als de kolom een datumkolom is.

FALSE - Sla alle ontbrekende waarden op als NULL. Null-waarden die worden opgeslagen met het woord NULL in het afgebakende tekstbestand, worden geïmporteerd als de tekenreeks 'NULL'.

Codering = {'UTF8' | 'UTF16'} - SQL on-demand kan UTF8 en UTF16 gecodeerde gedelimited text files lezen.

DATA_COMPRESSION = *data_compression_method* - In dit argument wordt de gegevenscompressiemethode voor de externe gegevens opgegeven. Bij het lezen van externe tabellen wordt deze genegeerd. Het wordt alleen gebruikt bij het schrijven naar externe tabellen met [CETAS](develop-tables-cetas.md).

Het type PARQUET-bestandsindeling ondersteunt de volgende compressiemethoden:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

## <a name="example-for-create-external-file-format"></a>Voorbeeld voor EXTERNE BESTANDSNOTATIE MAKEN

In het volgende voorbeeld wordt een externe bestandsindeling gemaakt voor volkstellingsbestanden:

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>EXTERNE TABEL MAKEN

Met de opdracht EXTERNE TABEL MAKEN maakt u een externe tabel voor Synapse SQL om toegang te krijgen tot gegevens die zijn opgeslagen in Azure Blob Storage of Azure Data Lake Storage. 

## <a name="syntax-for-create-external-table"></a>Syntaxis voor EXTERNE TABEL MAKEN

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

## <a name="arguments-create-external-table"></a>Argumenten EXTERNE TABEL MAKEN

*{ database_name.schema_name.table_name | schema_name,table_name | table_name }*

De naam van één tot drie delen van de te maken tabel. Voor een externe tabel slaat SQL on-demand alleen de tabelmetagegevens op. Er worden geen werkelijke gegevens verplaatst of opgeslagen in SQL on-demand.

<column_definition>, ... *n* ]

CREATE EXTERNAL TABLE ondersteunt de mogelijkheid om kolomnaam, gegevenstype, nullability en collatie te configureren. U de standaardbeperking niet gebruiken voor externe tabellen.

>[!IMPORTANT]
>De kolomdefinities, inclusief de gegevenstypen en het aantal kolommen, moeten overeenkomen met de gegevens in de externe bestanden. Als er sprake is van een afwijking worden de bestandsrijen geweigerd wanneer er een query voor de daadwerkelijke gegevens wordt uitgevoerd.

Bij het lezen van parketbestanden u alleen de kolommen opgeven die u wilt lezen en de rest overslaan.

LOCATIE = '*folder_or_filepath*'

Hiermee geeft u de map of het bestandspad en de bestandsnaam op voor de werkelijke gegevens in Azure Blob Storage. De locatie begint vanuit de hoofdmap. De hoofdmap is de gegevenslocatie die in de externe gegevensbron is opgegeven.

Als u een mapLOCATIE opgeeft, selecteert een SQL on-demand query in de externe tabel en haalt u bestanden op uit de map.

> [!NOTE]
> In tegenstelling tot Hadoop en PolyBase retourneert SQL on-demand geen submappen. Hiermee worden bestanden geretourneerd waarvoor de bestandsnaam begint met een onderstreping (_) of een punt (.).

Als LOCATION='/webdata/', een SQL on-demand query, in dit voorbeeld rijen retourneert van mydata.txt en _hidden.txt. Het zal mydata2.txt en mydata3.txt niet retourneren omdat ze zich in een submap bevinden.

![Recursieve gegevens voor externe tabellen](./media/develop-tables-external-tables/folder-traversal.png)

DATA_SOURCE = *external_data_source_name* - Hiermee geeft u de naam op van de externe gegevensbron die de locatie van de externe gegevens bevat. Als u een externe gegevensbron wilt maken, gebruikt u [EXTERNE GEGEVENSBRON MAKEN](#create-external-data-source).

FILE_FORMAT = *external_file_format_name* - Hiermee geeft u de naam op van het object externe bestandsindeling dat het bestandstype en de compressiemethode voor de externe gegevens opslaat. Als u een externe bestandsindeling wilt maken, gebruikt u [EXTERNE BESTANDSINDELING MAKEN](#create-external-file-format).

## <a name="permissions-create-external-table"></a>Machtigingen MAKEN EXTERNE TABEL

Als u wilt kiezen uit een externe tabel, hebt u de juiste referenties met lijst- en leesmachtigingen nodig.

## <a name="example-create-external-table"></a>Voorbeeld EXTERNE TABEL MAKEN

In het volgende voorbeeld wordt een externe tabel maakt. Het geeft de eerste rij als resultaat:

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

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Externe tabellen maken en opvragen vanuit een bestand in Azure Data Lake

Met behulp van Data Lake-verkenningsmogelijkheden u nu een externe tabel maken en opvragen met SQL-pool of SQL on-demand met een eenvoudige klik met de rechtermuisknop op het bestand.

## <a name="prerequisites"></a>Vereisten

- U moet toegang hebben tot de werkruimte met ten minste de ARM Access-rol Storage Blob-gegevensbijdrage naar het ADLS Gen2-account

- U moet ten minste machtigingen hebben om externe tabellen in de SQL-groep of SQL OD [te maken](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#permissions-2) en op te vragen

- De gekoppelde service die is gekoppeld aan het ADLS Gen2-account **moet toegang hebben tot het bestand**. Als het verificatiemechanisme voor gekoppelde service bijvoorbeeld Beheerde identiteit is, moet de door de werkruimte beheerde identiteit ten minste opslagbloblezertoestemming hebben voor het opslagaccount

Selecteer in het deelvenster Gegevens het bestand waaruit u de externe tabel wilt maken:
> [!div class="mx-imgBorder"]
>![externe tabel1](./media/develop-tables-external-tables/external-table-1.png)

Er wordt een dialoogvenster geopend. Selecteer SQL-groep of SQL on-demand, geef een naam aan de tabel en selecteer open script:

> [!div class="mx-imgBorder"]
>![externe tabel2](./media/develop-tables-external-tables/external-table-2.png)

Het SQL Script wordt automatisch gegenereerd door het schema uit het bestand te verwijderen:
> [!div class="mx-imgBorder"]
>![externe tabel3](./media/develop-tables-external-tables/external-table-3.png)

Voer het script uit. Het script wordt automatisch uitgevoerd een Select Top 100 *.:
> [!div class="mx-imgBorder"]
>![externe tabel4](./media/develop-tables-external-tables/external-table-4.png)

De externe tabel is nu gemaakt, voor toekomstige verkenning van de inhoud van deze externe tabel kan de gebruiker deze rechtstreeks vanuit het deelvenster Gegevens opvragen:
> [!div class="mx-imgBorder"]
>![externe tabel5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>Volgende stappen

Controleer het [CETAS-artikel](develop-tables-cetas.md) voor het opslaan van de queryresultaten in een externe tabel in Azure Storage. U ook [Spark-tabellen](develop-storage-files-spark-tables.md)gaan opvragen.
