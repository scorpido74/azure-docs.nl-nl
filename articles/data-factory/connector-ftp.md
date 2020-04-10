---
title: Gegevens van een FTP-server kopiëren met Azure Data Factory
description: Meer informatie over het kopiëren van gegevens van een FTP-server naar een ondersteund sinkdataarchief met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/02/2020
ms.author: jingwang
ms.openlocfilehash: fc81e66e609400c6558f00ee957ccaee715bd7fa
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991634"
---
# <a name="copy-data-from-ftp-server-by-using-azure-data-factory"></a>Gegevens van FTP-server kopiëren met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
>
> * [Versie 1](v1/data-factory-ftp-connector.md)
> * [Huidige versie](connector-ftp.md)

In dit artikel wordt beschreven hoe u gegevens van de FTP-server kopiëren. Lees het [inleidende artikel](introduction.md)voor meer informatie over Azure Data Factory.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze FTP-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Activiteit Metagegevens](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)

Deze FTP-connector ondersteunt met name:

- Bestanden kopiëren met **basis-** of **anonieme** verificatie.
- Bestanden kopiëren as-is of ontleedbestanden met de [ondersteunde bestandsindelingen en compressiecodecs.](supported-file-formats-and-compression-codecs.md)

De FTP-connector ondersteunt FTP-server die in passieve modus wordt uitgevoerd. De actieve modus wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabriek die specifiek zijn voor FTP te definiëren.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor FTP-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **FtpServer**. | Ja |
| host | Geef de naam of het IP-adres van de FTP-server op. | Ja |
| poort | Geef de poort op waarop de FTP-server luistert.<br/>Toegestane waarden zijn: geheel getal, standaardwaarde is **21**. | Nee |
| inschakelenSsl | Geef op of FTP via een SSL/TLS-kanaal moet worden gebruikt.<br/>Toegestane waarden zijn: **true** (default), **false**. | Nee |
| enableServerCertificateValidation | Geef op of u serverTLS/SSL-certificaatvalidatie wilt inschakelen wanneer u FTP via SSL/TLS-kanaal gebruikt.<br/>Toegestane waarden zijn: **true** (default), **false**. | Nee |
| authenticationType | Geef het verificatietype op.<br/>Toegestane waarden zijn: **Basic,** **Anonymous** | Ja |
| userName | Geef de gebruiker op die toegang heeft tot de FTP-server. | Nee |
| wachtwoord | Geef het wachtwoord voor de gebruiker op (userName). Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Nee |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Meer informatie van de sectie [Voorwaarden.](#prerequisites) Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

>[!NOTE]
>De FTP-connector ondersteunt toegang tot FTP-server zonder versleuteling of expliciete SSL/TLS-versleuteling. het ondersteunt geen impliciete SSL / TLS-versleuteling.

**Voorbeeld 1: anonieme verificatie gebruiken**

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "<ftp server>",
            "port": 21,
            "enableSsl": true,
            "enableServerCertificateValidation": true,
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voorbeeld 2: basisverificatie gebruiken**

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "<ftp server>",
            "port": 21,
            "enableSsl": true,
            "enableServerCertificateValidation": true,
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [Gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund `location` voor FTP onder instellingen in op indeling gebaseerde gegevensset:

| Eigenschap   | Beschrijving                                                  | Vereist |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | De eigenschap `location` type onder in de gegevensset moet zijn ingesteld op **FtpServerLocatie**. | Ja      |
| folderPath | Het pad naar de map. Als u wildcard wilt gebruiken om de map te filteren, slaat u deze instelling over en geeft u op in de instellingen van de activiteitsbron. | Nee       |
| fileName   | De bestandsnaam onder de opgegeven mapPath. Als u wildcard wilt gebruiken om bestanden te filteren, slaat u deze instelling over en geeft u op in de instellingen van de activiteitsbron. | Nee       |

**Voorbeeld:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<FTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FtpServerLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door FTP-bron.

### <a name="ftp-as-source"></a>FTP als bron

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund `storeSettings` voor FTP onder instellingen in op indeling gebaseerde kopieerbron:

| Eigenschap                 | Beschrijving                                                  | Vereist                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | De eigenschap `storeSettings` type onder moet zijn ingesteld op **FtpReadSettings**. | Ja                                           |
| Recursieve                | Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. Houd er rekening mee dat wanneer recursieve is ingesteld op true en de gootsteen een op bestanden gebaseerd e-bestand is, een lege map of submap niet wordt gekopieerd of gemaakt aan de gootsteen. Toegestane waarden zijn **waar** (standaard) en **onwaar.** | Nee                                            |
| wildcardFolderPath       | Het mappad met jokertekens om bronmappen te filteren. <br>Toegestane wildcards zijn: `*` (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken); gebruiken `^` om te ontsnappen als uw werkelijke map naam wildcard of deze ontsnapping char binnen heeft. <br>Zie meer voorbeelden in [voorbeelden van mappen en bestandenfilters](#folder-and-file-filter-examples). | Nee                                            |
| wildcardFileName         | De bestandsnaam met jokertekens onder de opgegeven mapPath/wildcardFolderPath om bronbestanden te filteren. <br>Toegestane wildcards zijn: `*` (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken); gebruiken `^` om te ontsnappen als uw werkelijke map naam wildcard of deze ontsnapping char binnen heeft.  Zie meer voorbeelden in [voorbeelden van mappen en bestandenfilters](#folder-and-file-filter-examples). | Ja `fileName` als deze niet is opgegeven in de gegevensset |
| gebruikBinaryTransfer | Geef op of u de binaire overdrachtsmodus wilt gebruiken. De waarden gelden voor de binaire modus (standaard) en false voor ASCII. |Nee |
| maxConcurrentVerbindingen | Het aantal verbindingen dat gelijktijdig verbinding maakt met het gegevensarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "FtpReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Voorbeelden van mappen en bestandsfilters

In deze sectie wordt het resulterende gedrag van het mappad en de bestandsnaam met wildcardfilters beschreven.

| folderPath | fileName | Recursieve | Structuur en filterresultaat voor bronmappen **(vetgedrukte** bestanden worden opgehaald)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (leeg, standaard gebruiken) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (leeg, standaard gebruiken) | waar | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | waar | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Activiteitseigenschappen getMetadata

Voor meer informatie over de eigenschappen, controleert [GetMetadata-activiteit](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Activiteitseigenschappen verwijderen

Voor meer informatie over de eigenschappen schakelt u [Activiteit verwijderen](delete-activity.md)

## <a name="legacy-models"></a>Verouderde modellen

>[!NOTE]
>De volgende modellen worden nog steeds ondersteund als-is voor achterwaartse compatibiliteit. U wordt voorgesteld om het nieuwe model te gebruiken dat in bovengenoemde secties wordt vermeld die vooruit gaan, en ADF authoring UI is op het produceren van het nieuwe model overgestapt.

### <a name="legacy-dataset-model"></a>Verouderd gegevenssetmodel

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **FileShare** |Ja |
| folderPath | Pad naar de map. Wildcard filter wordt ondersteund, toegestane wildcards zijn: `*` (komt `?` overeen met nul of meer tekens) en (komt overeen met nul of een enkel teken); gebruiken `^` om te ontsnappen als uw werkelijke map naam wildcard of deze ontsnapping char binnen heeft. <br/><br/>Voorbeelden: rootmap/submap/, zie meer voorbeelden in [voorbeelden van mappen en bestandsfilters](#folder-and-file-filter-examples). |Ja |
| fileName | **Naam- of wildcardfilter** voor het bestand(en) onder de opgegeven 'mapPath'. Als u geen waarde voor deze eigenschap opgeeft, verwijst de gegevensset naar alle bestanden in de map. <br/><br/>Voor filter zijn toegestane jokertekens: `*` (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of één teken).<br/>- Voorbeeld 1:`"fileName": "*.csv"`<br/>- Voorbeeld 2:`"fileName": "???20180427.txt"`<br/>Gebruik `^` om te ontsnappen als uw werkelijke bestandsnaam wildcard of deze ontsnapping char binnen heeft. |Nee |
| formaat | Als u **bestanden** wilt kopiëren tussen bestandsopslag (binaire kopie), slaat u de sectie opmaak over in definities van zowel invoer- als uitvoergegevenssets.<br/><br/>Als u bestanden met een specifieke indeling wilt ontleden, worden de volgende bestandsindelingstypen ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **eigenschap type** onder opmaak in op een van deze waarden. Zie [Secties Tekstformaat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Json Format](supported-file-formats-and-compression-codecs-legacy.md#json-format), Avro [Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)en [Parquet Format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) voor meer informatie. |Nee (alleen voor binaire kopie scenario) |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Zie [Ondersteunde bestandsindelingen en compressiecodecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)voor meer informatie.<br/>Ondersteunde typen zijn: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**.<br/>Ondersteunde niveaus zijn: **Optimaal** en **snelste**. |Nee |
| gebruikBinaryTransfer | Geef op of u de binaire overdrachtsmodus wilt gebruiken. De waarden gelden voor de binaire modus (standaard) en false voor ASCII. |Nee |

>[!TIP]
>Als u alle bestanden onder een map wilt kopiëren, geeft u **alleen mapPath** op.<br>Als u één bestand met een bepaalde naam wilt kopiëren, geeft u **mapPath** op met maponderdeel en **bestandsnaam.**<br>Als u een subset van bestanden onder een map wilt kopiëren, geeft u **mapPath** op met maponderdeel en **bestandsnaam** met wildcardfilter.

>[!NOTE]
>Als u de eigenschap "fileFilter" gebruikt voor bestandsfilter, wordt deze nog steeds ondersteund als is, terwijl u wordt voorgesteld om de nieuwe filtermogelijkheid te gebruiken die wordt toegevoegd aan "fileName" in de toekomst.

**Voorbeeld:**

```json
{
    "name": "FTPDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<FTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Bronmodel voor verouderde kopieeractiviteit

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van kopieeractiviteit moet zijn ingesteld op: **FileSystemSource** |Ja |
| Recursieve | Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. Opmerking wanneer recursieve is ingesteld op true en sink is file-based store, lege map / sub-map zal niet worden gekopieerd / gemaakt op gootsteen.<br/>Toegestane waarden zijn: **true** (default), **false** | Nee |
| maxConcurrentVerbindingen | Het aantal verbindingen dat tegelijkertijd verbinding moet maken met het opslagarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<FTP input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
