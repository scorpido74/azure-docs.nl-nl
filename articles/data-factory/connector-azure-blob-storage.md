---
title: Gegevens kopiëren en transformeren in Azure Blob-opslag
description: Meer informatie over het kopiëren van gegevens van en naar Blob-opslag en het transformeren van gegevens in Blob-opslag met Behulp van Data Factory.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: b04ff409c95980a1569a2709a475dd8ec74d59b3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415490"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Gegevens kopiëren en transformeren in Azure Blob-opslag met Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-azure-blob-connector.md)
> * [Huidige versie](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u Activiteit kopiëren in Azure Data Factory gebruikt om gegevens van en naar Azure Blob-opslag te kopiëren en gegevensstroom te gebruiken om gegevens te transformeren in Azure Blob-opslag. Lees het [inleidende artikel](introduction.md)voor meer informatie over Azure Data Factory.

>[!TIP]
>Voor het migratiescenario voor gegevensbestanden of gegevensmagazijnen vindt u meer informatie uit [Azure Data Factory gebruiken om gegevens uit uw gegevensmeer of gegevensmagazijn naar Azure te migreren.](data-migration-guidance-overview.md)

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Blob-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Gegevensstroom toewijzen](concepts-data-flow-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Activiteit Metagegevens](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)

Voor Kopieeractiviteit ondersteunt deze Blob-opslagconnector:

- Blobs kopiëren van en naar Azure-opslagaccounts voor algemene doeleinden en hot/cool blob-opslag. 
- Blobs kopiëren met accountsleutel, service-handtekening voor gedeelde toegang, serviceprincipal of beheerde identiteiten voor Azure-bronverificaties.
- Blobs kopiëren van blok-, app-en-paginablobs of paginablobs en gegevens kopiëren om alleen blobs te blokkeren.
- Blobs kopiëren zoals het is of blobs ontleedt of genereert blobs met [ondersteunde bestandsindelingen en compressiecodecs.](supported-file-formats-and-compression-codecs.md)
- [Bestandsmetagegevens behouden tijdens het kopiëren](#preserve-metadata-during-copy).

>[!IMPORTANT]
>Als u vertrouwde **Microsoft-services toestaan toegang te geven tot deze opslagaccountoptie** in azure storage-firewallinstellingen en azure-integratieruntime wilt gebruiken om verbinding te maken met uw Blob Storage, moet u [beheerde identiteitsverificatie](#managed-identity)gebruiken.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabrieken te definiëren die specifiek zijn voor Blob-opslag.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

Azure Blob-connector ondersteunt de volgende verificatietypen, verwijzen naar de bijbehorende sectie over details:

- [Verificatie van accountsleutel](#account-key-authentication)
- [Verificatie van handtekening voor gedeelde toegang](#shared-access-signature-authentication)
- [Verificatie van service-principal](#service-principal-authentication)
- [Beheerde identiteiten voor Azure-bronverificatie](#managed-identity)

>[!NOTE]
>Wanneer u PolyBase gebruikt om gegevens in SQL Data Warehouse te laden, moet u, als uw bron- of faseringsblobopslag is geconfigureerd met Virtual Network-eindpunt, beheerde identiteitsverificatie gebruiken zoals vereist door PolyBase en Zelfgehoste Integratieruntime gebruiken met versie 3.18 of hoger. Zie de sectie [beheerde identiteitsverificatie](#managed-identity) met meer configuratievereisten.

>[!NOTE]
>HDInsights- en Azure Machine Learning-activiteiten ondersteunen alleen azure blob-opslagaccountsleutelverificatie.

### <a name="account-key-authentication"></a>Verificatie van accountsleutel

Als u verificatie van opslagaccountsleutels wilt gebruiken, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **AzureBlobStorage** (voorgesteld) of **AzureStorage** (zie opmerkingen hieronder). |Ja |
| Connectionstring | Geef de informatie op die nodig is om verbinding te maken met Opslag voor de eigenschap connectionString. <br/> U accountsleutel ook in Azure `accountKey` Key Vault plaatsen en de configuratie uit de verbindingstekenreeks halen. Raadpleeg de volgende voorbeelden en [store-referenties in het Azure Key Vault-artikel](store-credentials-in-key-vault.md) met meer details. |Ja |
| connectVia | De [inburgeringsruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. U Azure Integration Runtime of Self-hosted Integration Runtime gebruiken (als uw gegevensarchief zich in een privénetwerk bevindt). Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

>[!NOTE]
>Secundair Klodderserviceeindpunt wordt niet ondersteund bij het gebruik van accountsleutelverificatie. U andere verificatietypen gebruiken.

>[!NOTE]
>Als u de gekoppelde service van het type AzureStorage gebruikt, wordt deze nog steeds ondersteund als u wordt voorgesteld om dit nieuwe gekoppelde servicetype 'AzureBlobStorage' in de toekomst te gebruiken.

**Voorbeeld:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voorbeeld: accountsleutel opslaan in Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>Verificatie van handtekening voor gedeelde toegang

Een handtekening voor gedeelde toegang biedt gedelegeerde toegang tot bronnen in uw opslagaccount. U een handtekening voor gedeelde toegang gebruiken om een client gedurende een bepaalde tijd beperkte machtigingen te verlenen aan objecten in uw opslagaccount. U hoeft geen toegangssleutels voor uw account te delen. De handtekening voor gedeelde toegang is een URI die in de queryparameters alle informatie omvat die nodig is voor geverifieerde toegang tot een opslagbron. Om toegang te krijgen tot opslagbronnen met de handtekening van gedeelde toegang, hoeft de client alleen de handtekening voor gedeelde toegang door te geven aan de juiste constructor of methode. Zie Handtekeningen voor gedeelde toegang voor meer informatie over handtekeningen voor gedeelde [toegang: Het handtekeningmodel voor gedeelde toegang begrijpen](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Data Factory ondersteunt nu zowel **handtekeningen voor gedeelde toegang voor services** als handtekeningen voor gedeelde **accounttoegang.** Zie Beperkte toegang tot Azure [Storage-bronnen verlenen met behulp van gedeelde toegangshandtekeningen (SAS)](../storage/common/storage-sas-overview.md)voor meer informatie over handtekeningen voor gedeelde toegang.
>- In latere gegevenssetconfiguratie is het mappad het absolute pad dat begint vanaf containerniveau. U moet er een configureren die is afgestemd op het pad in uw SAS URI.

Als u verificatie van gedeelde toegangshandtekeningen wilt gebruiken, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **AzureBlobStorage** (voorgesteld) of **AzureStorage** (zie opmerkingen hieronder). |Ja |
| sasUri sasUri | Geef de uri voor gedeelde toegangshandtekeningen op voor de opslagbronnen zoals blob/container. <br/>Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory. U sas-token ook in Azure Key Vault plaatsen om automatische rotatie te gebruiken en het tokengedeelte te verwijderen. Raadpleeg de volgende voorbeelden en [store-referenties in het Azure Key Vault-artikel](store-credentials-in-key-vault.md) met meer details. |Ja |
| connectVia | De [inburgeringsruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. U de runtime azure-integratie of de runtime voor zelfgehoste integratie gebruiken (als uw gegevensarchief zich in een privénetwerk bevindt). Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

>[!NOTE]
>Als u de gekoppelde service van het type AzureStorage gebruikt, wordt deze nog steeds ondersteund als u wordt voorgesteld om dit nieuwe gekoppelde servicetype 'AzureBlobStorage' in de toekomst te gebruiken.

**Voorbeeld:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<container>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voorbeeld: accountsleutel opslaan in Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<container>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Wanneer u uri voor gedeelde toegangshandtekeningen maakt, moet u rekening houden met de volgende punten:

- Stel de juiste lees-/schrijfmachtigingen in op objecten op basis van hoe de gekoppelde service (lezen, schrijven, lezen/schrijven) wordt gebruikt in uw gegevensfabriek.
- Stel **de vervaldatum** op de juiste manier in. Zorg ervoor dat de toegang tot opslagobjecten niet verloopt binnen de actieve periode van de pijplijn.
- De URI moet worden gemaakt op de juiste container / blob op basis van de noodzaak. Met een uri met gedeelde toegangshandtekening tot een blob heeft Data Factory toegang tot die specifieke blob. Met een uri met gedeelde toegangshandtekening tot een Blob-opslagcontainer kan Data Factory blobs in die container herhalen. Als u later toegang wilt bieden tot meer of minder objecten of om de uri voor gedeelde toegangshandtekeningen bij te werken, moet u de gekoppelde service bijwerken met de nieuwe URI.

### <a name="service-principal-authentication"></a>Verificatie van service-principal

Raadpleeg voor de hoofdverificatie van azure storageservice in het algemeen [de toegang tot Azure Storage verifiëren met Azure Active Directory](../storage/common/storage-auth-aad.md).

Voer de volgende stappen uit om serviceprincipal-verificatie te gebruiken:

1. Registreer een toepassingsentiteit in Azure Active Directory (Azure AD) door Uw toepassing te registreren [bij een Azure AD-tenant.](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:

    - Toepassings-id
    - Toepassingssleutel
    - Tenant-id

2. Geef de serviceprincipal de juiste toestemming in Azure Blob-opslag. Raadpleeg [toegangsrechten beheren voor Azure Storage-gegevens met RBAC](../storage/common/storage-auth-aad-rbac.md) met meer informatie over de rollen.

    - **Als bron**, in Access control (IAM), verlenen ten minste **Storage Blob Data Reader** rol.
    - **Als sink**, in Access control (IAM), verlenen ten minste **Storage Blob Data Contributor** rol.

Deze eigenschappen worden ondersteund voor een Azure Blob-opslaggekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op **AzureBlobStorage**. |Ja |
| serviceEndpoint | Geef het eindpunt van de Azure `https://<accountName>.blob.core.windows.net/`Blob-opslagservice op met het patroon van . |Ja |
| servicePrincipalId | Geef de client-id van de toepassing op. | Ja |
| servicePrincipalKey | Geef de sleutel van de toepassing op. Markeer dit veld als een **SecureString** om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |
| tenant | Geef de tenantgegevens op (domeinnaam of tenant-id) waaronder uw toepassing zich bevindt. Haal deze op door met de muis in de rechterbovenhoek van de Azure-portal te zweven. | Ja |
| connectVia | De [inburgeringsruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. U Azure Integration Runtime of Self-hosted Integration Runtime gebruiken (als uw gegevensarchief zich in een privénetwerk bevindt). Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

>[!NOTE]
>Serviceprincipal-verificatie wordt alleen ondersteund door de gekoppelde service van het type AzureBlobStorage, maar niet door de vorige gekoppelde service 'AzureStorage'.

**Voorbeeld:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
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

Een gegevensfabriek kan worden gekoppeld aan een [beheerde identiteit voor Azure-resources,](data-factory-service-identity.md)die deze specifieke gegevensfabriek vertegenwoordigt. U deze beheerde identiteit rechtstreeks gebruiken voor Blob-opslagverificatie, vergelijkbaar met het gebruik van uw eigen serviceprincipal. Hiermee kan deze aangewezen fabriek toegang krijgen tot en gegevens kopiëren van/naar uw Blob-opslag.

Raadpleeg [toegang tot Azure Storage verifiëren met Azure Active Directory](../storage/common/storage-auth-aad.md) voor Azure Storage-verificatie in het algemeen. Voer de volgende stappen uit om beheerde identiteiten voor Azure-bronverificatie te gebruiken:

1. [Haal de beheerde identiteitsgegevens](data-factory-service-identity.md#retrieve-managed-identity) van de gegevensfabriek op door de waarde van **de beheerde identiteitsobject-id** te kopiëren die samen met uw fabriek is gegenereerd.

2. Geef de juiste machtiging voor beheerde identiteit in Azure Blob-opslag. Raadpleeg [toegangsrechten beheren voor Azure Storage-gegevens met RBAC](../storage/common/storage-auth-aad-rbac.md) met meer informatie over de rollen.

    - **Als bron**, in Access control (IAM), verlenen ten minste **Storage Blob Data Reader** rol.
    - **Als sink**, in Access control (IAM), verlenen ten minste **Storage Blob Data Contributor** rol.

>[!IMPORTANT]
>Als u PolyBase gebruikt om gegevens van Blob (als bron of als fasering) in SQL Data Warehouse te laden, moet u ervoor zorgen dat u bij het gebruik van beheerde identiteitsverificatie voor Blob ook stappen 1 en 2 in [deze richtlijnen](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) volgt om uw SQL Database-server te registreren met Azure Ad (Azure AD) en 2) de rol van opslagblobgegevensbijdrage toe te wijzen aan uw SQL Database-server; de rest wordt afgehandeld door Data Factory. Als uw Blob-opslag is geconfigureerd met een Azure Virtual Network-eindpunt, moet u beheerde identiteitsverificatie gebruiken zoals vereist door PolyBase om PolyBase te gebruiken om gegevens te laden.

Deze eigenschappen worden ondersteund voor een Azure Blob-opslaggekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op **AzureBlobStorage**. |Ja |
| serviceEndpoint | Geef het eindpunt van de Azure `https://<accountName>.blob.core.windows.net/`Blob-opslagservice op met het patroon van . |Ja |
| connectVia | De [inburgeringsruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. U Azure Integration Runtime of Self-hosted Integration Runtime gebruiken (als uw gegevensarchief zich in een privénetwerk bevindt). Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

> [!NOTE]
> Beheerde identiteiten voor Azure-resources-verificatie worden alleen ondersteund door de gekoppelde service van het type AzureBlobStorage, maar niet door de vorige gekoppelde service van het type AzureStorage. 

**Voorbeeld:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
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

De volgende eigenschappen worden ondersteund `location` voor Azure Blob onder instellingen in op indeling gebaseerde gegevensset:

| Eigenschap   | Beschrijving                                                  | Vereist |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | De eigenschap type van de locatie in de gegevensset moet worden ingesteld op **AzureBlobStorageLocation**. | Ja      |
| container  | De blobcontainer.                                          | Ja      |
| folderPath | Het pad naar de map onder de opgegeven container. Als u wildcard wilt gebruiken om de map te filteren, slaat u deze instelling over en geeft u op in de instellingen van de activiteitsbron. | Nee       |
| fileName   | De bestandsnaam onder de opgegeven container + mapPath. Als u wildcard wilt gebruiken om bestanden te filteren, slaat u deze instelling over en geeft u op in de instellingen van de activiteitsbron. | Nee       |

**Voorbeeld:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
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

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de blob-opslagbron en -sink.

### <a name="blob-storage-as-a-source-type"></a>Blob-opslag als brontype

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund `storeSettings` voor Azure Blob onder instellingen in op indeling gebaseerde kopieerbron:

| Eigenschap                 | Beschrijving                                                  | Vereist                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | De eigenschap `storeSettings` type onder moet zijn ingesteld op **AzureBlobStorageReadSettings**. | Ja                                           |
| Recursieve                | Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. Houd er rekening mee dat wanneer recursieve is ingesteld op true en de gootsteen een op bestanden gebaseerd e-bestand is, een lege map of submap niet wordt gekopieerd of gemaakt aan de gootsteen. Toegestane waarden zijn **waar** (standaard) en **onwaar.** | Nee                                            |
| Voorvoegsel                   | Voorvoegsel voor de blobnaam onder de opgegeven container die is geconfigureerd in de gegevensset om bronblobs te filteren. Blobs waarvan de naam begint met dit voorvoegsel zijn geselecteerd. <br>Geldt alleen `wildcardFolderPath` `wildcardFileName` wanneer en eigenschappen niet zijn opgegeven. | Nee                                                          |
| wildcardFolderPath       | Het mappad met jokertekens onder de opgegeven container die is geconfigureerd in de gegevensset om bronmappen te filteren. <br>Toegestane wildcards zijn: `*` (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken); gebruiken `^` om te ontsnappen als uw werkelijke map naam wildcard of deze ontsnapping char binnen heeft. <br>Zie meer voorbeelden in [voorbeelden van mappen en bestandenfilters](#folder-and-file-filter-examples). | Nee                                            |
| wildcardFileName         | De bestandsnaam met jokertekens onder de opgegeven container + mapPath/wildcardFolderPath om bronbestanden te filteren. <br>Toegestane wildcards zijn: `*` (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken); gebruiken `^` om te ontsnappen als uw werkelijke map naam wildcard of deze ontsnapping char binnen heeft.  Zie meer voorbeelden in [voorbeelden van mappen en bestandenfilters](#folder-and-file-filter-examples). | Ja `fileName` als deze niet is opgegeven in de gegevensset |
| gewijzigdDatumtimeStart    | Bestandenfilter op basis van het kenmerk: Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` tijd `modifiedDatetimeEnd`binnen het tijdsbereik tussen en . De tijd wordt toegepast op utc-tijdzone in de indeling "2018-12-01T05:00:00Z". <br> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestandskenmerk wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` de datumtijdwaarde is, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, worden geselecteerd.  Wanneer `modifiedDatetimeEnd` de datumtijdwaarde is, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, worden geselecteerd. | Nee                                            |
| gewijzigdDatumtimeEnd      | Hetzelfde als hierboven.                                               | Nee                                            |
| maxConcurrentVerbindingen | Het aantal verbindingen dat tegelijkertijd verbinding moet maken met het opslagarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee                                            |

> [!NOTE]
> Voor parquet/afgebakende tekstindeling wordt **blobsourceactiviteitsbron blobsource** die in de volgende sectie wordt genoemd, nog steeds ondersteund als-is voor achterwaartse compatibiliteit. U wordt voorgesteld om dit nieuwe model in de toekomst te gebruiken en de ADF-auteursinterface is overgeschakeld naar het genereren van deze nieuwe typen.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageReadSettings",
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

### <a name="blob-storage-as-a-sink-type"></a>Blob-opslag als gootsteentype

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund `storeSettings` voor Azure Blob onder instellingen in op indeling gebaseerde copy sink:

| Eigenschap                 | Beschrijving                                                  | Vereist |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | De eigenschap `storeSettings` type onder moet zijn ingesteld op **AzureBlobStorageWriteSettings**. | Ja      |
| copyBehavior             | Hiermee definieert u het kopieergedrag wanneer de bron bestanden is uit een gegevensarchief op basis van bestanden.<br/><br/>Toegestane waarden zijn:<br/><b>- BehoudenHiërarchie (standaard)</b>: Hiermee behoudt u de bestandshiërarchie in de doelmap. Het relatieve pad van bronbestand naar bronmap is identiek aan het relatieve pad van doelbestand naar doelmap.<br/><b>- FlattenHierarchy:</b>Alle bestanden uit de bronmap bevinden zich in het eerste niveau van de doelmap. De doelbestanden hebben automatisch gegenereerde namen. <br/><b>- MergeFiles</b>: Hiermee worden alle bestanden van de bronmap samengevoegd tot één bestand. Als de bestands- of blobnaam is opgegeven, is de samengevoegde bestandsnaam de opgegeven naam. Anders is het een automatisch gegenereerde bestandsnaam. | Nee       |
| blockSizeInMB blockSizeInMB | Geef de blokgrootte op in MB die wordt gebruikt om gegevens te schrijven om blobs te blokkeren. Meer informatie [over Blokblobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Toegestane waarde ligt **tussen 4 en 100 MB.** <br/>AdF bepaalt standaard automatisch de blokgrootte op basis van het type en de gegevens van het bronarchief. Voor niet-binaire kopie naar Blob is de standaardblokgrootte 100 MB om maximaal 4,95 TB-gegevens in te passen. Het is mogelijk niet optimaal wanneer uw gegevens niet groot zijn, vooral wanneer u Self-hosted Integration Runtime gebruikt met een slecht netwerk, wat resulteert in een time-out of prestatieprobleem. U expliciet een blokgrootte opgeven, terwijl ervoor zorgen dat blockSizeInMB*50000 groot genoeg is om de gegevens op te slaan, anders mislukt de kopieeractiviteit. | Nee |
| maxConcurrentVerbindingen | Het aantal verbindingen dat tegelijkertijd verbinding moet maken met het opslagarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee       |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageWriteSettings",
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
| `container/Folder*` | (leeg, standaard gebruiken) | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (leeg, standaard gebruiken) | waar | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | waar | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Enkele voorbeelden van recursieve en copyBehavior

In deze sectie wordt het resulterende gedrag van de bewerking Kopiëren beschreven voor verschillende combinaties van recursieve en copyBehavior-waarden.

| Recursieve | copyBehavior | Structuur van bronmappen | Resulterend doel |
|:--- |:--- |:--- |:--- |
| waar |behoudenHiërarchie | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap Map1 wordt gemaakt met dezelfde structuur als de bron:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 |
| waar |afvlakkenHiërarchie | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap1 wordt gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand5 |
| waar |mergeFiles | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap1 wordt gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5-inhoud worden samengevoegd tot één bestand met een automatisch gegenereerde bestandsnaam. |
| false |behoudenHiërarchie | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap Map1 wordt gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/><br/>Submap1 met Bestand3, File4 en File5 wordt niet opgehaald. |
| false |afvlakkenHiërarchie | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap Map1 wordt gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/><br/>Submap1 met Bestand3, File4 en File5 wordt niet opgehaald. |
| false |mergeFiles | Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 | De doelmap Map1 wordt gemaakt met de volgende structuur<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2-inhoud wordt samengevoegd tot één bestand met een automatisch gegenereerde bestandsnaam. automatisch gegenereerde naam voor Bestand1<br/><br/>Submap1 met Bestand3, File4 en File5 wordt niet opgehaald. |

## <a name="preserve-metadata-during-copy"></a>Metagegevens bewaren tijdens het kopiëren

Wanneer u bestanden van Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 kopieert naar Azure Data Lake Storage Gen2/Azure Blob, u ervoor kiezen om de bestandsmetagegevens samen met gegevens te bewaren. Meer informatie van [Metagegevens bewaren](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="mapping-data-flow-properties"></a>Gegevensstroomeigenschappen toewijzen

Wanneer u gegevens in kaartgegevensstroom transformeert, u bestanden lezen en schrijven vanuit Azure Blob Storage in JSON, Avro, Delimited Text of Parketformaat. Zie [brontransformatie](data-flow-source.md) en [sinktransformatie voor](data-flow-sink.md) meer informatie in de functie gegevensstroom toewijzing.

### <a name="source-transformation"></a>Brontransformatie

In de brontransformatie u lezen uit een container, map of afzonderlijk bestand in Azure Blob Storage. Op het tabblad **Bronopties** u beheren hoe de bestanden worden gelezen. 

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

In de sinktransformatie u schrijven naar een container of map in Azure Blob Storage. Op het tabblad **Instellingen** u beheren hoe de bestanden worden geschreven.

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
| type | De eigenschap type van de gegevensset moet worden ingesteld op **AzureBlob**. |Ja |
| folderPath | Pad naar de container en map in de blobopslag. <br/><br/>Wildcard-filter wordt ondersteund voor het pad, met uitzondering van de containernaam. Toegestane wildcards zijn: `*` (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken); gebruiken `^` om te ontsnappen als uw werkelijke map naam wildcard of deze ontsnapping char binnen heeft. <br/><br/>Voorbeelden: myblobcontainer/myblobfolder/, zie meer voorbeelden in [voorbeelden van mappen en bestandsfilters](#folder-and-file-filter-examples). |Ja voor kopieer-/opzoekactiviteit, nee voor activiteit GetMetadata |
| fileName | **Naam- of wildcardfilter** voor de blob(s) onder de opgegeven 'mapPath'. Als u geen waarde voor deze eigenschap opgeeft, verwijst de gegevensset naar alle blobs in de map. <br/><br/>Voor filter zijn toegestane jokertekens: `*` (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of één teken).<br/>- Voorbeeld 1:`"fileName": "*.csv"`<br/>- Voorbeeld 2:`"fileName": "???20180427.txt"`<br/>Gebruik `^` om te ontsnappen als uw werkelijke bestandsnaam wildcard of deze ontsnapping char binnen heeft.<br/><br/>Wanneer bestandsnaam niet is opgegeven voor een uitvoergegevensset en **de hiërarchie** niet is opgegeven in de activiteitssink, genereert de kopieeractiviteit automatisch de blobnaam met het volgende patroon: "*Gegevens.[ activiteit uitvoeren ID GUID]. [GUID als FlattenHierarchy]. [indeling indien geconfigureerd]. [compressie indien geconfigureerd]*", bijvoorbeeld "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"; Als u uit tabelbron kopieert met tabelnaam in plaats van query, is het naampatroon "*[tabelnaam].[ formaat]. [compressie indien geconfigureerd]*", bijvoorbeeld "MyTable.csv". |Nee |
| gewijzigdDatumtimeStart | Bestandenfilter op basis van het kenmerk: Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` tijd `modifiedDatetimeEnd`binnen het tijdsbereik tussen en . De tijd wordt toegepast op utc-tijdzone in de indeling "2018-12-01T05:00:00Z". <br/><br/> Houd er rekening mee dat de algehele prestaties van gegevensverplaatsing worden beïnvloed door deze instelling in te schakelen wanneer u bestandsfilter wilt doen uit enorme hoeveelheden bestanden. <br/><br/> De eigenschappen kunnen NULL zijn, zodat er geen filter voor bestandskenmerk wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` de datumtijdwaarde is, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, worden geselecteerd.  Wanneer `modifiedDatetimeEnd` de datumtijdwaarde is, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, worden geselecteerd.| Nee |
| gewijzigdDatumtimeEnd | Bestandenfilter op basis van het kenmerk: Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` tijd `modifiedDatetimeEnd`binnen het tijdsbereik tussen en . De tijd wordt toegepast op utc-tijdzone in de indeling "2018-12-01T05:00:00Z". <br/><br/> Houd er rekening mee dat de algehele prestaties van gegevensverplaatsing worden beïnvloed door deze instelling in te schakelen wanneer u bestandsfilter wilt doen uit enorme hoeveelheden bestanden. <br/><br/> De eigenschappen kunnen NULL zijn, zodat er geen filter voor bestandskenmerk wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` de datumtijdwaarde is, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, worden geselecteerd.  Wanneer `modifiedDatetimeEnd` de datumtijdwaarde is, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, worden geselecteerd.| Nee |
| formaat | Als u bestanden wilt kopiëren zoals tussen bestandengebaseerde opslag (binaire kopie), slaat u de sectie opmaak in zowel de definities van de invoer- als de uitvoergegevensset over.<br/><br/>Als u bestanden met een specifieke indeling wilt ontleden of genereren, worden de volgende bestandsindelingstypen ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**en **ParquetFormat**. Stel de **eigenschap type** onder **opmaak** in op een van deze waarden. Zie voor meer informatie de secties [Tekstindeling](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-indeling](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro-formaat](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc-indeling](supported-file-formats-and-compression-codecs-legacy.md#orc-format)en [Parketformaat.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Nee (alleen voor binaire kopie scenario) |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Zie [Ondersteunde bestandsindelingen en compressiecodecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)voor meer informatie.<br/>Ondersteunde typen zijn **GZip,** **Deflate,** **BZip2**en **ZipDeflate**.<br/>Ondersteunde niveaus zijn **optimaal** en **snelst.** |Nee |

>[!TIP]
>Als u alle blobs onder een map wilt kopiëren, geeft u **alleen mapPath** op.<br>Als u één blob met een bepaalde naam wilt kopiëren, geeft u **mapPath** op met maponderdeel en **bestandsnaam.**<br>Als u een subset van blobs onder een map wilt kopiëren, geeft u **mapPath** op met maponderdeel en **bestandsnaam** met wildcardfilter. 

**Voorbeeld:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
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
| type | De eigenschap type van de bron van kopieeractiviteit moet zijn ingesteld op **BlobSource**. |Ja |
| Recursieve | Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. Houd er rekening mee dat wanneer recursieve is ingesteld op true en de gootsteen een op bestanden gebaseerd e-bestand is, een lege map of submap niet wordt gekopieerd of gemaakt aan de gootsteen.<br/>Toegestane waarden zijn **waar** (standaard) en **onwaar.** | Nee |
| maxConcurrentVerbindingen | Het aantal verbindingen dat tegelijkertijd verbinding moet maken met het opslagarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
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
| type | De eigenschap type van de kopieeractiviteitmoet worden ingesteld op **BlobSink**. |Ja |
| copyBehavior | Hiermee definieert u het kopieergedrag wanneer de bron bestanden is uit een gegevensarchief op basis van bestanden.<br/><br/>Toegestane waarden zijn:<br/><b>- BehoudenHiërarchie (standaard)</b>: Hiermee behoudt u de bestandshiërarchie in de doelmap. Het relatieve pad van bronbestand naar bronmap is identiek aan het relatieve pad van doelbestand naar doelmap.<br/><b>- FlattenHierarchy:</b>Alle bestanden uit de bronmap bevinden zich in het eerste niveau van de doelmap. De doelbestanden hebben automatisch gegenereerde namen. <br/><b>- MergeFiles</b>: Hiermee worden alle bestanden van de bronmap samengevoegd tot één bestand. Als de bestands- of blobnaam is opgegeven, is de samengevoegde bestandsnaam de opgegeven naam. Anders is het een automatisch gegenereerde bestandsnaam. | Nee |
| maxConcurrentVerbindingen | Het aantal verbindingen dat tegelijkertijd verbinding moet maken met het opslagarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Volgende stappen

Zie [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Data Factory.
