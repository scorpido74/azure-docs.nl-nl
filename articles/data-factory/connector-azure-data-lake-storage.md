---
title: Gegevens kopiëren en transformeren in Azure Data Lake Storage Gen2
description: Meer informatie over het kopiëren van gegevens van en naar Azure Data Lake Storage Gen2 en het transformeren van gegevens in Azure Data Lake Storage Gen2 met Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 3c7ff0061a57d1a1a7525ec03b4f77c117415ca5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155848"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Gegevens kopiëren en transformeren in Azure Data Lake Storage Gen2 met Azure Data Factory

Azure Data Lake Storage Gen2 (ADLS Gen2) is een reeks mogelijkheden die zijn toegeveld naar big data-analyses die zijn ingebouwd in [Azure Blob-opslag.](../storage/blobs/storage-blobs-introduction.md) U het gebruiken om te communiceren met uw gegevens met behulp van zowel bestandssysteem en object opslag paradigma's.

In dit artikel wordt beschreven hoe u Activiteit kopiëren in Azure Data Factory gebruikt om gegevens van en naar Azure Data Lake Storage Gen2 te kopiëren en gegevensstroom te gebruiken om gegevens te transformeren in Azure Data Lake Storage Gen2. Lees het [inleidende artikel](introduction.md)voor meer informatie over Azure Data Factory.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Data Lake Storage Gen2-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Gegevensstroom toewijzen](concepts-data-flow-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Activiteit Metagegevens](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)

Voor Kopieeractiviteit u met deze connector het als volgt gaan:

- Kopieer gegevens van/naar Azure Data Lake Storage Gen2 met behulp van accountsleutel, serviceprincipal of beheerde identiteiten voor Azure-bronverificaties.
- Kopieer bestanden as-is of ontleed of genereer bestanden met [ondersteunde bestandsindelingen en compressiecodecs](supported-file-formats-and-compression-codecs.md).
- [Bestandsmetagegevens behouden tijdens het kopiëren](#preserve-metadata-during-copy).
- [ACL's behouden](#preserve-acls) bij het kopiëren vanuit Azure Data Lake Storage Gen1/Gen2.

>[!IMPORTANT]
>Als u vertrouwde **Microsoft-services toestaan toegang te geven tot deze opslagaccountoptie** in azure storage-firewall-instellingen en azure-integratieruntime wilt gebruiken om verbinding te maken met uw Data Lake Storage Gen2, moet u [beheerde identiteitsverificatie](#managed-identity) voor ADLS Gen2 gebruiken.

>[!TIP]
>Als u de hiërarchische naamruimte inschakelt, is er momenteel geen interoperabiliteit van bewerkingen tussen Blob- en Data Lake Storage Gen2-API's. Als u de fout "ErrorCode=FilesystemNotFound" indrukt met het bericht 'Het opgegeven bestandssysteem bestaat niet', wordt dit veroorzaakt door het opgegeven sink-bestandssysteem dat is gemaakt via de Blob API in plaats van Data Lake Storage Gen2 API elders. Als u het probleem wilt oplossen, geeft u een nieuw bestandssysteem op met een naam die niet bestaat als de naam van een Blob-container. Vervolgens maakt Data Factory automatisch dat bestandssysteem tijdens het kopiëren van gegevens.

## <a name="get-started"></a>Aan de slag

>[!TIP]
>Zie [Gegevens laden in Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md)voor een doorloop van het gebruik van de Data Lake Storage Gen2-connector.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabrieken te definiëren die specifiek zijn voor Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De Azure Data Lake Storage Gen2-connector ondersteunt de volgende verificatietypen. Zie de bijbehorende secties voor meer informatie:

- [Verificatie van accountsleutel](#account-key-authentication)
- [Verificatie van service-principal](#service-principal-authentication)
- [Beheerde identiteiten voor Azure-bronverificatie](#managed-identity)

>[!NOTE]
>Wanneer u PolyBase gebruikt om gegevens in SQL Data Warehouse te laden, moet u, als uw bron Data Lake Storage Gen2 is geconfigureerd met Virtual Network-eindpunt, beheerde identiteitsverificatie gebruiken zoals vereist door PolyBase. Zie de sectie [beheerde identiteitsverificatie](#managed-identity) met meer configuratievereisten.

### <a name="account-key-authentication"></a>Verificatie van accountsleutel

Als u verificatie van opslagaccountsleutels wilt gebruiken, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op **AzureBlobFS**. |Ja |
| url | Eindpunt voor Data Lake Storage Gen2 `https://<accountname>.dfs.core.windows.net`met het patroon van . | Ja |
| accountSleutel | Accountsleutel voor Data Lake Storage Gen2. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) |Ja |
| connectVia | De [inburgeringsruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. U de runtime voor Azure-integratie of een zelfgehoste nawerking van de integratie gebruiken als uw gegevensarchief zich in een privénetwerk bevindt. Als deze eigenschap niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

>[!NOTE]
>Secundair adls-bestandssysteemeindpunt wordt niet ondersteund bij het gebruik van accountsleutelverificatie. U andere verificatietypen gebruiken.

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

Voer deze stappen uit om serviceprincipal-verificatie te gebruiken.

1. Registreer een toepassingsentiteit in Azure Active Directory (Azure AD) door de stappen te volgen in [Uw toepassing registreren met een Azure AD-tenant](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:

    - Toepassings-id
    - Toepassingssleutel
    - Tenant-id

2. Geef de serviceprincipal de juiste toestemming. Voorbeelden bekijken over hoe toestemming werkt in Data Lake Storage Gen2 van [toegangscontrolelijsten voor bestanden en mappen](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Als bron**: Geef in Storage Explorer ten minste **uitvoertoestemming** voor ALLE upstreammappen en het bestandssysteem, samen met **Leestoestemming** voor de bestanden om te kopiëren. U ook in Toegangsbeheer (IAM) ten minste de rol **Storage Blob Data Reader** toekennen.
    - **Als sink**: Geef in Storage Explorer ten minste **uitvoertoestemming** voor ALLE upstreammappen en het bestandssysteem, samen met **Schrijftoestemming** voor de sinkmap. U ook in Toegangsbeheer (IAM) ten minste de rol **opslagblobgegevensbijdrager** verlenen.

>[!NOTE]
>Als u de gebruikersinterface van Data Factory gebruikt om te schrijven en de serviceprincipal is niet ingesteld met de rol 'Opslagblobgegevenslezer/inzender' in IAM, kiest u bij het uitvoeren van testverbinding of browsen/navigeren in mappen de optie 'Verbinding tot bestandspad testen' of 'Bladeren van opgegeven pad' en geeft u een pad op met **Lees + Uitvoertoestemming** om door te gaan.

Deze eigenschappen worden ondersteund voor de gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op **AzureBlobFS**. |Ja |
| url | Eindpunt voor Data Lake Storage Gen2 `https://<accountname>.dfs.core.windows.net`met het patroon van . | Ja |
| servicePrincipalId | Geef de client-id van de toepassing op. | Ja |
| servicePrincipalKey | Geef de sleutel van de toepassing op. Markeer dit `SecureString` veld als een om het veilig op te slaan in Data Factory. U ook [verwijzen naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |
| tenant | Geef de tenantgegevens op (domeinnaam of tenant-id) waaronder uw toepassing zich bevindt. Haal deze op door met de muis in de rechterbovenhoek van de Azure-portal te zweven. | Ja |
| connectVia | De [inburgeringsruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. U de runtime voor Azure-integratie of een zelfgehoste nawerking van de integratie gebruiken als uw gegevensarchief zich in een privénetwerk bevindt. Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Beheerde identiteiten voor Azure-bronverificatie

Een gegevensfabriek kan worden gekoppeld aan een [beheerde identiteit voor Azure-resources,](data-factory-service-identity.md)die deze specifieke gegevensfabriek vertegenwoordigt. U deze beheerde identiteit rechtstreeks gebruiken voor Data Lake Storage Gen2-verificatie, vergelijkbaar met het gebruik van uw eigen serviceprincipal. Hiermee kan deze aangewezen fabriek toegang krijgen tot en kopie van gegevens naar of van uw Data Lake Storage Gen2.

Voer deze stappen uit om beheerde identiteiten te gebruiken voor Azure-bronverificatie.

1. [Haal de beheerde identiteitsgegevens](data-factory-service-identity.md#retrieve-managed-identity) van de Data Factory op door de waarde te kopiëren van de **beheerde identiteitsobject-id** die samen met uw fabriek is gegenereerd.

2. Geef de beheerde identiteit de juiste toestemming. Zie voorbeelden over hoe toestemming werkt in Data Lake Storage Gen2 van [toegangscontrolelijsten voor bestanden en mappen.](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Als bron**: Geef in Storage Explorer ten minste **uitvoertoestemming** voor ALLE upstreammappen en het bestandssysteem, samen met **Leestoestemming** voor de bestanden om te kopiëren. U ook in Toegangsbeheer (IAM) ten minste de rol **Storage Blob Data Reader** toekennen.
    - **Als sink**: Geef in Storage Explorer ten minste **uitvoertoestemming** voor ALLE upstreammappen en het bestandssysteem, samen met **Schrijftoestemming** voor de sinkmap. U ook in Toegangsbeheer (IAM) ten minste de rol **opslagblobgegevensbijdrager** verlenen.

>[!NOTE]
>Als u de gebruikersinterface van Data Factory gebruikt om te schrijven en de beheerde identiteit niet is ingesteld met de rol 'Opslagblobgegevenslezer/inzender' in IAM, kiest u bij het uitvoeren van testverbinding of browsen/navigeren in mappen de optie 'Verbinding tot bestandspad testen' of 'Bladeren van opgegeven pad' en geeft u een pad op met **Lees + Uitvoertoestemming** om door te gaan.

>[!IMPORTANT]
>Als u PolyBase gebruikt om gegevens van Data Lake Storage Gen2 in SQL Data Warehouse te laden, moet u bij het gebruik van managed identity authentication voor Data Lake Storage Gen2 ervoor zorgen dat u ook stappen 1 en 2 in [deze richtlijnen](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) volgt om uw SQL Database-server te registreren met Azure Active Directory (Azure AD) en 2) de rol van OpslagBlob-gegevensbijdrage toe te wijzen aan uw SQL Database-server; de rest wordt afgehandeld door Data Factory. Als uw Data Lake Storage Gen2 is geconfigureerd met een Azure Virtual Network-eindpunt, moet u met PolyBase gegevens gebruiken om gegevens te laden, zoals vereist door PolyBase.

Deze eigenschappen worden ondersteund voor de gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op **AzureBlobFS**. |Ja |
| url | Eindpunt voor Data Lake Storage Gen2 `https://<accountname>.dfs.core.windows.net`met het patroon van . | Ja |
| connectVia | De [inburgeringsruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. U de runtime voor Azure-integratie of een zelfgehoste nawerking van de integratie gebruiken als uw gegevensarchief zich in een privénetwerk bevindt. Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

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

Zie [Gegevenssets](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets.

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor Data `location` Lake Storage Gen2 onder instellingen in de op indeling gebaseerde gegevensset:

| Eigenschap   | Beschrijving                                                  | Vereist |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | De eigenschap `location` type onder in de gegevensset moet worden ingesteld op **AzureBlobFSLocation**. | Ja      |
| Bestandssysteem | De naam van het Data Lake Storage Gen2-bestandssysteem.                              | Nee       |
| folderPath | Het pad naar een map onder het opgegeven bestandssysteem. Als u een wildcard wilt gebruiken om mappen te filteren, slaat u deze instelling over en geeft u deze op in de instellingen voor activiteitsbronnen. | Nee       |
| fileName   | De bestandsnaam onder het opgegeven fileSystem + folderPath. Als u een wildcard wilt gebruiken om bestanden te filteren, slaat u deze instelling over en geeft u deze op in de instellingen van de activiteitsbron. | Nee       |

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

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie [Activiteitsconfiguraties](copy-activity-overview.md#configuration) en [-activiteiten](concepts-pipelines-activities.md)kopiëren voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de bron en gootsteen van Data Lake Storage Gen2.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 als brontype

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor Data `storeSettings` Lake Storage Gen2 onder instellingen in op indeling gebaseerde kopieerbron:

| Eigenschap                 | Beschrijving                                                  | Vereist                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | De eigenschap `storeSettings` type onder moet zijn ingesteld op **AzureBlobFSReadSettings**. | Ja                                           |
| Recursieve                | Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. Wanneer recursieve is ingesteld op true en de gootsteen is een bestand op basis van opslag, een lege map of submap wordt niet gekopieerd of gemaakt op de gootsteen. Toegestane waarden zijn **waar** (standaard) en **onwaar.** | Nee                                            |
| wildcardFolderPath       | Het mappad met jokertekens onder het opgegeven bestandssysteem dat is geconfigureerd in de gegevensset om bronmappen te filteren. <br>Toegestane wildcards `*` zijn (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken). Gebruik `^` om te ontsnappen als uw werkelijke map naam heeft een wildcard of deze ontsnapping char binnen. <br>Zie meer voorbeelden in [voorbeelden van mappen en bestandenfilters](#folder-and-file-filter-examples). | Nee                                            |
| wildcardFileName         | De bestandsnaam met jokertekens onder het opgegeven bestandssysteem + mapPath/wildcardFolderPath om bronbestanden te filteren. <br>Toegestane wildcards `*` zijn (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken). Gebruik `^` om te ontsnappen als uw werkelijke map naam heeft een wildcard of deze ontsnapping char binnen. Zie meer voorbeelden in [voorbeelden van mappen en bestandenfilters](#folder-and-file-filter-examples). | Ja `fileName` als deze niet is opgegeven in de gegevensset |
| gewijzigdDatumtimeStart    | Bestandenfilteren op basis van het kenmerk Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` `modifiedDatetimeEnd`tijd binnen het tijdsbereik tussen en . De tijd wordt toegepast op de UTC-tijdzone in de indeling "2018-12-01T05:00:00Z". <br> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestandskenmerken wordt toegepast op de gegevensset. Wanneer `modifiedDatetimeStart` een datumtijdwaarde `modifiedDatetimeEnd` is, maar NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, worden geselecteerd. Wanneer `modifiedDatetimeEnd` een datumtijdwaarde `modifiedDatetimeStart` heeft, maar NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, zijn geselecteerd. | Nee                                            |
| gewijzigdDatumtimeEnd      | Hetzelfde als hierboven.                                               | Nee                                            |
| maxConcurrentVerbindingen | Het aantal verbindingen om gelijktijdig verbinding te maken met het opslagarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee                                            |

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSettings",
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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 als gootsteentype

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor Data `storeSettings` Lake Storage Gen2 onder instellingen in op indeling gebaseerde copy sink:

| Eigenschap                 | Beschrijving                                                  | Vereist |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | De eigenschap `storeSettings` type onder moet zijn ingesteld op **AzureBlobFSWriteSettings**. | Ja      |
| copyBehavior             | Hiermee definieert u het kopieergedrag wanneer de bron bestanden is uit een gegevensarchief op basis van bestanden.<br/><br/>Toegestane waarden zijn:<br/><b>- BehoudenHiërarchie (standaard)</b>: Hiermee behoudt u de bestandshiërarchie in de doelmap. Het relatieve pad van het bronbestand naar de bronmap is identiek aan het relatieve pad van het doelbestand naar de doelmap.<br/><b>- FlattenHierarchy:</b>Alle bestanden uit de bronmap bevinden zich in het eerste niveau van de doelmap. De doelbestanden hebben automatisch gegenereerde namen. <br/><b>- MergeFiles</b>: Hiermee worden alle bestanden van de bronmap samengevoegd tot één bestand. Als de bestandsnaam is opgegeven, is de samengevoegde bestandsnaam de opgegeven naam. Anders is het een automatisch gegenereerde bestandsnaam. | Nee       |
| blockSizeInMB blockSizeInMB | Geef de blokgrootte op in MB die wordt gebruikt om gegevens naar ADLS Gen2 te schrijven. Meer informatie [over Blokblobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Toegestane waarde ligt **tussen 4 en 100 MB.** <br/>AdF bepaalt standaard automatisch de blokgrootte op basis van het type en de gegevens van het bronarchief. Voor niet-binaire kopie in ADLS Gen2, de standaard blok grootte is 100 MB om te passen in maximaal 4,95 TB gegevens. Het is mogelijk niet optimaal wanneer uw gegevens niet groot zijn, vooral wanneer u Self-hosted Integration Runtime gebruikt met een slecht netwerk, wat resulteert in een time-out of prestatieprobleem. U expliciet een blokgrootte opgeven, terwijl ervoor zorgen dat blockSizeInMB*50000 groot genoeg is om de gegevens op te slaan, anders mislukt de kopieeractiviteit. | Nee |
| maxConcurrentVerbindingen | Het aantal verbindingen om gelijktijdig verbinding te maken met het gegevensarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee       |

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
                    "type": "AzureBlobFSWriteSettings",
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
| `Folder*` | (Leeg, standaard gebruiken) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Leeg, standaard gebruiken) | waar | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | waar | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Enkele voorbeelden van recursieve en copyBehavior

In deze sectie wordt het resulterende gedrag van de kopieerbewerking beschreven voor verschillende combinaties van recursieve en copyBehavior-waarden.

| Recursieve | copyBehavior | Structuur van bronmappen | Resulterend doel |
|:--- |:--- |:--- |:--- |
| waar |behoudenHiërarchie | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap1 wordt gemaakt met dezelfde structuur als de bron:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 |
| waar |afvlakkenHiërarchie | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap1 wordt gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand5 |
| waar |mergeFiles | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap1 wordt gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5-inhoud worden samengevoegd tot één bestand met een automatisch gegenereerde bestandsnaam. |
| false |behoudenHiërarchie | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap1 wordt gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/><br/>Submap1 met Bestand3, Bestand4 en Bestand5 wordt niet opgehaald. |
| false |afvlakkenHiërarchie | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap1 wordt gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/><br/>Submap1 met Bestand3, Bestand4 en Bestand5 wordt niet opgehaald. |
| false |mergeFiles | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap1 wordt gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2-inhoud wordt samengevoegd tot één bestand met een automatisch gegenereerde bestandsnaam. automatisch gegenereerde naam voor Bestand1<br/><br/>Submap1 met Bestand3, Bestand4 en Bestand5 wordt niet opgehaald. |

## <a name="preserve-metadata-during-copy"></a>Metagegevens bewaren tijdens het kopiëren

Wanneer u bestanden van Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 kopieert naar Azure Data Lake Storage Gen2/Azure Blob, u ervoor kiezen om de bestandsmetagegevens samen met gegevens te bewaren. Meer informatie van [Metagegevens bewaren](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a>ACL's behouden van Data Lake Storage Gen1/Gen2

Wanneer u bestanden kopieert van Azure Data Lake Storage Gen1/Gen2 naar Gen2, u ervoor kiezen om de POSIX-toegangscontrolelijsten (ACL's) samen met gegevens te behouden. Meer informatie over [Het behouden van ACL's van Data Lake Storage Gen1/Gen2 tot Gen2.](copy-activity-preserve-metadata.md#preserve-acls)

>[!TIP]
>Zie Gegevens van Azure Data Lake Storage Gen1 kopiëren naar Gen2 in het algemeen [met Azure Data Lake Storage Gen1 met Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) voor een doorloop- en aanbevolen procedures.

## <a name="mapping-data-flow-properties"></a>Gegevensstroomeigenschappen toewijzen

Wanneer u gegevens in kaartgegevensstroom transformeert, u bestanden lezen en schrijven vanuit Azure Data Lake Storage Gen2 in JSON, Avro, Delimited Text of Parquet-indeling. Zie [brontransformatie](data-flow-source.md) en [sinktransformatie voor](data-flow-sink.md) meer informatie in de functie gegevensstroom toewijzing.

### <a name="source-transformation"></a>Brontransformatie

In de brontransformatie u lezen uit een container, map of afzonderlijk bestand in Azure Data Lake Storage Gen2. Op het tabblad **Bronopties** u beheren hoe de bestanden worden gelezen. 

![Bronopties](media/data-flow/sourceOptions1.png "Bronopties")

**Wildcardpad:** Als u een wildcardpatroon gebruikt, geeft ADF opdracht om elke overeenkomende map en bestand in één brontransformatie te doorlopen. Dit is een effectieve manier om meerdere bestanden binnen één stroom te verwerken. Voeg meerdere wildcard matching patronen toe met het + teken dat wordt weergegeven wanneer u over uw bestaande wildcardpatroon zweeft.

Kies in uw broncontainer een reeks bestanden die overeenkomen met een patroon. Alleen de container kan worden opgegeven in de gegevensset. Uw wildcardpad moet daarom ook uw mappad uit de hoofdmap bevatten.

Voorbeelden van jokertekens:

* ```*```Vertegenwoordigt een set tekens
* ```**```Vertegenwoordigt recursieve mapnesting
* ```?```Vervangt één teken
* ```[]```Komt overeen met een van de meer tekens in de haakjes

* ```/data/sales/**/*.csv```Haalt alle csv-bestanden onder /data/sales
* ```/data/sales/20??/**/```Krijgt alle bestanden in de 20e eeuw
* ```/data/sales/*/*/*.csv```Krijgt csv-bestanden twee niveaus onder /data/sales
* ```/data/sales/2004/*/12/[XY]1?.csv```Krijgt alle csv-bestanden in 2004 in december te beginnen met X of Y vooraf vastgesteld door een twee-cijferig e-nummer

**Hoofdpad van partitie:** Als u partitiemappen in uw bestandsbron ```key=value``` hebt met een indeling (bijvoorbeeld jaar=2019), u het bovenste niveau van die partitiemapstructuur toewijzen aan een kolomnaam in uw gegevensstroom.

Stel eerst een wildcard in om alle paden op te nemen die de partitiemappen zijn plus de bladbestanden die u wilt lezen.

![Bestandsinstellingen voor partitiebron](media/data-flow/partfile2.png "Instelling voor partitiebestanden")

Gebruik de instelling Hoofdmap partitie om te bepalen wat het bovenste niveau van de mapstructuur is. Wanneer u de inhoud van uw gegevens bekijkt via een voorbeeld van gegevens, ziet u dat ADF de opgeloste partities in elk van uw mapniveaus toevoegt.

![Hoofdpad van partitie](media/data-flow/partfile1.png "Voorbeeld van het hoofdpad van partitie")

**Lijst met bestanden:** Dit is een bestandsset. Maak een tekstbestand met een lijst met relatieve padbestanden die u wilt verwerken. Wijs dit tekstbestand aan.

**Kolom om bestandsnaam op te slaan:** Sla de naam van het bronbestand op in een kolom in uw gegevens. Voer hier een nieuwe kolomnaam in om de tekenreeks met de bestandsnaam op te slaan.

**Na voltooiing:** Kies ervoor om niets te doen met het bronbestand nadat de gegevensstroom is uitgevoerd, het bronbestand te verwijderen of het bronbestand te verplaatsen. De paden voor de verhuizing zijn relatief.

Als u bronbestanden na bewerking naar een andere locatie wilt verplaatsen, selecteert u eerst 'Verplaatsen' voor bestandsbewerking. Stel vervolgens de map 'van' in. Als u geen wildcards voor uw pad gebruikt, is de instelling 'van' dezelfde map als uw bronmap.

Als u een bronpad met jokerteken hebt, ziet uw syntaxis er hieronder als volgt uit:

```/data/sales/20??/**/*.csv```

U 'van' opgeven als

```/data/sales```

En "aan" als

```/backup/priorSales```

In dit geval worden alle bestanden die zijn ingekocht onder /data/sales verplaatst naar /backup/priorSales.

> [!NOTE]
> Bestandsbewerkingen worden alleen uitgevoerd wanneer u de gegevensstroom start vanuit een pijplijnrun (een pijplijnfout foutopsporing of uitvoeringsrun) die de activiteit Gegevensstroom uitvoeren in een pijplijn gebruikt. Bestandsbewerkingen *worden niet* uitgevoerd in de foutopsporingsmodus Gegevensstroom.

**Filter op laatst gewijzigd:** U filteren welke bestanden u verwerkt door een datumbereik op te geven van wanneer ze voor het laatst zijn gewijzigd. Alle datum-tijden zijn in UTC. 

### <a name="sink-properties"></a>Gootsteeneigenschappen

In de sinktransformatie u schrijven naar een container of map in Azure Data Lake Storage Gen2. Op het tabblad **Instellingen** u beheren hoe de bestanden worden geschreven.

![sink opties](media/data-flow/file-sink-settings.png "sink opties")

**De map wissen:** Hiermee bepaalt u of de doelmap wordt gewist voordat de gegevens worden geschreven.

**Bestandsnaam, optie:** Hiermee bepaalt u hoe de doelbestanden worden benoemd in de doelmap. De opties voor bestandsnamen zijn:
   * **Standaard:** Sta Spark toe bestanden een naam te geven op basis van standaardinstellingen voor onderdeel.
   * **Patroon:** voer een patroon in dat uw uitvoerbestanden per partitie opsomt. **Leningen[n].csv** zullen bijvoorbeeld leningen creëren1.csv, loans2.csv, enzovoort.
   * **Per partitie**: Voer één bestandsnaam per partitie in.
   * **Als gegevens in kolom**: Stel het uitvoerbestand in op de waarde van een kolom. Het pad is relatief ten opzichte van de gegevenssetcontainer, niet de doelmap. Als u een mappad in uw gegevensset hebt, wordt deze overschreven.
   * **Uitvoer naar één bestand:** combineer de partitieuitvoerbestanden in één bestand met één naam. Het pad is relatief ten opzichte van de map gegevensset. Houd er rekening mee dat te merge operatie mogelijk kan mislukken op basis van knooppuntgrootte. Deze optie wordt niet aanbevolen voor grote gegevenssets.

**Quote alles:** Hiermee bepaalt u of alle waarden in aanhalingstekens moeten worden ingesloten

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
| type | De eigenschap type van de gegevensset moet worden ingesteld op **AzureBlobFSFile**. |Ja |
| folderPath | Ga naar de map in Data Lake Storage Gen2. Als dit niet is opgegeven, wijst deze naar de wortel. <br/><br/>Het jokerfilter wordt ondersteund. Toegestane wildcards `*` zijn (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken). Gebruik `^` om te ontsnappen als uw werkelijke map naam heeft een wildcard of deze ontsnapping char is binnen. <br/><br/>Voorbeelden: bestandssysteem/map/. Zie meer voorbeelden in [voorbeelden van mappen en bestandenfilters](#folder-and-file-filter-examples). |Nee |
| fileName | Naam- of wildcardfilter voor de bestanden onder de opgegeven mapPath. Als u geen waarde voor deze eigenschap opgeeft, verwijst de gegevensset naar alle bestanden in de map. <br/><br/>Voor filter zijn `*` de toegestane jokertekens (overeenkomsten met `?` nul of meer tekens) en (komt overeen met nul of één teken).<br/>- Voorbeeld 1:`"fileName": "*.csv"`<br/>- Voorbeeld 2:`"fileName": "???20180427.txt"`<br/>Gebruik `^` om te ontsnappen als uw werkelijke bestandsnaam heeft een wildcard of deze ontsnapping char is binnen.<br/><br/>Wanneer bestandsnaam niet is opgegeven voor een uitvoergegevensset en **de hiërarchie** niet is opgegeven in de activiteitssink, genereert de kopieeractiviteit automatisch de bestandsnaam met het volgende patroon: "*Gegevens.[ activiteit uitvoeren ID GUID]. [GUID als FlattenHierarchy]. [indeling indien geconfigureerd]. [compressie indien geconfigureerd]*", bijvoorbeeld "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Als u kopieert van een tabelbron met een tabelnaam in plaats van een query, is het naampatroon "*[tabelnaam].[ formaat]. [compressie indien geconfigureerd]*", bijvoorbeeld "MyTable.csv". |Nee |
| gewijzigdDatumtimeStart | Bestandenfilteren op basis van het kenmerk Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` `modifiedDatetimeEnd`tijd binnen het tijdsbereik tussen en . De tijd wordt toegepast op de UTC-tijdzone in de indeling "2018-12-01T05:00:00Z". <br/><br/> De algehele prestaties van gegevensverplaatsing worden beïnvloed door deze instelling in te schakelen wanneer u bestandsfilter met enorme hoeveelheden bestanden wilt doen. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestandskenmerken wordt toegepast op de gegevensset. Wanneer `modifiedDatetimeStart` een datumtijdwaarde `modifiedDatetimeEnd` is, maar NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, zijn geselecteerd. Wanneer `modifiedDatetimeEnd` een datumtijdwaarde `modifiedDatetimeStart` is, maar NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, zijn geselecteerd.| Nee |
| gewijzigdDatumtimeEnd | Bestandenfilteren op basis van het kenmerk Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` `modifiedDatetimeEnd`tijd binnen het tijdsbereik tussen en . De tijd wordt toegepast op de UTC-tijdzone in de indeling "2018-12-01T05:00:00Z". <br/><br/> De algehele prestaties van gegevensverplaatsing worden beïnvloed door deze instelling in te schakelen wanneer u bestandsfilter met enorme hoeveelheden bestanden wilt doen. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestandskenmerken wordt toegepast op de gegevensset. Wanneer `modifiedDatetimeStart` een datumtijdwaarde `modifiedDatetimeEnd` is, maar NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, zijn geselecteerd. Wanneer `modifiedDatetimeEnd` een datumtijdwaarde `modifiedDatetimeStart` is, maar NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, zijn geselecteerd.| Nee |
| formaat | Als u bestanden wilt kopiëren zoals tussen bestandengebaseerde opslag (binaire kopie), slaat u de sectie opmaak in zowel de definities van de invoer- als de uitvoergegevensset over.<br/><br/>Als u bestanden met een specifieke indeling wilt ontleden of genereren, worden de volgende bestandsindelingstypen ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**en **ParquetFormat**. Stel de **eigenschap type** onder **opmaak** in op een van deze waarden. Zie voor meer informatie de secties [Tekstindeling](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-indeling](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro-formaat,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [ORC-indeling](supported-file-formats-and-compression-codecs-legacy.md#orc-format)en [Parketformaat.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Nee (alleen voor binaire kopie scenario) |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Zie [Ondersteunde bestandsindelingen en compressiecodecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)voor meer informatie.<br/>Ondersteunde typen zijn **GZip,** **Deflate,** **BZip2**en **ZipDeflate**.<br/>Ondersteunde niveaus zijn **optimaal** en **snelst.** |Nee |

>[!TIP]
>Als u alle bestanden onder een map wilt kopiëren, geeft u **alleen mapPath** op.<br>Als u één bestand met een bepaalde naam wilt kopiëren, geeft u **mapPath** op met een maponderdeel en **bestandsnaam** met een bestandsnaam.<br>Als u een subset van bestanden onder een map wilt kopiëren, geeft u **mapPath** op met een maponderdeel en **bestandsnaam** met een wildcardfilter. 

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

### <a name="legacy-copy-activity-source-model"></a>Bronmodel voor verouderde kopieeractiviteit

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron voor kopieeractiviteit moet zijn ingesteld op **AzureBlobFSSource**. |Ja |
| Recursieve | Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. Wanneer recursieve is ingesteld op true en de gootsteen is een bestand op basis van opslag, een lege map of submap wordt niet gekopieerd of gemaakt op de gootsteen.<br/>Toegestane waarden zijn **waar** (standaard) en **onwaar.** | Nee |
| maxConcurrentVerbindingen | Het aantal verbindingen om gelijktijdig verbinding te maken met het gegevensarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee |

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

### <a name="legacy-copy-activity-sink-model"></a>Voorbeeld van kopieeractiviteitsinkmodel

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de inhoudsgroep voor kopiëren moet worden ingesteld op **AzureBlobFSSink**. |Ja |
| copyBehavior | Hiermee definieert u het kopieergedrag wanneer de bron bestanden is uit een gegevensarchief op basis van bestanden.<br/><br/>Toegestane waarden zijn:<br/><b>- BehoudenHiërarchie (standaard)</b>: Hiermee behoudt u de bestandshiërarchie in de doelmap. Het relatieve pad van het bronbestand naar de bronmap is identiek aan het relatieve pad van het doelbestand naar de doelmap.<br/><b>- FlattenHierarchy:</b>Alle bestanden uit de bronmap bevinden zich in het eerste niveau van de doelmap. De doelbestanden hebben automatisch gegenereerde namen. <br/><b>- MergeFiles</b>: Hiermee worden alle bestanden van de bronmap samengevoegd tot één bestand. Als de bestandsnaam is opgegeven, is de samengevoegde bestandsnaam de opgegeven naam. Anders is het een automatisch gegenereerde bestandsnaam. | Nee |
| maxConcurrentVerbindingen | Het aantal verbindingen om gelijktijdig verbinding te maken met het gegevensarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee |

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

## <a name="next-steps"></a>Volgende stappen

Zie [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Data Factory.
