---
title: Gegevens kopiëren naar/vanuit Azure SQL-database
description: Meer informatie over het kopiëren van gegevens naar/vanuit Azure SQL Database met Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7fc0b2822195d952c2a4f9c02bf3758c0e2b809a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260499"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Gegevens van en naar Azure SQL Database kopiëren met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-azure-sql-connector.md)
> * [Versie 2 (huidige versie)](../connector-azure-sql-database.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [Azure SQL Database-connector in V2](../connector-azure-sql-database.md)als u de huidige versie van de Data Factory-service gebruikt.

In dit artikel wordt uitgelegd hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens van en naar Azure SQL Database te verplaatsen. Het bouwt voort op het artikel [Data Movement Activities,](data-factory-data-movement-activities.md) dat een algemeen overzicht geeft van gegevensverplaatsing met de kopieeractiviteit.

## <a name="supported-scenarios"></a>Ondersteunde scenario's
U gegevens **uit Azure SQL Database** kopiëren naar de volgende gegevensopslag:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

U gegevens uit de volgende gegevensarchieven kopiëren **naar Azure SQL Database:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Ondersteund verificatietype
Azure SQL Database-connector ondersteunt basisverificatie.

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens van/naar een Azure SQL-database verplaatst met behulp van verschillende hulpprogramma's/API's.

De eenvoudigste manier om een pijplijn te maken, is door de **wizard Kopiëren**te gebruiken. Zie [Zelfstudie: Maak een pijplijn met wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough voor het maken van een pijplijn met de wizard Gegevens kopiëren.

U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-sjabloon,** **.NET API**en REST **API**. Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken.

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak een **gegevensfabriek**. Een gegevensfabriek kan een of meer pijplijnen bevatten.
2. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek. Als u bijvoorbeeld gegevens kopieert van een Azure blob-opslag naar een Azure SQL-database, maakt u twee gekoppelde services om uw Azure-opslagaccount en Azure SQL-database te koppelen aan uw gegevensfabriek. Zie [sectie gekoppelde service-eigenschappen](#linked-service-properties) voor gekoppelde service-eigenschappen die specifiek zijn voor Azure SQL Database.
3. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven. In het voorbeeld dat in de laatste stap wordt genoemd, maakt u een gegevensset om de blobcontainer en -map op te geven die de invoergegevens bevat. En u maakt een andere gegevensset om de SQL-tabel op te geven in de Azure SQL-database met de gegevens die zijn gekopieerd uit de blob-opslag. Zie sectie [gegevensseteigenschappen](#dataset-properties) voor gegevensseteigenschappen die specifiek zijn voor Azure Data Lake Store.
4. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt. In het eerder genoemde voorbeeld gebruikt u BlobSource als bron en SqlSink als een sink voor de kopieeractiviteit. Als u van Azure SQL Database naar Azure Blob Storage kopieert, gebruikt u SqlSource en BlobSink in de kopieeractiviteit. Zie [sectie activiteitseigenschappen kopiëren](#copy-activity-properties) voor eigenschappen voor kopiëren die specifiek zijn voor Azure SQL Database. Klik op de koppeling in de vorige sectie voor uw gegevensarchief voor meer informatie over het gebruik van een gegevensarchief als bron of gootsteen.

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u tools/API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling. Zie [JSON-voorbeelden](#json-examples-for-copying-data-to-and-from-sql-database) sectie van dit artikel voor voorbeelden van JSON-instellingen voor entiteiten in Gegevensfabriek die worden gebruikt om gegevens naar/vanuit een Azure SQL-database te kopiëren.

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor Azure SQL Database:

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
Een Azure SQL-gekoppelde service koppelt een Azure SQL-database aan uw gegevensfabriek. In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor azure SQL-gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet zijn ingesteld op: **AzureSqlDatabase** |Ja |
| Connectionstring |Geef informatie op die nodig is om verbinding te maken met de instantie Azure SQL Database voor de eigenschap connectionString. Alleen basisverificatie wordt ondersteund. |Ja |

> [!IMPORTANT]
> Configureer [Azure SQL Database Firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) de databaseserver zodat Azure Services toegang heeft tot de [server.](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) Als u gegevens van buiten Azure naar Azure SQL Database kopieert, inclusief van on-premises gegevensbronnen met een datafabriekgateway, configureert u bovendien het juiste IP-adresbereik voor de machine die gegevens naar Azure SQL Database verzendt.

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Als u een gegevensset wilt opgeven om invoer- of uitvoergegevens in een Azure SQL-database weer te geven, stelt u de eigenschap type van de gegevensset in op: **AzureSqlTable**. Stel de eigenschap **linkedServiceName** van de gegevensset in op de naam van de Azure SQL-gekoppelde service.

Zie het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties zoals structuur, beschikbaarheid en beleid van een gegevensset JSON zijn vergelijkbaar voor alle gegevenssettypen (Azure SQL, Azure blob, Azure table, etc.).

De sectie typeEigenschappen is verschillend voor elk type gegevensset en geeft informatie over de locatie van de gegevens in het gegevensarchief. De sectie **typeEigenschappen** voor de gegevensset van type **AzureSqlTable** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel of weergave in de Azure SQL Database-instantie waarnaar de gekoppelde service verwijst. |Ja |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [Pijplijnmaken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen en beleid zijn beschikbaar voor alle soorten activiteiten.

> [!NOTE]
> De kopieeractiviteit neemt slechts één invoer en produceert slechts één uitvoer.

Overwegende dat de eigenschappen die beschikbaar zijn in de sectie **typeEigenschappen** van de activiteit per activiteitstype verschillen. Voor Kopieeractiviteit variëren ze afhankelijk van de soorten bronnen en putten.

Als u gegevens verplaatst vanuit een Azure SQL-database, stelt u het brontype in de kopieeractiviteit in op **SqlSource.** Als u gegevens verplaatst naar een Azure SQL-database, stelt u het sinktype in de kopieeractiviteit in op **SqlSink.** In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door SqlSource en SqlSink.

### <a name="sqlsource"></a>SqlSource SqlSource
Wanneer de bron van type **SqlSource**is, zijn bij kopieeractiviteit de volgende eigenschappen beschikbaar in de sectie **typeProperties:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| sqlReaderQuery |Gebruik de aangepaste query om gegevens te lezen. |SQL-querytekenreeks. Bijvoorbeeld: `select * from MyTable`. |Nee |
| sqlReaderStoredProcedureName |Naam van de opgeslagen procedure die gegevens uit de brontabel leest. |Naam van de opgeslagen procedure. De laatste SQL-instructie moet een SELECT-instructie in de opgeslagen procedure zijn. |Nee |
| storedProcedureParameters |Parameters voor de opgeslagen procedure. |Naam/waardeparen. Namen en omhulsel van parameters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedureparameters. |Nee |

Als de **sqlReaderQuery** is opgegeven voor de SqlSource, voert de kopieeractiviteit deze query uit op de Azure SQL Database-bron om de gegevens op te halen. U ook een opgeslagen procedure opgeven door de **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven (als de opgeslagen procedure parameters neemt).

Als u sqlReaderQuery of sqlReaderStoredProcedureName niet opgeeft, worden de kolommen die zijn gedefinieerd in`select column1, column2 from mytable`de structuursectie van de gegevensset JSON gebruikt om een query ( ) te bouwen om tegen de Azure SQL Database uit te voeren. Als de gegevenssetdefinitie de structuur niet heeft, worden alle kolommen geselecteerd uit de tabel.

> [!NOTE]
> Wanneer u **sqlReaderStoredProcedureName**gebruikt, moet u nog steeds een waarde opgeven voor de eigenschap **tableName** in de gegevensset JSON. Er zijn echter geen validaties uitgevoerd tegen deze tabel.
>
>

### <a name="sqlsource-example"></a>SqlSource-voorbeeld

```JSON
"source": {
    "type": "SqlSource",
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

### <a name="sqlsink"></a>SqlSink SqlSink
**SqlSink** ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| writeBatchTimeout |Wacht de tijd voordat de batchinvoegbewerking is voltooid voordat deze een time-out heeft. |Tijdspanne<br/><br/> Voorbeeld: "00:30:00" (30 minuten). |Nee |
| writeBatchSize |Hiermee voegt u gegevens in de SQL-tabel in wanneer de buffergrootte writeBatchSize bereikt. |Geheel getal (aantal rijen) |Nee (standaard: 10000) |
| sqlWriterCleanupScript sqlWriterCleanupScript |Geef een query op voor Activiteit kopiëren om zodanig uit te voeren dat gegevens van een specifiek segment worden opgeschoond. Zie [herhaalbare kopie](#repeatable-copy)voor meer informatie. |Een queryverklaring. |Nee |
| sliceIdentifierColumnName |Geef een kolomnaam op die activiteit kopieert om te vullen met automatisch gegenereerde segment-id, die wordt gebruikt om gegevens van een specifiek segment op te schonen wanneer u opnieuw wordt uitgevoerd. Zie [herhaalbare kopie](#repeatable-copy)voor meer informatie. |Kolomnaam van een kolom met gegevenstype binaire(32). |Nee |
| sqlWriterStoredProcedureName |Naam van de opgeslagen procedure die definieert hoe brongegevens in doeltabel worden toegepast, bijvoorbeeld om upserts te doen of te transformeren met behulp van uw eigen bedrijfslogica. <br/><br/>Let op: deze opgeslagen procedure wordt **per batch ingeroepen.** Als u een bewerking wilt uitvoeren die slechts één keer wordt uitgevoerd en niets te `sqlWriterCleanupScript` maken heeft met brongegevens, zoals verwijderen/afkappen, gebruikt u de eigenschap. |Naam van de opgeslagen procedure. |Nee |
| storedProcedureParameters |Parameters voor de opgeslagen procedure. |Naam/waardeparen. Namen en omhulsel van parameters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedureparameters. |Nee |
| sqlWriterTableType sqlWriterTableType |Geef een tabeltypenaam op die in de opgeslagen procedure moet worden gebruikt. Als kopieeractiviteit de gegevens die worden verplaatst beschikbaar zijn in een tijdelijke tabel met dit tabeltype. Opgeslagen procedurecode kan vervolgens de gegevens die worden gekopieerd samenvoegen met bestaande gegevens. |Een tabeltypenaam. |Nee |

#### <a name="sqlsink-example"></a>SqlSink voorbeeld

```JSON
"sink": {
    "type": "SqlSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00",
    "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
    "sqlWriterTableType": "CopyTestTableType",
    "storedProcedureParameters": {
        "identifier": { "value": "1", "type": "Int" },
        "stringData": { "value": "str1" },
        "decimalData": { "value": "1", "type": "Decimal" }
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>JSON-voorbeelden voor het kopiëren van gegevens van en naar SQL Database
In de volgende voorbeelden worden voorbeeld-JSON-definities gegeven die u gebruiken om een pijplijn te maken met [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Ze laten zien hoe u gegevens van en naar Azure SQL Database en Azure Blob Storage kopieert. Gegevens kunnen echter **rechtstreeks** worden gekopieerd van een van de bronnen naar een van de putten die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) zijn vermeld met behulp van de kopieeractiviteit in Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Voorbeeld: Gegevens uit Azure SQL Database kopiëren naar Azure Blob
Hetzelfde definieert de volgende gegevensfabriekentiteiten:

1. Een gekoppelde service van het type [AzureSqlDatabase](#linked-service-properties).
2. Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een [invoergegevensset](data-factory-create-datasets.md) van het type [AzureSqlTable](#dataset-properties).
4. Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).
5. Een [pijplijn](data-factory-create-pipelines.md) met een kopieeractiviteit die [SqlSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)gebruikt.

Het voorbeeld kopieert tijdreeksgegevens (per uur, dagelijks, enz.) van een tabel in Azure SQL-database naar een blob elk uur. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

**Gekoppelde Azure SQL Database-service:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Zie de sectie Azure SQL Linked Service voor de lijst met eigenschappen die door deze gekoppelde service worden ondersteund.

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
Zie het [Azure Blob-artikel](data-factory-azure-blob-connector.md#azure-storage-linked-service) voor de lijst met eigenschappen die worden ondersteund door deze gekoppelde service.


**Azure SQL-invoergegevensset:**

In het voorbeeld wordt ervan uitgegaan dat u een tabel 'MyTable' in Azure SQL hebt gemaakt en dat deze kolom 'tijdstempelkolom' bevat voor tijdreeksgegevens.

Als u 'extern' instelt: "true" informeert de Azure Data Factory-service dat de gegevensset zich buiten de gegevensfabriek bevindt en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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

Zie de sectie Eigenschappen van Azure SQL-gegevenssettekst voor de lijst met eigenschappen die door dit gegevenssettype worden ondersteund.

**Azure Blob-uitvoergegevensset:**

Gegevens worden elk uur naar een nieuwe blob geschreven (frequentie: uur, interval: 1). Het mappad voor de blob wordt dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van de begintijd van jaar, maand, dag en uur.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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
Zie de sectie [Eigenschappen van Azure Blob-gegevenssettekst](data-factory-azure-blob-connector.md#dataset-properties) voor de lijst met eigenschappen die door dit gegevenssettype worden ondersteund.

**Een kopieeractiviteit in een pijplijn met SQL-bron en Blob-sink:**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **SqlSource** en is **het sinktype** ingesteld op **BlobSink**. De SQL-query die is opgegeven voor de eigenschap **SqlReaderQuery** selecteert de gegevens in het afgelopen uur die u wilt kopiëren.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
In het voorbeeld wordt **sqlReaderQuery** opgegeven voor de SqlSource. Met de kopieeractiviteit wordt deze query uitgevoerd op basis van de Azure SQL Database-bron om de gegevens op te halen. U ook een opgeslagen procedure opgeven door de **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven (als de opgeslagen procedure parameters neemt).

Als u sqlReaderQuery of sqlReaderStoredProcedureName niet opgeeft, worden de kolommen die zijn gedefinieerd in de structuursectie van de gegevensset JSON gebruikt om een query te bouwen die wordt uitgevoerd tegen de Azure SQL Database. Bijvoorbeeld: `select column1, column2 from mytable`. Als de gegevenssetdefinitie de structuur niet heeft, worden alle kolommen geselecteerd uit de tabel.

Zie de sectie [Sql Source](#sqlsource) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) voor de lijst met eigenschappen die worden ondersteund door SqlSource en BlobSink.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Voorbeeld: Gegevens kopiëren van Azure Blob naar Azure SQL Database
In het voorbeeld worden de volgende entiteiten in gegevensfabriek gedefinieerd:

1. Een gekoppelde service van het type [AzureSqlDatabase](#linked-service-properties).
2. Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een [invoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureSqlTable](#dataset-properties).
5. Een [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) en [SqlSink](#copy-activity-properties)gebruikt.

Het voorbeeld kopieert elk uur tijdreeksgegevens (per uur, dagelijks, enz.) van Azure blob naar een tabel in Azure SQL-database. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

**Azure SQL-gekoppelde service:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Zie de sectie Azure SQL Linked Service voor de lijst met eigenschappen die door deze gekoppelde service worden ondersteund.

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
Zie het [Azure Blob-artikel](data-factory-azure-blob-connector.md#azure-storage-linked-service) voor de lijst met eigenschappen die worden ondersteund door deze gekoppelde service.


**Azure Blob-invoergegevensset:**

Gegevens worden elk uur opgehaald uit een nieuwe blob (frequentie: uur, interval: 1). Het mappad en de bestandsnaam voor de blob worden dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt het jaar-, maand- en daggedeelte van de begintijd en bestandsnaam, het uurdeel van de begintijd. "extern": "true"-instelling informeert de dienst Data Factory dat deze tabel zich buiten de gegevensfabriek bevindt en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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
Zie de sectie [Eigenschappen van Azure Blob-gegevenssettekst](data-factory-azure-blob-connector.md#dataset-properties) voor de lijst met eigenschappen die door dit gegevenssettype worden ondersteund.

**Uitvoergegevensset azure SQL Database:**

Het voorbeeld kopieert gegevens naar een tabel met de naam 'MyTable' in Azure SQL. Maak de tabel in Azure SQL met hetzelfde aantal kolommen als u verwacht dat het Blob CSV-bestand bevat. Elk uur worden er nieuwe rijen aan de tabel toegevoegd.

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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
Zie de sectie Eigenschappen van Azure SQL-gegevenssettekst voor de lijst met eigenschappen die door dit gegevenssettype worden ondersteund.

**Een kopieeractiviteit in een pijplijn met Blob-bron en SQL-sink:**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **BlobSource** en is **het sinktype** ingesteld op **SqlSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
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
Zie de sectie [Sql Sink](#sqlsink) en [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) voor de lijst met eigenschappen die worden ondersteund door SqlSink en BlobSource.

## <a name="identity-columns-in-the-target-database"></a>Identiteitskolommen in de doeldatabase
In deze sectie vindt u een voorbeeld voor het kopiëren van gegevens uit een brontabel zonder identiteitskolom naar een doeltabel met een identiteitskolom.

**Brontabel:**

```SQL
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Bestemmingstabel:**

```SQL
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```
De doeltabel heeft een identiteitskolom.

**Brongegevensset JSON-definitie**

```JSON
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**Json-definitie van doelgegevensset**

```JSON
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

Merk op dat als uw bron en doeltabel hebben verschillende schema (doel heeft een extra kolom met identiteit). In dit scenario moet u **de eigenschap structuur** opgeven in de definitie van de doelgegevensset, die de identiteitskolom niet bevat.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Opgeslagen procedure aanroepen vanuit SQL-sink
Zie [Opgeslagen procedure voor SQL-sink](data-factory-invoke-stored-procedure-from-copy-activity.md) in het artikel kopiëren naar een opgeslagen procedure van SQL-sink in een kopieactiviteit van een pijplijn oproepen voor een aanroepen van een opgeslagen procedure vanuit SQL-sink in een kopieactiviteit.

## <a name="type-mapping-for-azure-sql-database"></a>Typetoewijzing voor Azure SQL Database
Zoals vermeld in het artikel [Gegevensverplaatsingsactiviteiten](data-factory-data-movement-activities.md) voert kopieeractiviteit automatische typeconversies uit van brontypen naar gootsteentypen met de volgende aanpak in twee stappen:

1. Converteren van native brontypen naar .NET-type
2. Converteren van .NET-type naar native sinktype

Bij het verplaatsen van gegevens van en naar Azure SQL Database worden de volgende toewijzingen gebruikt van SQL-type naar .NET-type en vice versa. De toewijzing is hetzelfde als de SQL Server Data Type Mapping voor ADO.NET.

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

## <a name="map-source-to-sink-columns"></a>Kaartbron om kolommen te laten zinken
Zie Kolommen van [gegevenssetsin Azure Data Factory](data-factory-map-columns.md)voor meer informatie over het toewijzen van kolommen in brongegevensset naar kolommen in sink dataset.

## <a name="repeatable-copy"></a>Herhaalbare kopie
Wanneer u gegevens kopieert naar SQL Server Database, voegt de kopieeractiviteit gegevens standaard toe aan de sinktabel. Als u in plaats daarvan een UPSERT wilt uitvoeren, raadpleegt u [Recidive schrijven naar sqlsink-artikel.](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink)

Houd bij het kopiëren van gegevens uit relationele gegevensopslag rekening met herhaalbaarheid om onbedoelde resultaten te voorkomen. In Azure Data Factory u een segment handmatig opnieuw uitvoeren. U ook het beleid voor een wijziging opnieuw configureren, zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment in beide richtingen wordt opnieuw uitgevoerd, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht hoe vaak een segment wordt uitgevoerd. Zie [Herhaalbaar lezen uit relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestaties en tuning
Zie [Handleiding activiteitsprestaties kopiëren & tuningom](data-factory-copy-activity-performance.md) meer te weten te komen over de belangrijkste factoren die van invloed zijn op de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory en op verschillende manieren om deze te optimaliseren.
