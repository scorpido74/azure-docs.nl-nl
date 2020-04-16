---
title: OpenROWSET gebruiken in SQL on-demand (voorbeeld)
description: In dit artikel wordt de syntaxis van OPENROWSET in SQL on-demand (voorbeeld) beschreven en wordt uitgelegd hoe argumenten moeten worden gebruikt.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 25eb93a01c59225b6d9e64db5d08b954adb4f8ab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424067"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>OpenROWSET gebruiken met SQL on-demand (voorbeeld)

Met de functie OPENROWSET(BULK...) hebt u toegang tot bestanden in Azure Storage. Binnen de SQL on-demand (preview)-bron wordt de bulkrijsetprovider OPENROWSET geopend door de functie OPENROWSET aan te roepen en de optie BULK op te geven.  

De functie OPENROWSET kan in de VAN-component van een query worden verwezen alsof het een tabelnaam OPENROWSET is. Het ondersteunt bulkbewerkingen via een ingebouwde BULK-provider waarmee gegevens uit een bestand kunnen worden gelezen en geretourneerd als een rijset.

OPENROWSET wordt momenteel niet ondersteund in SQL-groep.

## <a name="syntax"></a>Syntaxis

```
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

U hebt twee opties voor invoerbestanden die de doelgegevens bevatten voor query's. Geldige waarden zijn:

- 'CSV' - Bevat elk afgebakend tekstbestand met rij-/kolomscheidingstekens. Elk teken kan worden gebruikt als veldafscheider, zoals TSV: FIELDTERMINATOR = tab.

- 'PARQUET' - Binair bestand in Parketformaat 

'unstructured_data_path'

De unstructured_data_path die een pad naar de gegevens<prefix>vaststelt, is als volgt gestructureerd: " ://<storage_account_path>/<storage_path>" 
 
 
 Hieronder vindt u de relevante opslagaccountpaden die worden gekoppeld aan uw specifieke externe gegevensbron. 

| Externe gegevensbron       | Voorvoegsel | Pad met opslagaccount                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Store Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | https  | <storage_account>.dfs.core.windows.net              |

 '<storage_path>' 

 Hiermee geeft u een pad op in uw opslag dat verwijst naar de map of het bestand dat u wilt lezen. Als het pad naar een container of map verwijst, worden alle bestanden uit die specifieke container of map gelezen. Bestanden in submappen worden niet opgenomen. 
 
 U jokertekens gebruiken om meerdere bestanden of mappen te targeten. Het gebruik van meerdere niet-opeenvolgende wildcards is toegestaan.
Hieronder vindt u een voorbeeld dat alle *csv-bestanden* leest die beginnenhttps://sqlondemandstorage.blob.core.windows.net/csv/populationmet de *populatie* uit alle mappen die beginnen met */csv/populatie*: '*/populatie*.csv'.

Als u de unstructured_data_path als map opgeeft, haalt een SQL on-demand query bestanden uit die map op. 

> [!NOTE]
> In tegenstelling tot Hadoop en PolyBase retourneert SQL on-demand geen submappen. In tegenstelling tot Hadoop en PloyBase retourneert SQL on-demand bestanden waarvoor de bestandsnaam begint met een onderstreping (_) of een periode (.).

In het onderstaande voorbeeld,https://mystorageaccount.dfs.core.windows.net/webdata/als de unstructured_data_path=' ', een SQL on-demand query zal terug rijen van mydata.txt en _hidden.txt. Het zal mydata2.txt en mydata3.txt niet retourneren omdat ze zich in een submap bevinden.

![Recursieve gegevens voor externe tabellen](./media/develop-openrowset/folder-traversal.png)

[MET ( {'column_name' 'column_type" [ 'column_ordinal'] }) ]

Met de WITH-component u kolommen opgeven die u uit bestanden wilt lezen.

- Als u voor CSV-gegevensbestanden alle kolommen wilt lezen, worden kolomnamen en hun gegevenstypen verstrekt. Als u een subset van kolommen wilt, gebruikt u ordinale getallen om de kolommen uit de oorspronkelijke gegevensbestanden te kiezen op ordinaal. Kolommen zullen worden gebonden door de ordinale aanwijzing. 

> [!IMPORTANT]
> De WITH-clausule is verplicht voor CSV-bestanden.
- Geef voor parketgegevensbestanden kolomnamen op die overeenkomen met de kolomnamen in de oorspronkelijke gegevensbestanden. Kolommen zijn gebonden aan de naam. Als de WITH-clausule wordt weggelaten, worden alle kolommen uit parketbestanden geretourneerd.

column_name = Naam voor de uitvoerkolom. Indien aanwezig, overschrijft deze naam de kolomnaam in het bronbestand.

column_type = Gegevenstype voor de uitvoerkolom. De impliciete conversie van het gegevenstype vindt hier plaats.

column_ordinal = Ordinaal nummer van de kolom in het bronbestand(en). Dit argument wordt genegeerd voor Parketbestanden, omdat binding op naam wordt gedaan. In het volgende voorbeeld wordt een tweede kolom alleen uit een CSV-bestand teruggegeven:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```
<bulk_options>

FIELDTERMINATOR ='field_terminator'

Hiermee geeft u de veldbegeindiger op die moet worden gebruikt. De standaardveldterminator is een komma **(" ,**").

ROWTERMINATOR ='row_terminator''

Hiermee geeft u de rijbeg op die moet worden gebruikt. De standaardrijbegeindiger is een nieuwregelteken zoals \r\n.

ESCAPE_CHAR = 'char'

Hiermee geeft u het teken op in het bestand dat wordt gebruikt om aan zichzelf en alle delimiterwaarden in het bestand te ontsnappen. Als het escape-teken wordt gevolgd door een andere waarde dan zichzelf, of een van de waardevan de scheidingsteken, wordt het escape-teken verwijderd bij het lezen van de waarde. 

De parameter ESCAPE_CHAR wordt toegepast, ongeacht of de FIELDQUOTE wel of niet is ingeschakeld. Het zal niet worden gebruikt om te ontsnappen aan het citeren karakter. Het quotende teken wordt ontsnapt met dubbele aanhalingstekens in overeenstemming met het excel CSV-gedrag.

EERSTE RIJ = 'first_row' 

Hiermee geeft u het nummer op van de eerste rij die moet worden geladen. De standaardwaarde is 1. Dit geeft de eerste rij in het opgegeven gegevensbestand aan. De rijnummers worden bepaald door de rijterminators te tellen. FIRSTROW is 1-based.

FIELDQUOTE = 'field_quote' 

Hiermee geeft u een teken op dat wordt gebruikt als het teken in het CSV-bestand. Indien dit niet is opgegeven, wordt het aanhalingsteken (") gebruikt. 

## <a name="examples"></a>Voorbeelden

In het volgende voorbeeld worden slechts twee kolommen geretourneerd met ordinale nummers 1 en 4 uit de bestanden van populatie*.csv. Aangezien er geen koptekstrij in de bestanden is, begint het lezen van de eerste regel:

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



In het volgende voorbeeld worden alle kolommen van de eerste rij van de verzameling volkstellingsgegevens in parketindeling geretourneerd zonder kolomnamen en gegevenstypen op te geven: 

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

Ga voor meer voorbeelden naar [snel starten](query-data-storage.md) of sla de resultaten van uw query op in Azure Storage met [CETAS](develop-tables-cetas.md).
