---
title: Gegevens van een FTP-server verplaatsen met Azure Data Factory
description: Meer informatie over het verplaatsen van gegevens van een FTP-server met Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: eeeb122d240d8c3eae4ebe1650f67cf0e4b9dac6
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992042"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Gegevens van een FTP-server verplaatsen met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-ftp-connector.md)
> * [Versie 2 (huidige versie)](../connector-ftp.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [FTP-connector in V2](../connector-ftp.md)als u de huidige versie van de datafabriekservice gebruikt.

In dit artikel wordt uitgelegd hoe u de kopieeractiviteit in Azure Data Factory gebruiken om gegevens van een FTP-server te verplaatsen. Het bouwt voort op het artikel van de activiteiten van de [gegevensbeweging,](data-factory-data-movement-activities.md) wat een algemeen overzicht van gegevensbeweging met de exemplaaractiviteit voorstelt.

U gegevens van een FTP-server kopiëren naar een ondersteund sinkdataarchief. Zie de tabel [ondersteunde gegevensopslag](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die als sinks wordt ondersteund door de kopieeractiviteit. Data Factory ondersteunt momenteel alleen het verplaatsen van gegevens van een FTP-server naar andere gegevensopslag, maar niet het verplaatsen van gegevens van andere gegevensopslag naar een FTP-server. Het ondersteunt zowel on-premises als cloud FTP-servers.

> [!NOTE]
> De kopieeractiviteit verwijdert het bronbestand niet nadat het naar de bestemming is gekopieerd. Als u het bronbestand na een geslaagde kopie moet verwijderen, maakt u een aangepaste activiteit om het bestand te verwijderen en gebruikt u de activiteit in de pijplijn.

## <a name="enable-connectivity"></a>Connectiviteit inschakelen
Als u gegevens verplaatst van een **on-premises FTP-server** naar een cloudgegevensarchief (bijvoorbeeld naar Azure Blob-opslag), installeert en gebruikt u Data Management Gateway. De Data Management Gateway is een clientagent die is geïnstalleerd op uw on-premises machine en waarmee cloudservices verbinding kunnen maken met een on-premises resource. Zie Data [Management Gateway voor](data-factory-data-management-gateway.md)meer informatie. Zie [Gegevens verplaatsen tussen on-premises locaties en cloud](data-factory-move-data-between-onprem-and-cloud.md)voor stapsgewijze instructies voor het instellen en gebruiken ervan. U gebruikt de gateway om verbinding te maken met een FTP-server, zelfs als de server zich op een Azure-infrastructuur as a service (IAAS) virtuele machine (VM) bevindt.

Het is mogelijk om de gateway te installeren op dezelfde on-premises machine of IaaS VM als de FTP-server. We raden u echter aan de gateway op een aparte machine of IaaS VM te installeren om bronconflicten en betere prestaties te voorkomen. Wanneer u de gateway op een aparte machine installeert, moet de machine toegang hebben tot de FTP-server.

## <a name="get-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens van een FTP-bron verplaatst met behulp van verschillende hulpprogramma's of API's.

De eenvoudigste manier om een pijplijn te maken, is door de **wizard Gegevensfabriekkopiëren**te gebruiken. Zie [Zelfstudie: Maak een pijplijn met wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough.

U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **PowerShell,** **Azure Resource Manager-sjabloon**, **.NET API**en REST **API**. Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken.

Of u de hulpprogramma's of API's gebruikt, voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek.
2. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven.
3. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt.

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u hulpprogramma's of API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling. Zie het [VOORBEELD JSON: Gegevens kopiëren van FTP-server naar Azure blob-gedeelte](#json-example-copy-data-from-ftp-server-to-azure-blob) van dit artikel voor een voorbeeld met JSON-definities voor entiteiten in gegevensfabriek die worden gebruikt om gegevens uit een FTP-gegevensarchief te kopiëren.

> [!NOTE]
> Zie [Bestands- en compressieindelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md)voor meer informatie over ondersteunde bestands- en compressieindelingen.

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor FTP.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
In de volgende tabel worden JSON-elementen beschreven die specifiek zijn voor een FTP-gekoppelde service.

| Eigenschap | Beschrijving | Vereist | Standaard |
| --- | --- | --- | --- |
| type |Stel dit in op FtpServer. |Ja |&nbsp; |
| host |Geef de naam of het IP-adres van de FTP-server op. |Ja |&nbsp; |
| authenticationType |Geef het verificatietype op. |Ja |Basic, Anoniem |
| gebruikersnaam |Geef de gebruiker op die toegang heeft tot de FTP-server. |Nee |&nbsp; |
| wachtwoord |Geef het wachtwoord voor de gebruiker op (gebruikersnaam). |Nee |&nbsp; |
| versleuteldCredential |Geef de versleutelde referenties op om toegang te krijgen tot de FTP-server. |Nee |&nbsp; |
| gatewayNaam |Geef de naam op van de gateway in Data Management Gateway om verbinding te maken met een on-premises FTP-server. |Nee |&nbsp; |
| poort |Geef de poort op waarop de FTP-server luistert. |Nee |21 |
| inschakelenSsl |Geef op of FTP via een SSL/TLS-kanaal moet worden gebruikt. |Nee |waar |
| enableServerCertificateValidation |Geef op of u serverTLS/SSL-certificaatvalidatie wilt inschakelen wanneer u FTP via SSL/TLS-kanaal gebruikt. |Nee |waar |

>[!NOTE]
>De FTP-connector ondersteunt toegang tot FTP-server zonder versleuteling of expliciete SSL/TLS-versleuteling. het ondersteunt geen impliciete SSL / TLS-versleuteling.

### <a name="use-anonymous-authentication"></a>Anonieme verificatie gebruiken

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Gebruikersnaam en wachtwoord gebruiken in platte tekst voor basisverificatie

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Gebruik poort, enableSsl, enableServerCertificateValidation

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Versleutelde Credential gebruiken voor verificatie en gateway

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie [Gegevenssets maken](data-factory-create-datasets.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties zoals structuur, beschikbaarheid en beleid van een gegevensset JSON zijn vergelijkbaar voor alle gegevenssettypen.

De sectie **typeEigenschappen** is verschillend voor elk type gegevensset. Het biedt informatie die specifiek is voor het type gegevensset. De sectie **typeEigenschappen** voor een gegevensset van type **FileShare** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Subpad naar de map. Gebruik escape character ' \ ' voor speciale tekens in de tekenreeks. Zie Voorbeeld definities van gekoppelde service en gegevenssets voor voorbeelden.<br/><br/>U deze eigenschap combineren met **partitieDoor** mappaden te hebben op basis van begin- en einddatums van segmenten. |Ja |
| fileName |Geef de naam op van het bestand in de **mapPath** als u wilt dat de tabel verwijst naar een specifiek bestand in de map. Als u geen waarde voor deze eigenschap opgeeft, verwijst de tabel naar alle bestanden in de map.<br/><br/>Wanneer **bestandsnaam** niet is opgegeven voor een uitvoergegevensset, is de naam van het gegenereerde bestand in de volgende indeling: <br/><br/>`Data.<Guid>.txt`(Voorbeeld: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nee |
| fileFilter |Geef een filter op dat moet worden gebruikt om een subset van bestanden in de **mapPath**te selecteren, in plaats van alle bestanden.<br/><br/>Toegestane waarden zijn: `*` (meerdere `?` tekens) en (enkel teken).<br/><br/>Voorbeeld 1:`"fileFilter": "*.log"`<br/>Voorbeeld 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** is van toepassing op een bestandssetsetvan invoer. Deze eigenschap wordt niet ondersteund met Hadoop Distributed File System (HDFS). |Nee |
| partitionedBy |Wordt gebruikt om een dynamische **mapPath** en **fileName** op te geven voor tijdreeksgegevens. U bijvoorbeeld een **mapPath** opgeven die voor elk uur aan gegevens is geparameteriseerd. |Nee |
| formaat | De volgende formattypes worden ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **eigenschap type** onder opmaak in op een van deze waarden. Zie voor meer informatie de secties [Text Format](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), Avro [Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)en [Parquet Format.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Als u bestanden wilt kopiëren zoals ze zijn tussen bestandsgebaseerde opslag (binaire kopie), slaat u de sectie opmaak over in definities van zowel invoer- als uitvoergegevenssets. |Nee |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Ondersteunde typen zijn **GZip,** **Deflate,** **BZip2**en **ZipDeflate,** en ondersteunde niveaus zijn **optimaal** en **snelst.** Zie [Bestands- en compressieindelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |Nee |
| gebruikBinaryTransfer |Geef op of u de binaire overdrachtsmodus wilt gebruiken. De waarden gelden voor de binaire modus (dit is de standaardwaarde) en false voor ASCII. Deze eigenschap kan alleen worden gebruikt wanneer het gekoppelde gekoppelde servicetype van type is: FtpServer. |Nee |

> [!NOTE]
> **fileName** en **fileFilter** kunnen niet tegelijkertijd worden gebruikt.

### <a name="use-the-partionedby-property"></a>De eigenschap partionedBy gebruiken
Zoals in de vorige sectie is vermeld, u een dynamische **mapPath** en **fileName** opgeven voor tijdreeksgegevens met de eigenschap **partitionedBy.**

Zie [Gegevenssets maken,](data-factory-create-datasets.md) [Plannen en uitvoeren en](data-factory-scheduling-and-execution.md)Pijplijnen maken voor meer informatie over gegevenssets voor tijdreeksen, planning en segmenten. [Creating pipelines](data-factory-create-pipelines.md)

#### <a name="sample-1"></a>Voorbeeld 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
In dit voorbeeld wordt {Slice} vervangen door de waarde van Data Factory-systeemvariabele SliceStart, in de opgegeven indeling (YYYYMMDDHH). De SliceStart verwijst naar de begintijd van het segment. Het mappad is voor elk segment anders. (Bijvoorbeeld wikidatagateway/wikisampledataout/2014100103 of wikidatagateway/wikisampledataout/2014100104.)

#### <a name="sample-2"></a>Voorbeeld 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
In dit voorbeeld worden de jaar-, maand-, dag- en tijdvan SliceStart geëxtraheerd in afzonderlijke variabelen die worden gebruikt door de eigenschappen **folderPath** en **fileName.**

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie [Pijplijnen maken](data-factory-create-pipelines.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen en beleidsregels zijn beschikbaar voor alle soorten activiteiten.

Eigenschappen die beschikbaar zijn in de sectie **typeEigenschappen** van de activiteit, daarentegen, variëren per activiteitstype. Voor de kopieeractiviteit zijn de typeeigenschappen afhankelijk van de typen bronnen en putten.

Wanneer de bron van type **FileSystemSource**is, is bij kopieeractiviteit de volgende eigenschap beschikbaar in de sectie **typeProperties:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| Recursieve |Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. |True, False (standaard) |Nee |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>JSON-voorbeeld: gegevens van FTP-server kopiëren naar Azure Blob
In dit voorbeeld ziet u hoe u gegevens van een FTP-server kopieert naar Azure Blob-opslag. Gegevens kunnen echter rechtstreeks worden gekopieerd naar een van de putten die in de [ondersteunde gegevensarchieven en -indelingen](data-factory-data-movement-activities.md#supported-data-stores-and-formats)zijn vermeld, met behulp van de kopieeractiviteit in Data Factory.

In de volgende voorbeelden worden voorbeeld-JSON-definities gegeven die u gebruiken om een pijplijn te maken met [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)of [PowerShell:](data-factory-copy-activity-tutorial-using-powershell.md)

* Een gekoppelde service van het type [FtpServer](#linked-service-properties)
* Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Een [invoergegevensset](data-factory-create-datasets.md) van type [FileShare](#dataset-properties)
* Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Een [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [Bestandssysteembron](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) gebruikt

Het voorbeeld kopieert elk uur gegevens van een FTP-server naar een Azure-blob. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

### <a name="ftp-linked-service"></a>FTP-gekoppelde service

In dit voorbeeld wordt gebruik gemaakt van basisverificatie, waarbij de gebruikersnaam en het wachtwoord in platte tekst worden gebruikt. U ook een van de volgende manieren gebruiken:

* Anonieme verificatie
* Basisverificatie met versleutelde referenties
* FTP via SSL/TLS (FTPS)

Zie de [ftp-gekoppelde servicesectie](#linked-service-properties) voor verschillende soorten verificatie die u gebruiken.

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```
### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service

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
### <a name="ftp-input-dataset"></a>Ftp-invoergegevensset

Deze gegevensset verwijst `mysharedfolder` naar `test.csv`de FTP-map en het bestand . De pijplijn kopieert het bestand naar de bestemming.

Als **u extern** naar true **instelt,** wordt de gegevensfabriek serviceprovider geïnformeerd dat de gegevensset zich buiten de gegevensfabriek bevindt en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

### <a name="azure-blob-output-dataset"></a>Azure Blob-uitvoergegevensset

Gegevens worden elk uur naar een nieuwe blob geschreven (frequentie: uur, interval: 1). Het mappad voor de blob wordt dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt de delen van de begintijd jaar, maand, dag en uur.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Een kopieeractiviteit in een pijplijn met bestandssysteembron en blobsink

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **FileSystemSource**en wordt het **gootsteentype** ingesteld op **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
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
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> Zie Kolommen van [gegevensset toewijzing in Azure Data Factory](data-factory-map-columns.md)als u kolommen wilt toewijzen van brongegevensset naar kolommen uit de sink-gegevensset.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

* Zie de [handleiding Activiteitsprestaties en tuning](data-factory-copy-activity-performance.md)kopiëren voor meer informatie over de prestaties van gegevensverplaatsing (kopieeractiviteit) in Gegevensfabriek en verschillende manieren om deze te optimaliseren.

* Zie de [zelfstudie Activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor stapsgewijze instructies voor het maken van een pijplijn met een kopieeractiviteit.
