---
title: Gegevens verplaatsen van en naar SQL Server
description: Meer informatie over het verplaatsen van gegevens naar/vanuit SQL Server-database die on-premises of in een Azure VM is met Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5e4bbe1e6bd944787d47c5e3ed98de582c088a52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265764"
---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Gegevens verplaatsen van en naar SQL Server on-premises of op IaaS (Azure VM) met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-sqlserver-connector.md)
> * [Versie 2 (huidige versie)](../connector-sql-server.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [SQL Server-connector in V2](../connector-sql-server.md)als u de huidige versie van de datafabriekservice gebruikt.

In dit artikel wordt uitgelegd hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens naar/vanuit een on-premises SQL Server-database te verplaatsen. Het bouwt voort op het artikel [Data Movement Activities,](data-factory-data-movement-activities.md) dat een algemeen overzicht geeft van gegevensverplaatsing met de kopieeractiviteit.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Ondersteunde scenario's
U gegevens **uit een SQL Server-database** kopiëren naar de volgende gegevensarchieven:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

U gegevens uit de volgende gegevensarchieven kopiëren **naar een SQL Server-database:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Ondersteunde SQL Server-versies
Deze SQL Server-connector ondersteunt het kopiëren van gegevens van/naar de volgende versies van on-premises of in Azure IaaS gehoste instantie met behulp van zowel SQL-verificatie als Windows-verificatie: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Connectiviteit inschakelen
De concepten en stappen die nodig zijn voor het maken van verbinding met SQL Server die on-premises of in Azure IaaS-vm's (Infrastructure-as-a-Service) wordt gehost, zijn hetzelfde. In beide gevallen moet u Data Management Gateway gebruiken voor connectiviteit.

Bekijk [het verplaatsen van gegevens tussen on-premises locaties en een cloudartikel](data-factory-move-data-between-onprem-and-cloud.md) voor meer informatie over Data Management Gateway en stapsgewijze instructies voor het instellen van de gateway. Het instellen van een gateway-instantie is een vereiste voor het verbinden met SQL Server.

Hoewel u gateway installeren op dezelfde on-premises machine of cloud VM-instantie als de SQL Server voor betere prestaties, raden we u aan deze op afzonderlijke machines te installeren. Het hebben van de gateway en SQL Server op afzonderlijke machines vermindert de brongeschil.

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens naar/vanuit een on-premises SQL Server-database verplaatst met behulp van verschillende hulpprogramma's/API's.

De eenvoudigste manier om een pijplijn te maken, is door de **wizard Kopiëren**te gebruiken. Zie [Zelfstudie: Maak een pijplijn met wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough voor het maken van een pijplijn met de wizard Gegevens kopiëren.

U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-sjabloon,** **.NET API**en REST **API**. Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken.

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak een **gegevensfabriek**. Een gegevensfabriek kan een of meer pijplijnen bevatten.
2. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek. Als u bijvoorbeeld gegevens uit een SQL Server-database kopieert naar een Azure blob-opslag, maakt u twee gekoppelde services om uw SQL Server-database en Azure-opslagaccount te koppelen aan uw gegevensfabriek. Zie [sectie gekoppelde service-eigenschappen](#linked-service-properties) voor gekoppelde service-eigenschappen die specifiek zijn voor SQL Server-database.
3. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven. In het voorbeeld dat in de laatste stap wordt genoemd, maakt u een gegevensset om de SQL-tabel in uw SQL Server-database op te geven die de invoergegevens bevat. En u maakt een andere gegevensset om de blobcontainer en de map op te geven die de gegevens bevat die zijn gekopieerd uit de SQL Server-database. Zie sectie [gegevensseteigenschappen](#dataset-properties) voor gegevensseteigenschappen die specifiek zijn voor SQL Server-database.
4. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt. In het eerder genoemde voorbeeld gebruikt u SqlSource als bron en BlobSink als sink voor de kopieeractiviteit. Als u van Azure Blob Storage naar SQL Server Database kopieert, gebruikt u BlobSource en SqlSink in de kopieeractiviteit. Zie [sectie activiteitseigenschappen kopiëren](#copy-activity-properties) voor eigenschappen voor kopiëren die specifiek zijn voor SQL Server Database. Klik op de koppeling in de vorige sectie voor uw gegevensarchief voor meer informatie over het gebruik van een gegevensarchief als bron of gootsteen.

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u tools/API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling. Zie [JSON-voorbeelden](#json-examples-for-copying-data-from-and-to-sql-server) sectie van dit artikel voor voorbeelden van JSON-instellingen voor entiteiten in Gegevensfabriek die worden gebruikt om gegevens naar/vanuit een on-premises SQL Server-database te kopiëren.

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor SQL Server:

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
U maakt een gekoppelde service van het type **OnPremisesSqlServer** om een on-premises SQL Server-database te koppelen aan een gegevensfabriek. In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor de on-premises SQL Server-gekoppelde service.

In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor sql server-gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **OnPremisesSqlServer**. |Ja |
| Connectionstring |Verbindingsgegevens opgevenTekenreeksgegevens die nodig zijn om verbinding te maken met de on-premises SQL Server-database met SQL-verificatie of Windows-verificatie. |Ja |
| gatewayNaam |Naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises SQL Server-database. |Ja |
| gebruikersnaam |Geef de gebruikersnaam op als u Windows-verificatie gebruikt. Voorbeeld: **gebruikersnaam\\domeinnaam**. |Nee |
| wachtwoord |Geef een wachtwoord op voor het gebruikersaccount dat u hebt opgegeven voor de gebruikersnaam. |Nee |

U referenties versleutelen met de cmdlet **Nieuw-AzDataFactoryEncryptValue** en deze gebruiken in de verbindingstekenreeks zoals weergegeven in het volgende voorbeeld **(eigenschap EncryptedCredential):**

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Voorbeelden
**JSON voor het gebruik van SQL-verificatie**

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
**JSON voor het gebruik van Windows-verificatie**

Data Management Gateway doet zich voor als het opgegeven gebruikersaccount om verbinding te maken met de on-premises SQL Server-database.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
In de voorbeelden hebt u een gegevensset van het type **SqlServerTable** gebruikt om een tabel in een SQL Server-database weer te geven.

Zie het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties zoals structuur, beschikbaarheid en beleid van een gegevensset JSON zijn vergelijkbaar voor alle gegevenssettypen (SQL Server, Azure blob, Azure table, enz.).

De sectie typeEigenschappen is verschillend voor elk type gegevensset en geeft informatie over de locatie van de gegevens in het gegevensarchief. De sectie **typeEigenschappen** voor de gegevensset van type **SqlServerTable** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel of weergave in de SQL Server Database-instantie waarnaar de gekoppelde service verwijst. |Ja |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Als u gegevens verplaatst vanuit een SQL Server-database, stelt u het brontype in de kopieeractiviteit in op **SqlSource.** Als u gegevens naar een SQL Server-database verplaatst, stelt u het sinktype in de kopieeractiviteit in op **SqlSink.** In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door SqlSource en SqlSink.

Zie het artikel [Pijplijnmaken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen en beleidsregels zijn beschikbaar voor alle soorten activiteiten.

> [!NOTE]
> De kopieeractiviteit neemt slechts één invoer en produceert slechts één uitvoer.

Overwegende dat de eigenschappen die beschikbaar zijn in de sectie typeEigenschappen van de activiteit per activiteitstype verschillen. Voor Kopieeractiviteit variëren ze afhankelijk van de soorten bronnen en putten.

### <a name="sqlsource"></a>SqlSource SqlSource
Wanneer de bron in een kopieeractiviteit van type **SqlSource**is, zijn de volgende eigenschappen beschikbaar in de sectie **typeProperties:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| sqlReaderQuery |Gebruik de aangepaste query om gegevens te lezen. |SQL-querytekenreeks. Selecteer bijvoorbeeld * in MyTable. Kan verwijzen naar meerdere tabellen uit de database waarnaar wordt verwezen door de invoergegevensset. Als dit niet is opgegeven, wordt de SQL-instructie uitgevoerd: kies uit MyTable. |Nee |
| sqlReaderStoredProcedureName |Naam van de opgeslagen procedure die gegevens uit de brontabel leest. |Naam van de opgeslagen procedure. De laatste SQL-instructie moet een SELECT-instructie in de opgeslagen procedure zijn. |Nee |
| storedProcedureParameters |Parameters voor de opgeslagen procedure. |Naam/waardeparen. Namen en omhulsel van parameters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedureparameters. |Nee |

Als de **sqlReaderQuery** is opgegeven voor de SqlSource, voert de kopieeractiviteit deze query uit tegen de SQL Server Database-bron om de gegevens te krijgen.

U ook een opgeslagen procedure opgeven door de **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven (als de opgeslagen procedure parameters neemt).

Als u sqlReaderQuery of sqlReaderStoredProcedureName niet opgeeft, worden de kolommen die in de structuursectie zijn gedefinieerd, gebruikt om een selectiequery te bouwen die wordt uitgevoerd tegen de SQL Server-database. Als de gegevenssetdefinitie de structuur niet heeft, worden alle kolommen geselecteerd uit de tabel.

> [!NOTE]
> Wanneer u **sqlReaderStoredProcedureName**gebruikt, moet u nog steeds een waarde opgeven voor de eigenschap **tableName** in de gegevensset JSON. Er zijn echter geen validaties uitgevoerd tegen deze tabel.

### <a name="sqlsink"></a>SqlSink SqlSink
**SqlSink** ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| writeBatchTimeout |Wacht de tijd voordat de batchinvoegbewerking is voltooid voordat deze een time-out heeft. |Tijdspanne<br/><br/> Voorbeeld: "00:30:00" (30 minuten). |Nee |
| writeBatchSize |Hiermee voegt u gegevens in de SQL-tabel in wanneer de buffergrootte writeBatchSize bereikt. |Geheel getal (aantal rijen) |Nee (standaard: 10000) |
| sqlWriterCleanupScript sqlWriterCleanupScript |Geef query op voor Activiteit kopiëren om zodanig uit te voeren dat gegevens van een specifiek segment worden opgeschoond. Zie voor meer informatie [herhaalbare kopiesectie.](#repeatable-copy) |Een queryverklaring. |Nee |
| sliceIdentifierColumnName |Geef kolomnaam op voor Activiteit kopiëren om te vullen met automatisch gegenereerde segment-id, die wordt gebruikt om gegevens van een specifiek segment op te schonen bij het opnieuw uitvoeren. Zie voor meer informatie [herhaalbare kopiesectie.](#repeatable-copy) |Kolomnaam van een kolom met gegevenstype binaire(32). |Nee |
| sqlWriterStoredProcedureName |Naam van de opgeslagen procedure die definieert hoe brongegevens in doeltabel worden toegepast, bijvoorbeeld om upserts te doen of te transformeren met behulp van uw eigen bedrijfslogica. <br/><br/>Let op: deze opgeslagen procedure wordt **per batch ingeroepen.** Als u een bewerking wilt uitvoeren die slechts één keer wordt uitgevoerd en niets te `sqlWriterCleanupScript` maken heeft met brongegevens, zoals verwijderen/afkappen, gebruikt u de eigenschap. |Naam van de opgeslagen procedure. |Nee |
| storedProcedureParameters |Parameters voor de opgeslagen procedure. |Naam/waardeparen. Namen en omhulsel van parameters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedureparameters. |Nee |
| sqlWriterTableType sqlWriterTableType |Geef de naam van de tabeloptekst op die in de opgeslagen procedure moet worden gebruikt. Als kopieeractiviteit de gegevens die worden verplaatst beschikbaar zijn in een tijdelijke tabel met dit tabeltype. Opgeslagen procedurecode kan vervolgens de gegevens die worden gekopieerd samenvoegen met bestaande gegevens. |Een tabeltypenaam. |Nee |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>JSON-voorbeelden voor het kopiëren van gegevens van en naar SQL Server
In de volgende voorbeelden worden voorbeeld-JSON-definities gegeven die u gebruiken om een pijplijn te maken met [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) In de volgende voorbeelden ziet u hoe u gegevens van en naar SQL Server en Azure Blob Storage kopieert. Gegevens kunnen echter **rechtstreeks** worden gekopieerd van een van de bronnen naar een van de putten die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) zijn vermeld met behulp van de kopieeractiviteit in Azure Data Factory.

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Voorbeeld: Gegevens kopiëren van SQL Server naar Azure Blob
In het volgende voorbeeld wordt het volgende voorbeeld weergegeven:

1. Een gekoppelde service van het type [OnPremisesSqlServer](#linked-service-properties).
2. Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een [invoergegevensset](data-factory-create-datasets.md) van het type [SqlServerTable](#dataset-properties).
4. Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. De [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [SqlSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)gebruikt.

Het voorbeeld kopieert elk uur tijdreeksgegevens uit een SQL Server-tabel naar een Azure-blob. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

Als eerste stap, het instellen van de data management gateway. De instructies bevinden zich in de [verhuisgegevens tussen on-premises locaties en cloudartikelen.](data-factory-move-data-between-onprem-and-cloud.md)

**SQL Server-gekoppelde service**
```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Gekoppelde Azure Blob-opslagservice**

```json
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
**SQL Server-invoergegevensset**

In het voorbeeld wordt ervan uitgegaan dat u een tabel "MyTable" in SQL Server hebt gemaakt en dat deze kolom "tijdstempelkolom" bevat voor tijdreeksgegevens. U meerdere tabellen in dezelfde database doorzoeken met één gegevensset, maar één tabel moet worden gebruikt voor het type TableName van de gegevensset.

Als u "extern" instelt: "true" informeert de dienst Data Factory dat de gegevensset buiten de gegevensfabriek staat en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```json
{
  "name": "SqlServerInput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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
**Azure Blob-uitvoergegevensset**

Gegevens worden elk uur naar een nieuwe blob geschreven (frequentie: uur, interval: 1). Het mappad voor de blob wordt dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van de begintijd van jaar, maand, dag en uur.

```json
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
**Pijplijn met kopieeractiviteit**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om deze invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **SqlSource** en is **het sinktype** ingesteld op **BlobSink**. De SQL-query die is opgegeven voor de eigenschap **SqlReaderQuery** selecteert de gegevens in het afgelopen uur die u wilt kopiëren.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2016-06-01T18:00:00",
    "end":"2016-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
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
In dit voorbeeld wordt **sqlReaderQuery** opgegeven voor de SqlSource. Met de kopieeractiviteit wordt deze query uitgevoerd op basis van de SQL Server-databasebron om de gegevens op te halen. U ook een opgeslagen procedure opgeven door de **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven (als de opgeslagen procedure parameters neemt). De sqlReaderQuery kan verwijzen naar meerdere tabellen binnen de database waarnaar wordt verwezen door de invoergegevensset. Het is niet beperkt tot alleen de tabel set als tableName typeProperty van de gegevensset.

Als u sqlReaderQuery of sqlReaderStoredProcedureName niet opgeeft, worden de kolommen die in de structuursectie zijn gedefinieerd, gebruikt om een selectiequery te bouwen die wordt uitgevoerd tegen de SQL Server Database. Als de gegevenssetdefinitie de structuur niet heeft, worden alle kolommen geselecteerd uit de tabel.

Zie de sectie [Sql Source](#sqlsource) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) voor de lijst met eigenschappen die worden ondersteund door SqlSource en BlobSink.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Voorbeeld: Gegevens kopiëren van Azure Blob naar SQL Server
In het volgende voorbeeld wordt het volgende voorbeeld weergegeven:

1. De gekoppelde service van het type [OnPremisesSqlServer](#linked-service-properties).
2. De gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een [invoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Een [uitvoergegevensset](data-factory-create-datasets.md) van type [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. De [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) en SqlSink gebruikt.

Het voorbeeld kopieert elk uur tijdreeksgegevens van een Azure-blob naar een SQL Server-tabel. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

**SQL Server-gekoppelde service**

```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Gekoppelde Azure Blob-opslagservice**

```json
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
**Azure Blob-invoergegevensset**

Gegevens worden elk uur opgehaald uit een nieuwe blob (frequentie: uur, interval: 1). Het mappad en de bestandsnaam voor de blob worden dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt het jaar-, maand- en daggedeelte van de begintijd en bestandsnaam, het uurdeel van de begintijd. "extern": "true"-instelling informeert de Data Factory-service dat de gegevensset buiten de gegevensfabriek staat en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```json
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
**SQL Server-uitvoergegevensset**

Het voorbeeld kopieert gegevens naar een tabel met de naam 'MyTable' in SQL Server. Maak de tabel in SQL Server met hetzelfde aantal kolommen als u verwacht dat het Blob CSV-bestand bevat. Elk uur worden er nieuwe rijen aan de tabel toegevoegd.

```json
{
  "name": "SqlServerOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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
**Pijplijn met kopieeractiviteit**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om deze invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **BlobSource** en is **het sinktype** ingesteld op **SqlSink**.

```json
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
            "name": " SqlServerOutput "
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

## <a name="troubleshooting-connection-issues"></a>Verbindingsproblemen oplossen
1. Configureer uw SQL Server om externe verbindingen te accepteren. Start **SQL Server Management Studio,** klik met de rechtermuisknop op **server**en klik op **Eigenschappen**. Selecteer **Verbindingen** in de lijst en schakel **Externe verbindingen toestaan op de server**in .

    ![Externe verbindingen inschakelen](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Zie [De optie voor configuratie van rastoegangsserver configureren](https://msdn.microsoft.com/library/ms191464.aspx) voor gedetailleerde stappen.
2. **SQL Server Configuration Manager starten**. Vouw **SQL Server-netwerkconfiguratie** uit voor de instantie die u wilt en selecteer **Protocollen voor MSSQLSERVER**. Je zou protocollen in het rechterdeelvenster moeten zien. Tcp/IP inschakelen door met de rechtermuisknop op **TCP/IP** te klikken en op **Inschakelen**te klikken .

    ![TCP/IP inschakelen](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Zie [Een servernetwerkprotocol inschakelen of uitschakelen](https://msdn.microsoft.com/library/ms191294.aspx) voor meer informatie en alternatieve manieren om TCP/IP-protocol in te schakelen.
3. Dubbelklik in hetzelfde venster op **TCP/IP** om het venster **TCP/IP-eigenschappen** te starten.
4. Ga naar het tabblad **IP-adressen.** Scrol naar beneden om de sectie **IPAll** te bekijken. Noteer de **TCP-poort**(standaard is **1433**).
5. Maak een **regel voor de Windows Firewall** op de machine om binnenkomend verkeer via deze poort toe te staan.
6. **Verbinding verifiëren:** Als u verbinding wilt maken met de SQL Server met volledig gekwalificeerde naam, gebruikt u SQL Server Management Studio vanaf een andere machine. Bijvoorbeeld: "\<\>machine . \<domein\>\<.corp.\>bedrijf .com,1433."

   > [!IMPORTANT]
   > 
   > Zie [Gegevens verplaatsen tussen on-premises bronnen en de cloud met Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) voor gedetailleerde informatie.
   > 
   > Zie [Problemen met de gateway oplossen](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) voor tips over het oplossen van verbindings-/gatewaygerelateerde problemen.


## <a name="identity-columns-in-the-target-database"></a>Identiteitskolommen in de doeldatabase
In deze sectie vindt u een voorbeeld dat gegevens uit een brontabel zonder identiteitskolom kopieert naar een doeltabel met een identiteitskolom.

**Brontabel:**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Bestemmingstabel:**

```sql
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```

De doeltabel heeft een identiteitskolom.

**Brongegevensset JSON-definitie**

```json
{
    "name": "SampleSource",
    "properties": {
        "published": false,
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

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "published": false,
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
Zie [Opgeslagen procedure voor SQL-sink in het artikel voor kopieeractiviteit aanroepen](data-factory-invoke-stored-procedure-from-copy-activity.md) voor een voorbeeld van het inroepen van een opgeslagen procedure van SQL-sink in een kopieeractiviteit van een pijplijn.

## <a name="type-mapping-for-sql-server"></a>Typetoewijzing voor SQL-server
Zoals vermeld in het artikel [gegevensverplaatsingsactiviteiten](data-factory-data-movement-activities.md) voert de activiteit Kopiëren automatische typeconversies uit van brontypen naar sinktypen met de volgende benadering in twee stappen:

1. Converteren van native brontypen naar .NET-type
2. Converteren van .NET-type naar native sinktype

Bij het verplaatsen van gegevens naar & van SQL-server worden de volgende toewijzingen gebruikt van SQL-type naar .NET-type en vice versa.

De toewijzing is hetzelfde als de SQL Server Data Type Mapping voor ADO.NET.

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

## <a name="mapping-source-to-sink-columns"></a>Bron toewijzen om kolommen te laten zinken
Zie Kolommen van [gegevensset toewijzing in Azure Data Factory](data-factory-map-columns.md)als u kolommen wilt toewijzen van brongegevensset naar kolommen uit de sink-gegevensset.

## <a name="repeatable-copy"></a>Herhaalbare kopie
Wanneer u gegevens kopieert naar SQL Server Database, voegt de kopieeractiviteit gegevens standaard toe aan de sinktabel. Als u in plaats daarvan een UPSERT wilt uitvoeren, raadpleegt u [Recidive schrijven naar sqlsink-artikel.](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink)

Houd bij het kopiëren van gegevens uit relationele gegevensopslag rekening met herhaalbaarheid om onbedoelde resultaten te voorkomen. In Azure Data Factory u een segment handmatig opnieuw uitvoeren. U ook het beleid voor een wijziging opnieuw configureren, zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment in beide richtingen wordt opnieuw uitgevoerd, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht hoe vaak een segment wordt uitgevoerd. Zie [Herhaalbaar lezen uit relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestaties en tuning
Zie [Handleiding activiteitsprestaties kopiëren & tuningom](data-factory-copy-activity-performance.md) meer te weten te komen over de belangrijkste factoren die van invloed zijn op de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory en op verschillende manieren om deze te optimaliseren.
