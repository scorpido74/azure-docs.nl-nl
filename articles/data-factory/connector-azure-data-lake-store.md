---
title: Gegevens kopiëren naar of van Azure Data Lake Storage Gen1 met behulp van Data Factory
description: Informatie over het kopiëren van gegevens uit ondersteunde brongegevens archieven naar Azure Data Lake Store, of van Data Lake Store naar ondersteunde Sink-archieven, met behulp van Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 2aef04c4fe4713b107abe53fe459b7859a9c714e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681276"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Gegevens kopiëren naar of van Azure Data Lake Storage Gen1 met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Azure Data Factory die u gebruikt:"]
> * [Versie 1](v1/data-factory-azure-datalake-connector.md)
> * [Huidige versie](connector-azure-data-lake-store.md)

In dit artikel wordt beschreven hoe u gegevens kopieert van en naar Azure Data Lake Storage Gen1. Lees het [artikel Inleiding](introduction.md)voor meer informatie over Azure Data Factory.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Data Lake Storage Gen1-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md) 
- [Gegevens stroom toewijzen](concepts-data-flow-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)

Met name met deze connector kunt u het volgende doen:

- Kopieer bestanden met behulp van een van de volgende verificatie methoden: Service-Principal of beheerde identiteiten voor Azure-resources.
- Kopieer bestanden als bestand of parser of Genereer bestanden met de [ondersteunde bestands indelingen en compressie-codecs](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Als u gegevens kopieert met behulp van de zelf-hostende Integration runtime, configureert u de bedrijfs firewall om uitgaand verkeer toe te staan voor `<ADLS account name>.azuredatalakestore.net` en `login.microsoftonline.com/<tenant>/oauth2/token` op poort 443. De laatste is de Azure-beveiligings token service waarmee de Integration runtime moet communiceren om het toegangs token op te halen.

## <a name="get-started"></a>Aan de slag

> [!TIP]
> Zie [gegevens laden in azure data Lake Store](load-azure-data-lake-store.md)voor een overzicht van het gebruik van de Azure data Lake Store-connector.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Azure Data Lake Store.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de Azure Data Lake Store gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap `type` moet worden ingesteld op **AzureDataLakeStore**. | Ja |
| dataLakeStoreUri | Informatie over het Azure Data Lake Store-account. Deze informatie heeft een van de volgende indelingen: `https://[accountname].azuredatalakestore.net/webhdfs/v1` of `adl://[accountname].azuredatalakestore.net/`. | Ja |
| subscriptionId | De Azure-abonnements-ID waartoe het Data Lake Store-account behoort. | Vereist voor Sink |
| resourceGroupName | De naam van de Azure-resource groep waartoe het Data Lake Store-account behoort. | Vereist voor Sink |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt de Azure Integration runtime of een zelf-hostende Integration runtime gebruiken als uw gegevens archief zich in een particulier netwerk bevindt. Als deze eigenschap niet is opgegeven, wordt de standaard Azure Integration runtime gebruikt. |Nee |

### <a name="use-service-principal-authentication"></a>Service-Principal-verificatie gebruiken

Voer de volgende stappen uit om Service-Principal-verificatie te gebruiken.

1. Registreer een toepassings entiteit in Azure Active Directory en verleen deze toegang tot Data Lake Store. Zie [service-to-service-verificatie](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)voor gedetailleerde stappen. Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:

    - Toepassings-id
    - Toepassings sleutel
    - Tenant-id

2. Verleen de service-principal de juiste machtigingen. Bekijk voor beelden van de werking van machtigingen in Data Lake Storage Gen1 van [toegangs beheer in azure data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Als bron**: voor **toegang tot** > van **Data Explorer** , geeft u ten minste **uitvoerings** machtigingen voor alle stroomopwaarts mappen, inclusief de hoofdmap, samen met de machtiging **lezen** voor de bestanden die moeten worden gekopieerd. U kunt ervoor kiezen om toe te voegen aan **deze map en alle onderliggende items** voor recursief, en toe te voegen als **een toegangs machtiging en een standaard machtigings vermelding**. Er is geen vereiste op toegangs beheer op account niveau (IAM).
    - **Als Sink**: Wijs in **Data Explorer** > **toegang**ten minste **uitvoerings** machtiging toe voor alle stroomopwaarts mappen, inclusief de hoofdmap, en **Schrijf** machtiging voor de map sink. U kunt ervoor kiezen om toe te voegen aan **deze map en alle onderliggende items** voor recursief, en toe te voegen als **een toegangs machtiging en een standaard machtigings vermelding**. Als u een Azure Integration runtime gebruikt om te kopiëren (zowel de bron-als de Sink bevinden zich in de Cloud), moet u in IAM ten minste de rol van **lezer** toekennen om Data Factory de regio voor data Lake Store te laten detecteren. Als u deze IAM-rol wilt vermijden, maakt u expliciet [een Azure Integration runtime](create-azure-integration-runtime.md#create-azure-ir) met de locatie van data Lake Store. Als uw Data Lake Store bijvoorbeeld in Europa-west is, maakt u een Azure Integration runtime waarbij de locatie is ingesteld op ' Europa-west '. Koppel ze aan de Data Lake Store gekoppelde service, zoals wordt weer gegeven in het volgende voor beeld.

De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| servicePrincipalId | Geef de client-ID van de toepassing op. | Ja |
| servicePrincipalKey | Geef de sleutel van de toepassing op. Markeer dit veld als `SecureString` om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| bouw | Geef de Tenant gegevens op, zoals de domein naam of Tenant-ID, waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Ja |

**Voorbeeld:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a>Beheerde identiteiten gebruiken voor Azure-bronnen verificatie

Een data factory kan worden gekoppeld aan een [beheerde identiteit voor Azure-resources](data-factory-service-identity.md)die deze specifieke Data Factory vertegenwoordigt. U kunt deze beheerde identiteit rechtstreeks gebruiken voor Data Lake Store verificatie, vergelijkbaar met het gebruik van uw eigen service-principal. Hiermee kan deze toegewezen Factory toegang krijgen tot gegevens en deze kopiëren naar of van Data Lake Store.

Voer de volgende stappen uit om beheerde identiteiten te gebruiken voor Azure-bronnen verificatie.

1. [Haal de Data Factory beheerde identiteits gegevens](data-factory-service-identity.md#retrieve-managed-identity) op door de waarde te kopiëren van de toepassings-id van de service-identiteit die samen met uw fabriek is gegenereerd.

2. Verleen de beheerde identiteit toegang tot Data Lake Store. Bekijk voor beelden van de werking van machtigingen in Data Lake Storage Gen1 van [toegangs beheer in azure data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Als bron**: voor **toegang tot** > van **Data Explorer** , geeft u ten minste **uitvoerings** machtigingen voor alle stroomopwaarts mappen, inclusief de hoofdmap, samen met de machtiging **lezen** voor de bestanden die moeten worden gekopieerd. U kunt ervoor kiezen om toe te voegen aan **deze map en alle onderliggende items** voor recursief, en toe te voegen als **een toegangs machtiging en een standaard machtigings vermelding**. Er is geen vereiste op toegangs beheer op account niveau (IAM).
    - **Als Sink**: Wijs in **Data Explorer** > **toegang**ten minste **uitvoerings** machtiging toe voor alle stroomopwaarts mappen, inclusief de hoofdmap, en **Schrijf** machtiging voor de map sink. U kunt ervoor kiezen om toe te voegen aan **deze map en alle onderliggende items** voor recursief, en toe te voegen als **een toegangs machtiging en een standaard machtigings vermelding**. Als u een Azure Integration runtime gebruikt om te kopiëren (zowel de bron-als de Sink bevinden zich in de Cloud), moet u in IAM ten minste de rol van **lezer** toekennen om Data Factory de regio voor data Lake Store te laten detecteren. Als u deze IAM-rol wilt vermijden, maakt u expliciet [een Azure Integration runtime](create-azure-integration-runtime.md#create-azure-ir) met de locatie van data Lake Store. Koppel ze aan de Data Lake Store gekoppelde service, zoals wordt weer gegeven in het volgende voor beeld.

In Azure Data Factory hoeft u geen eigenschappen op te geven naast de algemene Data Lake Store informatie in de gekoppelde service.

**Voorbeeld:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor Azure Data Lake Store gen1 onder `location` instellingen in de op indeling gebaseerde gegevensset:

| Eigenschap   | Beschrijving                                                  | Vereist |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | De eigenschap type onder `location` in de gegevensset moet worden ingesteld op **AzureDataLakeStoreLocation**. | Ja      |
| folderPath | Het pad naar een map. Als u een Joker teken wilt gebruiken om mappen te filteren, slaat u deze instelling over en geeft u deze op in de activiteiten bron instellingen. | Nee       |
| fileName   | De bestands naam onder de opgegeven folderPath. Als u een Joker teken wilt gebruiken om bestanden te filteren, slaat u deze instelling over en geeft u deze op in de activiteiten bron instellingen. | Nee       |

**Voorbeeld:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
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

### <a name="legacy-dataset-model"></a>Verouderd gegevensset-model

>[!NOTE]
>Het volgende gegevensset model wordt nog steeds ondersteund voor compatibiliteit met eerdere versies. U wordt aangeraden het nieuwe model te gebruiken dat hierboven wordt genoemd en de gebruikers interface van de ADF-ontwerp functie is overgeschakeld op het genereren van het nieuwe model.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de DataSet moet worden ingesteld op **AzureDataLakeStoreFile**. |Ja |
| folderPath | Pad naar de map in Data Lake Store. Als deze niet wordt opgegeven, verwijst deze naar de hoofdmap. <br/><br/>Het Joker teken filter wordt ondersteund. Toegestane joker tekens zijn `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken). Gebruik `^` om te escapen als uw werkelijke mapnaam een Joker teken of escape-teken bevat. <br/><br/>Bijvoorbeeld: root folder/submap/. Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). |Nee |
| fileName | De naam of het Joker teken filter voor de bestanden onder het opgegeven folderPath. Als u geen waarde opgeeft voor deze eigenschap, wijst de gegevensset naar alle bestanden in de map. <br/><br/>Voor het filter zijn de toegestane joker tekens `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken).<br/>-Voor beeld 1: `"fileName": "*.csv"`<br/>-Voor beeld 2: `"fileName": "???20180427.txt"`<br/>Gebruik `^` om te escapen als uw werkelijke bestands naam een Joker teken of escape-teken bevat.<br/><br/>Als er geen bestands naam is opgegeven voor een uitvoer-gegevensset en **preserveHierarchy** niet is opgegeven in de Sink van de activiteit, genereert de Kopieer activiteit automatisch de bestands naam met het volgende patroon: "*gegevens. [ Run ID-GUID van activiteit]. [GUID if FlattenHierarchy]. [indeling indien geconfigureerd]. [compressie indien geconfigureerd]* ', bijvoorbeeld ' data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt. gz '. Als u van een bron in tabel vorm kopieert met behulp van een tabel naam in plaats van een query, is het naam patroon ' *[tabel naam]. [ indeling]. [compressie indien geconfigureerd]* ', bijvoorbeeld ' mytable. csv '. |Nee |
| modifiedDatetimeStart | Bestanden filteren op basis van het kenmerk dat het laatst is gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik ligt tussen `modifiedDatetimeStart` en `modifiedDatetimeEnd`. De tijd wordt toegepast op de UTC-tijd zone in de notatie "2018-12-01T05:00:00Z". <br/><br/> De algehele prestaties van het verplaatsen van gegevens worden beïnvloed door deze instelling in te scha kelen wanneer u bestands filter wilt uitvoeren met enorme hoeveel heden bestanden. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen bestands kenmerk filter op de gegevensset wordt toegepast. Als `modifiedDatetimeStart` een datum/tijd-waarde heeft, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter is dan of gelijk is aan de datum/tijd-waarde zijn geselecteerd. Als `modifiedDatetimeEnd` een datum/tijd-waarde heeft, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is, kleiner zijn dan de waarde voor datum/tijd.| Nee |
| modifiedDatetimeEnd | Bestanden filteren op basis van het kenmerk dat het laatst is gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik ligt tussen `modifiedDatetimeStart` en `modifiedDatetimeEnd`. De tijd wordt toegepast op de UTC-tijd zone in de notatie "2018-12-01T05:00:00Z". <br/><br/> De algehele prestaties van het verplaatsen van gegevens worden beïnvloed door deze instelling in te scha kelen wanneer u bestands filter wilt uitvoeren met enorme hoeveel heden bestanden. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen bestands kenmerk filter op de gegevensset wordt toegepast. Als `modifiedDatetimeStart` een datum/tijd-waarde heeft, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter is dan of gelijk is aan de datum/tijd-waarde zijn geselecteerd. Als `modifiedDatetimeEnd` een datum/tijd-waarde heeft, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is, kleiner zijn dan de waarde voor datum/tijd.| Nee |
| Formatteer | Als u bestanden wilt kopiëren als zich bevindt tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie opmaak over in de gegevensset voor invoer en uitvoer.<br/><br/>Als u bestanden wilt parseren of genereren met een specifieke indeling, worden de volgende typen bestands indelingen ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**en **ParquetFormat**. Stel de eigenschap **type** onder **indeling** in op een van deze waarden. Zie de secties [tekst indeling](supported-file-formats-and-compression-codecs.md#text-format), [JSON-indeling](supported-file-formats-and-compression-codecs.md#json-format), [Avro](supported-file-formats-and-compression-codecs.md#avro-format)-indeling, [Orc-indeling](supported-file-formats-and-compression-codecs.md#orc-format)en [Parquet-indeling](supported-file-formats-and-compression-codecs.md#parquet-format) voor meer informatie. |Nee (alleen voor het scenario binair kopiëren) |
| compressie | Geef het type en compressie niveau voor de gegevens op. Zie [ondersteunde bestands indelingen en compressie-codecs](supported-file-formats-and-compression-codecs.md#compression-support)voor meer informatie.<br/>Ondersteunde typen zijn **gzip**, **Deflate**, **bzip2**en **ZipDeflate**.<br/>Ondersteunde niveaus zijn **optimaal** en **snelst**. |Nee |

>[!TIP]
>Als u alle bestanden in een map wilt kopiëren, geeft u alleen **FolderPath** op.<br>Als u één bestand met een bepaalde naam wilt kopiëren, geeft u **FolderPath** op met een mappen onderdeel en **filename** met een bestands naam.<br>Als u een subset van bestanden onder een map wilt kopiëren, geeft u **FolderPath** op met een deel van een map en een **Bestands naam** met een Joker teken filter. 

**Voorbeeld:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
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

Voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, Zie [pijp lijnen](concepts-pipelines-activities.md). In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door Azure Data Lake Store bron en Sink.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store als bron

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor Azure Data Lake Store gen1 onder `storeSettings` instellingen in de indelings-gebaseerde Kopieer Bron:

| Eigenschap                 | Beschrijving                                                  | Vereist                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | De eigenschap type onder `storeSettings` moet zijn ingesteld op **AzureDataLakeStoreReadSetting**. | Ja                                           |
| recursieve                | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. Als recursief is ingesteld op True en de Sink een archief op basis van bestanden is, wordt een lege map of submap niet gekopieerd of gemaakt bij de sink. Toegestane waarden zijn **True** (standaard) en **Onwaar**. | Nee                                            |
| wildcardFolderPath       | Het mappad met Joker tekens om de bron mappen te filteren. <br>Toegestane joker tekens zijn `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken). Gebruik `^` om te escapen als uw werkelijke mapnaam een Joker teken of escape-teken bevat. <br>Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). | Nee                                            |
| wildcardFileName         | De naam van het bestand met Joker tekens onder de opgegeven folderPath/wildcardFolderPath voor het filteren van bron bestanden. <br>Toegestane joker tekens zijn `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken). Gebruik `^` om te escapen als uw werkelijke mapnaam een Joker teken of escape-teken bevat. Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). | Ja als `fileName` niet is opgegeven in de gegevensset |
| modifiedDatetimeStart    | Bestanden filteren op basis van het kenmerk dat het laatst is gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik ligt tussen `modifiedDatetimeStart` en `modifiedDatetimeEnd`. De tijd wordt toegepast op de UTC-tijd zone in de notatie "2018-12-01T05:00:00Z". <br> De eigenschappen kunnen NULL zijn, wat betekent dat er geen bestands kenmerk filter op de gegevensset wordt toegepast. Als `modifiedDatetimeStart` een datum/tijd-waarde heeft, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter is dan of gelijk is aan de datum/tijd-waarde zijn geselecteerd. Als `modifiedDatetimeEnd` een datum/tijd-waarde heeft, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is, kleiner zijn dan de waarde voor datum/tijd. | Nee                                            |
| modifiedDatetimeEnd      | Hetzelfde als hierboven.                                               | Nee                                            |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig met een opslag archief moet worden verbonden. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee                                            |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
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
                    "type": "AzureDataLakeStoreReadSetting",
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

#### <a name="legacy-source-model"></a>Verouderd bron model

>[!NOTE]
>Het volgende Kopieer bron model wordt nog steeds ondersteund voor compatibiliteit met eerdere versies. U wordt aangeraden het nieuwe model te gebruiken dat hierboven wordt beschreven en de gebruikers interface van de ADF-ontwerp functie is overgeschakeld op het genereren van het nieuwe model.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap `type` van de bron van de Kopieer activiteit moet zijn ingesteld op **AzureDataLakeStoreSource**. |Ja |
| recursieve | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. Als `recursive` is ingesteld op True en de Sink een archief op basis van bestanden is, wordt een lege map of submap niet gekopieerd of gemaakt bij de sink. Toegestane waarden zijn **True** (standaard) en **Onwaar**. | Nee |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met het gegevens archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store als Sink

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor Azure Data Lake Store gen1 onder `storeSettings` instellingen in het op indeling gebaseerde Kopieer-Sink:

| Eigenschap                 | Beschrijving                                                  | Vereist |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | De eigenschap type onder `storeSettings` moet zijn ingesteld op **AzureDataLakeStoreWriteSetting**. | Ja      |
| copyBehavior             | Hiermee wordt het Kopieer gedrag gedefinieerd wanneer de bron bestanden van een gegevens archief op basis van een bestand zijn.<br/><br/>Toegestane waarden zijn:<br/><b>-PreserveHierarchy (standaard instelling)</b>: behoudt de bestands hiërarchie in de doelmap. Het relatieve pad van het bron bestand naar de bronmap is identiek aan het relatieve pad van het doel bestand naar de doelmap.<br/><b>-FlattenHierarchy</b>: alle bestanden in de bronmap bevinden zich in het eerste niveau van de doelmap. De doel bestanden hebben automatisch gegenereerde namen. <br/><b>-MergeFiles</b>: alle bestanden van de bronmap worden samengevoegd met één bestand. Als de bestands naam is opgegeven, is de naam van het samengevoegde bestand de opgegeven naam. Anders is het een automatisch gegenereerde bestands naam. | Nee       |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met het gegevens archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee       |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
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
                    "type": "AzureDataLakeStoreWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="legacy-sink-model"></a>Verouderd Sink-model

>[!NOTE]
>Het volgende model voor het kopiëren van sinks wordt nog steeds ondersteund voor compatibiliteit met eerdere versies. U wordt aangeraden het nieuwe model te gebruiken dat hierboven wordt beschreven en de gebruikers interface van de ADF-ontwerp functie is overgeschakeld op het genereren van het nieuwe model.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap `type` van de Sink voor kopieer activiteiten moet worden ingesteld op **AzureDataLakeStoreSink**. |Ja |
| copyBehavior | Hiermee wordt het Kopieer gedrag gedefinieerd wanneer de bron bestanden van een gegevens archief op basis van een bestand zijn.<br/><br/>Toegestane waarden zijn:<br/><b>-PreserveHierarchy (standaard instelling)</b>: behoudt de bestands hiërarchie in de doelmap. Het relatieve pad van het bron bestand naar de bronmap is identiek aan het relatieve pad van het doel bestand naar de doelmap.<br/><b>-FlattenHierarchy</b>: alle bestanden in de bronmap bevinden zich in het eerste niveau van de doelmap. De doel bestanden hebben automatisch gegenereerde namen. <br/><b>-MergeFiles</b>: alle bestanden van de bronmap worden samengevoegd met één bestand. Als de bestands naam is opgegeven, is de naam van het samengevoegde bestand de opgegeven naam. Anders wordt de bestands naam automatisch gegenereerd. | Nee |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met het gegevens archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Voor beelden van map-en bestands filter

In deze sectie wordt het resulterende gedrag van het mappad en de bestands naam met Joker teken filters beschreven.

| folderPath | fileName | recursieve | De structuur van de bronmap en het filter resultaat ( **vetgedrukte** bestanden worden opgehaald)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Leeg, standaard instelling gebruiken) | onwaar | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**bestand1. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**bestand2. json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. CSV<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. CSV<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. CSV |
| `Folder*` | (Leeg, standaard instelling gebruiken) | true | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**bestand1. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**bestand2. json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. CSV**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. CSV |
| `Folder*` | `*.csv` | onwaar | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**bestand1. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2. json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. CSV<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. CSV<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. CSV |
| `Folder*` | `*.csv` | true | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**bestand1. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2. json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. CSV**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. CSV |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Voor beelden van gedrag van de Kopieer bewerking

In deze sectie wordt het resulterende gedrag van de Kopieer bewerking voor verschillende combi Naties van `recursive` en `copyBehavior` waarden beschreven.

| recursieve | copyBehavior | Structuur van bronmap | Resulterende doel |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | De doel-Map1 is gemaakt met dezelfde structuur als de bron:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | de doel-Map1 is gemaakt met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | de doel-Map1 is gemaakt met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1 + Bestand2 + File3 + File4 + File5 inhoud samengevoegd in één bestand, met een automatisch gegenereerde bestands naam. |
| onwaar |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | de doel-Map1 is gemaakt met de volgende structuur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/><br/>Subfolder1 met File3, File4 en File5 worden niet opgehaald. |
| onwaar |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | de doel-Map1 is gemaakt met de volgende structuur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/><br/>Subfolder1 met File3, File4 en File5 worden niet opgehaald. |
| onwaar |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | de doel-Map1 is gemaakt met de volgende structuur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1 + Bestand2-inhoud worden samengevoegd in één bestand met automatisch gegenereerde bestands naam. automatisch gegenereerde naam voor bestand1<br/><br/>Subfolder1 met File3, File4 en File5 worden niet opgehaald. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Acl's voor Data Lake Storage Gen2 behouden

Zie [acl's van data Lake Storage gen1 behouden](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1)als u de acl's (toegangs beheer lijsten) samen met gegevens bestanden wilt repliceren wanneer u een upgrade uitvoert van Data Lake Storage Gen1 naar Data Lake Storage Gen2.

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Meer informatie over [bron transformatie](data-flow-source.md) en [sink-trans formatie](data-flow-sink.md) vindt u in de functie gegevens stroom toewijzen.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="getmetadata-activity-properties"></a>Eigenschappen van GetMetadata-activiteit

Als u meer wilt weten over de eigenschappen, controleert u de [GetMetadata-activiteit](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Eigenschappen van activiteit verwijderen

Als u meer wilt weten over de eigenschappen, controleert u de [activiteit verwijderen](delete-activity.md)

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevens archieven](copy-activity-overview.md##supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
