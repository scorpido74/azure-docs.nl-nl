---
title: OPENROWSET in SQL on demand gebruiken (preview)
description: In dit artikel wordt de syntaxis van OPENROWSET in SQL op aanvraag (preview) beschreven en wordt uitgelegd hoe u argumenten gebruikt.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 6325d5555b01373b148dce69731ec64896d6e1fd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81680495"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>OPENROWSET gebruiken met SQL op aanvraag (preview)

Met de functie OPENROWSET (BULK...) kunt u toegang krijgen tot bestanden in Azure Storage. In de SQL on-demand-resource (preview) wordt de functie OPENROWSET bulksgewijze rijenset geopend door het aanroepen van OPENROWSET en het opgeven van de optie BULK.  

U kunt in de component FROM van een query naar de functie OPENROWSET verwijzen, alsof het een OPENROWSET-rijen tabel naam is. Het ondersteunt bulk bewerkingen via een ingebouwde BULK provider waarmee gegevens uit een bestand kunnen worden gelezen en geretourneerd als een rijenset.

OPENROWSET wordt momenteel niet ondersteund in de SQL-groep.

## <a name="syntax"></a>Syntaxis

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row'  ]     
[ , FIELDQUOTE = 'quote_characters']
```

## <a name="arguments"></a>Argumenten

U hebt twee opties voor invoer bestanden die de doel gegevens voor het uitvoeren van query's bevatten. Geldige waarden zijn:

- CSV-bevat een tekst bestand met scheidings tekens met scheidings tekens voor rijen en kolommen. Elk wille keurig teken kan worden gebruikt als een veld scheidings element, zoals TSV: FIELDTERMINATOR = tab.

- ' PARQUET '-binair bestand in Parquet-indeling 

**' unstructured_data_path '**

De unstructured_data_path die een pad naar de gegevens tot stand brengt, is als volgt gestructureerd:  
'\<voor voegsel>:/\</storage_account_path>\</storage_path> '
 
 
 Hieronder vindt u de relevante paden voor opslag accounts die worden gekoppeld aan uw specifieke externe gegevens bron. 

| Externe gegevens bron       | Voorvoegsel | Pad naar het opslag account                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | https  | \<storage_account>. blob.core.windows.net             |
| Azure Data Lake Store gen1 | https  | \<storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | https  | \<storage_account>. dfs.core.windows.net              |
||||

'\<storage_path> '

 Hiermee geeft u een pad in uw opslag op dat verwijst naar de map of het bestand dat u wilt lezen. Als het pad naar een container of map verwijst, worden alle bestanden gelezen van die specifieke container of map. Bestanden in submappen worden niet opgenomen. 
 
 U kunt joker tekens gebruiken om te richten op meerdere bestanden of mappen. Het gebruik van meerdere niet-opeenvolgende joker tekens is toegestaan.
Hieronder ziet u een voor beeld waarin alle *CSV* -bestanden worden gelezen die beginnen met de *populatie* vanuit alle mappen vanaf */CSV/population*:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Als u opgeeft dat de unstructured_data_path een map is, haalt een SQL op aanvraag-query bestanden uit die map op. 

> [!NOTE]
> In tegens telling tot Hadoop en poly base retour neren SQL on-demand geen submappen. In tegens telling tot Hadoop en PloyBase worden door SQL op aanvraag ook bestanden geretourneerd waarvoor de bestands naam begint met een onderstreping (_) of een punt (.).

Als in het onderstaande voor beeld de unstructured_data_path =`https://mystorageaccount.dfs.core.windows.net/webdata/`is, retourneert een SQL op aanvraag-query rijen van mydata. txt en _hidden. txt. Het retourneert niet mydata2. txt en mydata3. txt, omdat ze zich in een submap bevinden.

![Recursieve gegevens voor externe tabellen](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

MET de WITH-component kunt u kolommen opgeven die u wilt lezen uit bestanden.

- Als u wilt dat alle kolommen worden gelezen met CSV-gegevens bestanden, geeft u kolom namen en de bijbehorende gegevens typen op. Als u een subset met kolommen wilt, gebruikt u ordinale getallen om de kolommen uit de oorspronkelijke gegevens bestanden op rang telwoord te kiezen. Kolommen worden gebonden aan de rang nummer aanduiding. 

> [!IMPORTANT]
> De WITH-component is verplicht voor CSV-bestanden.
- Geef voor Parquet-gegevens bestanden kolom namen op die overeenkomen met de kolom namen in de oorspronkelijke gegevens bestanden. Kolommen worden gebonden op naam. Als de WITH-component wordt wegge laten, worden alle kolommen uit Parquet-bestanden geretourneerd.

column_name = naam voor de uitvoer kolom. Als u deze naam opgeeft, wordt de naam van de kolom in het bron bestand overschreven.

column_type = gegevens type voor de uitvoer kolom. De impliciete gegevens type conversie wordt hier uitgevoerd.

column_ordinal = rang nummer van de kolom in de bron bestand (en). Dit argument wordt genegeerd voor Parquet-bestanden omdat de binding op naam is uitgevoerd. In het volgende voor beeld wordt een tweede kolom alleen geretourneerd vanuit een CSV-bestand:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

**\<bulk_options>**

FIELDTERMINATOR = ' field_terminator '

Geeft aan welk veld scheidings teken moet worden gebruikt. De standaard veld scheidings teken is een komma ('**,**').

ROWTERMINATOR = ' row_terminator ' '

Geeft het rij-einde op dat moet worden gebruikt. Het standaard einde van de rij is een teken voor een nieuwe regel, zoals \r\n.

ESCAPE_CHAR = ' char '

Hiermee geeft u het teken in het bestand dat wordt gebruikt om zichzelf te escapen en alle scheidings waarden in het bestand. Als het escape teken wordt gevolgd door een andere waarde dan zichzelf of een van de scheidings waarden, wordt het escape-teken verwijderd bij het lezen van de waarde. 

De para meter ESCAPE_CHAR wordt toegepast, ongeacht of de FIELDQUOTE is of niet is ingeschakeld. Het wordt niet gebruikt om het aanhalings teken te escapepen. Het aanhalings teken wordt met dubbele aanhalings tekens in uitlijning met het CSV-gedrag van Excel.

FIRSTROW = ' first_row ' 

Hiermee geeft u het nummer op van de eerste rij die moet worden geladen. De standaardwaarde is 1. Hiermee wordt de eerste rij in het opgegeven gegevens bestand aangegeven. De rijnummers worden bepaald door de afsluitingen van de rij te tellen. FIRSTROW is op 1 gebaseerd.

FIELDQUOTE = ' field_quote ' 

Hiermee geeft u een teken op dat wordt gebruikt als aanhalings teken in het CSV-bestand. Als u niets opgeeft, wordt het aanhalings teken (") gebruikt. 

## <a name="examples"></a>Voorbeelden

In het volgende voor beeld worden slechts twee kolommen met ordinale getallen 1 en 4 uit de population*. CSV-bestanden geretourneerd. Omdat de bestanden geen veldnamenrij bevatten, begint het lezen van de eerste regel:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```



In het volgende voor beeld worden alle kolommen van de eerste rij als resultaat gegeven uit de set met gegevens in de Parquet-indeling zonder kolom namen en gegevens typen: 

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```



## <a name="next-steps"></a>Volgende stappen

Ga voor meer voor beelden naar [Quick](query-data-storage.md) starts of sla de resultaten van uw query op Azure Storage met behulp van [CETAS](develop-tables-cetas.md).
