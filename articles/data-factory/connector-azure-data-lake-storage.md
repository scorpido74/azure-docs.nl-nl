---
title: Gegevens kopiëren naar of van Azure Data Lake Storage Gen2 met behulp van Data Factory | Microsoft Docs
description: Meer informatie over het kopiëren van gegevens van en naar Azure Data Lake Storage Gen2 met behulp van Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 8f190f6b933c61072df9af954c8db01497e35e82
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010235"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Gegevens kopiëren naar of van Azure Data Lake Storage Gen2 met behulp van Azure Data Factory

Azure Data Lake Storage Gen2 (ADLS Gen2) is een set mogelijkheden die is toegewezen aan big data Analytics in [Azure Blob-opslag](../storage/blobs/storage-blobs-introduction.md). U kunt het gebruiken om de interface met uw gegevens te maken met behulp van zowel bestands systeem-als object opslag-modellen.

In dit artikel wordt beschreven hoe u gegevens kopieert van en naar Azure Data Lake Storage Gen2. Lees voor meer informatie over Azure Data Factory, de [inleidende artikel](introduction.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Data Lake Storage Gen2-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Gegevens stroom toewijzen](concepts-data-flow-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)

Met name met deze connector kunt u het volgende doen:

- Gegevens kopiëren met behulp van account sleutel, Service-Principal of beheerde identiteiten voor Azure-bronnen verificaties.
- Kopieer bestanden als bestand of parser of Genereer bestanden met [ondersteunde bestands indelingen en compressie-codecs](supported-file-formats-and-compression-codecs.md).

>[!TIP]
>Als u de hiërarchische naam ruimte inschakelt, is er momenteel geen interoperabiliteit van bewerkingen tussen Blob-en Data Lake Storage Gen2-Api's. Als u de fout "error code = FilesystemNotFound" bereikt met het bericht "het opgegeven bestands systeem bestaat niet," wordt het veroorzaakt door het opgegeven Sink-bestands systeem dat is gemaakt via de BLOB-API in plaats van Data Lake Storage Gen2-API elders. Om het probleem op te lossen, geeft u een nieuw bestands systeem met een naam op die niet bestaat als de naam van een BLOB-container. Vervolgens wordt het bestands systeem door Data Factory automatisch gemaakt tijdens het kopiëren van de gegevens.

>[!NOTE]
>Als u de optie **vertrouwde micro soft-Services toegang geven tot dit opslag account** in azure Storage Firewall-instellingen inschakelt, maakt Azure Integration runtime geen verbinding met data Lake Storage Gen2 en wordt er een verboden fout weer gegeven. Het fout bericht wordt weer gegeven omdat Data Factory niet wordt behandeld als een vertrouwde micro soft-service. Gebruik in plaats daarvan de zelf-hostende Integration runtime om verbinding te maken.

## <a name="get-started"></a>Aan de slag

>[!TIP]
>Zie [gegevens laden in azure data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md)voor een overzicht van het gebruik van de data Lake Storage Gen2-connector.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De Azure Data Lake Storage Gen2-connector ondersteunt de volgende verificatie typen. Zie de betreffende secties voor meer informatie:

- [Verificatie van account-sleutel](#account-key-authentication)
- [Verificatie van service-principal](#service-principal-authentication)
- [Beheerde identiteiten voor verificatie van de Azure-resources](#managed-identity)

>[!NOTE]
>Wanneer u poly base gebruikt voor het laden van gegevens in SQL Data Warehouse, als uw bron Data Lake Storage Gen2 is geconfigureerd met Virtual Network-eind punt, moet u beheerde identiteits verificatie gebruiken zoals vereist is door poly base. Zie de sectie [beheerde identiteits verificatie](#managed-identity) met meer configuratie vereisten.

### <a name="account-key-authentication"></a>Verificatie van account-sleutel

Voor het gebruik van storage-account sleutelverificatie, worden de volgende eigenschappen ondersteund:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **AzureBlobFS**. |Ja |
| url | Eind punt voor Data Lake Storage Gen2 met het patroon `https://<accountname>.dfs.core.windows.net`van. | Ja |
| accountKey | Account sleutel voor Data Lake Storage Gen2. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory, of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | De [integratieruntime](concepts-integration-runtime.md) moet worden gebruikt verbinding maken met het gegevensarchief. U kunt de Azure Integration runtime of een zelf-hostende Integration runtime gebruiken als uw gegevens archief zich in een particulier netwerk bevindt. Als deze eigenschap niet is opgegeven, wordt de standaard Azure Integration runtime gebruikt. |Nee |

>[!NOTE]
>Het ADLS-eind punt van het secundaire bestands systeem wordt niet ondersteund bij het gebruik van account sleutel verificatie. U kunt andere verificatie typen gebruiken.

**Voorbeeld:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Verificatie van service-principal

Voer de volgende stappen uit om Service-Principal-verificatie te gebruiken.

1. Registreer een toepassings entiteit in Azure Active Directory (Azure AD) door de stappen te volgen in [uw toepassing registreren bij een Azure AD-Tenant](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Noteer de volgende waarden, die u gebruikt voor het definiëren van de gekoppelde service:

    - Toepassings-id
    - Toepassingssleutel
    - Tenant-id

2. Verleen de service-principal de juiste machtigingen. Meer informatie over de werking van machtigingen in Data Lake Storage Gen2 van [toegangs beheer lijsten voor bestanden en mappen](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Als bron**: Wijs in Storage Explorer ten minste de machtiging **uitvoeren** vanuit het bron bestandssysteem toe, samen met de machtiging **lezen** voor de bestanden die moeten worden gekopieerd. U kunt ook in toegangs beheer (IAM) ten minste de rol **Storage BLOB data Reader** verlenen.
    - **Als Sink**: Ken in Storage Explorer ten minste de machtiging **uitvoeren** vanuit het sink-bestands systeem, samen met **Schrijf** machtiging voor de map sink. U kunt ook, in toegangs beheer (IAM), ten minste de rol van **BLOB voor gegevens opslag** verlenen.

>[!NOTE]
>Als u mappen wilt weer geven vanaf het account niveau of als u de verbinding wilt testen, moet u de machtiging instellen van de service-principal die wordt verleend aan het **opslag account met de machtiging Storage BLOB data Reader in iam**. Dit geldt wanneer u het volgende gebruikt:
>- **Kopieer het hulp programma data** om Kopieer pijp lijn te schrijven.
>- **Data Factory gebruikers interface** voor het testen van de verbinding en het navigeren door mappen tijdens het ontwerpen. 
>Als u problemen hebt met het verlenen van machtigingen op account niveau, slaat u tijdens het ontwerpen de test verbinding over en voert u een bovenliggend pad in met toestemming toegekend en kiest u vervolgens om te bladeren vanuit het opgegeven pad. De Kopieer activiteit werkt als de Service-Principal is verleend met de juiste machtigingen voor de bestanden die moeten worden gekopieerd.

Deze eigenschappen worden ondersteund voor de gekoppelde service:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **AzureBlobFS**. |Ja |
| url | Eind punt voor Data Lake Storage Gen2 met het patroon `https://<accountname>.dfs.core.windows.net`van. | Ja |
| servicePrincipalId | Opgeven van de toepassing client-ID. | Ja |
| servicePrincipalKey | Geef de sleutel van de toepassing. Markeer dit veld als een `SecureString` om het veilig op te slaan in Data Factory. U kunt ook [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | De tenantgegevens (domain name of tenant-ID) opgeven in uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Ja |
| connectVia | De [integratieruntime](concepts-integration-runtime.md) moet worden gebruikt verbinding maken met het gegevensarchief. U kunt de Azure Integration runtime of een zelf-hostende Integration runtime gebruiken als uw gegevens archief zich in een particulier netwerk bevindt. Als dat niet is opgegeven, wordt de standaard Azure Integration runtime gebruikt. |Nee |

**Voorbeeld:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Beheerde identiteiten voor verificatie van de Azure-resources

Een data factory kan worden gekoppeld aan een [beheerde identiteit voor de Azure-resources](data-factory-service-identity.md), die staat voor deze specifieke data factory. U kunt deze beheerde identiteit rechtstreeks gebruiken voor Data Lake Storage Gen2 verificatie, vergelijkbaar met het gebruik van uw eigen service-principal. Hiermee kan deze toegewezen Factory gegevens naar of van uw Data Lake Storage Gen2 benaderen en kopiëren.

Voer de volgende stappen uit om beheerde identiteiten voor Azure-resource verificatie te gebruiken.

1. [Haal de Data Factory beheerde identiteits gegevens](data-factory-service-identity.md#retrieve-managed-identity) op door de waarde van de **toepassings-id van de service-** id te kopiëren die samen met uw fabriek is gegenereerd.

2. Verleen de beheerde identiteit de juiste machtiging. Meer informatie over de werking van machtigingen in Data Lake Storage Gen2 van [toegangs beheer lijsten voor bestanden en mappen](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Als bron**: Wijs in Storage Explorer ten minste de machtiging **uitvoeren** vanuit het bron bestandssysteem toe, samen met de machtiging **lezen** voor de bestanden die moeten worden gekopieerd. U kunt ook in toegangs beheer (IAM) ten minste de rol **Storage BLOB data Reader** verlenen.
    - **Als Sink**: Ken in Storage Explorer ten minste de machtiging **uitvoeren** vanuit het sink-bestands systeem, samen met **Schrijf** machtiging voor de map sink. U kunt ook, in toegangs beheer (IAM), ten minste de rol van **BLOB voor gegevens opslag** verlenen.

>[!NOTE]
>Als u mappen wilt weer geven vanaf het account niveau of als u de verbinding wilt testen, moet u de machtiging instellen van de beheerde identiteit die wordt verleend aan het **opslag account met de machtiging opslag-BLOB-gegevens in iam**. Dit geldt wanneer u het volgende gebruikt:
>- **Kopieer het hulp programma data** om Kopieer pijp lijn te schrijven.
>- **Data Factory gebruikers interface** voor het testen van de verbinding en het navigeren door mappen tijdens het ontwerpen. 
>Als u problemen hebt met het verlenen van machtigingen op account niveau, slaat u tijdens het ontwerpen de test verbinding over en voert u een bovenliggend pad in met toestemming toegekend en kiest u vervolgens om te bladeren vanuit het opgegeven pad. De Kopieer activiteit werkt als de Service-Principal is verleend met de juiste machtigingen voor de bestanden die moeten worden gekopieerd.

>[!IMPORTANT]
>Als u poly base gebruikt voor het laden van gegevens van Data Lake Storage Gen2 naar SQL Data Warehouse, moet u, wanneer u beheerde identiteits verificatie voor Data Lake Storage Gen2 gebruikt, ervoor zorgen dat u de stappen 1 en 2 in [deze](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) hand leiding uitvoert naar 1) uw SQL database-server registreren bij Azure Active Directory (Azure AD) en 2) wijs de rol van BLOB voor gegevens opslag toe aan uw SQL Database-Server. de rest wordt afgehandeld door Data Factory. Als uw Data Lake Storage Gen2 is geconfigureerd met een Azure Virtual Network-eind punt, moet u de beheerde identiteits verificatie gebruiken zoals vereist is door poly Base om poly Base te gebruiken voor het laden van gegevens.

Deze eigenschappen worden ondersteund voor de gekoppelde service:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **AzureBlobFS**. |Ja |
| url | Eind punt voor Data Lake Storage Gen2 met het patroon `https://<accountname>.dfs.core.windows.net`van. | Ja |
| connectVia | De [integratieruntime](concepts-integration-runtime.md) moet worden gebruikt verbinding maken met het gegevensarchief. U kunt de Azure Integration runtime of een zelf-hostende Integration runtime gebruiken als uw gegevens archief zich in een particulier netwerk bevindt. Als dat niet is opgegeven, wordt de standaard Azure Integration runtime gebruikt. |Nee |

**Voorbeeld:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie [gegevens sets](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets.

- Raadpleeg voor **Parquet, tekst met scheidings tekens, JSON, AVRO en binaire indeling**, de sectie [Parquet, gescheiden tekst, JSON, AVRO en binaire indeling gegevensset](#format-based-dataset) .
- Raadpleeg de sectie [andere indelings gegevensset](#other-format-dataset) voor andere indelingen, zoals **Orc-indeling**.

### <a name="format-based-dataset"></a>Parquet, gescheiden tekst, JSON, AVRO en binaire indeling gegevensset

Als u gegevens wilt kopiëren naar en van **Parquet, tekst met scheidings tekens, AVRO of binaire indeling**, raadpleegt u [Parquet indeling](format-parquet.md), [tekst indeling met scheidings tekens](format-delimited-text.md), [Avro-indeling](format-avro.md) en [binaire-indeling](format-binary.md) op op indeling gebaseerde gegevensset en ondersteunde instellingen. De volgende eigenschappen worden ondersteund voor data Lake Storage Gen2 onder `location` instellingen in de gegevensset op basis van een indeling:

| Eigenschap   | Description                                                  | Vereist |
| ---------- | ------------------------------------------------------------ | -------- |
| Type       | De eigenschap type onder `location` in de gegevensset moet worden ingesteld op **AzureBlobFSLocation**. | Ja      |
| fileSystem | De naam van het Data Lake Storage Gen2-bestands systeem.                              | Nee       |
| folderPath | Het pad naar een map onder het opgegeven bestands systeem. Als u een Joker teken wilt gebruiken om mappen te filteren, slaat u deze instelling over en geeft u deze op in de activiteiten bron instellingen. | Nee       |
| fileName   | De bestands naam onder het opgegeven bestands systeem en folderPath. Als u een Joker teken wilt gebruiken om bestanden te filteren, slaat u deze instelling over en geeft u deze op in de activiteiten bron instellingen. | Nee       |

> [!NOTE]
> De **AzureBlobFSFile** -gegevensset met Parquet of tekst indeling die in de volgende sectie wordt vermeld, wordt nog steeds ondersteund als voor Copy-, lookup-of GetMetadata-activiteiten voor achterwaartse compatibiliteit. Maar dit werkt niet met de functie gegevens stroom toewijzen. U wordt aangeraden dit nieuwe model verder te gebruiken. Deze nieuwe typen worden gegenereerd door de gebruikers interface van de Data Factory-ontwerp functie.

**Voorbeeld:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
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

### <a name="other-format-dataset"></a>Gegevensset voor andere indeling

Als u gegevens wilt kopiëren naar en van Data Lake Storage Gen2 in de **Orc-indeling**, worden de volgende eigenschappen ondersteund:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op **AzureBlobFSFile**. |Ja |
| folderPath | Pad naar de map in Data Lake Storage Gen2. Indien niet opgegeven, wordt deze verwijst naar de hoofdmap. <br/><br/>Het Joker teken filter wordt ondersteund. Toegestane joker tekens zijn `*` (komt overeen met nul of meer tekens `?` ) en (komt overeen met nul of één teken). Gebruik `^` om te escapen als uw werkelijke mapnaam een Joker teken bevat of dit escape-teken in. <br/><br/>Voor beelden: bestands systeem/map/. Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). |Nee |
| fileName | De naam of het Joker teken filter voor de bestanden onder het opgegeven folderPath. Als u een waarde voor deze eigenschap niet opgeeft, wordt de gegevensset verwijst naar alle bestanden in de map. <br/><br/>Voor het filter zijn `*` de toegestane joker tekens (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken).<br/>-Voorbeeld 1: `"fileName": "*.csv"`<br/>-Voorbeeld 2: `"fileName": "???20180427.txt"`<br/>Gebruik `^` om te escapen als uw werkelijke bestands naam een Joker teken bevat of dit escape-teken in.<br/><br/>Als er geen bestands naam is opgegeven voor een uitvoer-gegevensset en **preserveHierarchy** niet is opgegeven in de Sink van de activiteit, genereert de Kopieer activiteit automatisch de bestands naam met het volgende patroon: "*Gegevens. [GUID van run-ID van activiteit]. [GUID if FlattenHierarchy]. [indeling indien geconfigureerd]. [compressie indien geconfigureerd]* ', bijvoorbeeld ' data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt. gz '. Als u vanuit een tabellaire bron kopieert met behulp van een tabel naam in plaats van een query, is het naam patroon ' *[tabel naam]. [ indeling]. [compressie indien geconfigureerd]* ', bijvoorbeeld ' mytable. csv '. |Nee |
| modifiedDatetimeStart | Bestanden filteren op basis van het kenmerk dat het laatst is gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik `modifiedDatetimeStart` ligt `modifiedDatetimeEnd`tussen en. De tijd wordt toegepast op de UTC-tijd zone in de notatie "2018-12-01T05:00:00Z". <br/><br/> De algehele prestaties van het verplaatsen van gegevens worden beïnvloed door deze instelling in te scha kelen wanneer u bestands filter wilt uitvoeren met enorme hoeveel heden bestanden. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen bestands kenmerk filter op de gegevensset wordt toegepast. Wanneer `modifiedDatetimeStart` heeft een datum/tijd `modifiedDatetimeEnd` -waarde, maar null is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter is dan of gelijk is aan de datum/tijd-waarde zijn geselecteerd. Wanneer `modifiedDatetimeEnd` heeft een datum/tijd `modifiedDatetimeStart` -waarde, maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is, kleiner zijn dan de waarde voor datum/tijd.| Nee |
| modifiedDatetimeEnd | Bestanden filteren op basis van het kenmerk dat het laatst is gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik `modifiedDatetimeStart` ligt `modifiedDatetimeEnd`tussen en. De tijd wordt toegepast op de UTC-tijd zone in de notatie "2018-12-01T05:00:00Z". <br/><br/> De algehele prestaties van het verplaatsen van gegevens worden beïnvloed door deze instelling in te scha kelen wanneer u bestands filter wilt uitvoeren met enorme hoeveel heden bestanden. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen bestands kenmerk filter op de gegevensset wordt toegepast. Wanneer `modifiedDatetimeStart` heeft een datum/tijd `modifiedDatetimeEnd` -waarde, maar null is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter is dan of gelijk is aan de datum/tijd-waarde zijn geselecteerd. Wanneer `modifiedDatetimeEnd` heeft een datum/tijd `modifiedDatetimeStart` -waarde, maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is, kleiner zijn dan de waarde voor datum/tijd.| Nee |
| format | Als u wilt kopiëren van bestanden als tussen winkels op basis van bestanden (binaire kopie), moet u de sectie indeling in de invoer en uitvoer gegevenssetdefinities overslaan.<br/><br/>Als u bestanden wilt parseren of genereren met een specifieke indeling, worden de volgende typen bestands indelingen ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**en **ParquetFormat**. Stel de **type** eigenschap onder **indeling** op een van deze waarden. Zie de secties [tekst indeling](supported-file-formats-and-compression-codecs.md#text-format), [JSON-indeling](supported-file-formats-and-compression-codecs.md#json-format), [Avro](supported-file-formats-and-compression-codecs.md#avro-format)-indeling, [Orc-indeling](supported-file-formats-and-compression-codecs.md#orc-format)en [Parquet-indeling](supported-file-formats-and-compression-codecs.md#parquet-format) voor meer informatie. |Nee (alleen voor binaire kopie-scenario) |
| compression | Geef het type en het niveau van compressie voor de gegevens. Zie voor meer informatie, [ondersteunde indelingen en codecs voor compressie](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Ondersteunde typen zijn **GZip**, **Deflate**, **BZip2**, en **ZipDeflate**.<br/>Ondersteunde niveaus zijn **optimale** en **snelst**. |Nee |

>[!TIP]
>Alle bestanden in een map wilt kopiëren, geef **folderPath** alleen.<br>Als u één bestand met een bepaalde naam wilt kopiëren, geeft u **FolderPath** op met een mappen onderdeel en **filename** met een bestands naam.<br>Als u een subset van bestanden onder een map wilt kopiëren, geeft u **FolderPath** op met een deel van een map en een **Bestands naam** met een Joker teken filter. 

**Voorbeeld:**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
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

Zie [configuraties van activiteiten](copy-activity-overview.md#configuration) en [pijp lijnen en activiteiten](concepts-pipelines-activities.md)kopiëren voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Data Lake Storage Gen2 bron en sink.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 als bron type

- Als u wilt kopiëren van **Parquet, tekst met scheidings tekens, JSON, AVRO en binaire indeling**, raadpleegt u de sectie [Parquet, tekst met scheidings tekens, JSON, AVRO en binaire indelings bron](#format-based-source) .
- Als u wilt kopiëren van andere indelingen, zoals de **indeling Orc**, raadpleegt u de sectie [andere indelings bron](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, tekst met scheidings tekens, JSON, AVRO en binaire indelings bron

Als u gegevens wilt kopiëren uit **Parquet, tekst met scheidings tekens, JSON, AVRO en binaire indeling**, raadpleegt u [Parquet-indeling](format-parquet.md), [tekst indeling met scheidings tekens](format-delimited-text.md), [Avro-indeling](format-avro.md) en [binaire indeling](format-binary.md) artikel op op indeling gebaseerde Kopieer activiteit bron en ondersteund Instellingen. De volgende eigenschappen worden ondersteund voor data Lake Storage Gen2 onder `storeSettings` instellingen in een op indeling gebaseerde kopie bron:

| Eigenschap                 | Description                                                  | Vereist                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| Type                     | De eigenschap type onder `storeSettings` moet worden ingesteld op **AzureBlobFSReadSetting**. | Ja                                           |
| recursive                | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen voor de opgegeven map. Als recursief is ingesteld op True en de Sink een archief op basis van bestanden is, wordt een lege map of submap niet gekopieerd of gemaakt bij de sink. Toegestane waarden zijn **waar** (standaard) en **false**. | Nee                                            |
| wildcardFolderPath       | Het mappad met Joker tekens onder het opgegeven bestands systeem dat is geconfigureerd in de gegevensset om bron mappen te filteren. <br>Toegestane joker tekens zijn `*` (komt overeen met nul of meer tekens `?` ) en (komt overeen met nul of één teken). Gebruik `^` om te escapen als uw werkelijke mapnaam een Joker teken of escape-teken bevat. <br>Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). | Nee                                            |
| wildcardFileName         | De naam van het bestand met Joker tekens onder het opgegeven bestands systeem + folderPath/wildcardFolderPath voor het filteren van bron bestanden. <br>Toegestane joker tekens zijn `*` (komt overeen met nul of meer tekens `?` ) en (komt overeen met nul of één teken). Gebruik `^` om te escapen als uw werkelijke mapnaam een Joker teken of escape-teken bevat. Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). | Ja als `fileName` niet is opgegeven in de gegevensset |
| modifiedDatetimeStart    | Bestanden filteren op basis van het kenmerk dat het laatst is gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik `modifiedDatetimeStart` ligt `modifiedDatetimeEnd`tussen en. De tijd wordt toegepast op de UTC-tijd zone in de notatie "2018-12-01T05:00:00Z". <br> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestands kenmerken wordt toegepast op de gegevensset. Wanneer `modifiedDatetimeStart` heeft een datum/tijd `modifiedDatetimeEnd` -waarde, maar null is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter dan of gelijk is aan de datum/tijd-waarde zijn geselecteerd. Wanneer `modifiedDatetimeEnd` heeft een datum/tijd `modifiedDatetimeStart` -waarde, maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is, kleiner zijn dan de waarde voor datum/tijd. | Nee                                            |
| modifiedDatetimeEnd      | Hetzelfde als hierboven.                                               | Nee                                            |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig met een opslag archief moet worden verbonden. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee                                            |

> [!NOTE]
> Voor de Parquet-of gescheiden tekst indeling wordt de bron van de Kopieer activiteit **AzureBlobFSSource** die in de volgende sectie wordt genoemd, nog steeds ondersteund voor compatibiliteit met eerdere versies. U wordt aangeraden dit nieuwe model verder te gebruiken. Deze nieuwe typen worden gegenereerd door de gebruikers interface van de Data Factory-ontwerp functie.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
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
                    "type": "AzureBlobFSReadSetting",
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

#### <a name="other-format-source"></a>Andere indelings bron

Als u gegevens wilt kopiëren van Data Lake Storage Gen2 in de **Orc-indeling**, worden de volgende eigenschappen ondersteund in het gedeelte **bron** van de Kopieer activiteit:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron voor kopiëren-activiteit moet worden ingesteld op **AzureBlobFSSource**. |Ja |
| recursive | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen voor de opgegeven map. Als recursief is ingesteld op True en de Sink een archief op basis van bestanden is, wordt een lege map of submap niet gekopieerd of gemaakt bij de sink.<br/>Toegestane waarden zijn **waar** (standaard) en **false**. | Nee |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met het gegevens archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
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
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 als een sink-type

- Als u wilt kopiëren naar **Parquet, tekst met scheidings tekens, JSON, AVRO en binary**, verwijzen wij u naar [Parquet, gescheiden tekst, JSON, AVRO en de sectie Sink voor binaire indeling](#format-based-sink) .
- Als u wilt kopiëren naar andere indelingen, zoals de **Orc/JSON-indeling**, raadpleegt u de sectie een [andere indelings Sink](#other-format-sink) .

#### <a name="format-based-sink"></a>Parquet, tekst met scheidings tekens, JSON, AVRO en binaire indelings Sink

Als u gegevens wilt kopiëren naar **Parquet, tekst met scheidings tekens, JSON-, AVRO-en binaire indeling**, raadpleegt u [Parquet-indeling](format-parquet.md), [tekst indeling met scheidings tekens](format-delimited-text.md), [Avro-indeling](format-avro.md) en [binaire-indeling](format-binary.md) artikel op op indeling gebaseerde kopie van de Kopieer activiteit en wordt ondersteund Instellingen. De volgende eigenschappen worden ondersteund voor data Lake Storage Gen2 onder `storeSettings` instellingen in op indeling gebaseerde kopie-Sink:

| Eigenschap                 | Description                                                  | Vereist |
| ------------------------ | ------------------------------------------------------------ | -------- |
| Type                     | De eigenschap type onder `storeSettings` moet worden ingesteld op **AzureBlobFSWriteSetting**. | Ja      |
| copyBehavior             | Definieert het gedrag kopiëren wanneer de bron bestanden vanuit een bestandsgebaseerde gegevensarchief is.<br/><br/>Toegestane waarden zijn:<br/><b>-PreserveHierarchy (standaard)</b>: Hiermee behoudt u de bestands hiërarchie in de doelmap. Het relatieve pad van het bron bestand naar de bronmap is identiek aan het relatieve pad van het doel bestand naar de doelmap.<br/><b>-FlattenHierarchy</b>: Alle bestanden van de bronmap bevinden zich in het eerste niveau van de doelmap. De doelbestanden hebben automatisch gegenereerde namen. <br/><b>-MergeFiles</b>: Alle bestanden van de bronmap worden samengevoegd met één bestand. Als de bestandsnaam is opgegeven, is de naam van het samengevoegde de opgegeven naam. Anders is de naam van een automatisch gegenereerde bestand. | Nee       |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met het gegevens archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee       |

> [!NOTE]
> Voor de Parquet-of gescheiden tekst indeling wordt de Sink van de Kopieer activiteit **AzureBlobFSSink** die in de volgende sectie wordt vermeld, nog steeds ondersteund voor compatibiliteit met eerdere versies. U wordt aangeraden dit nieuwe model verder te gebruiken. Deze nieuwe typen worden gegenereerd door de gebruikers interface van de Data Factory-ontwerp functie.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
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
                    "type": "AzureBlobFSWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Andere opmaak Sink

Als u gegevens wilt kopiëren naar Data Lake Storage Gen2 in de **Orc-indeling**, worden de volgende eigenschappen ondersteund in de sectie **sink** :

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de kopie-activiteit-sink moet worden ingesteld op **AzureBlobFSSink**. |Ja |
| copyBehavior | Definieert het gedrag kopiëren wanneer de bron bestanden vanuit een bestandsgebaseerde gegevensarchief is.<br/><br/>Toegestane waarden zijn:<br/><b>-PreserveHierarchy (standaard)</b>: Hiermee behoudt u de bestands hiërarchie in de doelmap. Het relatieve pad van het bron bestand naar de bronmap is identiek aan het relatieve pad van het doel bestand naar de doelmap.<br/><b>-FlattenHierarchy</b>: Alle bestanden van de bronmap bevinden zich in het eerste niveau van de doelmap. De doelbestanden hebben automatisch gegenereerde namen. <br/><b>-MergeFiles</b>: Alle bestanden van de bronmap worden samengevoegd met één bestand. Als de bestandsnaam is opgegeven, is de naam van het samengevoegde de opgegeven naam. Anders is de naam van een automatisch gegenereerde bestand. | Nee |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met het gegevens archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Voor beelden van map-en bestands filter

In deze sectie wordt het resulterende gedrag van het mappad en de bestands naam met Joker teken filters beschreven.

| folderPath | fileName | recursive | De structuur van de bronmap en het filter resultaat ( **vetgedrukte** bestanden worden opgehaald)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Leeg, standaard instelling gebruiken) | false | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Leeg, standaard instelling gebruiken) | true | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Enkele voorbeelden van recursieve en copyBehavior

In deze sectie wordt het resulterende gedrag van de Kopieer bewerking voor verschillende combi Naties van recursieve en copyBehavior waarden beschreven.

| recursive | copyBehavior | Structuur van de gegevensbron | Resulterende doel |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | De doel-Map1 is gemaakt met dezelfde structuur als de bron:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Het doel Map1 is gemaakt met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Het doel Map1 is gemaakt met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1 bestand2 + bestand3 + File4 + File5 inhoud worden samengevoegd in één bestand met een automatisch gegenereerde naam. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Het doel Map1 is gemaakt met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 met File3, File4 en File5 worden niet opgehaald. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Het doel Map1 is gemaakt met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand2<br/><br/>Subfolder1 met File3, File4 en File5 worden niet opgehaald. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Het doel Map1 is gemaakt met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1 + bestand2 inhoud worden samengevoegd in één bestand met een automatisch gegenereerde naam. automatisch gegenereerde naam voor File1<br/><br/>Subfolder1 met File3, File4 en File5 worden niet opgehaald. |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Acl's van Data Lake Storage Gen1 behouden

>[!TIP]
>Als u gegevens van Azure Data Lake Storage Gen1 wilt kopiëren naar Gen2 in het algemeen, raadpleegt u [gegevens kopiëren van Azure data Lake Storage gen1 naar Gen2 met Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) voor een stapsgewijze en best practices.

Wanneer u bestanden kopieert van Azure Data Lake Storage Gen1 naar Gen2, kunt u ervoor kiezen om de POSIX Access Control Lists (Acl's) samen met de gegevens te behouden. Zie voor meer informatie over toegangs beheer [toegangs beheer in azure data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md) en [toegangs beheer in azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

De volgende typen Acl's kunnen worden bewaard met behulp van de Azure Data Factory Kopieer activiteit. U kunt een of meer typen selecteren:

- **ACL**: POSIX Access Control Lists kopiëren en bewaren voor bestanden en mappen. Hiermee worden de volledige bestaande Acl's van de bron naar de Sink gekopieerd. 
- **Eigenaar**: Kopieer en bewaar de gebruiker die eigenaar is van bestanden en mappen. Super gebruikers hebben toegang tot Sink Data Lake Storage Gen2 vereist.
- **Groep**: Kopieer en bewaar de groep die eigenaar is van bestanden en mappen. Super gebruikers hebben toegang tot Sink Data Lake Storage Gen2 of de gebruiker die eigenaar is (als de gebruiker die eigenaar is ook lid is van de doel groep) is vereist.

Als u opgeeft dat u wilt kopiëren vanuit een map, Data Factory repliceert de acl's voor die bepaalde map en de bestanden en mappen daaronder als `recursive` deze is ingesteld op True. Als u opgeeft dat u vanuit één bestand wilt kopiëren, worden de Acl's voor dat bestand gekopieerd.

>[!IMPORTANT]
>Wanneer u ervoor kiest om Acl's te behouden, moet u ervoor zorgen dat u Maxi maal voldoende machtigingen voor Data Factory verleent voor het uitvoeren van uw Sink Data Lake Storage Gen2-account. Gebruik bijvoorbeeld account sleutel verificatie of wijs de rol Storage BLOB data owner toe aan de service-principal of beheerde identiteit.

Wanneer u bron configureert als Data Lake Storage Gen1 met de optie binaire kopie of binaire indeling en Sink als Data Lake Storage Gen2 met de optie voor binaire kopieën of binaire indeling, kunt u de optie **behouden** vinden op de pagina **instellingen van gegevens kopiëren-programma** of op het tabblad**instellingen** van de **Kopieer activiteit** > voor het ontwerpen van de activiteit.

![Data Lake Storage Gen1 Gen2-ACL behouden](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Hier volgt een voor beeld van de JSON- `preserve`configuratie (zie): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Meer informatie over [bron transformatie](data-flow-source.md) en [sink-trans formatie](data-flow-sink.md) vindt u in de functie gegevens stroom toewijzen.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="getmetadata-activity-properties"></a>Eigenschappen van GetMetadata-activiteit

Als u meer wilt weten over de eigenschappen, controleert u de [GetMetadata-activiteit](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Eigenschappen van activiteit verwijderen

Als u meer wilt weten over de eigenschappen, controleert u de [activiteit verwijderen](delete-activity.md)
## <a name="next-steps"></a>Volgende stappen

Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md##supported-data-stores-and-formats).
