---
title: Gegevens verplaatsen van PostgreSQL met Azure Data Factory
description: Meer informatie over het verplaatsen van gegevens uit PostgreSQL-database met Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 37c83e77cadae002ff701a08c4b36a86f7cab9a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281234"
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Gegevens verplaatsen van PostgreSQL met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-onprem-postgresql-connector.md)
> * [Versie 2 (huidige versie)](../connector-postgresql.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [PostgreSQL-connector in V2](../connector-postgresql.md)als u de huidige versie van de Data Factory-service gebruikt.


In dit artikel wordt uitgelegd hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens uit een on-premises PostgreSQL-database te verplaatsen. Het bouwt voort op het artikel [Data Movement Activities,](data-factory-data-movement-activities.md) dat een algemeen overzicht geeft van gegevensverplaatsing met de kopieeractiviteit.

U gegevens uit een on-premises PostgreSQL-gegevensarchief kopiëren naar elk ondersteund sinkdataarchief. Zie [ondersteunde gegevensopslag](data-factory-data-movement-activities.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die als sinks worden ondersteund door de kopieeractiviteit. Datafactory ondersteunt momenteel het verplaatsen van gegevens van een PostgreSQL-database naar andere gegevensopslag, maar niet voor het verplaatsen van gegevens van andere gegevensopslag naar een PostgreSQL-database.

## <a name="prerequisites"></a>Vereisten

Data Factory-service ondersteunt het verbinden met on-premises PostgreSQL-bronnen met behulp van de Data Management Gateway. Bekijk [het verplaatsen van gegevens tussen on-premises locaties en een cloudartikel](data-factory-move-data-between-onprem-and-cloud.md) voor meer informatie over Data Management Gateway en stapsgewijze instructies voor het instellen van de gateway.

Gateway is vereist, zelfs als de PostgreSQL-database wordt gehost in een Azure IaaS VM. U gateway installeren op dezelfde IaaS VM als het gegevensarchief of op een andere VM, zolang de gateway verbinding kan maken met de database.

> [!NOTE]
> Zie [Problemen met de gateway oplossen](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) voor tips over het oplossen van verbindings-/gatewaygerelateerde problemen.

## <a name="supported-versions-and-installation"></a>Ondersteunde versies en installatie
Als U wilt dat gegevensbeheergateway verbinding maakt met de PostgreSQL-database, installeert u de [Ngpsql-gegevensprovider voor PostgreSQL](https://go.microsoft.com/fwlink/?linkid=282716) met versie tussen 2.0.12 en 3.1.9 op hetzelfde systeem als de Data Management Gateway. PostgreSQL versie 7.4 en hoger wordt ondersteund.

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens verplaatst van een on-premises PostgreSQL-gegevensarchief met behulp van verschillende hulpprogramma's/API's.

- De eenvoudigste manier om een pijplijn te maken, is door de **wizard Kopiëren**te gebruiken. Zie [Zelfstudie: Maak een pijplijn met wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough voor het maken van een pijplijn met de wizard Gegevens kopiëren.
- U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken:
  - Visual Studio
  - Azure PowerShell
  - Azure Resource Manager-sjabloon
  - .NET API
  - REST API

    Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken.

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek.
2. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven.
3. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt.

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u tools/API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling. Zie [JSON-voorbeeld: Gegevens kopiëren van PostgreSQL naar Azure Blob](#json-example-copy-data-from-postgresql-to-azure-blob) voor een voorbeeld van JSON met JSON-definities voor entiteiten in Gegevensfabriek die worden gebruikt om gegevens uit een on-premises PostgreSQL-gegevensarchief te kopiëren.

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om gegevensfabrieksentiteiten te definiëren die specifiek zijn voor een PostgreSQL-gegevensarchief:

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor de gekoppelde PostgreSQL-service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet zijn ingesteld op: **OnPremisesPostgreSql** |Ja |
| server |Naam van de PostgreSQL-server. |Ja |
| database |Naam van de PostgreSQL-database. |Ja |
| schema |Naam van het schema in de database. De schemanaam is hoofdlettergevoelig. |Nee |
| authenticationType |Type verificatie wordt gebruikt om verbinding te maken met de PostgreSQL-database. Mogelijke waarden zijn: Anoniem, Basic en Windows. |Ja |
| gebruikersnaam |Geef de gebruikersnaam op als u basis- of Windows-verificatie gebruikt. |Nee |
| wachtwoord |Geef een wachtwoord op voor het gebruikersaccount dat u hebt opgegeven voor de gebruikersnaam. |Nee |
| gatewayNaam |Naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises PostgreSQL-database. |Ja |

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties zoals structuur, beschikbaarheid en beleid van een gegevensset JSON zijn vergelijkbaar voor alle gegevenssettypen.

De sectie typeEigenschappen is verschillend voor elk type gegevensset en geeft informatie over de locatie van de gegevens in het gegevensarchief. De sectie typeEigenschappen voor de gegevensset van type **RelationalTable** (die PostgreSQL-gegevensset bevat) heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel in de instantie PostgreSQL Database waarnaar de gekoppelde service verwijst. De tabelNaam is hoofdlettergevoelig. |Nee (als **query** van **RelationalSource** is opgegeven) |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [Pijplijnmaken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen en beleid zijn beschikbaar voor alle soorten activiteiten.

Overwegende dat de eigenschappen die beschikbaar zijn in de sectie typeEigenschappen van de activiteit per activiteitstype verschillen. Voor Kopieeractiviteit variëren ze afhankelijk van de soorten bronnen en putten.

Wanneer de bron van type **RelationalSource** is (waaronder PostgreSQL) zijn de volgende eigenschappen beschikbaar in de sectie typeProperties:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-querytekenreeks. Bijvoorbeeld: `"query": "select * from \"MySchema\".\"MyTable\""`. |Nee (als **tabelNaam** van **de gegevensset** is opgegeven) |

> [!NOTE]
> Schema- en tabelnamen zijn hoofdlettergevoelig. Sluit ze `""` in (dubbele aanhalingstekens) in de query.

**Voorbeeld:**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>JSON-voorbeeld: Gegevens kopiëren van PostgreSQL naar Azure Blob
In dit voorbeeld worden voorbeeld-JSON-definities gegeven die u gebruiken om een pijplijn te maken met Behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Ze laten zien hoe u gegevens uit de PostgreSQL-database kopieert naar Azure Blob Storage. Gegevens kunnen echter worden gekopieerd naar een van de putten die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) zijn vermeld met behulp van de kopieeractiviteit in Azure Data Factory.

> [!IMPORTANT]
> Dit voorbeeld bevat JSON-fragmenten. Het bevat geen stapsgewijze instructies voor het maken van de gegevensfabriek. Zie [gegevens verplaatsen tussen on-premises locaties en cloudartikelen](data-factory-move-data-between-onprem-and-cloud.md) voor stapsgewijze instructies.

Het voorbeeld heeft de volgende gegevensfabriekentiteiten:

1. Een gekoppelde service van het type [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties).
2. Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een [invoergegevensset](data-factory-create-datasets.md) van type [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties).
4. Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. De [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)gebruikt.

Het voorbeeld kopieert elk uur gegevens uit een queryresultaat in de PostgreSQL-database naar een blob. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

Stel als eerste stap de datamanagementgateway in. De instructies bevinden zich in de [verhuisgegevens tussen on-premises locaties en cloudartikelen.](data-factory-move-data-between-onprem-and-cloud.md)

**PostgreSQL-gekoppelde service:**

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
**Gekoppelde Azure Blob-opslagservice:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```
**PostgreSQL-invoergegevensset:**

In het voorbeeld wordt ervan uitgegaan dat u een tabel "MyTable" in PostgreSQL hebt gemaakt en een kolom bevat die "tijdstempel" wordt genoemd voor tijdreeksgegevens.

De `"external": true` instelling informeert de datafabriekservice dat de gegevensset buiten de gegevensfabriek staat en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
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

Gegevens worden elk uur naar een nieuwe blob geschreven (frequentie: uur, interval: 1). Het mappad en de bestandsnaam voor de blob worden dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van de begintijd van jaar, maand, dag en uur.

```json
{
    "name": "AzureBlobPostgreSqlDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur wordt uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **RelationalSource** en wordt **het gootsteentype** ingesteld op **BlobSink**. De SQL-query die is opgegeven voor de **eigenschap query** selecteert de gegevens uit de tabel public.usstates in de PostgreSQL-database.

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"public\".\"usstates\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "PostgreSqlDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobPostgreSqlDataSet"
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
                "name": "PostgreSqlToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
## <a name="type-mapping-for-postgresql"></a>Toewijzing typen voor PostgreSQL
Zoals vermeld in het artikel [Gegevensverplaatsingsactiviteiten](data-factory-data-movement-activities.md) voert kopieeractiviteit automatische typeconversies uit van brontypen naar gootsteentypen met de volgende aanpak in twee stappen:

1. Converteren van native brontypen naar .NET-type
2. Converteren van .NET-type naar native sinktype

Bij het verplaatsen van gegevens naar PostgreSQL worden de volgende toewijzingen gebruikt van PostgreSQL-type naar .NET-type.

| PostgreSQL-databasetype | PostgresSQL-aliassen | .NET Framework type |
| --- | --- | --- |
| abstime | |Datum/tijd |
| bigint |int8 |Int64 |
| bigserial |serie8 |Int64 |
| bit [n)] | |Byte[], Tekenreeks |
| beetje variërend [ (n) ] |varbit |Byte[], Tekenreeks |
| booleaans |Booleaanse waarde |Booleaans |
| Vak | |Byte[], Tekenreeks |
| bytea | |Byte[], Tekenreeks |
| teken [(n)] |char [(n)] |Tekenreeks |
| teken variërend [(n)] |varchar [n)] |Tekenreeks |
| Cid | |Tekenreeks |
| cidr | |Tekenreeks |
| cirkel | |Byte[], Tekenreeks |
| date | |Datum/tijd |
| daterange | |Tekenreeks |
| dubbele precisie |vlotter8 |Double |
| inet | |Byte[], Tekenreeks |
| intarry | |Tekenreeks |
| int4range | |Tekenreeks |
| int8range | |Tekenreeks |
| geheel getal |int, int4 |Int32 |
| interval [velden] [(p)] | |Periode |
| json | |Tekenreeks |
| jsonb | |Byte |
| lijn | |Byte[], Tekenreeks |
| lseg | |Byte[], Tekenreeks |
| macaddr | |Byte[], Tekenreeks |
| Geld | |Decimal |
| numeriek [(p, s)] |decimaal [(p, s)] |Decimal |
| numrange | |Tekenreeks |
| Oid | |Int32 |
| path | |Byte[], Tekenreeks |
| pg_lsn | |Int64 |
| Punt | |Byte[], Tekenreeks |
| Veelhoek | |Byte[], Tekenreeks |
| real |vlotter4 |Enkel |
| smallint |int2 |Int16 |
| smallserial smallserial |serieel2 |Int16 |
| Seriële |serieel4 |Int32 |
| tekst | |Tekenreeks |

## <a name="map-source-to-sink-columns"></a>Kaartbron om kolommen te laten zinken
Zie Kolommen van [gegevenssetsin Azure Data Factory](data-factory-map-columns.md)voor meer informatie over het toewijzen van kolommen in brongegevensset naar kolommen in sink dataset.

## <a name="repeatable-read-from-relational-sources"></a>Herhaalbaar lezen uit relationele bronnen
Houd bij het kopiëren van gegevens uit relationele gegevensopslag rekening met herhaalbaarheid om onbedoelde resultaten te voorkomen. In Azure Data Factory u een segment handmatig opnieuw uitvoeren. U ook het beleid voor een wijziging opnieuw configureren, zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment in beide richtingen wordt opnieuw uitgevoerd, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht hoe vaak een segment wordt uitgevoerd. Zie [Herhaalbaar lezen uit relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestaties en tuning
Zie [Handleiding activiteitsprestaties kopiëren & tuningom](data-factory-copy-activity-performance.md) meer te weten te komen over de belangrijkste factoren die van invloed zijn op de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory en op verschillende manieren om deze te optimaliseren.
