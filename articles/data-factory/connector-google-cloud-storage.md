---
title: Gegevens uit Google Cloud Storage kopiëren met Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit Google Cloud Storage naar ondersteunde sink data stores met Azure Data Factory.
services: data-factory
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 2ee0fc7f7b7e3ef465a43eed2bd47f33e87162b7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417306"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Gegevens uit Google Cloud Storage kopiëren met Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u gegevens uit Google Cloud Storage kopiëren. Lees het [inleidende artikel](introduction.md)voor meer informatie over Azure Data Factory.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Google Cloud Storage-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Activiteit Metagegevens](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)

Deze Google Cloud Storage-connector ondersteunt met name het kopiëren van bestanden as-is of ontleedbestanden met de [ondersteunde bestandsindelingen en compressiecodecs.](supported-file-formats-and-compression-codecs.md)

>[!NOTE]
>Het kopiëren van gegevens uit Google Cloud Storage maakt gebruik van de [Amazon S3-connector](connector-amazon-simple-storage-service.md) met bijbehorend aangepast S3-eindpunt, omdat Google Cloud Storage s3-compatibele interoperabiliteit biedt.

## <a name="prerequisites"></a>Vereisten

De volgende set-up is vereist voor uw Google Cloud Storage-account:

1. Interoperabiliteit inschakelen voor uw Google Cloud Storage-account
2. Het standaardproject instellen dat de gegevens bevat die u wilt kopiëren
3. Maak een toegangssleutel.

![Toegangssleutel ophalen voor Google Cloud Storage](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>Vereiste machtigingen

Als u gegevens uit Google Cloud Storage wilt kopiëren, controleert u of u de volgende machtigingen hebt gekregen:

- **Voor de uitvoering van kopieeractiviteiten:**: `s3:GetObject` en `s3:GetObjectVersion` voor Objectbewerkingen.
- **Voor GUI-ontwerpen voor gegevensfabriek:** `s3:ListAllMyBuckets` en `s3:ListBucket` / `s3:GetBucketLocation` voor Bucket Operations zijn bovendien machtigingen vereist voor bewerkingen zoals testverbinding en bladeren/navigeren op bestandspaden. Als u deze machtigingen niet wilt verlenen, slaat u de testverbinding op de pagina Voor het maken van gekoppelde service over en geeft u het pad rechtstreeks op in de gegevenssetinstellingen.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor Google Cloud Storage.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de gekoppelde service van Google Cloud Storage:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op **GoogleCloudStorage**. | Ja |
| accessKeyId accessKeyId | ID van de geheime toegangssleutel. Zie [Voorwaarden](#prerequisites)voor het vinden van de toegangssleutel en het geheim. |Ja |
| secretAccessKey | De geheime toegangssleutel zelf. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) |Ja |
| serviceUrl | Geef het aangepaste S3-eindpunt op als **`https://storage.googleapis.com`**. | Ja |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. U Azure Integration Runtime of Self-hosted Integration Runtime gebruiken (als uw gegevensarchief zich in een privénetwerk bevindt). Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

Hier volgt een voorbeeld:

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "GoogleCloudStorage",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor `location` Google Cloud Storage onder instellingen in op indeling gebaseerde gegevensset:

| Eigenschap   | Beschrijving                                                  | Vereist |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | De eigenschap `location` type onder in de gegevensset moet worden ingesteld op **AmazonS3Location**. | Ja      |
| bucketName | De Naam van de S3-emmer.                                          | Ja      |
| folderPath | Het pad naar de map onder de opgegeven bucket. Als u wildcard wilt gebruiken om de map te filteren, slaat u deze instelling over en geeft u op in de instellingen van de activiteitsbron. | Nee       |
| fileName   | De bestandsnaam onder de opgegeven bucket + folderPath. Als u wildcard wilt gebruiken om bestanden te filteren, slaat u deze instelling over en geeft u op in de instellingen van de activiteitsbron. | Nee       |

**Voorbeeld:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
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

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Bron van Google Cloud-opslag.

### <a name="google-cloud-storage-as-source"></a>Google Cloud Storage als bron

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor `storeSettings` Google Cloud Storage onder instellingen in op indeling gebaseerde kopieerbron:

| Eigenschap                 | Beschrijving                                                  | Vereist                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | De eigenschap `storeSettings` type onder moet zijn ingesteld op **AmazonS3ReadSettings**. | Ja                                                         |
| Recursieve                | Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. Houd er rekening mee dat wanneer recursieve is ingesteld op true en de gootsteen een op bestanden gebaseerd e-bestand is, een lege map of submap niet wordt gekopieerd of gemaakt aan de gootsteen. Toegestane waarden zijn **waar** (standaard) en **onwaar.** | Nee                                                          |
| Voorvoegsel                   | Voorvoegsel voor de S3-objectsleutel onder de opgegeven bucket die is geconfigureerd in de gegevensset om bronobjecten te filteren. Objecten waarvan de toetsen met dit voorvoegsel beginnen, zijn geselecteerd. Geldt alleen `wildcardFolderPath` `wildcardFileName` wanneer en eigenschappen niet zijn opgegeven. |                                                             |
| wildcardFolderPath       | Het mappad met jokertekens onder de opgegeven bucket die is geconfigureerd in de gegevensset om bronmappen te filteren. <br>Toegestane wildcards zijn: `*` (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken); gebruiken `^` om te ontsnappen als uw werkelijke map naam wildcard of deze ontsnapping char binnen heeft. <br>Zie meer voorbeelden in [voorbeelden van mappen en bestandenfilters](#folder-and-file-filter-examples). | Nee                                                          |
| wildcardFileName         | De bestandsnaam met jokertekens onder de opgegeven bucket + mapPath/wildcardFolderPath om bronbestanden te filteren. <br>Toegestane wildcards zijn: `*` (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken); gebruiken `^` om te ontsnappen als uw werkelijke map naam wildcard of deze ontsnapping char binnen heeft.  Zie meer voorbeelden in [voorbeelden van mappen en bestandenfilters](#folder-and-file-filter-examples). | Ja `fileName` als in `prefix` de gegevensset en niet zijn opgegeven |
| gewijzigdDatumtimeStart    | Bestandenfilter op basis van het kenmerk: Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` tijd `modifiedDatetimeEnd`binnen het tijdsbereik tussen en . De tijd wordt toegepast op utc-tijdzone in de indeling "2018-12-01T05:00:00Z". <br> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestandskenmerk wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` de datumtijdwaarde is, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, worden geselecteerd.  Wanneer `modifiedDatetimeEnd` de datumtijdwaarde is, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, worden geselecteerd. | Nee                                                          |
| gewijzigdDatumtimeEnd      | Hetzelfde als hierboven.                                               | Nee                                                          |
| maxConcurrentVerbindingen | Het aantal verbindingen dat tegelijkertijd verbinding moet maken met het opslagarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee                                                          |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
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
                    "type": "AmazonS3ReadSettings",
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

| Emmer | sleutel | Recursieve | Structuur en filterresultaat voor bronmappen (vetgedrukte bestanden worden opgehaald)|
|:--- |:--- |:--- |:--- |
| Emmer | `Folder*/*` | false | Emmer<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| Emmer | `Folder*/*` | waar | Emmer<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| Emmer | `Folder*/*.csv` | false | Emmer<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| Emmer | `Folder*/*.csv` | waar | Emmer<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

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
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **AmazonS3Object** |Ja |
| bucketName | De Naam van de S3-emmer. Wildcard-filter wordt niet ondersteund. |Ja voor kopieer-/opzoekactiviteit, nee voor activiteit GetMetadata |
| sleutel | Het **naam- of wildcardfilter** van de objectsleutel Van S3 onder de opgegeven bucket. Geldt alleen wanneer de eigenschap 'voorvoegsel' niet is opgegeven. <br/><br/>Het jokerfilter wordt ondersteund voor zowel het maponderdeel als het bestandsnaamgedeelte. Toegestane wildcards zijn: `*` (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken).<br/>- Voorbeeld 1:`"key": "rootfolder/subfolder/*.csv"`<br/>- Voorbeeld 2:`"key": "rootfolder/subfolder/???20180427.txt"`<br/>Zie meer voorbeelden in [voorbeelden van mappen en bestandenfilters](#folder-and-file-filter-examples). Gebruik `^` om te ontsnappen als uw werkelijke map / bestandsnaam wildcard of deze ontsnapping char binnen heeft. |Nee |
| Voorvoegsel | Voorvoegsel voor de S3-objecttoets. Objecten waarvan de toetsen met dit voorvoegsel beginnen, zijn geselecteerd. Geldt alleen wanneer de eigenschap "sleutel" niet is opgegeven. |Nee |
| versie | De versie van het S3-object, als S3-versieing is ingeschakeld. |Nee |
| gewijzigdDatumtimeStart | Bestandenfilter op basis van het kenmerk: Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` tijd `modifiedDatetimeEnd`binnen het tijdsbereik tussen en . De tijd wordt toegepast op utc-tijdzone in de indeling "2018-12-01T05:00:00Z". <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestandskenmerk wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` de datumtijdwaarde is, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, worden geselecteerd.  Wanneer `modifiedDatetimeEnd` de datumtijdwaarde is, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, worden geselecteerd.| Nee |
| gewijzigdDatumtimeEnd | Bestandenfilter op basis van het kenmerk: Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` tijd `modifiedDatetimeEnd`binnen het tijdsbereik tussen en . De tijd wordt toegepast op utc-tijdzone in de indeling "2018-12-01T05:00:00Z". <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestandskenmerk wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` de datumtijdwaarde is, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, worden geselecteerd.  Wanneer `modifiedDatetimeEnd` de datumtijdwaarde is, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, worden geselecteerd.| Nee |
| formaat | Als u **bestanden** wilt kopiëren tussen bestandsopslag (binaire kopie), slaat u de sectie opmaak over in definities van zowel invoer- als uitvoergegevenssets.<br/><br/>Als u bestanden met een specifieke indeling wilt ontleden of genereren, worden de volgende bestandsindelingstypen ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **eigenschap type** onder opmaak in op een van deze waarden. Zie [Secties Tekstformaat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Json Format](supported-file-formats-and-compression-codecs-legacy.md#json-format), Avro [Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)en [Parquet Format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) voor meer informatie. |Nee (alleen voor binaire kopie scenario) |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Zie [Ondersteunde bestandsindelingen en compressiecodecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)voor meer informatie.<br/>Ondersteunde typen zijn: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**.<br/>Ondersteunde niveaus zijn: **Optimaal** en **snelste**. |Nee |

>[!TIP]
>Als u alle bestanden onder een map wilt kopiëren, geeft u **bucketName** voor bucket en **voorvoegsel** op voor maponderdeel.<br>Als u één bestand met een bepaalde naam wilt kopiëren, geeft u **bucketName** voor bucket en **sleutel** op voor maponderdeel plus bestandsnaam.<br>Als u een subset van bestanden onder een map wilt kopiëren, geeft u **bucketName** voor bucket en **sleutel** voor maponderdeel plus wildcardfilter op.

**Voorbeeld: voorvoegsel gebruiken**

```json
{
    "name": "GoogleCloudStorageDataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
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
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensopslag die wordt ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
