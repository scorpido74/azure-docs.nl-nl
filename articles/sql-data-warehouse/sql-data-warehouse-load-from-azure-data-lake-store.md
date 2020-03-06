---
title: Zelf studie gegevens laden van Azure Data Lake Storage
description: Gebruik poly base externe tabellen om gegevens van Azure Data Lake Storage voor SQL Analytics te laden.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 03/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: b0b9cffe0b69545a6d0219941b48ac9eb0f399b3
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300584"
---
# <a name="load-data-from-azure-data-lake-storage-for-sql-analytics"></a>Gegevens laden uit Azure Data Lake Storage voor SQL Analytics
In deze hand leiding wordt beschreven hoe u met poly base externe tabellen gegevens laadt van Azure Data Lake Storage. Hoewel u ad hoc query's kunt uitvoeren op gegevens die zijn opgeslagen in Data Lake Storage, raden we u aan om de gegevens te importeren voor de beste prestaties. 

> [!NOTE]  
> Een alternatief voor het laden is de [instructie Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) die momenteel beschikbaar is als open bare preview.  De instructie COPY biedt de meeste flexibiliteit. Als u feedback wilt geven over de instructie COPY, stuurt u een e-mail naar de volgende distributie lijst: sqldwcopypreview@service.microsoft.com.
>
> [!div class="checklist"]

> * Maak database objecten die vereist zijn om te laden uit Data Lake Storage.
> * Verbinding maken met een Data Lake Storage Directory.
> * Gegevens laden in het Data Warehouse.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint
Download en installeer voordat u met deze zelfstudie begint de nieuwste versie van [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) (SQL Server Management Studio).

Als u deze zelf studie wilt uitvoeren, hebt u het volgende nodig:

* Een SQL-groep. Zie [een SQL-groep en query gegevens maken](create-data-warehouse-portal.md).
* Een Data Lake Storage-account. Zie [aan de slag met Azure data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md). Voor dit opslag account moet u een van de volgende referenties configureren of opgeven om te laden: een sleutel voor een opslag account, een Azure Directory-toepassings gebruiker of een AAD-gebruiker die de juiste RBAC-rol heeft voor het opslag account. 

##  <a name="create-a-credential"></a>Een referentie maken
U kunt deze sectie overs Laan en door gaan met het maken van de externe gegevens bron bij het verifiëren met behulp van AAD Pass-through. Een Data Base-bereik referentie hoeft niet te worden gemaakt of opgegeven wanneer AAD Pass-Through wordt gebruikt, maar zorg ervoor dat uw AAD-gebruiker over de juiste RBAC-rol (Storage BLOB data Reader, Inzender of eigenaar) beschikt voor het opslag account. Meer informatie vindt u [hier](https://techcommunity.microsoft.com/t5/Azure-SQL-Data-Warehouse/How-to-use-PolyBase-by-authenticating-via-AAD-pass-through/ba-p/862260). 

Als u toegang wilt krijgen tot uw Data Lake Storage-account, moet u een database hoofd sleutel maken om uw referentie geheim te versleutelen. Vervolgens maakt u een referentie data base-scope om uw geheim op te slaan. Bij verificatie met Service-principals (Azure Directory-toepassings gebruiker) worden de referenties van de data base-Scope opgeslagen die zijn ingesteld in AAD. U kunt ook de data base-scoped referentie gebruiken om de sleutel van het opslag account op te slaan voor Gen2.

Als u verbinding wilt maken met Data Lake Storage met Service-principals, moet u **eerst** een Azure Active Directory toepassing maken, een toegangs sleutel maken en de toepassing toegang verlenen tot het data Lake Storage-account. Zie [verifiëren voor Azure data Lake Storage met behulp van Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)voor instructies.

Meld u aan bij de SQL-groep met een gebruiker met machtigingen op het niveau van het besturings element en voer de volgende SQL-instructies uit voor uw Data Base:

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B (for service principal authentication): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    -- Always use the OAuth 2.0 authorization endpoint (v1)
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- B (for Gen2 storage key authentication): Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;

-- It should look something like this when authenticating using service principal:
CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>De externe gegevens bron maken
Gebruik deze opdracht voor het maken van een [externe gegevens bron](/sql/t-sql/statements/create-external-data-source-transact-sql) om de locatie van de gegevens op te slaan. Als u verificatie uitvoert met AAD Pass-Through, is de para meter CREDENTIAL niet vereist. 

```sql
-- C (for Gen1): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSCredential
);

-- C (for Gen2): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen2 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION='abfs[s]://<container>@<AzureDataLake account_name>.dfs.core.windows.net', -- Please note the abfss endpoint for when your account has secure transfer enabled
    CREDENTIAL = ADLSCredential
);
```

## <a name="configure-data-format"></a>Gegevens indeling configureren
Als u de gegevens uit Data Lake Storage wilt importeren, moet u de externe bestands indeling opgeven. Dit object bepaalt hoe de bestanden worden geschreven in Data Lake Storage.
Bekijk voor de volledige lijst onze T-SQL-documentatie [externe BESTANDS indeling maken](/sql/t-sql/statements/create-external-file-format-transact-sql)

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

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
Nu u de gegevens bron en bestands indeling hebt opgegeven, bent u klaar om de externe tabellen te maken. Externe tabellen zijn de manier waarop u met externe gegevens communiceert. Met de locatie parameter kunt u een bestand of een map opgeven. Als u een map opgeeft, worden alle bestanden in de map geladen.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStorage
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Overwegingen voor externe tabellen
Het is eenvoudig om een externe tabel te maken, maar er zijn enkele nuances die moeten worden besproken.

Externe tabellen zijn sterk getypeerd. Dit betekent dat elke rij van de gegevens die worden opgenomen, moet voldoen aan de definitie van het tabel schema.
Als een rij niet overeenkomt met de schema definitie, wordt de rij van de belasting geweigerd.

Met de opties REJECT_TYPE en REJECT_VALUE kunt u definiëren hoeveel rijen of welk percentage van de gegevens in de uiteindelijke tabel aanwezig moeten zijn. Als de afwijzings waarde is bereikt tijdens het laden, mislukt de belasting. De meest voorkomende oorzaak van geweigerde rijen is een niet-overeenkomende schema definitie. Als een kolom bijvoorbeeld foutief het schema van int krijgt wanneer de gegevens in het bestand een teken reeks zijn, kan elke rij niet worden geladen.

Data Lake Storage Gen1 gebruikt op rollen gebaseerd Access Control (RBAC) om de toegang tot de gegevens te beheren. Dit betekent dat de Service-Principal Lees machtigingen moet hebben voor de mappen die zijn gedefinieerd in de para meter locatie en naar de onderliggende map en bestanden. Dit maakt poly base mogelijk voor het verifiëren en laden van die gegevens. 

## <a name="load-the-data"></a>De gegevens laden
Als u gegevens uit Data Lake Storage wilt laden, gebruikt u de instructie [Create Table als Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) . 

CTAS maakt een nieuwe tabel en vult deze met de resultaten van een SELECT-instructie. CTAS definieert de nieuwe tabel om dezelfde kolommen en gegevens typen te hebben als de resultaten van de SELECT-instructie. Als u alle kolommen uit een externe tabel selecteert, is de nieuwe tabel een replica van de kolommen en gegevens typen in de externe tabel.

In dit voor beeld maken we een gedistribueerde hash-tabel met de naam DimProduct vanuit onze externe tabel DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Column Store-compressie optimaliseren
Tabellen worden standaard gedefinieerd als een geclusterde column store-index. Nadat het laden is voltooid, zijn sommige gegevens rijen mogelijk niet gecomprimeerd naar de column Store.  Er zijn verschillende redenen waarom dit kan gebeuren. Zie [Column Store-indexen beheren](sql-data-warehouse-tables-index.md)voor meer informatie.

Als u de query prestaties en column Store-compressie wilt optimaliseren na een laad opdracht, bouwt u de tabel opnieuw op om ervoor te zorgen dat de column store-index alle rijen kan comprimeren.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Statistieken optimaliseren
Het is aan te raden om statistieken voor één kolom direct na een belasting te maken. Er zijn enkele keuzes voor statistieken. Als u bijvoorbeeld statistieken voor één kolom maakt voor elke kolom, kan het lang duren om alle statistieken opnieuw samen te stellen. Als u weet dat bepaalde kolommen zich niet in query predikaten bevinden, kunt u het maken van statistieken voor die kolommen overs Laan.

Als u besluit om met één kolom statistieken te maken voor elke kolom van elke tabel, kunt u de voor beeld-`prc_sqldw_create_stats` code van de opgeslagen procedure in het [statistiek](sql-data-warehouse-tables-statistics.md) artikel gebruiken.

Het volgende voor beeld is een goed uitgangs punt voor het maken van statistieken. Er worden statistieken voor één kolom gemaakt voor elke kolom in de dimensie tabel en voor elke join-kolom in de feiten tabellen. U kunt later altijd statistieken met één of meerdere kolommen toevoegen aan andere feiten tabel kolommen.

## <a name="achievement-unlocked"></a>Prestaties vergren delen.
U hebt gegevens geladen in uw data warehouse. Fantastische taak!

## <a name="next-steps"></a>Volgende stappen 
In deze zelf studie hebt u externe tabellen gemaakt om de structuur te definiëren voor gegevens die zijn opgeslagen in Data Lake Storage Gen1. vervolgens gebruikt u de poly base-CREATE TABLE als SELECT-instructie voor het laden van gegevens in uw data warehouse. 

U hebt het volgende gedaan:
> [!div class="checklist"]
>
> * Er zijn data base-objecten gemaakt die nodig zijn om te laden uit Data Lake Storage.
> * Verbonden met een Data Lake Storage Directory.
> * De gegevens zijn geladen in het Data Warehouse.
>

Het laden van gegevens is de eerste stap bij het ontwikkelen van een Data Warehouse-oplossing met behulp van Azure Synapse Analytics. Bekijk onze ontwikkelings bronnen.

> [!div class="nextstepaction"]
> [Meer informatie over het ontwikkelen van tabellen voor gegevens opslag](sql-data-warehouse-tables-overview.md)
