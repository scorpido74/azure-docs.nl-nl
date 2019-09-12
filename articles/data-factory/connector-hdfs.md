---
title: Gegevens kopiëren van HDFS met Azure Data Factory | Microsoft Docs
description: Meer informatie over het kopiëren van gegevens uit een Cloud of een on-premises HDFS-bron naar ondersteunde Sink-gegevens archieven door gebruik te maken van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 0a695f08f00b99fcd0bc634d12e30c0f3cfbd312
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813086"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Gegevens kopiëren van HDFS met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-hdfs-connector.md)
> * [Huidige versie](connector-hdfs.md)

In dit artikel wordt beschreven hoe u gegevens kopieert van HDFS-server. Lees voor meer informatie over Azure Data Factory, de [inleidende artikel](introduction.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze HDFS-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

Deze HDFS-connector ondersteunt met name:

- Bestanden kopiëren met behulp van **Windows** (Kerberos) of **anonieme** verificatie.
- Bestanden kopiëren met behulp van **webhdfs** -protocol of **ingebouwde DistCp** -ondersteuning.
- Kopiëren van bestanden als-is of het parseren van/genereren van bestanden met de [ondersteunde indelingen en codecs voor compressie](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Zorg ervoor dat de Integration Runtime toegang heeft tot **alle** [naam knooppunt server]: [naam knooppunt poort] en [gegevens knooppunt Servers]: [Data knoop punt poort] van het Hadoop-cluster. De standaard poort voor [naam knooppunt] is 50070 en de standaard poort van het gegevens knooppunt is 50075.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor HDFS.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor de gekoppelde service van HDFS:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type moet worden ingesteld op: **Hdfs**. | Ja |
| url |URL naar HDFS |Ja |
| authenticationType | Toegestane waarden zijn: **Anoniem**of **Windows**. <br><br> Als u **Kerberos-verificatie** wilt gebruiken voor HDFS-connector, raadpleegt u [deze sectie](#use-kerberos-authentication-for-hdfs-connector) om uw on-premises omgeving dienovereenkomstig in te stellen. |Ja |
| userName |Gebruikers naam voor Windows-verificatie. Voor Kerberos-verificatie geeft `<username>@<domain>.com`u op. |Ja (voor Windows-verificatie) |
| password |Wacht woord voor Windows-verificatie. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory, of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). |Ja (voor Windows-verificatie) |
| connectVia | De [Integration Runtime](concepts-integration-runtime.md) moet worden gebruikt verbinding maken met het gegevensarchief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als niet is opgegeven, wordt de standaard Azure Integration Runtime. |Nee |

**Voor beeld: anonieme verificatie gebruiken**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld: Windows-verificatie gebruiken**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
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

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. 

- Raadpleeg voor **Parquet, tekst met scheidings tekens, JSON, AVRO en binaire indeling**, de sectie [Parquet, gescheiden tekst, JSON, AVRO en binaire indeling gegevensset](#format-based-dataset) .
- Raadpleeg de sectie [andere indelings gegevensset](#other-format-dataset) voor andere indelingen, zoals **Orc-indeling**.

### <a name="format-based-dataset"></a>Parquet, gescheiden tekst, JSON, AVRO en binaire indeling gegevensset

Als u gegevens wilt kopiëren uit **Parquet, tekst met scheidings tekens, JSON, AVRO en binaire indeling**, raadpleegt u [Parquet-indeling](format-parquet.md), [tekst indeling met scheidings tekens](format-delimited-text.md), [Avro-indeling](format-avro.md) en [binaire indeling](format-binary.md) voor een op indeling gebaseerde gegevensset en ondersteunde instellingen. De volgende eigenschappen worden ondersteund voor HDFS onder `location` instellingen in gegevensset op basis van indeling:

| Eigenschap   | Description                                                  | Vereist |
| ---------- | ------------------------------------------------------------ | -------- |
| Type       | De eigenschap type onder `location` in DataSet moet worden ingesteld op **HdfsLocation**. | Ja      |
| folderPath | Het pad naar de map. Als u een Joker teken wilt gebruiken om de map te filteren, slaat u deze instelling over en geeft u de instellingen voor de activiteit bron op. | Nee       |
| fileName   | De bestands naam onder de opgegeven folderPath. Als u Joker tekens wilt gebruiken om bestanden te filteren, slaat u deze instelling over en geeft u de instellingen van de activiteit bron op. | Nee       |

> [!NOTE]
> **File share** type-gegevensset met Parquet/Text-indeling die in de volgende sectie wordt vermeld, wordt nog steeds ondersteund als-is voor de activiteit Copy/lookup voor achterwaartse compatibiliteit. U wordt aangeraden dit nieuwe model verder te gebruiken en de gebruikers interface van de ADF-ontwerp functie is overgeschakeld op het genereren van deze nieuwe typen.

**Voorbeeld:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
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

### <a name="other-format-dataset"></a>Gegevensset voor andere indeling

Als u gegevens wilt kopiëren van HDFS in de **Orc-indeling**, worden de volgende eigenschappen ondersteund:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van de gegevensset moet worden ingesteld op: **FileShare** |Ja |
| folderPath | Pad naar de map. Het Joker teken filter wordt ondersteund, toegestane joker tekens `*` zijn: (komt overeen met nul of `?` meer tekens) en (komt overeen met nul `^` of één teken); gebruik om te escapen als uw werkelijke bestands naam Joker teken of escape-teken bevat. <br/><br/>Voor beelden: root folder/submap/, zie voor beelden van [mappen en bestands filters](#folder-and-file-filter-examples)voor meer voor beelden. |Ja |
| fileName |  **Naam of het jokerteken filter** voor de bestanden die onder het opgegeven 'folderPath'. Als u een waarde voor deze eigenschap niet opgeeft, wordt de gegevensset verwijst naar alle bestanden in de map. <br/><br/>Voor het filter toegestane jokertekens zijn: `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken).<br/>-Voorbeeld 1: `"fileName": "*.csv"`<br/>-Voorbeeld 2: `"fileName": "???20180427.txt"`<br/>Gebruik `^` om te escapen als uw werkelijke mapnaam Joker teken of escape-teken bevat. |Nee |
| modifiedDatetimeStart | Bestanden filteren op basis van het kenmerk: Laatst gewijzigd. De bestanden worden geselecteerd als hun laatst gewijzigd binnen het tijdsinterval tussen zijn `modifiedDatetimeStart` en `modifiedDatetimeEnd`. De tijd wordt toegepast op de UTC-tijdzone in de notatie ' 2018-12-01T05:00:00Z '. <br/><br/> Houd er rekening mee dat de prestaties van het verplaatsen van gegevens worden beïnvloed door deze instelling in te scha kelen wanneer u bestands filter van enorme hoeveel heden bestanden wilt uitvoeren. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestands kenmerken wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` datum / tijdwaarde heeft, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het kenmerk Laatst gewijzigde groter dan is of gelijk is aan de datum / tijdwaarde wordt geselecteerd.  Wanneer `modifiedDatetimeEnd` datum / tijdwaarde heeft, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het kenmerk Laatst gewijzigde lager is dan de datum / tijdwaarde wordt geselecteerd.| Nee |
| modifiedDatetimeEnd | Bestanden filteren op basis van het kenmerk: Laatst gewijzigd. De bestanden worden geselecteerd als hun laatst gewijzigd binnen het tijdsinterval tussen zijn `modifiedDatetimeStart` en `modifiedDatetimeEnd`. De tijd wordt toegepast op de UTC-tijdzone in de notatie ' 2018-12-01T05:00:00Z '. <br/><br/> Houd er rekening mee dat de prestaties van het verplaatsen van gegevens worden beïnvloed door deze instelling in te scha kelen wanneer u bestands filter van enorme hoeveel heden bestanden wilt uitvoeren. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestands kenmerken wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` datum / tijdwaarde heeft, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het kenmerk Laatst gewijzigde groter dan is of gelijk is aan de datum / tijdwaarde wordt geselecteerd.  Wanneer `modifiedDatetimeEnd` datum / tijdwaarde heeft, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het kenmerk Laatst gewijzigde lager is dan de datum / tijdwaarde wordt geselecteerd.| Nee |
| format | Als u wilt **bestanden als kopiëren-is** overslaan tussen op basis van bestanden (binaire kopie), het gedeelte indeling in beide definities van de gegevensset voor invoer en uitvoer.<br/><br/>Als u bestanden wilt parseren met een specifieke indeling, worden de volgende typen bestands indelingen ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **type** eigenschap onder indeling op een van deze waarden. Zie voor meer informatie, [tekstindeling](supported-file-formats-and-compression-codecs.md#text-format), [Json-indeling](supported-file-formats-and-compression-codecs.md#json-format), [Avro-indeling](supported-file-formats-and-compression-codecs.md#avro-format), [Orc-indeling](supported-file-formats-and-compression-codecs.md#orc-format), en [Parquet-indeling](supported-file-formats-and-compression-codecs.md#parquet-format) secties. |Nee (alleen voor binaire kopie-scenario) |
| compression | Geef het type en het niveau van compressie voor de gegevens. Zie voor meer informatie, [ondersteunde indelingen en codecs voor compressie](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Ondersteunde typen zijn: **Gzip**, **Deflate**, **bzip2**en **ZipDeflate**.<br/>Ondersteunde niveaus zijn: **Optimaal** en **snelst**. |Nee |

>[!TIP]
>Alle bestanden in een map wilt kopiëren, geef **folderPath** alleen.<br>Voor het kopiëren van één bestand met een specifieke naam, geef **folderPath** met maponderdeel en **fileName** met bestandsnaam.<br>Als u wilt kopiëren van een subset van de bestanden in een map, geef **folderPath** met maponderdeel en **fileName** met filteren op jokerteken.

**Voorbeeld:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
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

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de bron HDFS.

### <a name="hdfs-as-source"></a>HDFS als bron

- Als u wilt kopiëren van **Parquet, tekst met scheidings tekens, JSON, AVRO en binaire indeling**, raadpleegt u de sectie [Parquet, tekst met scheidings tekens, JSON, AVRO en binaire indelings bron](#format-based-source) .
- Als u wilt kopiëren van andere indelingen, zoals de **indeling Orc**, raadpleegt u de sectie [andere indelings bron](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, tekst met scheidings tekens, JSON, AVRO en binaire indelings bron

Als u gegevens wilt kopiëren uit **Parquet, tekst met scheidings tekens, JSON, AVRO en binaire indeling**, raadpleegt u [Parquet-indeling](format-parquet.md), [tekst indeling met scheidings tekens](format-delimited-text.md), [Avro-indeling](format-avro.md) en [binaire indeling](format-binary.md) artikel op op indeling gebaseerde Kopieer activiteit bron en ondersteund Instellingen. De volgende eigenschappen worden ondersteund voor HDFS onder `storeSettings` instellingen in op Format-gebaseerde Kopieer Bron:

| Eigenschap                 | Description                                                  | Vereist                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| Type                     | De eigenschap type onder `storeSettings` moet worden ingesteld op **HdfsReadSetting**. | Ja                                           |
| recursive                | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen voor de opgegeven map. Houd er rekening mee dat wanneer recursieve is ingesteld op true en de sink is een opslagplaats op basis van bestanden, een lege map of submap is niet gekopieerd of gemaakt in de sink. Toegestane waarden zijn **waar** (standaard) en **false**. | Nee                                            |
| wildcardFolderPath       | Het mappad met Joker tekens om de bron mappen te filteren. <br>Toegestane joker tekens zijn: `*` (komt overeen met nul of meer tekens `?` ) en (komt overeen met nul of één `^` teken); gebruik om te escapen als uw werkelijke mapnaam Joker teken of escape-teken bevat. <br>Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). | Nee                                            |
| wildcardFileName         | De naam van het bestand met Joker tekens onder de opgegeven folderPath/wildcardFolderPath voor het filteren van bron bestanden. <br>Toegestane joker tekens zijn: `*` (komt overeen met nul of meer tekens `?` ) en (komt overeen met nul of één `^` teken); gebruik om te escapen als uw werkelijke mapnaam Joker teken of escape-teken bevat.  Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). | Ja als `fileName` niet is opgegeven in de gegevensset |
| modifiedDatetimeStart    | Bestanden filteren op basis van het kenmerk: Laatst gewijzigd. De bestanden worden geselecteerd als hun laatst gewijzigd binnen het tijdsinterval tussen zijn `modifiedDatetimeStart` en `modifiedDatetimeEnd`. De tijd wordt toegepast op de UTC-tijdzone in de notatie ' 2018-12-01T05:00:00Z '. <br> De eigenschappen is NULL. Dit betekent dat er geen kenmerk bestandsfilter worden toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` datum / tijdwaarde heeft, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het kenmerk Laatst gewijzigde groter dan is of gelijk is aan de datum / tijdwaarde wordt geselecteerd.  Wanneer `modifiedDatetimeEnd` datum / tijdwaarde heeft, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het kenmerk Laatst gewijzigde lager is dan de datum / tijdwaarde wordt geselecteerd. | Nee                                            |
| modifiedDatetimeEnd      | Hetzelfde als hierboven.                                               | Nee                                            |
| distcpSettings | Eigenschaps groep bij gebruik van HDFS DistCp. | Nee |
| resourceManagerEndpoint | Het eind punt van het garen van een andere bron | Ja als u DistCp gebruikt |
| tempScriptPath | Een mappad dat wordt gebruikt voor het opslaan van het tijdelijke DistCp-opdracht script. Het script bestand wordt gegenereerd door Data Factory en wordt verwijderd nadat de Kopieer taak is voltooid. | Ja als u DistCp gebruikt |
| distcpOptions | Aanvullende opties voor de opdracht DistCp. | Nee |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met opslag archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee                                            |

> [!NOTE]
> Voor een Parquet/gescheiden tekst indeling wordt de bron van de Kopieer activiteit van het type **FileSystemSource** die in de volgende sectie wordt vermeld, nog steeds ondersteund als-is voor achterwaartse compatibiliteit. U wordt aangeraden dit nieuwe model verder te gebruiken en de gebruikers interface van de ADF-ontwerp functie is overgeschakeld op het genereren van deze nieuwe typen.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
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
                    "type": "HdfsReadSetting",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Andere indelings bron

Als u gegevens wilt kopiëren van HDFS in **Orc-indeling**, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van de bron van de Kopieer activiteit moet worden ingesteld op: **HdfsSource** |Ja |
| recursive | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen voor de opgegeven map. Houd er rekening mee wanneer recursieve is ingesteld op true en sink is opslag op basis van bestanden, lege map/ondergeschikt-folder worden niet gekopieerd/gemaakt bij de sink.<br/>Toegestane waarden zijn: **waar** (standaard), **false** | Nee |
| distcpSettings | Eigenschaps groep bij gebruik van HDFS DistCp. | Nee |
| resourceManagerEndpoint | Het eind punt van het garen van een andere bron | Ja als u DistCp gebruikt |
| tempScriptPath | Een mappad dat wordt gebruikt voor het opslaan van het tijdelijke DistCp-opdracht script. Het script bestand wordt gegenereerd door Data Factory en wordt verwijderd nadat de Kopieer taak is voltooid. | Ja als u DistCp gebruikt |
| distcpOptions | Aanvullende opties voor de opdracht DistCp. | Nee |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met opslag archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee |

**Voorbeeld: HDFS-bron in Kopieer activiteit met behulp van DistCp**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

Meer informatie over het gebruik van DistCp voor het efficiënt kopiëren van gegevens van HDFS vanuit de volgende sectie.

### <a name="folder-and-file-filter-examples"></a>Voor beelden van map-en bestands filter

In deze sectie wordt het resulterende gedrag van het mappad en de bestands naam met Joker teken filters beschreven.

| folderPath | fileName             | recursive | De structuur van de bronmap en het filter resultaat ( **vetgedrukte** bestanden worden opgehaald) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (leeg, standaard instelling gebruiken) | false     | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (leeg, standaard instelling gebruiken) | true      | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | false     | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>DistCp gebruiken om gegevens te kopiëren van HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) is een Hadoop native opdracht regel programma voor het uitvoeren van gedistribueerde kopieën in een Hadoop-cluster. Wanneer u een Distcp-opdracht uitvoert, worden eerst alle bestanden die moeten worden gekopieerd, in het Hadoop-cluster gemaakt en worden er met elke toewijzings taak binaire kopie van bron naar sink.

Ondersteuning voor kopieer activiteiten met behulp van DistCp voor het kopiëren van bestanden als-is naar Azure Blob (inclusief [gefaseerde kopie](copy-activity-performance.md)) of Azure data Lake Store, in dat geval kan de stroom van het cluster volledig worden benut in plaats van dat het zelf-hostende Integration runtime wordt uitgevoerd. Het biedt een betere Kopieer doorvoer, vooral als uw cluster zeer krachtig is. Kopieer activiteit maakt op basis van uw configuratie in Azure Data Factory automatisch een distcp-opdracht, verzendt deze naar uw Hadoop-cluster en bewaak de Kopieer status.

### <a name="prerequisites"></a>Vereisten

Als u DistCp wilt gebruiken om bestanden te kopiëren als-afkomstig van HDFS naar Azure Blob (inclusief gefaseerde kopie) of Azure Data Lake Store, moet u ervoor zorgen dat uw Hadoop-cluster voldoet aan onderstaande vereisten:

1. De services MapReduce en garens zijn ingeschakeld.
2. De versie van garen is 2,5 of hoger.
3. HDFS-server is geïntegreerd met uw doel gegevens opslag-Azure Blob of Azure Data Lake Store:

    - Het Azure Blob-bestands systeem wordt standaard ondersteund sinds Hadoop 2,7. U hoeft alleen jar-pad op te geven in de configuratie van Hadoop-env.
    - Azure Data Lake Store bestands systeem wordt verpakt vanaf Hadoop 3.0.0-alpha1. Als uw Hadoop-cluster lager is dan die versie, moet u de ADLS gerelateerde jar-pakketten (Azure-datalake-Store. jar) hand matig in het cluster importeren en het jar-pad opgeven in de [configuratie van de](https://hadoop.apache.org/releases.html)Hadoop-env.

4. Bereid een tijdelijke map voor in HDFS. Deze tijdelijke map wordt gebruikt voor het opslaan van het DistCp-shell script, waardoor er ruimte op KB-niveau in beslag neemt.
5. Zorg ervoor dat het gebruikers account dat is verstrekt in HDFS linked service gemachtigd is om een toepassing in garens te verzenden. b) de machtiging voor het maken van submap-, lees-en schrijf bestanden onder de map Temp.

### <a name="configurations"></a>Configuraties

Zie DistCp-gerelateerde configuraties en voor beelden in [HDFS als bron](#hdfs-as-source) sectie.

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Kerberos-verificatie gebruiken voor HDFS-connector

Er zijn twee opties om de on-premises omgeving in te stellen, zodat u Kerberos-verificatie kunt gebruiken in HDFS-connector. U kunt kiezen welk het beste past bij uw situatie.
* Optie 1: [Een zelf-hostende Integration Runtime machine toevoegen aan een Kerberos-realm](#kerberos-join-realm)
* Optie 2: [Wederzijdse vertrouwens relatie tussen Windows-domein en Kerberos-realm inschakelen](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Optie 1: Een zelf-hostende Integration Runtime machine toevoegen aan een Kerberos-realm

#### <a name="requirements"></a>Vereisten

* De zelf-hostende Integration Runtime computer moet worden toegevoegd aan de Kerberos-realm en kan niet worden toegevoegd aan een Windows-domein.

#### <a name="how-to-configure"></a>Configureren

**Op een zelf-hostende Integration Runtime computer:**

1.  Voer het hulp programma **Ksetup** uit om de Kerberos KDC-server en-realm te configureren.

    De machine moet worden geconfigureerd als lid van een werk groep, omdat een Kerberos-realm afwijkt van een Windows-domein. Dit kan worden bereikt door de Kerberos-realm in te stellen en een KDC-server als volgt toe te voegen. Vervang *realm.com* door uw eigen respectieve realm als dat nodig is.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Start** de computer opnieuw op nadat u deze twee opdrachten hebt uitgevoerd.

2.  Controleer de configuratie met de opdracht **Ksetup** . De uitvoer moet er als volgt uitzien:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**In Azure Data Factory:**

* Configureer de HDFS-connector met behulp van **Windows-verificatie** samen met uw Principal-naam en-wacht woord voor Kerberos om verbinding te maken met de HDFS-gegevens bron. Raadpleeg de sectie eigenschappen van koppelen aan de [gekoppelde service](#linked-service-properties) in configuratie details.

### <a name="kerberos-mutual-trust"></a>Optie 2: Wederzijdse vertrouwens relatie tussen Windows-domein en Kerberos-realm inschakelen

#### <a name="requirements"></a>Vereisten

*   De zelf-hostende Integration Runtime computer moet lid worden van een Windows-domein.
*   U moet gemachtigd zijn om de instellingen van de domein controller bij te werken.

#### <a name="how-to-configure"></a>Configureren

> [!NOTE]
> Vervang REALM.COM en AD.COM in de volgende zelf studie met uw eigen respectievelijke realm en domein controller naar behoefte.

**Op KDC-server:**

1. Bewerk de KDC-configuratie in het bestand **krb5. conf** zodat het Windows-domein KDC kan vertrouwen naar de volgende configuratie sjabloon. De configuratie bevindt zich standaard op **/etc/krb5.conf**.

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log
            
           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true
            
           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }
            
           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM
            
           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   **Start** de KDC-service na de configuratie opnieuw.

2. Bereid een principal met de naam **krbtgt/realm\@. com ad.com** in KDC server met de volgende opdracht:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. Voeg in **Hadoop. Security. auth_to_local** HDFS service configuration file toe `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**Op domein controller:**

1.  Voer de volgende **Ksetup** -opdrachten uit om een realm-vermelding toe te voegen:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Een vertrouwens relatie tot stand brengen tussen Windows-domein en Kerberos-realm. [wacht woord] is het wacht woord voor de principal **krbtgt/realm\@. com-ad.com**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Selecteer het versleutelings algoritme dat in Kerberos wordt gebruikt.

    1. Ga naar Serverbeheer > groepsbeleid beheer > domein >-Groepsbeleid objecten > het standaard beleid of het actieve domein en bewerk.

    2. Ga in het pop-upvenster **Groepsbeleidsbeheer-editor** naar computer configuratie > beleid > Windows-instellingen > beveiligings instellingen > lokale beleids regels > beveiligings opties **en configureer netwerk beveiliging: Configureer de versleutelings typen**die voor Kerberos zijn toegestaan.

    3. Selecteer de versleutelings algoritme die u wilt gebruiken bij het maken van verbinding met KDC. Normaal gesp roken hoeft u alleen maar alle opties te selecteren.

        ![Configuratie versleutelings typen voor Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Gebruik de opdracht **Ksetup** om de versleutelings algoritme op te geven die moet worden gebruikt voor de specifieke realm.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Maak de toewijzing tussen het domein account en de Kerberos-principal, zodat u Kerberos principal in het Windows-domein kunt gebruiken.

    1. Start de beheer Programma's > **Active Directory gebruikers en computers**.

    2. Configureer geavanceerde functies door te klikken op**geavanceerde functies** **weer geven** > .

    3. Zoek het account waaraan u toewijzingen wilt maken en klik met de rechter muisknop om **naam toewijzingen** weer te geven > op het tabblad **Kerberos-namen** .

    4. Voeg een principal uit de realm toe.

        ![Beveiligings identiteit toewijzen](media/connector-hdfs/map-security-identity.png)

**Op een zelf-hostende Integration Runtime computer:**

* Voer de volgende **Ksetup** -opdrachten uit om een realm-vermelding toe te voegen.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**In Azure Data Factory:**

* Configureer de HDFS-connector met behulp van **Windows-verificatie** samen met uw domein account of Kerberos-principal om verbinding te maken met de HDFS-gegevens bron. Raadpleeg de sectie eigenschappen van koppelen aan de [gekoppelde service](#linked-service-properties) in configuratie details.


## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats).
