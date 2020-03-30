---
title: Contoso-retailgegevens laden in een SQL Analytics-gegevensmagazijn
description: Gebruik PolyBase- en T-SQL-opdrachten om twee tabellen van de Contoso-retailgegevens in Azure SQL Analytics te laden.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 62105b783577d70ae975cf514304d2c564357641
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351464"
---
# <a name="load-contoso-retail-data-to-a-sql-analytics-data-warehouse"></a>Contoso-retailgegevens laden in een SQL Analytics-gegevensmagazijn

In deze zelfstudie leert u PolyBase- en T-SQL-opdrachten te gebruiken om twee tabellen uit de Contoso-retailgegevens in een SQL Analytics-gegevensmagazijn te laden. 

In deze tutorial zul je:

1. PolyBase configureren om te laden vanuit Azure blob-opslag
2. Openbare gegevens in uw database laden
3. Optimalisaties uitvoeren nadat de belasting is voltooid.

## <a name="before-you-begin"></a>Voordat u begint

Als u deze zelfstudie wilt uitvoeren, hebt u een Azure-account nodig dat al een SQL Analytics-gegevensmagazijn heeft. Zie [Een gegevensmagazijn maken en firewallregel op serverniveau instellen als](create-data-warehouse-portal.md)u geen gegevensmagazijn hebt ingericht.

## <a name="configure-the-data-source"></a>De gegevensbron configureren

PolyBase gebruikt externe T-SQL-objecten om de locatie en kenmerken van de externe gegevens te definiëren. De definities van externe objecten worden opgeslagen in uw SQL Analytics-gegevensmagazijn. De gegevens worden extern opgeslagen.

## <a name="create-a-credential"></a>Een referentie maken

**Sla deze stap over** als u de openbare Contoso-gegevens laadt. U hebt geen veilige toegang tot de openbare gegevens nodig, omdat deze al voor iedereen toegankelijk zijn.

**Sla deze stap niet over** als u deze zelfstudie gebruikt als sjabloon voor het laden van uw eigen gegevens. Als u toegang wilt krijgen tot gegevens via een referentie, gebruikt u het volgende script om een referentie met databasebereik te maken. Gebruik het vervolgens bij het definiëren van de locatie van de gegevensbron.

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

## <a name="create-the-external-data-source"></a>De externe gegevensbron maken

Gebruik deze opdracht [EXTERNE GEGEVENSBRON MAKEN](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-ver15) om de locatie van de gegevens en het gegevenstype op te slaan. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [!IMPORTANT]
> Als u ervoor kiest om uw azure blob-opslagcontainers openbaar te maken, moet u er rekening mee brengen dat u als gegevenseigenaar in rekening wordt gebracht voor gegevensuitweie wanneer gegevens het datacenter verlaten. 
> 

## <a name="configure-the-data-format"></a>De gegevensindeling configureren

De gegevens worden opgeslagen in tekstbestanden in Azure blob-opslag en elk veld wordt gescheiden met een scheidingsteken. Voer in SSMS de volgende opdracht EXTERNE BESTANDSNOTatie MAKEN uit om de indeling van de gegevens in de tekstbestanden op te geven. De Contoso-gegevens zijn ongecomprimeerd en de pijp worden afgebakend.

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

## <a name="create-the-external-tables"></a>De externe tabellen maken
Nu u de gegevensbron en bestandsindeling hebt opgegeven, bent u klaar om de externe tabellen te maken. 

## <a name="create-a-schema-for-the-data"></a>Een schema voor de gegevens maken
Als u een plaats wilt maken om de Contoso-gegevens in uw database op te slaan, maakt u een schema.

```sql
CREATE SCHEMA [asb]
GO
```

## <a name="create-the-external-tables"></a>De externe tabellen maken

Voer het volgende script uit om de externe tabellen DimProduct en FactOnlineSales te maken. Het enige wat u hier doet, is kolomnamen en gegevenstypen definiëren en deze koppelen aan de locatie en indeling van de Azure blob-opslagbestanden. De definitie wordt opgeslagen in het SQL Analytics-gegevensmagazijn en de gegevens bevinden zich nog steeds in de Azure Storage Blob.

De parameter **LOCATIE** is de map onder de hoofdmap in de Azure Storage Blob. Elke tabel bevindt zich in een andere map.

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

## <a name="load-the-data"></a>De gegevens laden
Er zijn verschillende manieren om toegang te krijgen tot externe gegevens.  U gegevens rechtstreeks vanuit de externe tabellen opvragen, de gegevens laden in nieuwe tabellen in het gegevensmagazijn of externe gegevens toevoegen aan bestaande gegevensmagazijntabellen.  

###  <a name="create-a-new-schema"></a>Een nieuw schema maken

CTAS maakt een nieuwe tabel met gegevens.  Maak eerst een schema voor de contoso-gegevens.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="load-the-data-into-new-tables"></a>De gegevens in nieuwe tabellen laden

Als u gegevens uit Azure blob-opslag wilt laden in de tabel met het gegevensmagazijn, gebruikt u de instructie [TABEL MAKEN ALS SELECT (Transact-SQL).](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=aps-pdw-2016-au7) Laden met [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md) maakt gebruik van de sterk getypte externe tabellen die u hebt gemaakt. Als u de gegevens in nieuwe tabellen wilt laden, gebruikt u één CTAS-instructie per tabel. 
 
CTAS maakt een nieuwe tabel en vult deze met de resultaten van een selecte instructie. CTAS definieert de nieuwe tabel met dezelfde kolommen en gegevenstypen als de resultaten van de select-instructie. Als u alle kolommen uit een externe tabel selecteert, wordt de nieuwe tabel een replica van de kolommen en gegevenstypen in de externe tabel.

In dit voorbeeld maken we zowel de dimensie als de feitentabel als hash gedistribueerde tabellen. 

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="track-the-load-progress"></a>De voortgang van de belasting bijhouden

U de voortgang van uw belasting bijhouden met behulp van dynamische beheerweergaven (DMVs). 

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

## <a name="optimize-columnstore-compression"></a>Compressie van kolomarchief optimaliseren

Standaard slaat het SQL Analytics-gegevensmagazijn de tabel op als een geclusterde kolomarchiefindex. Nadat een belasting is voltooid, worden sommige gegevensrijen mogelijk niet gecomprimeerd in het kolomarchief.  Er zijn verschillende redenen waarom dit kan gebeuren. Zie [kolomarchiefindexen beheren](sql-data-warehouse-tables-index.md)voor meer informatie .

Als u de queryprestaties en compressie van het kolomarchief na een belasting wilt optimaliseren, bouwt u de tabel opnieuw om de kolomarchiefindex te dwingen alle rijen te comprimeren. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Zie het artikel [indexen voor kolomarchief](sql-data-warehouse-tables-index.md) beheren voor meer informatie over het bijhouden van kolomarchiefindexen.

## <a name="optimize-statistics"></a>Statistieken optimaliseren

Het is het beste om statistieken met één kolom direct na een belasting te maken. Als u weet dat bepaalde kolommen niet in querypredicaten staan, u het maken van statistieken over die kolommen overslaan. Als u op elke kolom statistieken met één kolom maakt, kan het lang duren voordat alle statistieken opnieuw worden hersteld. 

Als u besluit om statistieken met één kolom te maken voor elke `prc_sqldw_create_stats` kolom van elke tabel, u het voorbeeld van de opgeslagen procedurecode gebruiken in het [statistiekartikel.](sql-data-warehouse-tables-statistics.md)

Het volgende voorbeeld is een goed uitgangspunt voor het maken van statistieken. Er worden statistieken met één kolom voor elke kolom in de dimensietabel en op elke verbindingskolom in de feitentabellen. U later altijd statistieken met één of meerdere kolommen toevoegen aan andere kolommen met feitentabel.

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

## <a name="achievement-unlocked"></a>Prestatie ontgrendeld!
U hebt met succes openbare gegevens in uw gegevensmagazijn geladen. Geweldig werk!

U nu beginnen met het opvragen van de tabellen om uw gegevens te verkennen. Voer de volgende query uit om de totale omzet per merk te achterhalen:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Volgende stappen
Als u de volledige gegevensset wilt laden, voert u het voorbeeld [het volledige Contoso-winkelgegevensmagazijn](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) uit vanuit de Microsoft SQL Server-opslagplaats voor voorbeelden.
Zie [Ontwerpbeslissingen en coderingstechnieken voor datawarehouses voor](sql-data-warehouse-overview-develop.md)meer ontwikkelingstips.
