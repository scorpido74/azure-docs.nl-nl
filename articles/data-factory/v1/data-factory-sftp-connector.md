---
title: Gegevens van SFTP-server verplaatsen met Azure Data Factory
description: Meer informatie over het verplaatsen van gegevens van een on-premises of een cloud SFTP-server met Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3f78934fb11dd4f9e34bf27d565d471d47f250b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265803"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Gegevens van een SFTP-server verplaatsen met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-sftp-connector.md)
> * [Versie 2 (huidige versie)](../connector-sftp.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [SFTPconnector in V2](../connector-sftp.md)als u de huidige versie van de datafabriekservice gebruikt.

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens van een on-premises/cloud-SFTP-server naar een ondersteund sink-gegevensarchief te verplaatsen. Dit artikel bouwt voort op het artikel over [gegevensverplaatsingsactiviteiten](data-factory-data-movement-activities.md) dat een algemeen overzicht geeft van gegevensverplaatsing met kopieeractiviteit en de lijst met gegevensarchieven die worden ondersteund als bronnen/putten.

Datafactory ondersteunt momenteel alleen het verplaatsen van gegevens van een SFTP-server naar andere gegevensopslag, maar niet voor het verplaatsen van gegevens van andere gegevensopslag naar een SFTP-server. Het ondersteunt zowel on-premises als cloud SFTP-servers.

> [!NOTE]
> Copy Activity verwijdert het bronbestand niet nadat het naar de bestemming is gekopieerd. Als u het bronbestand na een geslaagde kopie moet verwijderen, maakt u een aangepaste activiteit om het bestand te verwijderen en de activiteit in de pijplijn te gebruiken.

## <a name="supported-scenarios-and-authentication-types"></a>Ondersteunde scenario's en verificatietypen
U deze SFTP-connector gebruiken om gegevens te kopiëren van **zowel cloud SFTP-servers als on-premises SFTP-servers.** **Basis-** en **SshPublicKey-verificatietypen** worden ondersteund wanneer ze verbinding maken met de SFTP-server.

Wanneer u gegevens kopieert vanaf een on-premises SFTP-server, moet u een Data Management Gateway installeren in de on-premises omgeving/Azure VM. Zie [Data Management Gateway](data-factory-data-management-gateway.md) voor meer informatie over de gateway. Zie [gegevens verplaatsen tussen on-premises locaties en cloudartikelen](data-factory-move-data-between-onprem-and-cloud.md) voor stapsgewijze instructies over het instellen en gebruiken ervan.

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens van een SFTP-bron verplaatst met behulp van verschillende hulpprogramma's/API's.

- De eenvoudigste manier om een pijplijn te maken, is door de **wizard Kopiëren**te gebruiken. Zie [Zelfstudie: Maak een pijplijn met wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough voor het maken van een pijplijn met de wizard Gegevens kopiëren.

- U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-sjabloon,** **.NET API**en REST **API**. Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken. Zie [JSON Example: Gegevens kopiëren van SFTP-server naar Azure Blob-opslag voor](#json-example-copy-data-from-sftp-server-to-azure-blob) JSON-voorbeelden om gegevens van SFTP-server naar het gedeelte van de Azure-blob van dit artikel te kopiëren.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor ftp-gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type | De eigenschap type moet `Sftp`zijn ingesteld op . |Ja |
| host | Naam of IP-adres van de SFTP-server. |Ja |
| poort |Poort waarop de SFTP-server luistert. De standaardwaarde is: 21 |Nee |
| authenticationType |Geef verificatietype op. Toegestane waarden: **Basic**, **SshPublicKey**. <br><br> Raadpleeg het [gebruik van basisverificatie](#using-basic-authentication) [en het gebruik van ssh-verificatiesecties voor openbare sleutels](#using-ssh-public-key-authentication) op respectievelijk meer eigenschappen en JSON-voorbeelden. |Ja |
| skipHostKeyValidation | Geef op of u de validatie van hostsleutels wilt overslaan. | Nee. De standaardwaarde: false |
| hostKeyFingerprint | Geef de vingerafdruk van de hosttoets op. | Ja als `skipHostKeyValidation` het is ingesteld op vals.  |
| gatewayNaam |Naam van de Data Management Gateway om verbinding te maken met een on-premises SFTP-server. | Ja als u gegevens kopieert van een on-premises SFTP-server. |
| versleuteldCredential | Versleutelde referenties voor toegang tot de SFTP-server. Automatisch gegenereerd wanneer u basisverificatie (gebruikersnaam + wachtwoord) of SshPublicKey-verificatie (gebruikersnaam + privésleutelpad of -inhoud) opgeeft in de wizard Copy of in het pop-updialoogvenster ClickOnce. | Nee. Alleen toepassen bij het kopiëren van gegevens vanaf een on-premises SFTP-server. |

### <a name="using-basic-authentication"></a>Basisverificatie gebruiken

Als u basisverificatie `authenticationType` `Basic`wilt gebruiken, stelt u de volgende eigenschappen in naast de algemene eigenschappen van de SFTP-connector die in de laatste sectie zijn geïntroduceerd:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| gebruikersnaam | Gebruiker die toegang heeft tot de SFTP-server. |Ja |
| wachtwoord | Wachtwoord voor de gebruiker (gebruikersnaam). | Ja |

#### <a name="example-basic-authentication"></a>Voorbeeld: Basisverificatie
```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Voorbeeld: Basisverificatie met versleutelde referenties

```JSON
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
      }
}
```

### <a name="using-ssh-public-key-authentication"></a>Verificatie van SSH-public key gebruiken

Als u SSH-verificatie `authenticationType` met `SshPublicKey`openbare sleutels wilt gebruiken, stelt u de volgende eigenschappen in en geeft u de volgende eigenschappen op naast de algemene eigenschappen van de SFTP-connector die in de laatste sectie zijn geïntroduceerd:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| gebruikersnaam |Gebruiker die toegang heeft tot de SFTP-server |Ja |
| privateKeyPath | Geef een absoluut pad op naar het privésleutelbestand waartoe gateway toegang heeft. | Geef de `privateKeyPath` `privateKeyContent`or op . <br><br> Alleen toepassen bij het kopiëren van gegevens vanaf een on-premises SFTP-server. |
| privateKeyContent | Een geserialiseerde reeks van de persoonlijke sleutelinhoud. De wizard Kopiëren kan het privésleutelbestand lezen en de inhoud van de privésleutel automatisch extraheren. Als u een ander gereedschap/SDK gebruikt, gebruikt u in plaats daarvan de eigenschap privateKeyPath. | Geef de `privateKeyPath` `privateKeyContent`or op . |
| Passphrase | Geef de wachtwoordzin/het wachtwoord op om de privésleutel te decoderen als het sleutelbestand wordt beschermd door een wachtwoordzin. | Ja als het privésleutelbestand wordt beschermd door een wachtwoordzin. |

> [!NOTE]
> SFTP-connector ondersteunt RSA/DSA OpenSSH-toets. Zorg ervoor dat de inhoud van uw sleutelbestand begint met '-----BEGIN [RSA/DSA] PRIVATE KEY-----". Als het bestand met de privésleutel een ppk-formaatbestand is, gebruikt u het gereedschap Putty om te converteren van .ppk naar OpenSSH-indeling.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Voorbeeld: SshPublicKey-verificatie met behulp van bestanden met privésleutelPath

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Voorbeeld: SshPublicKey-verificatie met behulp van inhoud met privésleutels

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties zoals structuur, beschikbaarheid en beleid van een gegevensset JSON zijn vergelijkbaar voor alle gegevenssettypen.

De sectie **typeEigenschappen** is verschillend voor elk type gegevensset. Het biedt informatie die specifiek is voor het type gegevensset. De sectie typeEigenschappen voor een gegevensset van de gegevensset **BestandShare** van het type Heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Subpad naar de map. Gebruik escape character ' \ ' voor speciale tekens in de tekenreeks. Zie Voorbeeld definities van gekoppelde service en gegevenssets voor voorbeelden.<br/><br/>U deze eigenschap combineren met **partitieDoor** mappaden te hebben op basis van begin- en einddatums van segmenten. |Ja |
| fileName |Geef de naam op van het bestand in de **mapPath** als u wilt dat de tabel verwijst naar een specifiek bestand in de map. Als u geen waarde voor deze eigenschap opgeeft, verwijst de tabel naar alle bestanden in de map.<br/><br/>Wanneer bestandsnaam niet is opgegeven voor een uitvoergegevensset, wordt de naam van het gegenereerde bestand in de volgende deze indeling weergegeven: <br/><br/>`Data.<Guid>.txt`(Voorbeeld: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nee |
| fileFilter |Geef een filter op dat moet worden gebruikt om een subset van bestanden in de mapPath te selecteren in plaats van alle bestanden.<br/><br/>Toegestane waarden zijn: `*` (meerdere `?` tekens) en (enkel teken).<br/><br/>Voorbeelden 1:`"fileFilter": "*.log"`<br/>Voorbeeld 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter is van toepassing op een bestandssetsetvan invoer. Deze eigenschap wordt niet ondersteund met HDFS. |Nee |
| partitionedBy |partitionedBy kan worden gebruikt om een dynamische mapPath op te geven, bestandsnaam voor tijdreeksgegevens. Bijvoorbeeld, mapPath parameterized voor elk uur van de gegevens. |Nee |
| formaat | De volgende formattypes worden ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **eigenschap type** onder opmaak in op een van deze waarden. Zie [Secties Tekstformaat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), Avro [Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)en [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) voor meer informatie. <br><br> Als u **bestanden** wilt kopiëren tussen bestandsopslag (binaire kopie), slaat u de sectie opmaak over in definities van zowel invoer- als uitvoergegevenssets. |Nee |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Ondersteunde typen zijn: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**. Ondersteunde niveaus zijn: **Optimaal** en **snelste**. Zie [Bestands- en compressieindelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |Nee |
| gebruikBinaryTransfer |Geef op of u de binaire overdrachtsmodus gebruikt. Geldt voor binaire modus en valse ASCII. Standaardwaarde: True. Deze eigenschap kan alleen worden gebruikt wanneer gekoppeld gekoppeld servicetype van type is: FtpServer. |Nee |

> [!NOTE]
> bestandsnaam en fileFilter kunnen niet tegelijkertijd worden gebruikt.

### <a name="using-partionedby-property"></a>PartionedBy gebruiken, eigenschap
Zoals in de vorige sectie is vermeld, u een dynamische mapPath opgeven, bestandsnaam voor tijdreeksgegevens met partitionedBy. U dit doen met de macro's Gegevensfabriek en de systeemvariabele SliceStart, SliceEnd die de logische periode voor een bepaald gegevenssegment aangeven.

Zie [Gegevenssets maken,](data-factory-create-datasets.md) [plannen & uitvoeren](data-factory-scheduling-and-execution.md)en Pijplijnen maken voor meer informatie over gegevenssets voor tijdreeksen, planning en segmenten. [Creating Pipelines](data-factory-create-pipelines.md)

#### <a name="sample-1"></a>Voorbeeld 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
In dit voorbeeld wordt {Slice} vervangen door de waarde van Data Factory-systeemvariabele SliceStart in de opgegeven indeling (YYYYMMDDHH). De SliceStart verwijst naar de begintijd van het segment. De mapPath is verschillend voor elk segment. Voorbeeld: wikidatagateway/wikisampledataout/2014100103 of wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Voorbeeld 2:

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
In dit voorbeeld worden jaar, maand, dag en tijd van SliceStart geëxtraheerd in afzonderlijke variabelen die worden gebruikt door mapPath- en fileName-eigenschappen.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [Pijplijnmaken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen en beleidsregels zijn beschikbaar voor alle soorten activiteiten.

Overwegende dat de eigenschappen die beschikbaar zijn in de sectie typeEigenschappen van de activiteit per activiteitstype verschillen. Voor Kopieeractiviteit zijn de typeeigenschappen afhankelijk van de typen bronnen en putten.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Ondersteunde indelingen voor bestanden en compressie
Zie [Bestands- en compressie-indelingen in](data-factory-supported-file-and-compression-formats.md) het artikel van Azure Data Factory over details.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>JSON-voorbeeld: gegevens van SFTP-server kopiëren naar Azure-blob
In het volgende voorbeeld vindt u voorbeeld-JSON-definities die u gebruiken om een pijplijn te maken met Behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Ze laten zien hoe u gegevens van SFTP-bron naar Azure Blob Storage kopieert. Gegevens kunnen echter **rechtstreeks** worden gekopieerd van een van de bronnen naar een van de putten die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) zijn vermeld met behulp van de kopieeractiviteit in Azure Data Factory.

> [!IMPORTANT]
> Dit voorbeeld bevat JSON-fragmenten. Het bevat geen stapsgewijze instructies voor het maken van de gegevensfabriek. Zie [gegevens verplaatsen tussen on-premises locaties en cloudartikelen](data-factory-move-data-between-onprem-and-cloud.md) voor stapsgewijze instructies.

Het voorbeeld heeft de volgende gegevensfabriekentiteiten:

* Een gekoppelde service van type [sftp](#linked-service-properties).
* Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Een [invoergegevensset](data-factory-create-datasets.md) van het type [FileShare](#dataset-properties).
* Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Een [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [Bestandssysteembron](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)gebruikt.

Het voorbeeld kopieert elk uur gegevens van een SFTP-server naar een Azure-blob. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

**Gekoppelde SFTP-service**

In dit voorbeeld wordt de basisverificatie met gebruikersnaam en wachtwoord in platte tekst gebruikt. U ook een van de volgende manieren gebruiken:

* Basisverificatie met versleutelde referenties
* SSH-verificatie met openbare sleutels

Zie [ftp-gekoppelde servicesectie](#linked-service-properties) voor verschillende soorten verificatie die u gebruiken.

```JSON

{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "myuser",
            "password": "mypassword",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```
**Gekoppelde Azure Storage-service**

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
**SFTP-invoergegevensset**

Deze gegevensset verwijst naar `mysharedfolder` de `test.csv`SFTP-map en -bestand . De pijplijn kopieert het bestand naar de bestemming.

Als u "extern" instelt: "true" informeert de datafabriekservice dat de gegevensset zich buiten de gegevensfabriek bevindt en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```JSON
{
  "name": "SFTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "SftpLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Azure Blob-uitvoergegevensset**

Gegevens worden elk uur naar een nieuwe blob geschreven (frequentie: uur, interval: 1). Het mappad voor de blob wordt dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van de begintijd van jaar, maand, dag en uur.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pijplijn met kopieeractiviteit**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn is het **brontype** ingesteld op **FileSystemSource** en is **het sinktype** ingesteld op **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
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
        "start": "2017-02-20T18:00:00Z",
        "end": "2017-02-20T19:00:00Z"
    }
}
```

## <a name="performance-and-tuning"></a>Prestaties en tuning
Zie [Handleiding activiteitsprestaties kopiëren & tuningom](data-factory-copy-activity-performance.md) meer te weten te komen over de belangrijkste factoren die van invloed zijn op de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory en op verschillende manieren om deze te optimaliseren.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

* [Zelfstudie Activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijplijn met een kopieeractiviteit.
