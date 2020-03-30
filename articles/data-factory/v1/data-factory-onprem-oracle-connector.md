---
title: Gegevens kopiëren naar of van Oracle met Data Factory
description: Meer informatie over het kopiëren van gegevens naar of vanuit een on-premises Oracle-database met Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 066e32d5ab21f88b170498173606043c54fec586
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265855"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Gegevens kopiëren naar of van Oracle on-premises met Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-onprem-oracle-connector.md)
> * [Versie 2 (huidige versie)](../connector-oracle.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Azure Data Factory. Zie [Oracle-connector in V2](../connector-oracle.md)als u de huidige versie van de Azure Data Factory-service gebruikt.


In dit artikel wordt uitgelegd hoe u Activiteit kopiëren in Azure Data Factory gebruiken om gegevens naar of vanuit een on-premises Oracle-database te verplaatsen. Het artikel bouwt voort op activiteiten voor het verplaatsen van [gegevens,](data-factory-data-movement-activities.md)die een algemeen overzicht van gegevensverkeer weergeeft met behulp van Kopieeractiviteit.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

U gegevens *uit een Oracle-database* kopiëren naar de volgende gegevensarchieven:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

U gegevens uit de volgende gegevensarchieven kopiëren *naar een Oracle-database:*

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Vereisten

Data Factory ondersteunt het verbinden met on-premises Oracle-bronnen met behulp van Data Management Gateway. Zie [Data Management Gateway](data-factory-data-management-gateway.md) voor meer informatie over Data Management Gateway. Zie [Gegevens verplaatsen van on-premises naar de cloud](data-factory-move-data-between-onprem-and-cloud.md)voor stapsgewijze instructies over het instellen van de gateway in een gegevenspijplijn om gegevens te verplaatsen.

De gateway is vereist, zelfs als het Oracle wordt gehost in een AB-infrastructuur als een service (IaaS) VM. U de gateway installeren op dezelfde IaaS VM als het gegevensarchief of op een andere VM, zolang de gateway verbinding kan maken met de database.

> [!NOTE]
> Zie Problemen met de [gateway oplossen](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)voor tips over probleemproblemen die verband houden met de verbinding en de gateway.

## <a name="supported-versions-and-installation"></a>Ondersteunde versies en installatie

Deze Oracle-connector ondersteunt twee versies van stuurprogramma's:

- **Microsoft-stuurprogramma voor Oracle (aanbevolen)**: Vanaf Data Management Gateway versie 2.7 wordt een Microsoft-stuurprogramma voor Oracle automatisch geïnstalleerd met de gateway. U hoeft het stuurprogramma niet te installeren of bij te werken om de verbinding met Oracle tot stand te brengen. U ook betere kopieerprestaties ervaren met behulp van dit stuurprogramma. Deze versies van Oracle-databases worden ondersteund:
  - Oracle 12c R1 (12.1)
  - Oracle 11g R1, R2 (11.1, 11.2)
  - Oracle 10g R1, R2 (10.1, 10.2)
  - Oracle 9i R1, R2 (9.0.1, 9.2)
  - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Oracle-proxyserver wordt niet ondersteund.

    > [!IMPORTANT]
    > Momenteel ondersteunt de Microsoft-driver voor Oracle alleen het kopiëren van gegevens van Oracle. De driver ondersteunt geen schrijven naar Oracle. De testverbindingsmogelijkheid op het tabblad **Gegevensbeheergateway-diagnose** ondersteunt dit stuurprogramma niet. U ook de wizard Kopiëren gebruiken om de connectiviteit te valideren.
    >

- **Oracle Data Provider voor .NET:** U Oracle Data Provider gebruiken om gegevens van of naar Oracle te kopiëren. Dit onderdeel is opgenomen in [Oracle Data Access Components voor Windows.](https://www.oracle.com/technetwork/topics/dotnet/downloads/) Installeer de desbetreffende versie (32-bits of 64-bits) op de machine waar de gateway is geïnstalleerd. [Oracle Data Provider .NET 12.1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) heeft toegang tot Oracle Database 10g Release 2 en nieuwe versies.

    Als u **XCopy-installatie selecteert,** voert u de stappen uit die worden beschreven in het bestand readme.htm. We raden u aan de installatieprogramma te selecteren die de gebruikersinterface heeft (niet de XCopy-installer).

    Nadat u de provider hebt geïnstalleerd, start u de hostservice Data Management Gateway opnieuw op uw machine met behulp van de Services applet of Data Management Gateway Configuration Manager.

Als u de wizard Kopiëren gebruikt om de kopieerpijplijn te maken, wordt het stuurprogramma automatisch bepaald. Het Microsoft-stuurprogramma wordt standaard gebruikt, tenzij uw gatewayversie eerder is dan versie 2.7 of u Oracle als gootsteen selecteert.

## <a name="get-started"></a>Aan de slag

U een pijplijn maken met een kopieeractiviteit. De pijplijn verplaatst gegevens van of naar een on-premises Oracle-database met behulp van verschillende hulpprogramma's of API's.

De eenvoudigste manier om een pijplijn te maken, is door de wizard Kopiëren te gebruiken. Zie [Zelfstudie: Maak een pijplijn met de wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough voor het maken van een pijplijn met de wizard Gegevens kopiëren.

U ook een van de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **Azure PowerShell,** een **Azure Resource Manager-sjabloon,** de **.NET-API**of de **REST-API**. Zie de [zelfstudie Activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijplijn met een kopieeractiviteit.

Of u nu de hulpprogramma's of API's gebruikt, voer de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak een **gegevensfabriek**. Een gegevensfabriek kan een of meer pijplijnen bevatten.
2. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek. Als u bijvoorbeeld gegevens kopieert van een Oracle-database naar Azure Blob-opslag, maakt u twee gekoppelde services om uw Oracle-database en Azure-opslagaccount te koppelen aan uw gegevensfabriek. Zie [Gekoppelde serviceeigenschappen](#linked-service-properties)voor gekoppelde service-eigenschappen die specifiek zijn voor Oracle.
3. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven. In het voorbeeld in de vorige stap maakt u een gegevensset om de tabel in uw Oracle-database op te geven die de invoergegevens bevat. U maakt een andere gegevensset om de blobcontainer en de map met de gegevens die zijn gekopieerd uit de Oracle-database op te geven. Zie [Gegevensseteigenschappen](#dataset-properties)voor gegevensseteigenschappen die specifiek zijn voor Oracle .
4. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoer als uitvoer neemt. In het voorgaande voorbeeld gebruikt u **OracleSource** als bron en **BlobSink** als een sink voor de kopieeractiviteit. Als u van Azure Blob-opslag naar een Oracle-database kopieert, gebruikt u **BlobSource** en **OracleSink** in de kopieeractiviteit. Zie [Activiteitseigenschappen kopiëren](#copy-activity-properties)voor eigenschappen van activiteit kopiëren die specifiek zijn voor een Oracle-database. Selecteer de koppeling voor uw gegevensarchief in de vorige sectie voor meer informatie over het gebruik van een gegevensarchief als bron of gootsteen.

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabriekentiteiten automatisch voor u gemaakt: gekoppelde services, gegevenssets en de pijplijn. Wanneer u hulpprogramma's of API's gebruikt (met uitzondering van de .NET API), definieert u deze entiteiten in de datafabriek met behulp van de JSON-indeling. Zie JSON-voorbeelden voor voorbeelden van JSON-definities voor gegevensfabrieksentiteiten die u gebruikt om gegevens naar of uit een on-premises Oracle-database te kopiëren.

In de volgende secties vindt u informatie over JSON-eigenschappen die u gebruikt om entiteiten in Gegevensfabriek te definiëren.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

In de volgende tabel worden JSON-elementen beschreven die specifiek zijn voor de oracle-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De **eigenschap type** moet worden ingesteld op **OnPremisesOracle**. |Ja |
| driverType | Geef op welk stuurprogramma u moet gebruiken om gegevens van of naar een Oracle-database te kopiëren. Toegestane waarden zijn **Microsoft** en **ODP** (standaard). Zie [Ondersteunde versie en installatie](#supported-versions-and-installation) voor stuurprogrammadetails. | Nee |
| Connectionstring | Geef de informatie op die nodig is om verbinding te maken met de Oracle-databaseinstantie voor de eigenschap **connectionString.** | Ja |
| gatewayNaam | De naam van de gateway die wordt gebruikt om verbinding te maken met de on-premises Oracle-server. |Ja |

**Voorbeeld: het Microsoft-stuurprogramma gebruiken**

> [!TIP]
> Als u een fout ziet met de tekst 'ORA-01025: UPI-parameter buiten `WireProtocolMode=1` bereik' en uw Oracle versie 8i is, voegt u toe aan uw verbindingstekenreeks en probeert u het opnieuw:

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Voorbeeld: Het ODP-stuurprogramma gebruiken**

Zie [Oracle-gegevensprovider voor .NET ODP voor](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/)meer informatie over toegestane indelingen.

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>))); User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie [Gegevenssets maken](data-factory-create-datasets.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets.

De secties van een JSON-bestand met gegevensset, zoals structuur, beschikbaarheid en beleid, zijn vergelijkbaar voor alle gegevenssettypen (bijvoorbeeld voor Oracle, Azure Blob-opslag en Azure Table-opslag).

De sectie **typeEigenschappen** is verschillend voor elk type gegevensset en geeft informatie over de locatie van de gegevens in het gegevensarchief. De sectie **typeEigenschappen** voor de gegevensset van het type **OracleTable** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel in de Oracle-database waarnaar de gekoppelde service verwijst. |Nee (als **oracleReaderQuery** of **OracleSource** is opgegeven) |

## <a name="copy-activity-properties"></a>Activiteitseigenschappen kopiëren

Zie [Pijplijnen maken](data-factory-create-pipelines.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten.

Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen en beleid zijn beschikbaar voor alle soorten activiteiten.

> [!NOTE]
> Kopieeractiviteit neemt slechts één invoer en produceert slechts één uitvoer.

Eigenschappen die beschikbaar zijn in de sectie **typeEigenschappen** van de activiteit, verschillen per activiteitstype. De eigenschappen van kopieeractiviteit variëren afhankelijk van het type bron en de gootsteen.

### <a name="oraclesource"></a>OracleSource (OracleSource)

In Kopieeractiviteit zijn de **OracleSource** volgende eigenschappen beschikbaar in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| oracleReaderQuery |Gebruik de aangepaste query om gegevens te lezen. |Een SQL-querytekenreeks. Selecteer bijvoorbeeld \* uit **MyTable.** <br/><br/>Als deze SQL-instructie niet is opgegeven, wordt deze SQL-instructie uitgevoerd: 'selecteren \* uit **MyTable'** |Nee<br />(als **tabelNaam** van **de gegevensset** is opgegeven) |

### <a name="oraclesink"></a>OracleSink (OracleSink)

**OracleSink** ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| writeBatchTimeout |De wachttijd voor de batchinvoegbewerking is voltooid voordat deze een time-out heeft. |**Tijdspanne**<br/><br/> Voorbeeld: 00:30:00 (30 minuten) |Nee |
| writeBatchSize |Hiermee voegt u gegevens in de SQL-tabel in wanneer de buffergrootte de waarde van **writeBatchSize**bereikt. |Geheel getal (aantal rijen) |Nee (standaard: 100) |
| sqlWriterCleanupScript sqlWriterCleanupScript |Hiermee geeft u een query op voor Het kopiëren van activiteit om uit te voeren, zodat de gegevens van een specifiek segment worden opgeschoond. |Een queryverklaring. |Nee |
| sliceIdentifierColumnName |Hiermee geeft u de kolomnaam op die voor Kopieeractiviteit moet worden gevuld met een automatisch gegenereerde segment-id. De waarde voor **sliceIdentifierColumnName** wordt gebruikt om gegevens van een specifiek segment op te schonen wanneer u opnieuw wordt uitgevoerd. |De kolomnaam van een kolom met gegevenstype **binaire tekst(32)**. |Nee |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>JSON-voorbeelden voor het kopiëren van gegevens van en naar de Oracle-database

In de volgende voorbeelden worden voorbeeld-JSON-definities gegeven die u gebruiken om een pijplijn te maken met [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) In de voorbeelden wordt uitgelegd hoe u gegevens kopiëren van of naar een Oracle-database en van of naar Azure Blob-opslag. Gegevens kunnen echter worden gekopieerd naar een van de sinks die worden vermeld in [ondersteunde gegevensopslag en -indelingen](data-factory-data-movement-activities.md#supported-data-stores-and-formats) met Kopieeractiviteit in Azure Data Factory.

**Voorbeeld: Gegevens van Oracle kopiëren naar Azure Blob-opslag**

De steekproef heeft de volgende gegevensfabriekentiteiten:

* Een gekoppelde service van het type [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Een [invoergegevensset](data-factory-create-datasets.md) van het type [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Een [pijplijn](data-factory-create-pipelines.md) met een kopieeractiviteit die [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) als bron en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) als gootsteen gebruikt.

Het voorbeeld kopieert gegevens uit een tabel in een on-premises Oracle-database naar een blob per uur. Zie de secties die de voorbeelden volgen voor meer informatie over verschillende eigenschappen die in het monster worden gebruikt.

**Oracle gekoppelde service**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
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
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Oracle-invoergegevensset**

In het voorbeeld wordt ervan uitgegaan dat u een tabel met de naam **MyTable** in Oracle hebt gemaakt. Het bevat een kolom genaamd **tijdstempelkolom** voor tijdreeksgegevens.

Extern **instellen**: **true** informeert de datafabriekservice dat de gegevensset zich buiten de gegevensfabriek bevindt en dat de gegevensset niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
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

Gegevens worden elk uur naar een nieuwe blob geschreven **(frequentie**: **uur**, **interval**: **1**). Het mappad en de bestandsnaam voor de blob worden dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt het deel van de begintijd jaar, maand, dag en uur.

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

**Pijplijn met een kopieeractiviteit**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **OracleSource** en wordt het **gootsteentype** ingesteld op **BlobSink**. De SQL-query die u opgeeft met de eigenschap **oracleReaderQuery** selecteert de gegevens in het afgelopen uur die u wilt kopiëren.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for a copy activity",
        "activities":[
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

**Voorbeeld: Gegevens uit Azure Blob-opslag kopiëren naar Oracle**

In dit voorbeeld ziet u hoe u gegevens van een Azure Blob-opslagaccount kopiëren naar een on-premises Oracle-database. U gegevens echter *rechtstreeks* kopiëren uit een van de bronnen die worden vermeld in [ondersteunde gegevensopslag en -indelingen](data-factory-data-movement-activities.md#supported-data-stores-and-formats) met Kopieeractiviteit in Azure Data Factory.

De steekproef heeft de volgende gegevensfabriekentiteiten:

* Een gekoppelde service van het type [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Een [invoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Een [pijplijn](data-factory-create-pipelines.md) met een kopieeractiviteit die [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) als bron [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) als gootsteen gebruikt.

Het voorbeeld kopieert elk uur gegevens van een blob naar een tabel in een on-premises Oracle-database. Zie de secties die de voorbeelden volgen voor meer informatie over verschillende eigenschappen die in het monster worden gebruikt.

**Oracle gekoppelde service**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
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
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Azure Blob-invoergegevensset**

Gegevens worden elk uur opgehaald uit een nieuwe blob **(frequentie**: **uur**, **interval**: **1**). Het mappad en de bestandsnaam voor de blob worden dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt het jaar-, maand- en daggedeelte van de begintijd. De bestandsnaam gebruikt het uurdeel van de begintijd. De instelling **extern**: **true** informeert de dienst Data Factory dat deze tabel zich buiten de gegevensfabriek bevindt en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
            "frequency": "Day",
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

**Oracle-uitvoergegevensset**

In het voorbeeld wordt ervan uitgegaan dat u een tabel met de naam **MyTable** in Oracle hebt gemaakt. Maak de tabel in Oracle met hetzelfde aantal kolommen dat u verwacht dat het BLOB CSV-bestand bevat. Elk uur worden er nieuwe rijen aan de tabel toegevoegd.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**Pijplijn met een kopieeractiviteit**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **BlobSource** en wordt het **gootsteentype** ingesteld op **OracleSink**.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with a copy activity",
        "activities":[
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
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


## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

### <a name="problem-1-net-framework-data-provider"></a>Probleem 1: .NET Framework Data Provider

**Foutbericht**

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.

**Mogelijke oorzaken**

* De .NET Framework Data Provider voor Oracle is niet geïnstalleerd.
* De .NET Framework Data Provider voor Oracle is geïnstalleerd op .NET Framework 2.0 en is niet te vinden in de .NET Framework 4.0-mappen.

**Afsluiting**

* Als u de .NET-provider voor Oracle niet hebt geïnstalleerd, [installeert u deze](https://www.oracle.com/technetwork/topics/dotnet/downloads/)en probeert u het scenario opnieuw.
* Als u het foutbericht zelfs na de installatie van de provider ziet, voert u de volgende stappen uit:
    1. Open het machineconfig-bestand voor .NET 2.0\>vanuit de map <systeemschijf :\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Zoek naar **Oracle Data Provider voor .NET**. U moet in staat zijn om een vermelding te vinden, zoals weergegeven in de volgende steekproef onder **system.data** > **DbProviderFactories:**`<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Kopieer dit item naar het bestand machine.config in de volgende\>map .NET 4.0: <systeemschijf :\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Wijzig vervolgens de versie in 4.xxx.x.x.
* Installeer <ODP.NET\>Installed Path \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll in de global assembly cache (GAC) door **gacutil /i [providerpad]** uit te voeren.

### <a name="problem-2-datetime-formatting"></a>Probleem 2: Datum-/tijdopmaak

**Foutbericht**

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Afsluiting**

Mogelijk moet u de querytekenreeks in uw kopieeractiviteit aanpassen op basis van de manier waarop datums zijn geconfigureerd in uw Oracle-database. Hier is een voorbeeld (met behulp van de **to_date** functie):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Typetoewijzing voor Oracle

Zoals vermeld in [activiteiten voor gegevensverkeer](data-factory-data-movement-activities.md)voert Kopieeractiviteit automatische typeconversies uit van brontypen naar gootsteentypen met behulp van de volgende benadering in twee stappen:

1. Converteren van brontypen naar het .NET-type.
2. Converteren van het .NET-type naar het oorspronkelijke gootsteentype.

Wanneer u gegevens van Oracle verplaatst, worden de volgende toewijzingen gebruikt van het Oracle-gegevenstype naar het .NET-type en vice versa:

| Oracle-gegevenstype | .NET Framework-gegevenstype |
| --- | --- |
| BFILE |Byte |
| Blob |Byte<br/>(alleen ondersteund op Oracle 10g en latere versies wanneer u een Microsoft-stuurprogramma gebruikt) |
| Char |Tekenreeks |
| CLOB (CLOB) |Tekenreeks |
| DATE |DateTime |
| Float |Decimaal, Tekenreeks (als precisie > 28) |
| INTEGER |Decimaal, Tekenreeks (als precisie > 28) |
| INTERVAL JAAR TOT MAAND |Int32 |
| INTERVAL DAG NAAR TWEEDE |TimeSpan |
| Lange |Tekenreeks |
| LANG RAUW |Byte |
| Nchar |Tekenreeks |
| NCLOB (NCLOB) |Tekenreeks |
| Nummer |Decimaal, Tekenreeks (als precisie > 28) |
| NVARCHAR2 (NVARCHAR2) |Tekenreeks |
| Raw |Byte |
| RIJ-ID |Tekenreeks |
| Tijdstempel |DateTime |
| TIJDSTEMPEL MET LOKALE TIJDZONE |DateTime |
| TIJDSTEMPEL MET TIJDZONE |DateTime |
| NIET-ONDERTEKEND GEHEEL GETAL |Aantal |
| VARCHAR2 (VARCHAR2) |Tekenreeks |
| XML |Tekenreeks |

> [!NOTE]
> Gegevenstypen **INTERVAL VAN JAAR TOT MAAND** EN INTERVAL DAG TOT **SECONDE** worden niet ondersteund wanneer u een Microsoft-stuurprogramma gebruikt.

## <a name="map-source-to-sink-columns"></a>Kaartbron om kolommen te laten zinken

Zie Kolommen van [gegevenssetssetsin Gegevensfabriek](data-factory-map-columns.md)toewijzen voor meer informatie over het toewijzen van kolommen in de brongegevensset naar kolommen in de sinkdataset.

## <a name="repeatable-read-from-relational-sources"></a>Herhaalbaar lezen uit relationele bronnen

Wanneer u gegevens uit relationele gegevensopslag kopieert, moet u rekening houden met herhaalbaarheid om onbedoelde resultaten te voorkomen. In Azure Data Factory u een segment handmatig opnieuw uitvoeren. U ook een beleid voor het opnieuw proberen configureren voor een gegevensset, zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment opnieuw wordt uitgevoerd, handmatig of door een beleid voor nieuwe poging, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht hoe vaak een segment wordt uitgevoerd. Zie [Herhaalbaar lezen uit relationele bronnen voor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)meer informatie.

## <a name="performance-and-tuning"></a>Prestaties en afstemmen

Zie de [handleiding voor prestaties en tuning kopiëren](data-factory-copy-activity-performance.md) om meer te weten te komen over belangrijke factoren die van invloed zijn op de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory. U ook meer te weten komen over verschillende manieren om het te optimaliseren.
