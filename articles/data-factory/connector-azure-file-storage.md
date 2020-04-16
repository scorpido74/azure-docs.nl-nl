---
title: Gegevens kopiëren van/naar Azure-bestandsopslag
description: Meer informatie over het kopiëren van gegevens uit Azure File Storage naar ondersteunde sinkdatastores (of) van ondersteunde brongegevensopslag naar Azure File Storage met Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/10/2019
ms.openlocfilehash: d31ab1d9cb250e0395e8b57342a449a6b9383c64
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415067"
---
# <a name="copy-data-from-or-to-azure-file-storage-by-using-azure-data-factory"></a>Gegevens van of naar Azure-bestandsopslag kopiëren met Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u gegevens van en naar Azure File Storage kopieert. Lees het [inleidende artikel](introduction.md)voor meer informatie over Azure Data Factory.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure File Storage-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Activiteit Metagegevens](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)

Deze Azure File Storage-connector ondersteunt met name het kopiëren van bestanden als is of ontleedt/genereert bestanden met de [ondersteunde bestandsindelingen en compressiecodecs.](supported-file-formats-and-compression-codecs.md)

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor Azure File Storage.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor gekoppelde Azure File Storage-service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **AzureFileStorage**. | Ja |
| host | Hiermee geeft u het eindpunt azure bestandsopslag op als: <br/>-UI gebruiken: opgeven`\\<storage name>.file.core.windows.net\<file service name>`<br/>- Json `"host": "\\\\<storage name>.file.core.windows.net\\<file service name>"`gebruiken: . | Ja |
| userid | Geef de gebruiker op om toegang te krijgen tot de Azure File Storage als: <br/>-UI gebruiken: opgeven`AZURE\<storage name>`<br/>-Json gebruiken: `"userid": "AZURE\\<storage name>"`. | Ja |
| wachtwoord | Geef de opslagtoegangssleutel op. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. U Azure Integration Runtime of Self-hosted Integration Runtime gebruiken (als uw gegevensarchief zich in een privénetwerk bevindt). Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee voor bron, Ja voor gootsteen |

>[!IMPORTANT]
> - Als u gegevens wilt kopiëren naar Azure File Storage met Azure Integration Runtime, maakt u expliciet [een Azure IR](create-azure-integration-runtime.md#create-azure-ir) met de locatie van uw bestandsopslag en associeert u in de gekoppelde service als het volgende voorbeeld.
> - Als u gegevens wilt kopiëren van/naar Azure File Storage met self-hosted Integration Runtime buiten Azure, moet u uitgaande TCP-poort 445 openen in uw lokale netwerk.

>[!TIP]
>Wanneer u ADF-gebruikersinterface gebruikt voor het ontwerpen, vindt u de specifieke vermelding van `FileServer` 'Azure File Storage' voor het maken van gekoppelde services, die eronder tekstobject genereert.

**Voorbeeld:**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "host": "\\\\<storage name>.file.core.windows.net\\<file service name>",
            "userid": "AZURE\\<storage name>",
            "password": {
                "type": "SecureString",
                "value": "<storage access key>"
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

De volgende eigenschappen worden ondersteund voor `location` Azure File Storage onder instellingen in op indeling gebaseerde gegevensset:

| Eigenschap   | Beschrijving                                                  | Vereist |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | De eigenschap `location` type onder in de gegevensset moet worden ingesteld op **FileServerLocation**. | Ja      |
| folderPath | Het pad naar de map. Als u wildcard wilt gebruiken om de map te filteren, slaat u deze instelling over en geeft u op in de instellingen van de activiteitsbron. | Nee       |
| fileName   | De bestandsnaam onder de opgegeven mapPath. Als u wildcard wilt gebruiken om bestanden te filteren, slaat u deze instelling over en geeft u op in de instellingen van de activiteitsbron. | Nee       |

**Voorbeeld:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure File Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FileServerLocation",
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

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de bron en gootsteen van Azure File Storage.

### <a name="azure-file-storage-as-source"></a>Azure-bestandsopslag als bron

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor `storeSettings` Azure File Storage onder instellingen in op indeling gebaseerde kopieerbron:

| Eigenschap                 | Beschrijving                                                  | Vereist                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | De eigenschap `storeSettings` type onder moet zijn ingesteld op **FileServerReadSettings**. | Ja                                           |
| Recursieve                | Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. Houd er rekening mee dat wanneer recursieve is ingesteld op true en de gootsteen een op bestanden gebaseerd e-bestand is, een lege map of submap niet wordt gekopieerd of gemaakt aan de gootsteen. Toegestane waarden zijn **waar** (standaard) en **onwaar.** | Nee                                            |
| wildcardFolderPath       | Het mappad met jokertekens om bronmappen te filteren. <br>Toegestane wildcards zijn: `*` (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken); gebruiken `^` om te ontsnappen als uw werkelijke map naam wildcard of deze ontsnapping char binnen heeft. <br>Zie meer voorbeelden in [voorbeelden van mappen en bestandenfilters](#folder-and-file-filter-examples). | Nee                                            |
| wildcardFileName         | De bestandsnaam met jokertekens onder de opgegeven mapPath/wildcardFolderPath om bronbestanden te filteren. <br>Toegestane wildcards zijn: `*` (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken); gebruiken `^` om te ontsnappen als uw werkelijke map naam wildcard of deze ontsnapping char binnen heeft.  Zie meer voorbeelden in [voorbeelden van mappen en bestandenfilters](#folder-and-file-filter-examples). | Ja `fileName` als deze niet is opgegeven in de gegevensset |
| gewijzigdDatumtimeStart    | Bestandenfilter op basis van het kenmerk: Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` tijd `modifiedDatetimeEnd`binnen het tijdsbereik tussen en . De tijd wordt toegepast op utc-tijdzone in de indeling "2018-12-01T05:00:00Z". <br> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestandskenmerken wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` de datumtijdwaarde is, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, worden geselecteerd.  Wanneer `modifiedDatetimeEnd` de datumtijdwaarde is, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, worden geselecteerd. | Nee                                            |
| gewijzigdDatumtimeEnd      | Hetzelfde als hierboven.                                               | Nee                                            |
| maxConcurrentVerbindingen | Het aantal verbindingen dat tegelijkertijd verbinding moet maken met het opslagarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee                                            |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromAzureFileStorage",
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
                    "type": "FileServerReadSettings",
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

### <a name="azure-file-storage-as-sink"></a>Azure-bestandsopslag als gootsteen

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor `storeSettings` Azure File Storage onder instellingen in op indeling gebaseerde copy sink:

| Eigenschap                 | Beschrijving                                                  | Vereist |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | De eigenschap `storeSettings` type onder moet zijn ingesteld op **FileServerWriteSettings**. | Ja      |
| copyBehavior             | Hiermee definieert u het kopieergedrag wanneer de bron bestanden is uit een gegevensarchief op basis van bestanden.<br/><br/>Toegestane waarden zijn:<br/><b>- BehoudenHiërarchie (standaard)</b>: Hiermee behoudt u de bestandshiërarchie in de doelmap. Het relatieve pad van bronbestand naar bronmap is identiek aan het relatieve pad van doelbestand naar doelmap.<br/><b>- FlattenHierarchy:</b>Alle bestanden uit de bronmap bevinden zich in het eerste niveau van de doelmap. De doelbestanden hebben automatisch gegenereerde namen. <br/><b>- MergeFiles</b>: Hiermee worden alle bestanden van de bronmap samengevoegd tot één bestand. Als de bestandsnaam is opgegeven, is de samengevoegde bestandsnaam de opgegeven naam. Anders is het een automatisch gegenereerde bestandsnaam. | Nee       |
| maxConcurrentVerbindingen | Het aantal verbindingen dat gelijktijdig verbinding maakt met het gegevensarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee       |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "FileServerWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
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

### <a name="recursive-and-copybehavior-examples"></a>recursieve en copyBehavior voorbeelden

In deze sectie wordt het resulterende gedrag van de bewerking Kopiëren beschreven voor verschillende combinaties van recursieve en copyBehavior-waarden.

| Recursieve | copyBehavior | Structuur van bronmappen | Resulterend doel |
|:--- |:--- |:--- |:--- |
| waar |behoudenHiërarchie | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap Map1 wordt gemaakt met dezelfde structuur als de bron:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5. |
| waar |afvlakkenHiërarchie | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap1 wordt gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand5 |
| waar |mergeFiles | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap1 wordt gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5-inhoud wordt samengevoegd in één bestand met automatisch gegenereerde bestandsnaam |
| false |behoudenHiërarchie | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap Map1 wordt gemaakt met de volgende structuur<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/><br/>Submap1 met Bestand3, File4 en File5 worden niet opgehaald. |
| false |afvlakkenHiërarchie | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap Map1 wordt gemaakt met de volgende structuur<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/><br/>Submap1 met Bestand3, File4 en File5 worden niet opgehaald. |
| false |mergeFiles | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap Map1 wordt gemaakt met de volgende structuur<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2-inhoud wordt samengevoegd tot één bestand met automatisch gegenereerde bestandsnaam. automatisch gegenereerde naam voor Bestand1<br/><br/>Submap1 met Bestand3, File4 en File5 worden niet opgehaald. |

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
| folderPath | Pad naar de map. <br/><br/>Wildcard filter wordt ondersteund, toegestane wildcards zijn: `*` (komt `?` overeen met nul of meer tekens) en (komt overeen met nul of een enkel teken); gebruiken `^` om te ontsnappen als uw werkelijke map naam wildcard of deze ontsnapping char binnen heeft. <br/><br/>Voorbeelden: rootmap/submap/, zie meer voorbeelden in [voorbeelden van mappen en bestandsfilters](#folder-and-file-filter-examples). |Ja |
| fileName | **Naam- of wildcardfilter** voor het bestand(en) onder de opgegeven 'mapPath'. Als u geen waarde voor deze eigenschap opgeeft, verwijst de gegevensset naar alle bestanden in de map. <br/><br/>Voor filter zijn toegestane jokertekens: `*` (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of één teken).<br/>- Voorbeeld 1:`"fileName": "*.csv"`<br/>- Voorbeeld 2:`"fileName": "???20180427.txt"`<br/>Gebruik `^` om te ontsnappen als uw werkelijke bestandsnaam wildcard of deze ontsnapping char binnen heeft.<br/><br/>Wanneer bestandsnaam niet is opgegeven voor een uitvoergegevensset en **de hiërarchie** niet is opgegeven in de activiteitssink, genereert de kopieeractiviteit automatisch de bestandsnaam met het volgende patroon: "*Gegevens.[ activiteit uitvoeren ID GUID]. [GUID als FlattenHierarchy]. [indeling indien geconfigureerd]. [compressie indien geconfigureerd]*", bijvoorbeeld "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"; Als u uit tabelbron kopieert met tabelnaam in plaats van query, is het naampatroon "*[tabelnaam].[ formaat]. [compressie indien geconfigureerd]*", bijvoorbeeld "MyTable.csv". |Nee |
| gewijzigdDatumtimeStart | Bestandenfilter op basis van het kenmerk: Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` tijd `modifiedDatetimeEnd`binnen het tijdsbereik tussen en . De tijd wordt toegepast op utc-tijdzone in de indeling "2018-12-01T05:00:00Z". <br/><br/> Houd er rekening mee dat de algehele prestaties van gegevensverplaatsing worden beïnvloed door deze instelling in te schakelen wanneer u bestandsfilter wilt doen uit enorme hoeveelheden bestanden. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestandskenmerk wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` de datumtijdwaarde is, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, worden geselecteerd.  Wanneer `modifiedDatetimeEnd` de datumtijdwaarde is, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, worden geselecteerd.| Nee |
| gewijzigdDatumtimeEnd | Bestandenfilter op basis van het kenmerk: Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` tijd `modifiedDatetimeEnd`binnen het tijdsbereik tussen en . De tijd wordt toegepast op utc-tijdzone in de indeling "2018-12-01T05:00:00Z". <br/><br/> Houd er rekening mee dat de algehele prestaties van gegevensverplaatsing worden beïnvloed door deze instelling in te schakelen wanneer u bestandsfilter wilt doen uit enorme hoeveelheden bestanden. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestandskenmerk wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` de datumtijdwaarde is, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, worden geselecteerd.  Wanneer `modifiedDatetimeEnd` de datumtijdwaarde is, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, worden geselecteerd.| Nee |
| formaat | Als u **bestanden** wilt kopiëren tussen bestandsopslag (binaire kopie), slaat u de sectie opmaak over in definities van zowel invoer- als uitvoergegevenssets.<br/><br/>Als u bestanden met een specifieke indeling wilt ontleden of genereren, worden de volgende bestandsindelingstypen ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **eigenschap type** onder opmaak in op een van deze waarden. Zie [Secties Tekstformaat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Json Format](supported-file-formats-and-compression-codecs-legacy.md#json-format), Avro [Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)en [Parquet Format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) voor meer informatie. |Nee (alleen voor binaire kopie scenario) |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Zie [Ondersteunde bestandsindelingen en compressiecodecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)voor meer informatie.<br/>Ondersteunde typen zijn: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**.<br/>Ondersteunde niveaus zijn: **Optimaal** en **snelste**. |Nee |

>[!TIP]
>Als u alle bestanden onder een map wilt kopiëren, geeft u **alleen mapPath** op.<br>Als u één bestand met een bepaalde naam wilt kopiëren, geeft u **mapPath** op met maponderdeel en **bestandsnaam.**<br>Als u een subset van bestanden onder een map wilt kopiëren, geeft u **mapPath** op met maponderdeel en **bestandsnaam** met wildcardfilter.

>[!NOTE]
>Als u de eigenschap "fileFilter" gebruikt voor bestandsfilter, wordt deze nog steeds ondersteund als is, terwijl u wordt voorgesteld om de nieuwe filtermogelijkheid te gebruiken die wordt toegevoegd aan "fileName" in de toekomst.

**Voorbeeld:**

```json
{
    "name": "AzureFileStorageDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<Azure File Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
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
        "name": "CopyFromAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure File Storage input dataset name>",
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

### <a name="legacy-copy-activity-sink-model"></a>Voorbeeld van kopieeractiviteitsinkmodel

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de kopieeractiviteitmoet worden ingesteld op: **FileSystemSink** |Ja |
| copyBehavior | Hiermee definieert u het kopieergedrag wanneer de bron bestanden uit het gegevensarchief op basis van bestanden is.<br/><br/>Toegestane waarden zijn:<br/><b>- PreserveHiërarchie (standaard)</b>: behoudt de bestandshiërarchie in de doelmap. Het relatieve pad van bronbestand naar bronmap is identiek aan het relatieve pad van doelbestand naar doelmap.<br/><b>- FlattenHierarchy:</b>alle bestanden uit de bronmap bevinden zich in het eerste niveau van de doelmap. De doelbestanden hebben een automatisch gegenereerde naam. <br/><b>- MergeFiles:</b>voegt alle bestanden van de bronmap samen tot één bestand. Als de bestandsnaam is opgegeven, is de samengevoegde bestandsnaam de opgegeven naam. anders zou worden automatisch gegenereerde bestandsnaam. | Nee |
| maxConcurrentVerbindingen | Het aantal verbindingen dat tegelijkertijd verbinding moet maken met het opslagarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure File Storage output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "FileSystemSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
