---
title: Gegevens kopiëren naar of van Azure Data Lake Storage Gen1
description: Meer informatie over het kopiëren van gegevens uit ondersteunde brongegevensopslag naar Azure Data Lake Store of van Data Lake Store naar ondersteunde sinkstores met Behulp van Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 61bb8fe950de8cd9be91bc76bd24aa0151f3fb79
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415417"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Gegevens kopiëren naar of van Azure Data Lake Storage Gen1 met Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van Azure Data Factory die u gebruikt:"]
> * [Versie 1](v1/data-factory-azure-datalake-connector.md)
> * [Huidige versie](connector-azure-data-lake-store.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u gegevens van en naar Azure Data Lake Storage Gen1 kopieert. Lees het [inleidende artikel](introduction.md)voor meer informatie over Azure Data Factory.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Data Lake Storage Gen1-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md) 
- [Gegevensstroom toewijzen](concepts-data-flow-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Activiteit Metagegevens](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)

Specifiek, met deze connector u:

- Kopieer bestanden met behulp van een van de volgende verificatiemethoden: serviceprincipal of beheerde identiteiten voor Azure-bronnen.
- Kopieer bestanden zoals het is of ontleed t.a.v. bestanden met de [ondersteunde bestandsindelingen en compressiecodecs](supported-file-formats-and-compression-codecs.md).
- [ACL's behouden](#preserve-acls-to-data-lake-storage-gen2) wanneer u kopieert naar Azure Data Lake Storage Gen2.

> [!IMPORTANT]
> Als u gegevens kopieert met behulp van de zelf gehoste runtime `<ADLS account name>.azuredatalakestore.net` `login.microsoftonline.com/<tenant>/oauth2/token` voor integratie, configureert u de bedrijfsfirewall om uitgaand verkeer van en naar poort 443 toe te staan. Dit laatste is de Azure Security Token Service waarmee de integratieruntime moet communiceren om het toegangstoken te krijgen.

## <a name="get-started"></a>Aan de slag

> [!TIP]
> Zie [Gegevens laden in Azure Data Lake Store](load-azure-data-lake-store.md)voor een doorloop van het gebruik van de Azure Data Lake Store-connector.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor Azure Data Lake Store.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de gekoppelde Azure Data Lake Store-service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De `type` eigenschap moet zijn ingesteld op **AzureDataLakeStore**. | Ja |
| dataLakeStoreUri | Informatie over het Azure Data Lake Store-account. Deze informatie neemt een van `https://[accountname].azuredatalakestore.net/webhdfs/v1` `adl://[accountname].azuredatalakestore.net/`de volgende formaten: of . | Ja |
| subscriptionId | De Azure-abonnements-ID waartoe het Data Lake Store-account behoort. | Vereist voor gootsteen |
| resourceGroupName | De naam van de Azure-brongroep waartoe het Data Lake Store-account behoort. | Vereist voor gootsteen |
| connectVia | De [inburgeringsruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. U de runtime voor Azure-integratie of een zelfgehoste nawerking van de integratie gebruiken als uw gegevensarchief zich in een privénetwerk bevindt. Als deze eigenschap niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

### <a name="use-service-principal-authentication"></a>Serviceprincipal-verificatie gebruiken

Voer deze stappen uit om serviceprincipal-verificatie te gebruiken.

1. Registreer een toepassingsentiteit in Azure Active Directory en geef deze toegang tot Data Lake Store. Zie [Service-to-service-verificatie voor](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)gedetailleerde stappen . Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:

    - Toepassings-id
    - Toepassingssleutel
    - Tenant-id

2. Geef de serviceprincipal de juiste toestemming. Zie voorbeelden over hoe toestemming werkt in Data Lake Storage Gen1 vanuit [Access-besturingselement in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Als bron**: In **Data explorer** > **Access**, verlenen ten minste **Uitvoer** toestemming voor alle upstream mappen met inbegrip van de root, samen met **Lees** toestemming voor de bestanden te kopiëren. U ervoor kiezen om deze **map en alle kinderen** toe te voegen voor recursieve, en toe te voegen als een **toegangsmachtiging en een standaard machtiging stotiste.** Er is geen vereiste voor toegangsbeheer op accountniveau (IAM).
    - **Als sink**: In **Data explorer** > **Access,** verlenen ten minste **Uitvoer** toestemming voor alle upstream mappen met inbegrip van de root, samen met **Schrijf** toestemming voor de gootsteen map. U ervoor kiezen om deze **map en alle kinderen** toe te voegen voor recursieve, en toe te voegen als een **toegangsmachtiging en een standaard machtiging stotiste.** Als u een Azure-integratieruntijd gebruikt om te kopiëren (zowel bron als gootsteen zijn in de cloud), verleent u in IAM ten minste de rol **Reader** om Data Factory de regio voor Data Lake Store te laten detecteren. Als u deze IAM-rol wilt vermijden, maakt u expliciet [een azure-integratieruntijd](create-azure-integration-runtime.md#create-azure-ir) met de locatie van De Data Lake Store. Als uw Data Lake Store zich bijvoorbeeld in West-Europa bevindt, maakt u een azure-integratieruntijd met locatie die is ingesteld op 'West-Europa'. Koppel ze aan de gekoppelde service van de Data Lake Store, zoals in het volgende voorbeeld wordt weergegeven.

De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| servicePrincipalId | Geef de client-id van de toepassing op. | Ja |
| servicePrincipalKey | Geef de sleutel van de toepassing op. Markeer dit `SecureString` veld als een om het veilig op te slaan in Data Factory, of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |
| tenant | Geef de tenantgegevens op, zoals domeinnaam of tenant-id, waaronder uw toepassing zich bevindt. U deze ophalen door met de muis in de rechterbovenhoek van de Azure-portal te zweven. | Ja |

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

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Beheerde identiteiten gebruiken voor Azure-bronverificatie

Een gegevensfabriek kan worden gekoppeld aan een [beheerde identiteit voor Azure-resources,](data-factory-service-identity.md)die deze specifieke gegevensfabriek vertegenwoordigt. U deze beheerde identiteit rechtstreeks gebruiken voor Data Lake Store-verificatie, vergelijkbaar met het gebruik van uw eigen serviceprincipal. Hiermee kan deze aangewezen fabriek toegang krijgen tot en kopie van gegevens naar of van Data Lake Store.

Voer deze stappen uit om beheerde identiteiten te gebruiken voor azure-verificatie, als u deze stappen wilt uitvoeren.

1. [Haal de beheerde identiteitsgegevens](data-factory-service-identity.md#retrieve-managed-identity) van de gegevensfabriek op door de waarde te kopiëren van de "Service Identity Application ID" die samen met uw fabriek wordt gegenereerd.

2. Geef de beheerde identiteit toegang tot Data Lake Store. Zie voorbeelden over hoe toestemming werkt in Data Lake Storage Gen1 vanuit [Access-besturingselement in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Als bron**: In **Data explorer** > **Access**, verlenen ten minste **Uitvoer** toestemming voor alle upstream mappen met inbegrip van de root, samen met **Lees** toestemming voor de bestanden te kopiëren. U ervoor kiezen om deze **map en alle kinderen** toe te voegen voor recursieve, en toe te voegen als een **toegangsmachtiging en een standaard machtiging stotiste.** Er is geen vereiste voor toegangsbeheer op accountniveau (IAM).
    - **Als sink**: In **Data explorer** > **Access,** verlenen ten minste **Uitvoer** toestemming voor alle upstream mappen met inbegrip van de root, samen met **Schrijf** toestemming voor de gootsteen map. U ervoor kiezen om deze **map en alle kinderen** toe te voegen voor recursieve, en toe te voegen als een **toegangsmachtiging en een standaard machtiging stotiste.** Als u een Azure-integratieruntijd gebruikt om te kopiëren (zowel bron als gootsteen zijn in de cloud), verleent u in IAM ten minste de rol **Reader** om Data Factory de regio voor Data Lake Store te laten detecteren. Als u deze IAM-rol wilt vermijden, maakt u expliciet [een azure-integratieruntijd](create-azure-integration-runtime.md#create-azure-ir) met de locatie van De Data Lake Store. Koppel ze aan de gekoppelde service van de Data Lake Store, zoals in het volgende voorbeeld wordt weergegeven.

In Azure Data Factory hoeft u geen eigenschappen op te geven naast de algemene Gegevensmeerarchiefgegevens in de gekoppelde service.

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

Zie het artikel [Gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor Azure `location` Data Lake Store Gen1 onder instellingen in de op indeling gebaseerde gegevensset:

| Eigenschap   | Beschrijving                                                  | Vereist |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | De eigenschap `location` type onder in de gegevensset moet worden ingesteld op **AzureDataLakeStoreLocatie**. | Ja      |
| folderPath | Het pad naar een map. Als u een wildcard wilt gebruiken om mappen te filteren, slaat u deze instelling over en geeft u deze op in de instellingen voor activiteitsbronnen. | Nee       |
| fileName   | De bestandsnaam onder de opgegeven mapPath. Als u een wildcard wilt gebruiken om bestanden te filteren, slaat u deze instelling over en geeft u deze op in de instellingen van de activiteitsbron. | Nee       |

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

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie [Pijplijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de bron en gootsteen van Azure Data Lake Store.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store als bron

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor Azure `storeSettings` Data Lake Store Gen1 onder instellingen in de op indeling gebaseerde kopieerbron:

| Eigenschap                 | Beschrijving                                                  | Vereist                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | De eigenschap `storeSettings` type onder moet zijn ingesteld op **AzureDataLakeStoreReadSettings**. | Ja                                           |
| Recursieve                | Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. Wanneer recursieve is ingesteld op true en de gootsteen is een bestand op basis van opslag, een lege map of submap wordt niet gekopieerd of gemaakt op de gootsteen. Toegestane waarden zijn **waar** (standaard) en **onwaar.** | Nee                                            |
| wildcardFolderPath       | Het mappad met jokertekens om bronmappen te filteren. <br>Toegestane wildcards `*` zijn (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken). Gebruik `^` om te ontsnappen als uw werkelijke map naam heeft een wildcard of deze ontsnapping char binnen. <br>Zie meer voorbeelden in [voorbeelden van mappen en bestandenfilters](#folder-and-file-filter-examples). | Nee                                            |
| wildcardFileName         | De bestandsnaam met jokertekens onder de opgegeven mapPath/wildcardFolderPath om bronbestanden te filteren. <br>Toegestane wildcards `*` zijn (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken). Gebruik `^` om te ontsnappen als uw werkelijke map naam heeft een wildcard of deze ontsnapping char binnen. Zie meer voorbeelden in [voorbeelden van mappen en bestandenfilters](#folder-and-file-filter-examples). | Ja `fileName` als deze niet is opgegeven in de gegevensset |
| gewijzigdDatumtimeStart    | Bestandenfilteren op basis van het kenmerk Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` `modifiedDatetimeEnd`tijd binnen het tijdsbereik tussen en . De tijd wordt toegepast op de UTC-tijdzone in de indeling "2018-12-01T05:00:00Z". <br> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestandskenmerken wordt toegepast op de gegevensset. Wanneer `modifiedDatetimeStart` een datumtijdwaarde `modifiedDatetimeEnd` is, maar NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, zijn geselecteerd. Wanneer `modifiedDatetimeEnd` een datumtijdwaarde `modifiedDatetimeStart` is, maar NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, zijn geselecteerd. | Nee                                            |
| gewijzigdDatumtimeEnd      | Hetzelfde als hierboven.                                               | Nee                                            |
| maxConcurrentVerbindingen | Het aantal verbindingen om gelijktijdig verbinding te maken met het opslagarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee                                            |

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureDataLakeStoreReadSettings",
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

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store als gootsteen

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor Azure `storeSettings` Data Lake Store Gen1 onder instellingen in de op indeling gebaseerde copy sink:

| Eigenschap                 | Beschrijving                                                  | Vereist |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | De eigenschap `storeSettings` type onder moet zijn ingesteld op **AzureDataLakeStoreWriteSettings**. | Ja      |
| copyBehavior             | Hiermee definieert u het kopieergedrag wanneer de bron bestanden is uit een gegevensarchief op basis van bestanden.<br/><br/>Toegestane waarden zijn:<br/><b>- BehoudenHiërarchie (standaard)</b>: Hiermee behoudt u de bestandshiërarchie in de doelmap. Het relatieve pad van het bronbestand naar de bronmap is identiek aan het relatieve pad van het doelbestand naar de doelmap.<br/><b>- FlattenHierarchy:</b>Alle bestanden uit de bronmap bevinden zich in het eerste niveau van de doelmap. De doelbestanden hebben automatisch gegenereerde namen. <br/><b>- MergeFiles</b>: Hiermee worden alle bestanden van de bronmap samengevoegd tot één bestand. Als de bestandsnaam is opgegeven, is de samengevoegde bestandsnaam de opgegeven naam. Anders is het een automatisch gegenereerde bestandsnaam. | Nee       |
| expiryDateTime | Hiermee geeft u de vervaldatum van de geschreven bestanden op. De tijd wordt toegepast op de UTC-tijd in de indeling "2020-03-01T08:00:00Z". Standaard is het NULL, wat betekent dat de geschreven bestanden nooit zijn verlopen. | Nee |
| maxConcurrentVerbindingen | Het aantal verbindingen om gelijktijdig verbinding te maken met het gegevensarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee       |

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
                    "type": "AzureDataLakeStoreWriteSettings",
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

### <a name="examples-of-behavior-of-the-copy-operation"></a>Voorbeelden van gedrag van de kopieerbewerking

In deze sectie wordt het resulterende gedrag van `recursive` `copyBehavior` de kopieerbewerking beschreven voor verschillende combinaties van en waarden.

| Recursieve | copyBehavior | Structuur van bronmappen | Resulterend doel |
|:--- |:--- |:--- |:--- |
| waar |behoudenHiërarchie | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap1 wordt gemaakt met dezelfde structuur als de bron:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5. |
| waar |afvlakkenHiërarchie | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap1 wordt gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand5 |
| waar |mergeFiles | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap1 wordt gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5-inhoud worden samengevoegd tot één bestand, met een automatisch gegenereerde bestandsnaam. |
| false |behoudenHiërarchie | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap1 wordt gemaakt met de volgende structuur:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/><br/>Submap1 met Bestand3, File4 en File5 worden niet opgehaald. |
| false |afvlakkenHiërarchie | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap1 wordt gemaakt met de volgende structuur:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/><br/>Submap1 met Bestand3, File4 en File5 worden niet opgehaald. |
| false |mergeFiles | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap1 wordt gemaakt met de volgende structuur:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2-inhoud wordt samengevoegd tot één bestand met automatisch gegenereerde bestandsnaam. automatisch gegenereerde naam voor Bestand1<br/><br/>Submap1 met Bestand3, File4 en File5 worden niet opgehaald. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>ACL's behouden voor Data Lake Storage Gen2

>[!TIP]
>Zie Gegevens van Azure Data Lake Storage Gen1 kopiëren naar Gen2 in het algemeen [met Azure Data Lake Storage Gen1 met Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) voor een doorloop- en aanbevolen procedures.

Zie [ACL's](copy-activity-preserve-metadata.md#preserve-acls)van Data Lake Storage Gen1 behouden als u de toegangscontrolelijsten (ACL's) wilt repliceren, samen met gegevensbestanden wanneer u een upgrade uitvoert van Data Lake Storage Gen1 naar Data Lake Storage Gen2.

## <a name="mapping-data-flow-properties"></a>Gegevensstroomeigenschappen toewijzen

Wanneer u gegevens in kaartgegevensstroom transformeert, u bestanden lezen en schrijven vanuit Azure Data Lake Storage Gen1 in JSON, Avro, Delimited Text of Parquet-indeling. Zie [brontransformatie](data-flow-source.md) en [sinktransformatie voor](data-flow-sink.md) meer informatie in de functie gegevensstroom toewijzing.

### <a name="source-transformation"></a>Brontransformatie

In de brontransformatie u lezen uit een container, map of afzonderlijk bestand in Azure Data Lake Storage Gen1. Op het tabblad **Bronopties** u beheren hoe de bestanden worden gelezen. 

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

In de sinktransformatie u schrijven naar een container of map in Azure Data Lake Storage Gen1. Op het tabblad **Instellingen** u beheren hoe de bestanden worden geschreven.

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
| type | De eigenschap type van de gegevensset moet worden ingesteld op **AzureDataLakeStoreFile**. |Ja |
| folderPath | Pad naar de map in Data Lake Store. Als dit niet is opgegeven, wijst deze naar de wortel. <br/><br/>Het jokerfilter wordt ondersteund. Toegestane wildcards `*` zijn (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken). Gebruik `^` om te ontsnappen als uw werkelijke map naam heeft een wildcard of deze ontsnapping char binnen. <br/><br/>Bijvoorbeeld: rootmap/submap/. Zie meer voorbeelden in [voorbeelden van mappen en bestandenfilters](#folder-and-file-filter-examples). |Nee |
| fileName | Naam- of wildcardfilter voor de bestanden onder de opgegeven mapPath. Als u geen waarde voor deze eigenschap opgeeft, verwijst de gegevensset naar alle bestanden in de map. <br/><br/>Voor filter zijn `*` de toegestane jokertekens (overeenkomsten met `?` nul of meer tekens) en (komt overeen met nul of één teken).<br/>- Voorbeeld 1:`"fileName": "*.csv"`<br/>- Voorbeeld 2:`"fileName": "???20180427.txt"`<br/>Gebruik `^` om te ontsnappen als uw werkelijke bestandsnaam heeft een wildcard of deze ontsnapping char binnen.<br/><br/>Wanneer bestandsnaam niet is opgegeven voor een uitvoergegevensset en **de hiërarchie** niet is opgegeven in de activiteitssink, genereert de kopieeractiviteit automatisch de bestandsnaam met het volgende patroon: "*Gegevens.[ activiteit uitvoeren ID GUID]. [GUID als FlattenHierarchy]. [indeling indien geconfigureerd]. [compressie indien geconfigureerd]*", bijvoorbeeld "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Als u kopieert van een tabelbron met behulp van een tabelnaam in plaats van een query, is het naampatroon "*[tabelnaam].[ formaat]. [compressie indien geconfigureerd]*", bijvoorbeeld "MyTable.csv". |Nee |
| gewijzigdDatumtimeStart | Bestandenfilteren op basis van het kenmerk Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` `modifiedDatetimeEnd`tijd binnen het tijdsbereik tussen en . De tijd wordt toegepast op de UTC-tijdzone in de indeling "2018-12-01T05:00:00Z". <br/><br/> De algehele prestaties van gegevensverplaatsing worden beïnvloed door deze instelling in te schakelen wanneer u bestandsfilter met enorme hoeveelheden bestanden wilt doen. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestandskenmerken wordt toegepast op de gegevensset. Wanneer `modifiedDatetimeStart` een datumtijdwaarde `modifiedDatetimeEnd` is, maar NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, zijn geselecteerd. Wanneer `modifiedDatetimeEnd` een datumtijdwaarde `modifiedDatetimeStart` is, maar NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, zijn geselecteerd.| Nee |
| gewijzigdDatumtimeEnd | Bestandenfilteren op basis van het kenmerk Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` `modifiedDatetimeEnd`tijd binnen het tijdsbereik tussen en . De tijd wordt toegepast op de UTC-tijdzone in de indeling "2018-12-01T05:00:00Z". <br/><br/> De algehele prestaties van gegevensverplaatsing worden beïnvloed door deze instelling in te schakelen wanneer u bestandsfilter met enorme hoeveelheden bestanden wilt doen. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestandskenmerken wordt toegepast op de gegevensset. Wanneer `modifiedDatetimeStart` een datumtijdwaarde `modifiedDatetimeEnd` is, maar NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, zijn geselecteerd. Wanneer `modifiedDatetimeEnd` een datumtijdwaarde `modifiedDatetimeStart` is, maar NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, zijn geselecteerd.| Nee |
| formaat | Als u bestanden wilt kopiëren zoals tussen bestandsgebaseerde opslag (binaire kopie), slaat u de sectie opmaak over in definities van zowel invoer- als uitvoergegevenssets.<br/><br/>Als u bestanden met een specifieke indeling wilt ontleden of genereren, worden de volgende bestandsindelingstypen ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**en **ParquetFormat**. Stel de **eigenschap type** onder **opmaak** in op een van deze waarden. Zie voor meer informatie de secties [Tekstindeling](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-indeling](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro-formaat](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc-indeling](supported-file-formats-and-compression-codecs-legacy.md#orc-format)en [Parketformaat.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Nee (alleen voor binaire kopie scenario) |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Zie [Ondersteunde bestandsindelingen en compressiecodecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)voor meer informatie.<br/>Ondersteunde typen zijn **GZip,** **Deflate,** **BZip2**en **ZipDeflate**.<br/>Ondersteunde niveaus zijn **optimaal** en **snelst.** |Nee |

>[!TIP]
>Als u alle bestanden onder een map wilt kopiëren, geeft u **alleen mapPath** op.<br>Als u één bestand met een bepaalde naam wilt kopiëren, geeft u **mapPath** op met een maponderdeel en **bestandsnaam** met een bestandsnaam.<br>Als u een subset van bestanden onder een map wilt kopiëren, geeft u **mapPath** op met een maponderdeel en **bestandsnaam** met een wildcardfilter. 

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

### <a name="legacy-copy-activity-source-model"></a>Bronmodel voor verouderde kopieeractiviteit

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De `type` eigenschap van de bron voor kopieeractiviteit moet zijn ingesteld op **AzureDataLakeStoreSource**. |Ja |
| Recursieve | Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. Wanneer `recursive` is ingesteld op true en de gootsteen is een bestand op basis van opslag, een lege map of submap wordt niet gekopieerd of gemaakt op de gootsteen. Toegestane waarden zijn **waar** (standaard) en **onwaar.** | Nee |
| maxConcurrentVerbindingen | Het aantal verbindingen om gelijktijdig verbinding te maken met het gegevensarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee |

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

### <a name="legacy-copy-activity-sink-model"></a>Voorbeeld van kopieeractiviteitsinkmodel

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De `type` eigenschap van de inhoud van de kopieeractiviteit moet worden ingesteld op **AzureDataLakeStoreSink**. |Ja |
| copyBehavior | Hiermee definieert u het kopieergedrag wanneer de bron bestanden is uit een gegevensarchief op basis van bestanden.<br/><br/>Toegestane waarden zijn:<br/><b>- BehoudenHiërarchie (standaard)</b>: Hiermee behoudt u de bestandshiërarchie in de doelmap. Het relatieve pad van het bronbestand naar de bronmap is identiek aan het relatieve pad van het doelbestand naar de doelmap.<br/><b>- FlattenHierarchy:</b>Alle bestanden uit de bronmap bevinden zich in het eerste niveau van de doelmap. De doelbestanden hebben automatisch gegenereerde namen. <br/><b>- MergeFiles</b>: Hiermee worden alle bestanden van de bronmap samengevoegd tot één bestand. Als de bestandsnaam is opgegeven, is de samengevoegde bestandsnaam de opgegeven naam. Anders wordt de bestandsnaam automatisch gegenereerd. | Nee |
| maxConcurrentVerbindingen | Het aantal verbindingen om gelijktijdig verbinding te maken met het gegevensarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee |

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

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
