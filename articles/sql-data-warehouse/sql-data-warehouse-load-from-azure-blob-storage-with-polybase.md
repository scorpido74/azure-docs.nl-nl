---
title: Retail-gegevens van Contoso laden in Azure SQL Data Warehouse | Microsoft Docs
description: Gebruik poly base-en T-SQL-opdrachten om twee tabellen uit de Retail gegevens van Contoso te laden in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b96b65b7dd38900fccb8d5d3a9133f37ee93949f
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "67595522"
---
# <a name="load-contoso-retail-data-to-azure-sql-data-warehouse"></a>Retail-gegevens van Contoso naar Azure SQL Data Warehouse laden

In deze zelf studie leert u hoe u poly base-en T-SQL-opdrachten gebruikt om twee tabellen uit de Retail gegevens van Contoso te laden in Azure SQL Data Warehouse. 

In deze zelf studie doet u het volgende:

1. Poly base configureren om te laden vanuit Azure Blob-opslag
2. Open bare gegevens in uw data base laden
3. Voer optimalisaties uit nadat het laden is voltooid.

## <a name="before-you-begin"></a>Voordat u begint
Als u deze zelf studie wilt uitvoeren, hebt u een Azure-account nodig dat al een SQL Data Warehouse heeft. Als u geen data warehouse hebt ingericht, raadpleegt u [een SQL Data Warehouse maken en firewall regel op server niveau instellen][Create a SQL Data Warehouse].

## <a name="1-configure-the-data-source"></a>1. De gegevens bron configureren
Poly base maakt gebruik van externe T-SQL-objecten om de locatie en kenmerken van de externe gegevens te definiëren. De definities voor externe objecten worden opgeslagen in SQL Data Warehouse. De gegevens worden extern opgeslagen.

### <a name="11-create-a-credential"></a>1.1. Een referentie maken
**Sla deze stap over** als u de open bare gegevens van Contoso laadt. U hebt geen beveiligde toegang tot de open bare gegevens nodig omdat deze al toegankelijk is voor iedereen.

**Sla deze stap niet over** als u deze zelf studie gebruikt als sjabloon voor het laden van uw eigen gegevens. Als u toegang wilt krijgen tot gegevens via een referentie, gebruikt u het volgende script om een Data Base-scoped referentie te maken en gebruikt u deze bij het definiëren van de locatie van de gegevens bron.

```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

### <a name="12-create-the-external-data-source"></a>1.2. De externe gegevens bron maken
Gebruik deze opdracht [externe gegevens bron maken][CREATE EXTERNAL DATA SOURCE] om de locatie van de gegevens en het type gegevens op te slaan. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [!IMPORTANT]
> Als u ervoor kiest om uw Azure Blob-opslag containers openbaar te maken, moet u er rekening mee houden dat als de eigenaar van de gegevens in rekening wordt gebracht voor de kosten voor gegevens uitvoer wanneer gegevens het Data Center verlaten. 
> 
> 

## <a name="2-configure-data-format"></a>2. Gegevens indeling configureren
De gegevens worden opgeslagen in tekst bestanden in Azure Blob-opslag en elk veld wordt gescheiden met een scheidings teken. Voer in SSMS de volgende opdracht voor het maken van een [externe BESTANDS indeling][CREATE EXTERNAL FILE FORMAT] uit om de indeling van de gegevens in de tekst bestanden op te geven. De contoso-gegevens worden niet gecomprimeerd en door pipes gescheiden.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat 
WITH 
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE 
                    )
);
``` 

## <a name="3-create-the-external-tables"></a>3. De externe tabellen maken
Nu u de gegevens bron en bestands indeling hebt opgegeven, bent u klaar om de externe tabellen te maken. 

### <a name="31-create-a-schema-for-the-data"></a>3.1. Maak een schema voor de gegevens.
Maak een schema om een locatie te maken voor het opslaan van de contoso-gegevens in uw data base.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3.2. Maak de externe tabellen.
Voer het volgende script uit om de externe tabellen DimProduct en FactOnlineSales te maken. U hoeft hier geen kolom namen en gegevens typen te definiëren en deze te binden aan de locatie en indeling van de Azure Blob-opslag bestanden. De definitie wordt opgeslagen in SQL Data Warehouse en de gegevens bevinden zich nog in de Azure Storage Blob.

De **locatie** parameter is de map onder de hoofdmap in de Azure Storage blob. Elke tabel bevindt zich in een andere map.

```sql
--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales 
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="4-load-the-data"></a>4. De gegevens laden
Er zijn verschillende manieren om toegang te krijgen tot externe gegevens.  U kunt gegevens rechtstreeks vanuit de externe tabellen opvragen, de gegevens in nieuwe tabellen in het Data Warehouse laden of externe gegevens toevoegen aan bestaande Data Warehouse-tabellen.  

### <a name="41-create-a-new-schema"></a>4.1. Een nieuw schema maken
CTAS maakt een nieuwe tabel die gegevens bevat.  Maak eerst een schema voor de contoso-gegevens.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2. De gegevens in nieuwe tabellen laden
Als u gegevens uit Azure Blob-opslag wilt laden in de Data Warehouse-tabel, gebruikt u de instructie [create table as select (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] . Het laden met CTAS maakt gebruik van de sterk getypeerde externe tabellen die u hebt gemaakt. Als u de gegevens in nieuwe tabellen wilt laden, gebruikt u een [CTAS][CTAS] -instructie per tabel. 
 
CTAS maakt een nieuwe tabel en vult deze met de resultaten van een SELECT-instructie. CTAS definieert de nieuwe tabel om dezelfde kolommen en gegevens typen te hebben als de resultaten van de SELECT-instructie. Als u alle kolommen uit een externe tabel selecteert, is de nieuwe tabel een replica van de kolommen en gegevens typen in de externe tabel.

In dit voor beeld maken we zowel de dimensie als de feiten tabel als gedistribueerde hash-tabellen. 

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4,3 de voortgang van het laden volgen
U kunt de voortgang van het laden volgen met dynamische beheer weergaven (Dmv's). 

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE 
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="5-optimize-columnstore-compression"></a>5. Column Store-compressie optimaliseren
SQL Data Warehouse slaat de tabel standaard op als een geclusterde column store-index. Nadat het laden is voltooid, zijn sommige gegevens rijen mogelijk niet gecomprimeerd naar de column Store.  Er zijn verschillende redenen waarom dit kan gebeuren. Zie [Column Store-indexen beheren][manage columnstore indexes]voor meer informatie.

Als u de query prestaties en column Store-compressie wilt optimaliseren na een laad opdracht, bouwt u de tabel opnieuw op om ervoor te zorgen dat de column store-index alle rijen kan comprimeren. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Zie het artikel [Column Store-indexen beheren][manage columnstore indexes] voor meer informatie over het onderhouden van Column Store-indexen.

## <a name="6-optimize-statistics"></a>6. Statistieken optimaliseren
Het is het beste om statistieken voor één kolom direct na een belasting te maken. Als u weet dat bepaalde kolommen niet in query predikaten staan, kunt u het maken van statistieken voor die kolommen overs Laan. Als u een statistieken voor één kolom maakt voor elke kolom, kan het lang duren om alle statistieken opnieuw samen te stellen. 

Als u besluit om met één kolom statistieken te maken voor elke kolom van elke tabel, kunt u het voor beeld `prc_sqldw_create_stats` van de opgeslagen procedure code in het [statistiek][statistics] artikel gebruiken.

Het volgende voor beeld is een goed uitgangs punt voor het maken van statistieken. Er worden statistieken voor één kolom gemaakt voor elke kolom in de dimensie tabel en voor elke join-kolom in de feiten tabellen. U kunt later altijd statistieken met één of meerdere kolommen toevoegen aan andere feiten tabel kolommen.

```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Prestaties vergren delen.
U hebt open bare gegevens in Azure SQL Data Warehouse geladen. Fantastische taak!

U kunt nu een query uitvoeren op de tabellen om uw gegevens te verkennen. Voer de volgende query uit om de totale verkoop per merk te vinden:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Volgende stappen
Als u de volledige gegevensset wilt laden, voert u het voor beeld [laden van het volledige contoso Retail Data Warehouse](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) vanuit de opslag plaats Microsoft SQL Server samples.

Zie [Overzicht van SQL Data Warehouse voor ontwikkelaars][SQL Data Warehouse development overview] voor meer tips voor ontwikkelaars.

<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[manage columnstore indexes]: sql-data-warehouse-tables-index.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
