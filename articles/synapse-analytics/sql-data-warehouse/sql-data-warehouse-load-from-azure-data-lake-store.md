---
title: Zelfstudieladen gegevens van Azure Data Lake Storage
description: Gebruik externe tabellen van PolyBase om gegevens uit Azure Data Lake Storage voor Synapse SQL te laden.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/08/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 9713d73ee132f743ceea98cbaca6a83f36fd3a45
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416117"
---
# <a name="load-data-from-azure-data-lake-storage-for-sql-analytics"></a>Gegevens laden vanuit Azure Data Lake Storage voor SQL Analytics

In deze handleiding wordt beschreven hoe u externe tabellen van PolyBase gebruiken om gegevens uit Azure Data Lake Storage te laden. Hoewel u adhocquery's uitvoeren op gegevens die zijn opgeslagen in Data Lake Storage, raden we u aan de gegevens te importeren voor de beste prestaties.

> [!NOTE]  
> Een alternatief voor laden is de [copy-instructie](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) die momenteel in openbare preview staat.  De COPY-instructie biedt de meeste flexibiliteit. Als u feedback wilt geven over de kopie-instructie, stuurt u een e-mail naar de volgende distributielijst: sqldwcopypreview@service.microsoft.com.
>
> [!div class="checklist"]
>
> * Maak databaseobjecten die nodig zijn om te laden vanuit Data Lake Storage.
> * Maak verbinding met een gegevensmap voor gegevensopslag.
> * Gegevens laden in het datawarehouse.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

Download en installeer voordat u met deze zelfstudie begint de nieuwste versie van [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SQL Server Management Studio).

Als u deze zelfstudie wilt uitvoeren, moet u het sein

* Een SQL-pool. Zie [Een SQL-groep en querygegevens maken](create-data-warehouse-portal.md).
* Een Data Lake Storage-account. Zie [Aan de slag met Azure Data Lake Storage](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Voor dit opslagaccount moet u een van de volgende referenties configureren of opgeven om te laden: een opslagaccountsleutel, een Azure Directory-toepassingsgebruiker of een AAD-gebruiker die de juiste RBAC-rol heeft voor het opslagaccount.

## <a name="create-a-credential"></a>Een referentie maken

U deze sectie overslaan en overgaan tot De externe gegevensbron maken wanneer u de AAD-doorgeefinformatie gebruikt. Een databasemet scoped referentie is niet vereist om te worden gemaakt of opgegeven bij het gebruik van AAD-doorgeefservice, maar zorg ervoor dat uw AAD-gebruiker de juiste RBAC-rol (Storage Blob Data Reader, Contributor of Owner Role) heeft voor het opslagaccount. Meer details worden [hier](https://techcommunity.microsoft.com/t5/Azure-SQL-Data-Warehouse/How-to-use-PolyBase-by-authenticating-via-AAD-pass-through/ba-p/862260)beschreven.

Als u toegang wilt krijgen tot uw Data Lake Storage-account, moet u een Database Master-sleutel maken om uw referentiesgeheim te versleutelen. Vervolgens maakt u een databasescopedreferentie om uw geheim op te slaan. Wanneer u de serviceprincipals (Azure Directory Application-gebruiker) authenticeert, worden de serviceprincipal-referenties van de service opgeslagen in AAD. U de databasescopedreferentie ook gebruiken om de opslagaccountsleutel voor Gen2 op te slaan.

Als u verbinding wilt maken met Data Lake Storage met serviceprincipals, moet u **eerst** een Azure Active Directory-toepassing maken, een toegangssleutel maken en de toepassing toegang verlenen tot het Data Lake Storage-account. Zie [Authenticeren naar Azure Data Lake-opslag met Active Directory](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)voor instructies.

Meld u aan bij uw SQL-groep met machtigingen voor besturingselementen met CONTROL-niveau en voer de volgende SQL-instructies uit tegen uw database:

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest

CREATE MASTER KEY;


-- B (for service principal authentication): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest

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

## <a name="create-the-external-data-source"></a>De externe gegevensbron maken

Gebruik deze opdracht [EXTERNE GEGEVENSBRON MAKEN](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om de locatie van de gegevens op te slaan. Als u authenticeren met AAD-doorwerking, is de parameter CREDENTIAL niet vereist. Als u Managed Identity voor serviceeindpunten authenticert, volgt u deze [documentatie](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-sql-data-warehouse-polybase) om de externe gegevensbron in te stellen.

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

## <a name="configure-data-format"></a>Gegevensindeling configureren

Als u de gegevens uit Data Lake Storage wilt importeren, moet u de externe bestandsindeling opgeven. Met dit object wordt gedefinieerd hoe de bestanden worden geschreven in Data Lake Storage.
Bekijk voor de volledige lijst onze T-SQL-documentatie [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

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

Nu u de gegevensbron en bestandsindeling hebt opgegeven, bent u klaar om de externe tabellen te maken. Externe tabellen zijn de manier waarop u met externe gegevens omgaat. De locatieparameter kan een bestand of een map opgeven. Als er een map wordt opgegeven, worden alle bestanden in de map geladen.

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

## <a name="external-table-considerations"></a>Externe tabeloverwegingen

Het maken van een externe tabel is eenvoudig, maar er zijn enkele nuances die moeten worden besproken.

Externe tabellen zijn sterk getypt. Dit betekent dat elke rij van de gegevens die worden ingenomen, moet voldoen aan de definitie van het tabelschema.
Als een rij niet overeenkomt met de schemadefinitie, wordt de rij van de belasting afgewezen.

Met de opties REJECT_TYPE en REJECT_VALUE u bepalen hoeveel rijen of welk percentage van de gegevens in de laatste tabel aanwezig moet zijn. Tijdens de belasting, als de afwijswaarde wordt bereikt, mislukt de belasting. De meest voorkomende oorzaak van afgewezen rijen is een mismatch bij de schemadefinitie. Als een kolom bijvoorbeeld ten onrechte het schema van int krijgt wanneer de gegevens in het bestand een tekenreeks zijn, wordt elke rij niet geladen.

Data Lake Storage Gen1 gebruikt RBAC (Role Based Access Control) om de toegang tot de gegevens te beheren. Dit betekent dat de Service Principal leesmachtigingen moet hebben voor de mappen die zijn gedefinieerd in de locatieparameter en aan de kinderen van de uiteindelijke map en bestanden. Hierdoor kan PolyBase die gegevens verifiëren en laden.

## <a name="load-the-data"></a>De gegevens laden

Als u gegevens uit Data Lake Storage wilt laden, gebruikt u de instructie [CREATE TABLE AS SELECT (Transact-SQL).](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

CTAS maakt een nieuwe tabel en vult deze met de resultaten van een selecte instructie. CTAS definieert de nieuwe tabel met dezelfde kolommen en gegevenstypen als de resultaten van de select-instructie. Als u alle kolommen uit een externe tabel selecteert, is de nieuwe tabel een replica van de kolommen en gegevenstypen in de externe tabel.

In dit voorbeeld maken we een hash distributed table genaamd DimProduct uit onze externe tabel DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```

## <a name="optimize-columnstore-compression"></a>Compressie van kolomarchief optimaliseren

Tabellen worden standaard gedefinieerd als een geclusterde kolomarchiefindex. Nadat een belasting is voltooid, worden sommige gegevensrijen mogelijk niet gecomprimeerd in het kolomarchief.  Er zijn verschillende redenen waarom dit kan gebeuren. Zie [kolomarchiefindexen beheren](sql-data-warehouse-tables-index.md)voor meer informatie .

Als u de queryprestaties en compressie van het kolomarchief na een belasting wilt optimaliseren, bouwt u de tabel opnieuw om de kolomarchiefindex te dwingen alle rijen te comprimeren.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Statistieken optimaliseren

Het is het beste om statistieken met één kolom direct na een belasting te maken. Er zijn een aantal keuzes voor statistieken. Als u bijvoorbeeld statistieken met één kolom maakt voor elke kolom, kan het lang duren voordat alle statistieken opnieuw zijn opgebouwd. Als u weet dat bepaalde kolommen niet in querypredicaten staan, u het maken van statistieken over die kolommen overslaan.

Als u besluit om statistieken met één kolom te maken voor elke `prc_sqldw_create_stats` kolom van elke tabel, u het voorbeeld van de opgeslagen procedurecode gebruiken in het [statistiekartikel.](sql-data-warehouse-tables-statistics.md)

Het volgende voorbeeld is een goed uitgangspunt voor het maken van statistieken. Er worden statistieken met één kolom voor elke kolom in de dimensietabel en op elke verbindingskolom in de feitentabellen. U later altijd statistieken met één of meerdere kolommen toevoegen aan andere kolommen met feitentabel.

## <a name="achievement-unlocked"></a>Prestatie ontgrendeld!

U hebt gegevens met succes in uw datawarehouse geladen. Geweldig werk!

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u externe tabellen gemaakt om de structuur te definiëren voor gegevens die zijn opgeslagen in Data Lake Storage Gen1 en vervolgens de tabel PolyBase CREATE ALS SELECT-instructie gebruikt om gegevens in uw gegevensmagazijn te laden.

U hebt het volgende gedaan:
> [!div class="checklist"]
>
> * Gemaakt databaseobjecten die moeten worden geladen vanuit Data Lake Storage.
> * Verbonden met een gegevensmap voor gegevensopslag.
> * Geladen gegevens in het datawarehouse.
>

Het laden van gegevens is de eerste stap naar het ontwikkelen van een datawarehouse-oplossing met Azure Synapse Analytics. Bekijk onze ontwikkelingsbronnen.

> [!div class="nextstepaction"]
> [Meer informatie over het ontwikkelen van tabellen voor gegevensopslag](sql-data-warehouse-tables-overview.md)
