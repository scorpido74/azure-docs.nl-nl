---
title: Gegevens kopiëren naar/vanuit Azure SQL Data Warehouse
description: Meer informatie over het kopiëren van gegevens naar/vanuit Azure SQL Data Warehouse met Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 2df49e65603573e4a3adcdda0635982252e70b18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130820"
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Gegevens van en naar Azure SQL Data Warehouse kopiëren met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Versie 2 (huidige versie)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [Azure SQL Data Warehouse-connector in V2](../connector-azure-sql-data-warehouse.md)als u de huidige versie van de Data Factory-service gebruikt.

In dit artikel wordt uitgelegd hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens naar/vanuit Azure SQL Data Warehouse te verplaatsen. Het bouwt voort op het artikel [Data Movement Activities,](data-factory-data-movement-activities.md) dat een algemeen overzicht geeft van gegevensverplaatsing met de kopieeractiviteit.

> [!TIP]
> Gebruik PolyBase om gegevens in Azure SQL Data Warehouse te laden om de beste prestaties te bereiken. De sectie [PolyBase gebruiken om gegevens te laden in Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) bevat details. Zie 1 TB laden in [Azure SQL Data Warehouse binnen 15 minuten laden met Azure Data Factory](data-factory-load-sql-data-warehouse.md)voor een walkthrough met een use case.

## <a name="supported-scenarios"></a>Ondersteunde scenario's
U gegevens **uit Azure SQL Data Warehouse** kopiëren naar de volgende gegevensopslag:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

U gegevens uit de volgende gegevensarchieven kopiëren **naar Azure SQL Data Warehouse:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Wanneer gegevens van SQL Server of Azure SQL Database naar Azure SQL Data Warehouse worden gekopieerd, kan Data Factory, als de tabel niet in het doelarchief bestaat, de tabel in SQL Data Warehouse automatisch maken met behulp van het schema van de tabel in het brongegevensarchief. Zie [Automatisch maken van tabelvoor](#auto-table-creation) meer informatie.

## <a name="supported-authentication-type"></a>Ondersteund verificatietype
Azure SQL Data Warehouse-connector ondersteunt basisverificatie.

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens van/naar een Azure SQL Data Warehouse verplaatst met behulp van verschillende hulpprogramma's/API's.

De eenvoudigste manier om een pijplijn te maken die gegevens kopieert naar/van Azure SQL Data Warehouse, is door de wizard Gegevens kopiëren te gebruiken. Zie [Zelfstudie: Gegevens laden in SQL Data Warehouse met Data Factory](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) voor een snelle walkthrough voor het maken van een pijplijn met de wizard Gegevens kopiëren.

U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-sjabloon,** **.NET API**en REST **API**. Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken.

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak een **gegevensfabriek**. Een gegevensfabriek kan een of meer pijplijnen bevatten. 
2. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek. Als u bijvoorbeeld gegevens kopieert van een Azure blob-opslag naar een Azure SQL-gegevensmagazijn, maakt u twee gekoppelde services om uw Azure-opslagaccount en Azure SQL-gegevensmagazijn te koppelen aan uw gegevensfabriek. Zie [sectie gekoppelde serviceeigenschappen](#linked-service-properties) voor gekoppelde service-eigenschappen die specifiek zijn voor Azure SQL Data Warehouse. 
3. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven. In het voorbeeld dat in de laatste stap wordt genoemd, maakt u een gegevensset om de blobcontainer en -map op te geven die de invoergegevens bevat. En u maakt een andere gegevensset om de tabel op te geven in het Azure SQL-gegevensmagazijn met de gegevens die zijn gekopieerd uit de blob-opslag. Zie sectie [gegevensseteigenschappen](#dataset-properties) voor gegevensseteigenschappen die specifiek zijn voor Azure SQL Data Warehouse.
4. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt. In het eerder genoemde voorbeeld gebruikt u BlobSource als bron en SqlDWSink als een sink voor de kopieeractiviteit. Als u ook kopieert van Azure SQL Data Warehouse naar Azure Blob Storage, gebruikt u SqlDWSource en BlobSink in de kopieeractiviteit. Zie [sectie activiteitseigenschappen kopiëren](#copy-activity-properties) voor eigenschappen voor kopiëren die specifiek zijn voor Azure SQL Data Warehouse. Klik op de koppeling in de vorige sectie voor uw gegevensarchief voor meer informatie over het gebruik van een gegevensarchief als bron of gootsteen.

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u tools/API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling. Zie [JSON-voorbeelden](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) sectie van dit artikel voor voorbeelden van JSON-instellingen voor entiteiten in Gegevensfabriek die worden gebruikt om gegevens naar/vanuit een Azure SQL Data Warehouse te kopiëren.

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor Azure SQL Data Warehouse:

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor de gekoppelde azure SQL Data Warehouse-service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet zijn ingesteld op: **AzureSqlDW** |Ja |
| Connectionstring |Geef informatie op die nodig is om verbinding te maken met de instantie Azure SQL Data Warehouse voor de eigenschap connectionString. Alleen basisverificatie wordt ondersteund. |Ja |

> [!IMPORTANT]
> Configureer [Azure SQL Database Firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) en de databaseserver zodat Azure Services toegang heeft tot de [server.](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) Als u gegevens naar Azure SQL Data Warehouse kopieert van buiten Azure, inclusief van on-premises gegevensbronnen met een datafabriekgateway, configureert u bovendien het juiste IP-adresbereik voor de machine die gegevens naar Azure SQL Data Warehouse verzendt.

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties zoals structuur, beschikbaarheid en beleid van een gegevensset JSON zijn vergelijkbaar voor alle gegevenssettypen (Azure SQL, Azure blob, Azure table, etc.).

De sectie typeEigenschappen is verschillend voor elk type gegevensset en geeft informatie over de locatie van de gegevens in het gegevensarchief. De sectie **typeEigenschappen** voor de gegevensset van het type **AzureSqlDWTable** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel of weergave in de Azure SQL Data Warehouse-database waarnaar de gekoppelde service verwijst. |Ja |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [Pijplijnmaken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen en beleid zijn beschikbaar voor alle soorten activiteiten.

> [!NOTE]
> De kopieeractiviteit neemt slechts één invoer en produceert slechts één uitvoer.

Overwegende dat de eigenschappen die beschikbaar zijn in de sectie typeEigenschappen van de activiteit per activiteitstype verschillen. Voor Kopieeractiviteit variëren ze afhankelijk van de soorten bronnen en putten.

### <a name="sqldwsource"></a>SqlDWSource SqlDWSource
Wanneer de bron sqldwsource van het type **SqlDWSource is,** zijn de volgende eigenschappen beschikbaar in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| sqlReaderQuery |Gebruik de aangepaste query om gegevens te lezen. |SQL-querytekenreeks. Selecteer bijvoorbeeld * in MyTable. |Nee |
| sqlReaderStoredProcedureName |Naam van de opgeslagen procedure die gegevens uit de brontabel leest. |Naam van de opgeslagen procedure. De laatste SQL-instructie moet een SELECT-instructie in de opgeslagen procedure zijn. |Nee |
| storedProcedureParameters |Parameters voor de opgeslagen procedure. |Naam/waardeparen. Namen en omhulsel van parameters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedureparameters. |Nee |

Als de **sqlReaderQuery** is opgegeven voor de SqlDWSource, voert de kopieeractiviteit deze query uit op de Azure SQL Data Warehouse-bron om de gegevens te krijgen.

U ook een opgeslagen procedure opgeven door de **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven (als de opgeslagen procedure parameters neemt).

Als u sqlReaderQuery of sqlReaderStoredProcedureName niet opgeeft, worden de kolommen die zijn gedefinieerd in de structuursectie van de gegevensset JSON gebruikt om een query te bouwen die wordt uitgevoerd tegen het Azure SQL Data Warehouse. Bijvoorbeeld: `select column1, column2 from mytable`. Als de gegevenssetdefinitie de structuur niet heeft, worden alle kolommen geselecteerd uit de tabel.

#### <a name="sqldwsource-example"></a>SqlDWSource voorbeeld

```JSON
"source": {
    "type": "SqlDWSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**De definitie van de opgeslagen procedure:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqldwsink"></a>SqlDWSink SqlDWSink
**SqlDWSink** ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| sqlWriterCleanupScript sqlWriterCleanupScript |Geef een query op voor Activiteit kopiëren om zodanig uit te voeren dat gegevens van een specifiek segment worden opgeschoond. Zie voor meer informatie [de sectie herhaalbaarheid](#repeatability-during-copy). |Een queryverklaring. |Nee |
| allowPolyBase |Geeft aan of polybase (indien van toepassing) moet worden gebruikt in plaats van bulkinsert-mechanisme. <br/><br/> **Het gebruik van PolyBase is de aanbevolen manier om gegevens in SQL Data Warehouse te laden.** Zie [PolyBase gebruiken om gegevens te laden in de](#use-polybase-to-load-data-into-azure-sql-data-warehouse) sectie Azure SQL Data Warehouse voor beperkingen en details. |True <br/>False (standaard) |Nee |
| polyBaseInstellingen |Een groep eigenschappen die kan worden opgegeven wanneer de eigenschap **allowPolybase** is ingesteld op **true**. |&nbsp; |Nee |
| rejectValue |Hiermee geeft u het aantal of het percentage rijen op dat kan worden geweigerd voordat de query mislukt. <br/><br/>Meer informatie over de afwijsopties van PolyBase vindt u in de sectie **Argumenten** van het onderwerp [TRANSact-SQL maken.](https://msdn.microsoft.com/library/dn935021.aspx) |0 (standaard), 1, 2, ... |Nee |
| rejectType |Hiermee geeft u op of de optie rejectValue is opgegeven als een letterlijke waarde of een percentage. |Waarde (standaard), Percentage |Nee |
| rejectSampleValue |Hiermee bepaalt u het aantal rijen dat moet worden opgehaald voordat de PolyBase het percentage afgewezen rijen opnieuw berekent. |1, 2, ... |Ja, als **rejectType** **percentage** is |
| useTypeDefault |Hiermee geeft u op hoe ontbrekende waarden in afgebakende tekstbestanden moeten worden verwerkt wanneer PolyBase gegevens uit het tekstbestand ophaalt.<br/><br/>Meer informatie over deze eigenschap vindt u in de sectie Argumenten in [EXTERNE BESTANDSINDELING MAKEN (Transact-SQL).](https://msdn.microsoft.com/library/dn935026.aspx) |True, False (standaard) |Nee |
| writeBatchSize |Hiermee voegt u gegevens in de SQL-tabel in wanneer de buffergrootte writeBatchSize bereikt |Geheel getal (aantal rijen) |Nee (standaard: 10000) |
| writeBatchTimeout |Wacht de tijd voordat de batchinvoegbewerking is voltooid voordat deze een time-out heeft. |Tijdspanne<br/><br/> Voorbeeld: "00:30:00" (30 minuten). |Nee |

#### <a name="sqldwsink-example"></a>SqlDWSink voorbeeld

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>PolyBase gebruiken om gegevens te laden in Azure SQL Data Warehouse
Het gebruik van **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** is een efficiënte manier om grote hoeveelheden gegevens te laden in Azure SQL Data Warehouse met een hoge doorvoer. U een grote winst in de doorvoer zien door PolyBase te gebruiken in plaats van het standaard BULKINSERT-mechanisme. Zie [referentienummer voor kopieerprestaties](data-factory-copy-activity-performance.md#performance-reference) met gedetailleerde vergelijking. Zie 1 TB laden in [Azure SQL Data Warehouse binnen 15 minuten laden met Azure Data Factory](data-factory-load-sql-data-warehouse.md)voor een walkthrough met een use case.

* Als uw brongegevens zich in **Azure Blob of Azure Data Lake Store**bevinden en de indeling compatibel is met PolyBase, u rechtstreeks naar Azure SQL Data Warehouse kopiëren met PolyBase. Zie **[Direct kopiëren met PolyBase](#direct-copy-using-polybase)** met details.
* Als uw brongegevensarchief en -indeling oorspronkelijk niet door PolyBase worden ondersteund, u in plaats daarvan de functie Gefaseerde kopie gebruiken **[met polybase.](#staged-copy-using-polybase)** Het biedt u ook een betere doorvoer door de gegevens automatisch om te zetten in polybase-compatibele indeling en de gegevens op te slaan in Azure Blob-opslag. Vervolgens worden gegevens geladen in SQL Data Warehouse.

Stel `allowPolyBase` de eigenschap **in op true** zoals in het volgende voorbeeld voor Azure Data Factory om PolyBase te gebruiken om gegevens naar Azure SQL Data Warehouse te kopiëren. Wanneer u toestaanPolyBase instelt op true, `polyBaseSettings` u PolyBase-specifieke eigenschappen opgeven met behulp van de eigenschappengroep. zie de sectie [SqlDWSink](#sqldwsink) voor meer informatie over eigenschappen die u gebruiken met polyBaseSettings.

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

### <a name="direct-copy-using-polybase"></a>Directe kopie met PolyBase
SQL Data Warehouse PolyBase ondersteunt rechtstreeks Azure Blob en Azure Data Lake Store (met serviceprincipal) als bron en met specifieke vereisten voor bestandsindeling. Als uw brongegevens voldoen aan de criteria die in deze sectie worden beschreven, u rechtstreeks kopiëren van het brongegevensarchief naar Azure SQL Data Warehouse met PolyBase. Anders u Gefaseerde kopie gebruiken [met PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Als u gegevens van Data Lake Store naar SQL Data Warehouse efficiënt wilt kopiëren, u meer informatie vinden in [Azure Data Factory, waardoor het nog eenvoudiger en handiger is om inzichten uit gegevens te ontdekken wanneer u Data Lake Store gebruikt met SQL Data Warehouse.](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)

Als niet aan de vereisten wordt voldaan, controleert Azure Data Factory de instellingen en valt automatisch terug naar het BULKINSERT-mechanisme voor de gegevensverplaatsing.

1. **Source linked service** is van type: **AzureStorage** of **AzureDataLakeStore met serviceprincipal authentication**.
2. De **invoergegevensset** is van type: **AzureBlob** of **AzureDataLakeStore**en het opmaaktype `type` onder eigenschappen is **OrcFormat,** **ParquetFormat**of **TextFormat** met de volgende configuraties:

   1. `rowDelimiter`moet **\n**zijn .
   2. `nullValue`is ingesteld op **lege** tekenreeks `treatEmptyAsNull` (""), of is ingesteld op **true**.
   3. `encodingName`is ingesteld op **utf-8**, wat **standaardwaarde** is.
   4. `escapeChar`, `quoteChar` `firstRowAsHeader`, `skipLineCount` en zijn niet gespecificeerd.
   5. `compression`kan **geen compressie**zijn, **GZip**of **Leeglopen.**

      ```JSON
      "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
      },
      ```

3. Er is `skipHeaderLineCount` geen instelling onder **BlobSource** of **AzureDataLakeStore** voor de kopieeractiviteit in de pijplijn.
4. Er is `sliceIdentifierColumnName` geen instelling onder **SqlDWSink** voor de activiteit Kopiëren in de pijplijn. (PolyBase garandeert dat alle gegevens worden bijgewerkt of niets wordt bijgewerkt in een enkele run. Om **herhaalbaarheid**te bereiken, u gebruiken). `sqlWriterCleanupScript`
5. Er wordt `columnMapping` geen wordt gebruikt in de bijbehorende activiteit Kopiëren.

### <a name="staged-copy-using-polybase"></a>Gefaseerde kopie met PolyBase
Wanneer uw brongegevens niet voldoen aan de criteria die in de vorige sectie zijn geïntroduceerd, u het kopiëren van gegevens inschakelen via een tijdelijke tijdelijke azure blob-opslag (kan geen Premium-opslag zijn). In dit geval voert Azure Data Factory automatisch transformaties uit op de gegevens om te voldoen aan de vereisten voor gegevensindeling van PolyBase, vervolgens PolyBase te gebruiken om gegevens in SQL Data Warehouse te laden en eindelijk uw tijdelijke gegevens op te schonen uit de Blob-opslag. Zie [Gefaseerde kopie](data-factory-copy-activity-performance.md#staged-copy) voor meer informatie over hoe het kopiëren van gegevens via een Azure Blob in het algemeen werkt.

> [!NOTE]
> Wanneer u gegevens kopieert van een on-premises gegevensarchief naar Azure SQL Data Warehouse met PolyBase en staging, als uw Data Management Gateway-versie lager is dan 2.4, is JRE (Java Runtime Environment) vereist op uw gatewaymachine die wordt gebruikt om uw bron te transformeren gegevens in de juiste indeling. Stel voor dat u uw gateway upgradet naar de laatste om dergelijke afhankelijkheid te voorkomen.
>

Als u deze functie wilt gebruiken, maakt u een [gekoppelde Azure Storage-service](data-factory-azure-blob-connector.md#azure-storage-linked-service) `enableStaging` die `stagingSettings` verwijst naar het Azure Storage-account met de tussentijdse blobopslag en geeft u de eigenschappen en eigenschappen op voor de kopieeractiviteit zoals weergegeven in de volgende code:

```json
"activities":[
{
    "name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlDwSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob"
        }
    }
}
]
```

## <a name="best-practices-when-using-polybase"></a>Aanbevolen procedures bij het gebruik van PolyBase
De volgende secties bieden aanvullende aanbevolen procedures voor de aanbevolen procedures die worden genoemd in [Best practices voor Azure SQL Data Warehouse.](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md)

### <a name="required-database-permission"></a>Vereiste databasemachtigingen
Om PolyBase te gebruiken, moet de gebruiker worden gebruikt om gegevens te laden in SQL Data Warehouse heeft de ["CONTROL" toestemming](https://msdn.microsoft.com/library/ms191291.aspx) op de doeldatabase. Een manier om dat te bereiken is om die gebruiker toe te voegen als lid van "db_owner" rol. Meer informatie over hoe u dit doen door [deze sectie](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)te volgen.

### <a name="row-size-and-data-type-limitation"></a>Rijgrootte en beperking van het gegevenstype
Polybase-belastingen zijn beperkt tot het laden van rijen die kleiner zijn dan **1 MB** en kunnen niet worden geladen op VARCHR(MAX), NVARCHAR(MAX) of VARBINARY (MAX). Zie [hier](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Als u brongegevens hebt met rijen van groter dan 1 MB, u de brontabellen verticaal splitsen in verschillende kleine gegevens waar de grootste rijgrootte van elk van deze tabellen de limiet niet overschrijdt. De kleinere tabellen kunnen vervolgens worden geladen met PolyBase en samengevoegd in Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse-resourceklasse
Om de best mogelijke doorvoer te bereiken, u overwegen om grotere resourceklasse toe te wijzen aan de gebruiker die wordt gebruikt om gegevens via PolyBase in SQL Data Warehouse te laden. Meer informatie over hoe u dit doen door [het voorbeeld van een gebruikersbronklasse](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md)wijzigen te volgen.

### <a name="tablename-in-azure-sql-data-warehouse"></a>tableName in Azure SQL Data Warehouse
In de volgende tabel vindt u voorbeelden over het opgeven van de eigenschap **tableName** in de json van de gegevensset voor verschillende combinaties van schema en tabelnaam.

| DB-schema | Tabelnaam | tableName JSON, eigenschap |
| --- | --- | --- |
| Dbo |Mytable |MyTable of dbo. MyTable of [dbo]. [Mijntafel] |
| dbo1 dbo1 |Mytable |dbo1. MyTable of [dbo1]. [Mijntafel] |
| Dbo |Mijn.Tabel |[Mijn.Tafel] of [dbo]. [Mijn.Tabel] |
| dbo1 dbo1 |Mijn.Tabel |[dbo1]. [Mijn.Tabel] |

Als u de volgende fout ziet, kan dit een probleem zijn met de waarde die u hebt opgegeven voor de eigenschap tableName. Zie de tabel voor de juiste manier om waarden op te geven voor de eigenschap tabelName JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Kolommen met standaardwaarden
Momenteel accepteert de PolyBase-functie in Gegevensfabriek alleen hetzelfde aantal kolommen als in de doeltabel. U hebt bijvoorbeeld een tabel met vier kolommen en een van deze kolommen wordt gedefinieerd met een standaardwaarde. De invoergegevens moeten nog steeds vier kolommen bevatten. Als u een gegevensset met invoer met drie kolommen opgeeft, levert dit een fout op die vergelijkbaar is met het volgende bericht:

```
All columns of the table must be specified in the INSERT BULK statement.
```
NULL-waarde is een speciale vorm van standaardwaarde. Als de kolom nietig kan worden verklaard, kunnen de invoergegevens (in blob) voor die kolom leeg zijn (mag niet ontbreken in de invoergegevensset). PolyBase voegt NULL voor deze in het Azure SQL Data Warehouse.

## <a name="auto-table-creation"></a>Autotabelcreatie
Als u wizard Kopiëren gebruikt om gegevens van SQL Server of Azure SQL Database naar Azure SQL Data Warehouse te kopiëren en de tabel die overeenkomt met de brontabel niet bestaat in het doelarchief, kan Data Factory de tabel in het gegevensmagazijn automatisch maken door met behulp van het schema van de brontabel.

Data Factory maakt de tabel in het doelarchief met dezelfde tabelnaam in het brongegevensarchief. De gegevenstypen voor kolommen worden gekozen op basis van de volgende typetoewijzing. Indien nodig voert het typeconversies uit om eventuele onverenigbaarheden tussen bron- en bestemmingsopslag op te lossen. Het maakt ook gebruik van Round Robin tafeldistributie.

| Kolomtype Bron SQL-database | Kolomtype DoelSQL DW (groottebeperking) |
| --- | --- |
| Int | Int |
| Bigint | Bigint |
| Smallint | Smallint |
| TinyInt TinyInt | TinyInt TinyInt |
| Beetje | Beetje |
| Decimal | Decimal |
| Numeriek | Decimal |
| Drijvend | Drijvend |
| Money | Money |
| Echte | Echte |
| KleinGeld | KleinGeld |
| Binair | Binair |
| Varbinary (Varbinary) | Varbinary (tot 8000) |
| Date | Date |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Time | Time |
| Datumtijdverschuiving | Datumtijdverschuiving |
| SmallDateTime | SmallDateTime |
| Tekst | Varchar (tot 8000) |
| NText | NVarChar (tot 4000) |
| Installatiekopie | VarBinary (tot 8000) |
| UniekId | UniekId |
| Char | Char |
| Nchar | Nchar |
| Varchar | VarChar (tot 8000) |
| Nvarchar | NVarChar (tot 4000) |
| Xml | Varchar (tot 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Typetoewijzing voor Azure SQL Data Warehouse
Zoals vermeld in het artikel [gegevensverplaatsingsactiviteiten](data-factory-data-movement-activities.md) voert kopieeractiviteit automatische typeconversies uit van brontypen naar sinktypen met de volgende benadering in twee stappen:

1. Converteren van native brontypen naar .NET-type
2. Converteren van .NET-type naar native sinktype

Wanneer gegevens worden verplaatst naar & vanuit Azure SQL Data Warehouse, worden de volgende toewijzingen gebruikt van SQL-type naar .NET-type en vice versa.

De toewijzing is hetzelfde als de [SQL Server Data Type Mapping voor ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| SQL Server Database Engine-type | .NET Framework type |
| --- | --- |
| bigint |Int64 |
| binair |Byte |
| bit |Booleaans |
| Char |Tekenreeks, Char[] |
| date |DateTime |
| Datum/tijd |DateTime |
| datetime2 |DateTime |
| Datumtijdverschuiving |Datumtijdverschuiving |
| Decimal |Decimal |
| FILESTREAM-kenmerk (varbinary(max.) |Byte |
| Drijvend |Double |
| installatiekopie |Byte |
| int |Int32 |
| Geld |Decimal |
| Nchar |Tekenreeks, Char[] |
| ntekst |Tekenreeks, Char[] |
| numeriek |Decimal |
| nvarchar |Tekenreeks, Char[] |
| real |Enkel |
| rijversie |Byte |
| smalldatetijd |DateTime |
| smallint |Int16 |
| kleingeld |Decimal |
| sql_variant |Object * |
| tekst |Tekenreeks, Char[] |
| tijd |TimeSpan |
| tijdstempel |Byte |
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary varbinary |Byte |
| varchar |Tekenreeks, Char[] |
| xml |Xml |

U kolommen van brongegevensset ook toewijzen aan kolommen uit de sink-gegevensset in de definitie van kopieeractiviteit. Zie Kolommen [met gegevenssetstoewijzen in Azure Data Factory](data-factory-map-columns.md)voor meer informatie.

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>JSON-voorbeelden voor het kopiëren van gegevens van en naar SQL Data Warehouse
In de volgende voorbeelden worden voorbeeld-JSON-definities gegeven die u gebruiken om een pijplijn te maken met [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Ze laten zien hoe u gegevens kopieert van en naar Azure SQL Data Warehouse en Azure Blob Storage. Gegevens kunnen echter **rechtstreeks** worden gekopieerd van een van de bronnen naar een van de putten die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) zijn vermeld met behulp van de kopieeractiviteit in Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Voorbeeld: Gegevens kopiëren van Azure SQL Data Warehouse naar Azure Blob
In het voorbeeld worden de volgende entiteiten in gegevensfabriek gedefinieerd:

1. Een gekoppelde service van het type [AzureSqlDW](#linked-service-properties).
2. Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een [invoergegevensset](data-factory-create-datasets.md) van het type [AzureSqlDWTable](#dataset-properties).
4. Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Een [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [SqlDWSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)gebruikt.

Het voorbeeld kopieert tijdreeksgegevens (per uur, dagelijks, enz.) van een tabel in de Azure SQL Data Warehouse-database naar een blob elk uur. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

**Gekoppelde Azure SQL Data Warehouse-service:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Gekoppelde Azure Blob-opslagservice:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure SQL Data Warehouse-invoergegevensset:**

In het voorbeeld wordt ervan uitgegaan dat u een tabel 'MyTable' hebt gemaakt in Azure SQL Data Warehouse en dat deze kolom 'tijdstempelkolom' bevat voor tijdreeksgegevens.

Als u "extern" instelt: "true" informeert de datafabriekservice dat de gegevensset zich buiten de gegevensfabriek bevindt en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure Blob-uitvoergegevensset:**

Gegevens worden elk uur naar een nieuwe blob geschreven (frequentie: uur, interval: 1). Het mappad voor de blob wordt dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van de begintijd van jaar, maand, dag en uur.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Kopieer activiteit in een pijplijn met SqlDWSource en BlobSink:**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn is het **brontype** ingesteld op **SqlDWSource** en is **het sinktype** ingesteld op **BlobSink**. De SQL-query die is opgegeven voor de eigenschap **SqlReaderQuery** selecteert de gegevens in het afgelopen uur die u wilt kopiëren.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
> [!NOTE]
> In het voorbeeld wordt **sqlReaderQuery** opgegeven voor de SqlDWSource. Met de kopieeractiviteit wordt deze query uitgevoerd op basis van de Azure SQL Data Warehouse-bron om de gegevens op te halen.
>
> U ook een opgeslagen procedure opgeven door de **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven (als de opgeslagen procedure parameters neemt).
>
> Als u sqlReaderQuery of sqlReaderStoredProcedureName niet opgeeft, worden de kolommen die zijn gedefinieerd in de structuursectie van de gegevensset JSON gebruikt om een query te bouwen (kolom1, kolom2 van mytable) om uit te voeren tegen het Azure SQL Data Warehouse. Als de gegevenssetdefinitie de structuur niet heeft, worden alle kolommen geselecteerd uit de tabel.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Voorbeeld: Gegevens kopiëren van Azure Blob naar Azure SQL Data Warehouse
In het voorbeeld worden de volgende entiteiten in gegevensfabriek gedefinieerd:

1. Een gekoppelde service van het type [AzureSqlDW](#linked-service-properties).
2. Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een [invoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureSqlDWTable](#dataset-properties).
5. Een [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) en [SqlDWSink](#copy-activity-properties)gebruikt.

Het voorbeeld kopieert elk uur tijdreeksgegevens (per uur, dagelijks, enz.) naar een tabel in azure SQL Data Warehouse-database. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

**Gekoppelde Azure SQL Data Warehouse-service:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Gekoppelde Azure Blob-opslagservice:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Blob-invoergegevensset:**

Gegevens worden elk uur opgehaald uit een nieuwe blob (frequentie: uur, interval: 1). Het mappad en de bestandsnaam voor de blob worden dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt het jaar-, maand- en daggedeelte van de begintijd en bestandsnaam, het uurdeel van de begintijd. "extern": "true"-instelling informeert de dienst Data Factory dat deze tabel zich buiten de gegevensfabriek bevindt en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Uitvoergegevensset Azure SQL Data Warehouse:**

Het voorbeeld kopieert gegevens naar een tabel met de naam 'MyTable' in Azure SQL Data Warehouse. Maak de tabel in Azure SQL Data Warehouse met hetzelfde aantal kolommen als u verwacht dat het Blob CSV-bestand bevat. Elk uur worden er nieuwe rijen aan de tabel toegevoegd.

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Kopieer activiteit in een pijplijn met BlobSource en SqlDWSink:**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **BlobSource** en is **het sinktype** ingesteld op **SqlDWSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Zie 1 TB laden [in Azure SQL Data Warehouse binnen 15 minuten met Azure Data Factory](data-factory-load-sql-data-warehouse.md) en Gegevens laden met Azure Data [Factory](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) in de Azure SQL Data Warehouse-documentatie voor een walkthrough.

## <a name="performance-and-tuning"></a>Prestaties en tuning
Zie [Handleiding activiteitsprestaties kopiëren & tuningom](data-factory-copy-activity-performance.md) meer te weten te komen over de belangrijkste factoren die van invloed zijn op de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory en op verschillende manieren om deze te optimaliseren.
