---
title: Gegevens kopiëren van/naar Azure File Storage
description: Leer hoe u gegevens kopiëren van Azure File Storage om ondersteunde sink-gegevensopslag (of) van de ondersteunde bron-gegevensopslag met Azure File Storage met behulp van Azure Data Factory.
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
ms.openlocfilehash: 42036654ec4fcfd95fc7301c3885c3b34fcc48b7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440727"
---
# <a name="copy-data-from-or-to-azure-file-storage-by-using-azure-data-factory"></a>Gegevens kopiëren van of naar Azure File Storage met behulp van Azure Data Factory

In dit artikel wordt beschreven hoe u gegevens kopieert van en naar Azure File Storage. Lees voor meer informatie over Azure Data Factory, de [inleidende artikel](introduction.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure File Storage-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)

Specifiek,-bestanden kopiëren als biedt ondersteuning voor deze connector Azure File Storage-is of het parseren van/genereren van bestanden met de [ondersteunde indelingen en codecs voor compressie](supported-file-formats-and-compression-codecs.md).

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten meer informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten specifieke met Azure File Storage.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor Azure File Storage gekoppelde service:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **AzureFileStorage**. | Ja |
| host | Hiermee geeft u het Azure File Storage-eind punt op als: <br/>-Gebruikers interface gebruiken: `\\<storage name>.file.core.windows.net\<file service name>` opgeven<br/>-JSON gebruiken: `"host": "\\\\<storage name>.file.core.windows.net\\<file service name>"`. | Ja |
| userid | Geef de gebruiker op om toegang te krijgen tot de Azure-File Storage als: <br/>-Gebruikers interface gebruiken: `AZURE\<storage name>` opgeven<br/>-JSON gebruiken: `"userid": "AZURE\\<storage name>"`. | Ja |
| wachtwoord | Geef de toegangssleutel voor opslag. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory, of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | De [Integration Runtime](concepts-integration-runtime.md) moet worden gebruikt verbinding maken met het gegevensarchief. U kunt Azure Integration Runtime of zelfgehoste Cloudintegratieruntime gebruiken (als het gegevensarchief bevindt zich in een particulier netwerk). Als niet is opgegeven, wordt de standaard Azure Integration Runtime. |Nee voor bron, Ja voor sink |

>[!IMPORTANT]
> - Gegevens te kopiëren naar Azure File Storage met behulp van Azure Integration Runtime expliciet [maken van een Azure IR](create-azure-integration-runtime.md#create-azure-ir) door de locatie van uw bestandsopslag en koppelen in de gekoppelde service als in het volgende voorbeeld.
> - Houd er rekening mee om gegevens te kopiëren van/naar Azure File Storage met behulp van zelfgehoste Cloudintegratieruntime buiten Azure, uitgaande TCP-poort 445 in uw lokale netwerk openen.

>[!TIP]
>Wanneer u de ADF-gebruikers interface voor het ontwerpen wilt gebruiken, kunt u de specifieke vermelding ' Azure File Storage ' vinden voor het maken van gekoppelde services, die onder Type `FileServer`-object wordt gegenereerd.

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

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor Azure-File Storage onder `location` instellingen in gegevensset op basis van indeling:

| Eigenschap   | Beschrijving                                                  | Verplicht |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | De eigenschap type onder `location` in DataSet moet worden ingesteld op **FileServerLocation**. | Ja      |
| folderPath | Het pad naar de map. Als u een Joker teken wilt gebruiken om de map te filteren, slaat u deze instelling over en geeft u de instellingen voor de activiteit bron op. | Nee       |
| fileName   | De bestands naam onder de opgegeven folderPath. Als u Joker tekens wilt gebruiken om bestanden te filteren, slaat u deze instelling over en geeft u de instellingen van de activiteit bron op. | Nee       |

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

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door Azure File Storage-bron- en sinkblobpaden.

### <a name="azure-file-storage-as-source"></a>Azure File Storage als bron

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor Azure-File Storage onder `storeSettings` instellingen in op indeling gebaseerde Kopieer Bron:

| Eigenschap                 | Beschrijving                                                  | Verplicht                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | De eigenschap type onder `storeSettings` moet zijn ingesteld op **FileServerReadSetting**. | Ja                                           |
| recursive                | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen voor de opgegeven map. Houd er rekening mee dat wanneer recursieve is ingesteld op true en de sink is een opslagplaats op basis van bestanden, een lege map of submap is niet gekopieerd of gemaakt in de sink. Toegestane waarden zijn **waar** (standaard) en **false**. | Nee                                            |
| wildcardFolderPath       | Het mappad met Joker tekens om de bron mappen te filteren. <br>Toegestane joker tekens zijn: `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken). gebruik `^` om te escapen als uw daad werkelijke mapnaam Joker teken of escape-teken bevat. <br>Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). | Nee                                            |
| wildcardFileName         | De naam van het bestand met Joker tekens onder de opgegeven folderPath/wildcardFolderPath voor het filteren van bron bestanden. <br>Toegestane joker tekens zijn: `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken). gebruik `^` om te escapen als uw daad werkelijke mapnaam Joker teken of escape-teken bevat.  Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). | Ja als `fileName` niet is opgegeven in de gegevensset |
| modifiedDatetimeStart    | Bestanden filteren op basis van het kenmerk: laatst gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik ligt tussen `modifiedDatetimeStart` en `modifiedDatetimeEnd`. De tijd wordt toegepast op de UTC-tijdzone in de notatie ' 2018-12-01T05:00:00Z '. <br> De eigenschappen kunnen NULL zijn, wat betekent dat er geen bestands kenmerk filter op de gegevensset wordt toegepast.  Wanneer `modifiedDatetimeStart` datum / tijdwaarde heeft, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het kenmerk Laatst gewijzigde groter dan is of gelijk is aan de datum / tijdwaarde wordt geselecteerd.  Wanneer `modifiedDatetimeEnd` datum / tijdwaarde heeft, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het kenmerk Laatst gewijzigde lager is dan de datum / tijdwaarde wordt geselecteerd. | Nee                                            |
| modifiedDatetimeEnd      | Hetzelfde als hierboven.                                               | Nee                                            |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met opslag archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee                                            |

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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "FileServerReadSetting",
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

### <a name="azure-file-storage-as-sink"></a>Azure File Storage als sink

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor Azure-File Storage onder `storeSettings` instellingen in op indeling gebaseerde kopie-Sink:

| Eigenschap                 | Beschrijving                                                  | Verplicht |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | De eigenschap type onder `storeSettings` moet zijn ingesteld op **FileServerWriteSetting**. | Ja      |
| copyBehavior             | Definieert het gedrag kopiëren wanneer de bron bestanden vanuit een bestandsgebaseerde gegevensarchief is.<br/><br/>Toegestane waarden zijn:<br/><b>-PreserveHierarchy (standaard)</b>: behoudt de bestandshiërarchie in de doelmap. Het relatieve pad van het bronbestand voor bronmap is identiek aan het relatieve pad van doelbestand naar doelmap.<br/><b>-FlattenHierarchy</b>: alle bestanden uit de bronmap van het zich in het eerste niveau van de doelmap. De doelbestanden hebben automatisch gegenereerde namen. <br/><b>-MergeFiles</b>: alle bestanden uit de bronmap naar één bestand worden samengevoegd. Als de bestandsnaam is opgegeven, is de naam van het samengevoegde de opgegeven naam. Anders is de naam van een automatisch gegenereerde bestand. | Nee       |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met het gegevens archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee       |

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
                    "type": "FileServerWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Voor beelden van map-en bestands filter

In deze sectie wordt het resulterende gedrag van het mappad en de bestands naam met Joker teken filters beschreven.

| folderPath | fileName | recursive | De structuur van de bronmap en het filter resultaat ( **vetgedrukte** bestanden worden opgehaald)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (leeg, standaard instelling gebruiken) | false | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (leeg, standaard instelling gebruiken) | waar | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | waar | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="recursive-and-copybehavior-examples"></a>recursieve en copyBehavior voorbeelden

Deze sectie beschrijft het resulterende gedrag van de kopieerbewerking voor de verschillende combinaties van recursieve en copyBehavior waarden.

| recursive | copyBehavior | Structuur van de gegevensbron | Resulterende doel |
|:--- |:--- |:--- |:--- |
| waar |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | de doelmap Map1 is gemaakt met dezelfde structuur als de bron:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| waar |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Het doel Map1 is gemaakt met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File5 |
| waar |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Het doel Map1 is gemaakt met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1 + Bestand2 + File3 + File4 + inhoud van bestand 5 worden samengevoegd in één bestand met automatisch gegenereerde bestands naam |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | De doelmap Map1 wordt gemaakt met de volgende structuur<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 bestand3 File4 en File5 worden niet doorgevoerd. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | De doelmap Map1 wordt gemaakt met de volgende structuur<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand2<br/><br/>Subfolder1 bestand3 File4 en File5 worden niet doorgevoerd. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | De doelmap Map1 wordt gemaakt met de volgende structuur<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1 + Bestand2-inhoud worden samengevoegd in één bestand met automatisch gegenereerde bestands naam. automatisch gegenereerde naam voor File1<br/><br/>Subfolder1 bestand3 File4 en File5 worden niet doorgevoerd. |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="getmetadata-activity-properties"></a>Eigenschappen van GetMetadata-activiteit

Als u meer wilt weten over de eigenschappen, controleert u de [GetMetadata-activiteit](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Eigenschappen van activiteit verwijderen

Als u meer wilt weten over de eigenschappen, controleert u de [activiteit verwijderen](delete-activity.md)

## <a name="legacy-models"></a>Verouderde modellen

>[!NOTE]
>De volgende modellen worden nog steeds ondersteund voor compatibiliteit met eerdere versies. U wordt aangeraden het nieuwe model te gebruiken dat hierboven wordt beschreven en de gebruikers interface van de ADF-ontwerp functie is overgeschakeld op het genereren van het nieuwe model.

### <a name="legacy-dataset-model"></a>Verouderd gegevensset-model

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **bestandsshare** |Ja |
| folderPath | Pad naar de map. <br/><br/>Het Joker teken filter wordt ondersteund. toegestane joker tekens zijn: `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken). gebruik `^` om te escapen als uw daad werkelijke mapnaam Joker teken of escape-teken bevat. <br/><br/>Voor beelden: root folder/submap/, zie voor beelden van [mappen en bestands filters](#folder-and-file-filter-examples)voor meer voor beelden. |Ja |
| fileName | **Naam of het jokerteken filter** voor de bestanden die onder het opgegeven 'folderPath'. Als u een waarde voor deze eigenschap niet opgeeft, wordt de gegevensset verwijst naar alle bestanden in de map. <br/><br/>Voor het filter toegestane jokertekens zijn: `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken).<br/>-Voorbeeld 1: `"fileName": "*.csv"`<br/>-Voorbeeld 2: `"fileName": "???20180427.txt"`<br/>Gebruik `^` als escape voor als de bestandsnaam van uw werkelijke jokertekens of deze escape-teken in.<br/><br/>Als er geen bestands naam is opgegeven voor een uitvoer-gegevensset en **preserveHierarchy** niet is opgegeven in de Sink van de activiteit, genereert de Kopieer activiteit automatisch de bestands naam met het volgende patroon: "*gegevens. [ Run ID-GUID van activiteit]. [GUID if FlattenHierarchy]. [indeling indien geconfigureerd]. [compressie indien geconfigureerd]* ', bijvoorbeeld ' data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt. gz '; Als u vanuit tabellaire bron kopieert met behulp van de tabel naam in plaats van een query, is het naam patroon ' *[tabel naam]. [ indeling]. [compressie indien geconfigureerd]* ", bijvoorbeeld" mytable. csv ". |Nee |
| modifiedDatetimeStart | Bestanden filteren op basis van het kenmerk: laatst gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik ligt tussen `modifiedDatetimeStart` en `modifiedDatetimeEnd`. De tijd wordt toegepast op de UTC-tijdzone in de notatie ' 2018-12-01T05:00:00Z '. <br/><br/> Houd er rekening mee dat de prestaties van het verplaatsen van gegevens worden beïnvloed door deze instelling in te scha kelen wanneer u bestands filter van enorme hoeveel heden bestanden wilt uitvoeren. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen bestands kenmerk filter op de gegevensset wordt toegepast.  Wanneer `modifiedDatetimeStart` datum / tijdwaarde heeft, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het kenmerk Laatst gewijzigde groter dan is of gelijk is aan de datum / tijdwaarde wordt geselecteerd.  Wanneer `modifiedDatetimeEnd` datum / tijdwaarde heeft, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het kenmerk Laatst gewijzigde lager is dan de datum / tijdwaarde wordt geselecteerd.| Nee |
| modifiedDatetimeEnd | Bestanden filteren op basis van het kenmerk: laatst gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik ligt tussen `modifiedDatetimeStart` en `modifiedDatetimeEnd`. De tijd wordt toegepast op de UTC-tijdzone in de notatie ' 2018-12-01T05:00:00Z '. <br/><br/> Houd er rekening mee dat de prestaties van het verplaatsen van gegevens worden beïnvloed door deze instelling in te scha kelen wanneer u bestands filter van enorme hoeveel heden bestanden wilt uitvoeren. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen bestands kenmerk filter op de gegevensset wordt toegepast.  Wanneer `modifiedDatetimeStart` datum / tijdwaarde heeft, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het kenmerk Laatst gewijzigde groter dan is of gelijk is aan de datum / tijdwaarde wordt geselecteerd.  Wanneer `modifiedDatetimeEnd` datum / tijdwaarde heeft, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het kenmerk Laatst gewijzigde lager is dan de datum / tijdwaarde wordt geselecteerd.| Nee |
| format | Als u wilt **bestanden als kopiëren-is** overslaan tussen op basis van bestanden (binaire kopie), het gedeelte indeling in beide definities van de gegevensset voor invoer en uitvoer.<br/><br/>Als u wilt parseren of bestanden met een specifieke indeling genereren, de volgende indeling bestandstypen worden ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **type** eigenschap onder indeling op een van deze waarden. Zie voor meer informatie, [tekstindeling](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Json-indeling](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro-indeling](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc-indeling](supported-file-formats-and-compression-codecs-legacy.md#orc-format), en [Parquet-indeling](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) secties. |Nee (alleen voor binaire kopie-scenario) |
| compression | Geef het type en het niveau van compressie voor de gegevens. Zie voor meer informatie, [ondersteunde indelingen en codecs voor compressie](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Ondersteunde typen zijn: **GZip**, **Deflate**, **BZip2**, en **ZipDeflate**.<br/>Ondersteunde niveaus: **optimale** en **snelst**. |Nee |

>[!TIP]
>Alle bestanden in een map wilt kopiëren, geef **folderPath** alleen.<br>Voor het kopiëren van één bestand met een specifieke naam, geef **folderPath** met maponderdeel en **fileName** met bestandsnaam.<br>Als u wilt kopiëren van een subset van de bestanden in een map, geef **folderPath** met maponderdeel en **fileName** met filteren op jokerteken.

>[!NOTE]
>Als u 'fileFilter'-eigenschap voor bestandsfilter, nog steeds wordt ondersteund als-is, terwijl u gebruik van het nieuwe filter mogelijkheid toegevoegd aan 'fileName' voortaan worden voorgesteld.

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

### <a name="legacy-copy-activity-source-model"></a>Bron model van verouderde Kopieer activiteit

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron voor kopiëren-activiteit moet worden ingesteld op: **FileSystemSource** |Ja |
| recursive | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen voor de opgegeven map. Houd er rekening mee wanneer recursieve is ingesteld op true en sink is opslag op basis van bestanden, lege map/ondergeschikt-folder worden niet gekopieerd/gemaakt bij de sink.<br/>Toegestane waarden zijn: **waar** (standaard), **false** | Nee |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met opslag archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee |

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

### <a name="legacy-copy-activity-sink-model"></a>Sink model voor verouderde Kopieer activiteit

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type van de kopie-activiteit-sink moet worden ingesteld op: **FileSystemSink** |Ja |
| copyBehavior | Definieert het gedrag kopiëren wanneer de bron-bestanden van gegevensopslag op basis van bestanden.<br/><br/>Toegestane waarden zijn:<br/><b>-PreserveHierarchy (standaard)</b>: behoudt de bestandshiërarchie in de doelmap. Het relatieve pad van het bronbestand voor bronmap is identiek aan het relatieve pad van doelbestand naar doelmap.<br/><b>-FlattenHierarchy</b>: alle bestanden uit de bronmap van het zich in het eerste niveau van de doelmap. De doel bestanden hebben een automatisch gegenereerde naam. <br/><b>-MergeFiles</b>: alle bestanden uit de bronmap naar één bestand worden samengevoegd. Als de bestands naam is opgegeven, is de naam van het samengevoegde bestand de opgegeven naam. anders is de bestands naam automatisch gegenereerd. | Nee |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met opslag archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee |

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
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md##supported-data-stores-and-formats).
