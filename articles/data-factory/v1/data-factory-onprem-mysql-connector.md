---
title: Gegevens uit MySQL verplaatsen met Azure Data Factory
description: Meer informatie over het verplaatsen van gegevens uit MySQL-database met Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 452f4fce-9eb5-40a0-92f8-1e98691bea4c
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/06/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 90fccba016a3db9ff85f8ec7c8fd426ef3c896a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281286"
---
# <a name="move-data-from-mysql-using-azure-data-factory"></a>Gegevens verplaatsen van MySQL met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-onprem-mysql-connector.md)
> * [Versie 2 (huidige versie)](../connector-mysql.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [MySQL-connector in V2](../connector-mysql.md)als u de huidige versie van de Data Factory-service gebruikt.


In dit artikel wordt uitgelegd hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens uit een on-premises MySQL-database te verplaatsen. Het bouwt voort op het artikel [Data Movement Activities,](data-factory-data-movement-activities.md) dat een algemeen overzicht geeft van gegevensverplaatsing met de kopieeractiviteit.

U gegevens uit een on-premises MySQL-gegevensarchief kopiëren naar elk ondersteund sinkdataarchief. Zie de tabel [Ondersteunde gegevensopslag](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die als sinks worden ondersteund door de kopieeractiviteit. Datafactory ondersteunt momenteel alleen het verplaatsen van gegevens van een MySQL-gegevensarchief naar andere gegevensopslag, maar niet voor het verplaatsen van gegevens van andere gegevensopslag naar een MySQL-gegevensarchief. 

## <a name="prerequisites"></a>Vereisten
Data Factory-service ondersteunt het verbinden met on-premises MySQL-bronnen met behulp van de Data Management Gateway. Bekijk [het verplaatsen van gegevens tussen on-premises locaties en een cloudartikel](data-factory-move-data-between-onprem-and-cloud.md) voor meer informatie over Data Management Gateway en stapsgewijze instructies voor het instellen van de gateway.

Gateway is vereist, zelfs als de MySQL-database wordt gehost in een Azure IaaS virtuele machine (VM). U de gateway op dezelfde VM installeren als het gegevensarchief of op een andere virtuele machine, zolang de gateway verbinding kan maken met de database.

> [!NOTE]
> Zie [Problemen met de gateway oplossen](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) voor tips over het oplossen van verbindings-/gatewaygerelateerde problemen.

## <a name="supported-versions-and-installation"></a>Ondersteunde versies en installatie
Als u wilt dat gegevensbeheergateway verbinding maakt met de MySQL-database, moet u de [MySQL Connector/NET voor Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) (versie tussen 6.6.5 en 6.10.7) installeren op hetzelfde systeem als de Data Management Gateway. Dit 32-bits stuurprogramma is compatibel met 64-bits Data Management Gateway. MySQL versie 5.1 en hoger wordt ondersteund.

> [!TIP]
> Als u fout op 'Verificatie mislukt omdat de externe partij de transportstream heeft gesloten' hebt geraakt, u overwegen de MySQL Connector/NET te upgraden naar een hogere versie.

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens verplaatst van een on-premises Cassandra-gegevensarchief met behulp van verschillende hulpprogramma's/API's. 

- De eenvoudigste manier om een pijplijn te maken, is door de **wizard Kopiëren**te gebruiken. Zie [Zelfstudie: Maak een pijplijn met wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough voor het maken van een pijplijn met de wizard Gegevens kopiëren. 
- U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-sjabloon,** **.NET API**en REST **API**. Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken. 

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek.
2. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven. 
3. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt. 

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u tools/API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling.  Zie [JSON-voorbeeld: Gegevens kopiëren van MySQL naar Azure Blob](#json-example-copy-data-from-mysql-to-azure-blob) voor een voorbeeld van JSON voor gegevensfabriekentiteiten die worden gebruikt om gegevens uit een on-premises MySQL-gegevensarchief te kopiëren. 

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om gegevensfabrieksentiteiten te definiëren die specifiek zijn voor een MySQL-gegevensarchief:

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor de gekoppelde MySQL-service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet zijn ingesteld op: **OnPremisesMySql** |Ja |
| server |Naam van de MySQL-server. |Ja |
| database |Naam van de MySQL-database. |Ja |
| schema |Naam van het schema in de database. |Nee |
| authenticationType |Type verificatie wordt gebruikt om verbinding te maken met de MySQL-database. Mogelijke waarden `Basic`zijn: . |Ja |
| userName |Geef de gebruikersnaam op om verbinding te maken met de MySQL-database. |Ja |
| wachtwoord |Geef een wachtwoord op voor het opgegeven gebruikersaccount. |Ja |
| gatewayNaam |Naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises MySQL-database. |Ja |

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties zoals structuur, beschikbaarheid en beleid van een gegevensset JSON zijn vergelijkbaar voor alle gegevenssettypen (Azure SQL, Azure blob, Azure table, etc.).

De sectie **typeEigenschappen** is verschillend voor elk type gegevensset en geeft informatie over de locatie van de gegevens in het gegevensarchief. De sectie typeEigenschappen voor de gegevensset van type **RelationalTable** (inclusief MySQL-gegevensset) heeft de volgende eigenschappen

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel in de instantie MySQL Database waarnaar de gekoppelde service verwijst. |Nee (als **query** van **RelationalSource** is opgegeven) |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [Pijplijnmaken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen zijn beschikbaar voor alle soorten activiteiten.

Overwegende dat de eigenschappen die beschikbaar zijn in de sectie **typeEigenschappen** van de activiteit per activiteitstype verschillen. Voor Kopieeractiviteit variëren ze afhankelijk van de soorten bronnen en putten.

Wanneer de bron in kopieeractiviteit van het type **RelationalSource** is (inclusief MySQL), zijn de volgende eigenschappen beschikbaar in de sectie typeProperties:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-querytekenreeks. Selecteer bijvoorbeeld * in MyTable. |Nee (als **tabelNaam** van **de gegevensset** is opgegeven) |


## <a name="json-example-copy-data-from-mysql-to-azure-blob"></a>JSON-voorbeeld: Gegevens kopiëren van MySQL naar Azure Blob
In dit voorbeeld worden voorbeeld-JSON-definities gegeven die u gebruiken om een pijplijn te maken met Behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Hierin wordt weergegeven hoe u gegevens uit een on-premises MySQL-database kopiëren naar een Azure Blob Storage. Gegevens kunnen echter worden gekopieerd naar een van de putten die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) zijn vermeld met behulp van de kopieeractiviteit in Azure Data Factory.

> [!IMPORTANT]
> Dit voorbeeld bevat JSON-fragmenten. Het bevat geen stapsgewijze instructies voor het maken van de gegevensfabriek. Zie [gegevens verplaatsen tussen on-premises locaties en cloudartikelen](data-factory-move-data-between-onprem-and-cloud.md) voor stapsgewijze instructies.

Het voorbeeld heeft de volgende gegevensfabriekentiteiten:

1. Een gekoppelde service van het type [OnPremisesMySql](data-factory-onprem-mysql-connector.md#linked-service-properties).
2. Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een [invoergegevensset](data-factory-create-datasets.md) van type [RelationalTable](data-factory-onprem-mysql-connector.md#dataset-properties).
4. Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Een [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [RelationalSource](data-factory-onprem-mysql-connector.md#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)gebruikt.

Het voorbeeld kopieert gegevens uit een queryresultaat in de MySQL-database naar een blob per uur. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

Als eerste stap, het instellen van de data management gateway. De instructies bevinden zich in de [verhuisgegevens tussen on-premises locaties en cloudartikelen.](data-factory-move-data-between-onprem-and-cloud.md)

**MySQL-gekoppelde service:**

```JSON
    {
      "name": "OnPremMySqlLinkedService",
      "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
          "server": "<server name>",
          "database": "<database name>",
          "schema": "<schema name>",
          "authenticationType": "<authentication type>",
          "userName": "<user name>",
          "password": "<password>",
          "gatewayName": "<gateway>"
        }
      }
    }
```

**Gekoppelde Azure Storage-service:**

```JSON
    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }
```

**MySQL-invoergegevensset:**

In het voorbeeld wordt ervan uitgegaan dat u een tabel "MyTable" in MySQL hebt gemaakt en een kolom bevat die "tijdstempelkolom" wordt genoemd voor tijdreeksgegevens.

Als u "extern" instelt: "true" informeert de service Data Factory dat de tabel zich buiten de gegevensfabriek bevindt en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```JSON
    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremMySqlLinkedService",
            "typeProperties": {},
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
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
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
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
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
```

**Pijplijn met kopieeractiviteit:**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **RelationalSource** en wordt **het gootsteentype** ingesteld op **BlobSink**. De SQL-query die is opgegeven voor de **eigenschap query** selecteert de gegevens in het afgelopen uur die u wilt kopiëren.

```JSON
    {
        "name": "CopyMySqlToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }
```


### <a name="type-mapping-for-mysql"></a>Typetoewijzing voor MySQL
Zoals vermeld in het artikel [gegevensverplaatsingsactiviteiten](data-factory-data-movement-activities.md) voert kopieeractiviteit automatische typeconversies uit van brontypen naar sinktypen met de volgende benadering in twee stappen:

1. Converteren van native brontypen naar .NET-type
2. Converteren van .NET-type naar native sinktype

Bij het verplaatsen van gegevens naar MySQL worden de volgende toewijzingen gebruikt van MySQL-typen naar .NET-typen.

| MySQL-databasetype | .NET Framework type |
| --- | --- |
| bigint unsigned |Decimal |
| bigint |Int64 |
| bit |Decimal |
| blob |Byte |
| Booleaanse waarde |Booleaans |
| Char |Tekenreeks |
| date |Datum/tijd |
| datum/tijd |Datum/tijd |
| decimal |Decimal |
| dubbele precisie |Double |
| double |Double |
| Enum |Tekenreeks |
| float |Enkel |
| int unsigned |Int64 |
| int |Int32 |
| geheel niet ondertekend |Int64 |
| geheel getal |Int32 |
| lange varbinary |Byte |
| lange varchar |Tekenreeks |
| longblob (longblob) |Byte |
| Longtext |Tekenreeks |
| mediumblob |Byte |
| mediumint unsigned |Int64 |
| mediumint |Int32 |
| mediumtekst |Tekenreeks |
| numeriek |Decimal |
| real |Double |
| set |Tekenreeks |
| smallint unsigned |Int32 |
| smallint |Int16 |
| tekst |Tekenreeks |
| tijd |TimeSpan |
| tijdstempel |Datum/tijd |
| tinyblob |Byte |
| tinyint unsigned |Int16 |
| tinyint |Int16 |
| tinytext |Tekenreeks |
| varchar |Tekenreeks |
| jaar |Int |

## <a name="map-source-to-sink-columns"></a>Kaartbron om kolommen te laten zinken
Zie Kolommen van [gegevenssetsin Azure Data Factory](data-factory-map-columns.md)voor meer informatie over het toewijzen van kolommen in brongegevensset naar kolommen in sink dataset.

## <a name="repeatable-read-from-relational-sources"></a>Herhaalbaar lezen uit relationele bronnen
Houd bij het kopiëren van gegevens uit relationele gegevensopslag rekening met herhaalbaarheid om onbedoelde resultaten te voorkomen. In Azure Data Factory u een segment handmatig opnieuw uitvoeren. U ook het beleid voor een wijziging opnieuw configureren, zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment in beide richtingen wordt opnieuw uitgevoerd, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht hoe vaak een segment wordt uitgevoerd. Zie [Herhaalbaar lezen uit relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestaties en tuning
Zie [Handleiding activiteitsprestaties kopiëren & tuningom](data-factory-copy-activity-performance.md) meer te weten te komen over de belangrijkste factoren die van invloed zijn op de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory en op verschillende manieren om deze te optimaliseren.
