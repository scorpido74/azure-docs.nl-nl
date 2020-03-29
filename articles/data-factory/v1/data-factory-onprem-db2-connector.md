---
title: Gegevens van DB2 verplaatsen met Azure Data Factory
description: Meer informatie over het verplaatsen van gegevens uit een on-premises DB2-database met Azure Data Factory Copy Activity
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e5d2c6b0460c3a7566adb17601aceb57e57f4d0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931790"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Gegevens verplaatsen van DB2 met Azure Data Factory Copy Activity
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-onprem-db2-connector.md)
> * [Versie 2 (huidige versie)](../connector-db2.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [DB2-connector in V2](../connector-db2.md)als u de huidige versie van de datafabriekservice gebruikt.


In dit artikel wordt beschreven hoe u Activiteit kopiëren in Azure Data Factory gebruiken om gegevens uit een on-premises DB2-database naar een gegevensarchief te kopiëren. U gegevens kopiëren naar elk archief dat wordt vermeld als een ondersteunde gootsteen in het artikel [gegevensverplaatsingsactiviteiten van Data Factory.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Dit onderwerp bouwt voort op het artikel Data Factory, dat een overzicht geeft van gegevensverplaatsing met behulp van Kopieeractiviteit en de ondersteunde combinaties van gegevensopslagbevat. 

Data Factory ondersteunt momenteel alleen het verplaatsen van gegevens van een DB2-database naar een [ondersteund sink data store.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Het verplaatsen van gegevens van andere gegevensopslag naar een DB2-database wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten
Data Factory ondersteunt het maken van verbinding met een on-premises DB2-database met behulp van de [data management gateway.](data-factory-data-management-gateway.md) Zie de [gegevens verplaatsen van on-premises naar het cloudartikel](data-factory-move-data-between-onprem-and-cloud.md) voor stapsgewijze instructies voor het instellen van de gatewaygegevenspijplijn om uw gegevens te verplaatsen.

Een gateway is vereist, zelfs als de DB2 wordt gehost op Azure IaaS VM. U de gateway installeren op dezelfde IaaS VM als het gegevensarchief. Als de gateway verbinding kan maken met de database, u de gateway op een andere vm installeren.

De datamanagementgateway biedt een ingebouwd DB2-stuurprogramma, zodat u geen stuurprogramma handmatig hoeft te installeren om gegevens van DB2 te kopiëren.

> [!NOTE]
> Zie het artikel [Problemen met gatewayproblemen oplossen](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) voor tips over het oplossen van verbindings- en gatewayproblemen.


## <a name="supported-versions"></a>Ondersteunde versies
De Data Factory DB2-connector ondersteunt de volgende IBM DB2-platforms en -versies met DRDA-versies 9, 10 en 11 van Distributed Relational Database Architecture (DRDA):

* IBM DB2 voor z/OS versie 11.1
* IBM DB2 voor z/OS versie 10.1
* IBM DB2 voor i (AS400) versie 7.2
* IBM DB2 voor i (AS400) versie 7.1
* IBM DB2 voor Linux, UNIX en Windows (LUW) versie 11
* IBM DB2 voor LUW versie 10.5
* IBM DB2 voor LUW versie 10.1

> [!TIP]
> Als u het foutbericht ontvangt "Het pakket dat overeenkomt met een SQL-instructieuitvoeringsaanvraag is niet gevonden. SQLSTATE=51002 SQLCODE=-805," de reden is dat een noodzakelijk pakket niet is gemaakt voor de normale gebruiker op het besturingssysteem. Volg de volgende instructies voor het type DB2-server om dit probleem op te lossen:
> - DB2 voor i (AS400): Laat een poweruser de verzameling maken voor de normale gebruiker voordat u Kopieeractiviteit uitvoert. Als u de verzameling wilt maken, gebruikt u de opdracht:`create collection <username>`
> - DB2 voor z/OS of LUW: Gebruik een account met hoge bevoegdheden - een power user of admin die pakketautoriteiten en BIND, BINDADD, GRANT EXECUTE heeft om openbare machtigingen te gebruiken - om de kopie eenmalig uit te voeren. Het benodigde pakket wordt automatisch gemaakt tijdens de kopie. Daarna u terugschakelen naar de normale gebruiker voor uw volgende exemplaar wordt uitgevoerd.

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit om gegevens uit een on-premises DB2-gegevensarchief te verplaatsen met behulp van verschillende hulpprogramma's en API's: 

- De eenvoudigste manier om een pijplijn te maken, is door de wizard Kopiëren van Azure Data Factory te gebruiken. Zie de [pijplijn Zelfstudie: Een pijplijn maken met de wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md)voor een snelle walkthrough voor het maken van een pijplijn met de wizard Kopiëren . 
- U ook hulpprogramma's gebruiken om een pijplijn te maken, waaronder Visual Studio, Azure PowerShell, een Azure Resource Manager-sjabloon, de .NET-API en de REST-API. Zie de [zelfstudie Activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor stapsgewijze instructies voor het maken van een pijplijn met een kopieeractiviteit. 

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak gekoppelde services om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek.
2. Maak gegevenssets om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven. 
3. Maak een pijplijn met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt. 

Wanneer u de wizard Kopiëren gebruikt, worden JSON-definities voor de gekoppelde services, gegevenssets en pijplijnentiteiten voor u automatisch gemaakt. Wanneer u hulpprogramma's of API's gebruikt (behalve de .NET API), definieert u de entiteiten van de gegevensfabriek met behulp van de JSON-indeling. Het JSON-voorbeeld: gegevens kopiëren van DB2 naar Azure Blob-opslag toont de JSON-definities voor de entiteiten van de gegevensfabriek die worden gebruikt om gegevens uit een on-premises DB2-gegevensarchief te kopiëren.

In de volgende secties vindt u informatie over de JSON-eigenschappen die worden gebruikt om de entiteiten van de gegevensfabriek te definiëren die specifiek zijn voor een DB2-gegevensarchief.

## <a name="db2-linked-service-properties"></a>DB2 gekoppelde service-eigenschappen
In de volgende tabel worden de JSON-eigenschappen weergegeven die specifiek zijn voor een door DB2 gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| **Type** |Deze eigenschap moet worden ingesteld op **OnPremisesDb2**. |Ja |
| **Server** |De naam van de DB2-server. |Ja |
| **database** |De naam van de DB2-database. |Ja |
| **schema** |De naam van het schema in de DB2-database. Deze eigenschap is hoofdlettergevoelig. |Nee |
| **authenticationType** |Het type verificatie dat wordt gebruikt om verbinding te maken met de DB2-database. De mogelijke waarden zijn: Anoniem, Basic en Windows. |Ja |
| **Gebruikersnaam** |De naam voor het gebruikersaccount als u basis- of Windows-verificatie gebruikt. |Nee |
| **wachtwoord** |Het wachtwoord voor het gebruikersaccount. |Nee |
| **gatewayNaam** |De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises DB2-database. |Ja |

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties, zoals **structuur,** **beschikbaarheid**en het **beleid** voor een json-gegevensset, zijn vergelijkbaar voor alle gegevenssettypen (Azure SQL, Azure Blob-opslag, Azure Table-opslag, enzovoort).

De sectie **typeEigenschappen** is verschillend voor elk type gegevensset en geeft informatie over de locatie van de gegevens in het gegevensarchief. De sectie **typeEigenschappen** voor een gegevensset van type **RelationalTable**, die de DB2-gegevensset bevat, heeft de volgende eigenschap:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| **tableName** |De naam van de tabel in de DB2-databaseinstantie waarnaar de gekoppelde service verwijst. Deze eigenschap is hoofdlettergevoelig. |Nee (als de **eigenschap query** van een kopieeractiviteit van het type **RelationalSource** is opgegeven) |

## <a name="copy-activity-properties"></a>Activiteitseigenschappen kopiëren
Zie het artikel [Pijplijnmaken](data-factory-create-pipelines.md) voor een lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van kopieeractiviteiten. Activiteitseigenschappen kopiëren, zoals **naam,** **beschrijving,** **invoertabel,** **uitvoertabel** en **beleid,** zijn beschikbaar voor alle soorten activiteiten. De eigenschappen die beschikbaar zijn in de sectie **typeEigenschappen** van de activiteit, verschillen per activiteitstype. Voor Kopieeractiviteit zijn de eigenschappen afhankelijk van de typen gegevensbronnen en -putten.

Voor Kopieeractiviteit, wanneer de bron van type **RelationalSource** is (dat DB2 bevat), zijn de volgende eigenschappen beschikbaar in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| **Query** |Gebruik de aangepaste query om de gegevens te lezen. |SQL-querytekenreeks. Bijvoorbeeld: `"query": "select * from "MySchema"."MyTable""` |Nee (als de eigenschap **tableName** van een gegevensset is opgegeven) |

> [!NOTE]
> Schema- en tabelnamen zijn hoofdlettergevoelig. Sluit in de queryinstructie eigenschapsnamen in met behulp van '' (dubbele aanhalingstekens).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>JSON-voorbeeld: gegevens van DB2 kopiëren naar Azure Blob-opslag
In dit voorbeeld worden voorbeeld-JSON-definities gegeven die u gebruiken om een pijplijn te maken met behulp van Visual [Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). In het voorbeeld ziet u hoe u gegevens uit een DB2-database kopiëren naar Blob-opslag. Gegevens kunnen echter worden gekopieerd naar [elk ondersteund gegevensarchiefsinktype](data-factory-data-movement-activities.md#supported-data-stores-and-formats) met azure datafactorykopieeractiviteit.

De steekproef heeft de volgende gegevensfabriekentiteiten:

- Een DB2-gekoppelde service van het type [OnPremisesDb2](data-factory-onprem-db2-connector.md)
- Een Azure Blob-opslaggekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Een [invoergegevensset](data-factory-create-datasets.md) van type [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- Een [pijplijn](data-factory-create-pipelines.md) met een kopieeractiviteit die de eigenschappen [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) gebruikt

Het voorbeeld kopieert gegevens uit een queryresultaat in een DB2-database naar een Azure-blob per uur. De JSON-eigenschappen die in het monster worden gebruikt, worden beschreven in de secties die de entiteitsdefinities volgen.

Installeer en configureer als eerste stap een datagateway. Instructies staan in de [gegevens verplaatsen tussen on-premises locaties en cloudartikelen.](data-factory-move-data-between-onprem-and-cloud.md)

**DB2 gekoppelde service**

```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
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

**Gekoppelde Azure Blob-opslagservice**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**DB2-invoergegevensset**

In het voorbeeld wordt ervan uitgegaan dat u een tabel in DB2 hebt gemaakt met de naam 'MyTable' met een kolom met het label 'tijdstempel' voor de tijdreeksgegevens.

Het **externe** pand is ingesteld op 'true'. Met deze instelling wordt de datafabriekservice geïnformeerd dat deze gegevensset buiten de gegevensfabriek staat en niet wordt geproduceerd door een activiteit in de gegevensfabriek. De **eigenschap type** is ingesteld op **RelationalTable**.


```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
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

**Azure Blob-uitvoergegevensset**

Gegevens worden elk uur naar een nieuwe blob geschreven door de **eigenschap frequency-eigenschap** in te stellen op 'Uur' en de eigenschap **interval** op 1. De eigenschap **folderPath** voor de blob wordt dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt de delen van de begintijd van jaar, maand, dag en uur.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pijplijn voor de kopieeractiviteit**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om opgegeven invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie voor de pijplijn wordt het **brontype** ingesteld op **RelationalSource** en wordt het **gootsteentype** ingesteld op **BlobSink**. De SQL-query die is opgegeven voor de **eigenschap query** selecteert de gegevens in de tabel Orders.

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
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
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-db2"></a>Typetoewijzing voor DB2
Zoals vermeld in het artikel [gegevensverplaatsingsactiviteiten](data-factory-data-movement-activities.md) voert Kopieeractiviteit automatische typeconversies uit van brontype naar gootsteentype met behulp van de volgende benadering in twee stappen:

1. Converteren van een native brontype naar een .NET-type
2. Converteren van een .NET-type naar een native gootsteentype

De volgende toewijzingen worden gebruikt wanneer Kopieeractiviteit de gegevens converteert van een DB2-type naar een .NET-type:

| DB2-databasetype | .NET Framework type |
| --- | --- |
| Smallint |Int16 |
| Geheel getal |Int32 |
| Bigint |Int64 |
| Echte |Enkel |
| Double |Double |
| Drijvend |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numeriek |Decimal |
| Date |DateTime |
| Time |TimeSpan |
| Tijdstempel |DateTime |
| Xml |Byte |
| Char |Tekenreeks |
| Varchar |Tekenreeks |
| Longvarchar Longvarchar |Tekenreeks |
| DB2DynArray DB2DynArray |Tekenreeks |
| Binair |Byte |
| VarBinary (VarBinary) |Byte |
| Longvarbinary (Longvarbinary) |Byte |
| Grafische |Tekenreeks |
| VarGrafisch |Tekenreeks |
| Longvargrafisch |Tekenreeks |
| Clob Clob |Tekenreeks |
| Blob |Byte |
| DbClob DbClob |Tekenreeks |
| Smallint |Int16 |
| Geheel getal |Int32 |
| Bigint |Int64 |
| Echte |Enkel |
| Double |Double |
| Drijvend |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numeriek |Decimal |
| Date |DateTime |
| Time |TimeSpan |
| Tijdstempel |DateTime |
| Xml |Byte |
| Char |Tekenreeks |

## <a name="map-source-to-sink-columns"></a>Kaartbron om kolommen te laten zinken
Zie Kolommen van [gegevenssetsin Azure Data Factory](data-factory-map-columns.md)toewijzen voor meer informatie over het toewijzen van kolommen in de brongegevensset aan kolommen in de sink-gegevensset.

## <a name="repeatable-reads-from-relational-sources"></a>Herhaalbare leest uit relationele bronnen
Wanneer u gegevens uit een relationeel gegevensarchief kopieert, moet u rekening houden met herhaalbaarheid om onbedoelde resultaten te voorkomen. In Azure Data Factory u een segment handmatig opnieuw uitvoeren. U ook de **eigenschap** Beleid opnieuw proberen voor een gegevensset configureren om een segment opnieuw uit te voeren wanneer er een fout optreedt. Zorg ervoor dat dezelfde gegevens worden gelezen, ongeacht hoe vaak het segment opnieuw wordt uitgevoerd en ongeacht hoe u het segment opnieuw uitvoert. Zie [Herhaalbare leest uit relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)voor meer informatie.

## <a name="performance-and-tuning"></a>Prestaties en afstemmen
Meer informatie over belangrijke factoren die van invloed zijn op de prestaties van kopieeractiviteit en manieren om de prestaties te optimaliseren in de [handleiding voor activiteitsprestaties en -afstemming](data-factory-copy-activity-performance.md)kopiëren .
