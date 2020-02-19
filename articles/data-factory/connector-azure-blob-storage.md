---
title: Gegevens kopiëren en transformeren in Azure Blob-opslag
description: Meer informatie over het kopiëren van gegevens naar en van Blob Storage en het transformeren van gegevens in Blob Storage met behulp van Data Factory.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/17/2020
ms.openlocfilehash: f21c99b18102adc9d43c21964e51b7c7b769771e
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77431210"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Gegevens kopiëren en transformeren in Azure Blob-opslag met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1](v1/data-factory-azure-blob-connector.md)
> * [Huidige versie](connector-azure-blob-storage.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar Azure Blob-opslag, en gegevens stroom te gebruiken om gegevens te transformeren in Azure Blob-opslag. Lees het [artikel Inleiding](introduction.md)voor meer informatie over Azure Data Factory.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Blob-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Gegevens stroom toewijzen](concepts-data-flow-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)

Voor kopieer activiteit ondersteunt deze Blob Storage-connector:

- Kopiëren van blobs en naar Azure-opslagaccounts voor algemeen gebruik en warme/koude blob-opslag. 
- Blobs kopiëren met behulp van de sleutel, service-handtekening voor gedeelde toegang, service-principal of beheerde identiteiten van Azure-resources-verificaties.
- Kopiëren blobs uit blokken, toevoegen of pagina-blobs en kopiëren van gegevens naar alleen blok-blobs.
- Blobs kopiëren als is of parseren en genereren van blobs met [ondersteunde bestands indelingen en compressie-codecs](supported-file-formats-and-compression-codecs.md).
- [Meta gegevens van bestand behouden tijdens kopiëren](#preserve-metadata-during-copy).

>[!IMPORTANT]
>Als u de optie **vertrouwde micro soft-Services toegang geven tot dit opslag account hebt** ingeschakeld voor Azure Storage Firewall-instellingen en Azure Integration runtime wilt gebruiken om verbinding te maken met uw Blob Storage, moet u [beheerde identiteits verificatie](#managed-identity)gebruiken.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten meer informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten specifieke naar Blob-opslag.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

Azure Blob-connector ondersteunt de volgende verificatietypen, verwijzen naar de overeenkomstige sectie voor meer informatie:

- [Verificatie van account sleutel](#account-key-authentication)
- [Verificatie van de Shared Access-hand tekening](#shared-access-signature-authentication)
- [Verificatie van service-principal](#service-principal-authentication)
- [Beheerde identiteiten voor Azure-bronnen verificatie](#managed-identity)

>[!NOTE]
>Wanneer u poly base gebruikt voor het laden van gegevens in SQL Data Warehouse, als uw bron of faserings-Blob-opslag is geconfigureerd met Virtual Network-eind punt, moet u beheerde identiteits verificatie gebruiken zoals vereist door poly base en zelf-hostende Integration Runtime gebruiken met versie 3,18 of hoger. Zie de sectie [beheerde identiteits verificatie](#managed-identity) met meer configuratie vereisten.

>[!NOTE]
>HDInsights en Azure Machine Learning-activiteiten bieden alleen ondersteuning voor de sleutel verificatie van Azure Blob-opslag accounts.

### <a name="account-key-authentication"></a>Verificatie van account-sleutel

Voor het gebruik van storage-account sleutelverificatie, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **AzureBlobStorage** (aanbevolen) of **opslag** (Zie de opmerkingen hieronder). |Ja |
| connectionString | Geef de informatie die nodig zijn voor het verbinding maken met opslag voor de connectionString-eigenschap. <br/> U kunt ook account sleutel in Azure Key Vault plaatsen en de `accountKey` configuratie uit de connection string halen. Raadpleeg de volgende voor beelden en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie. |Ja |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt Azure Integration Runtime of zelfgehoste Cloudintegratieruntime gebruiken (als uw gegevensopslag in een particulier netwerk is). Als niet is opgegeven, wordt de standaard Azure Integration Runtime. |Nee |

>[!NOTE]
>Secundair eind punt van BLOB service wordt niet ondersteund bij het gebruik van account sleutel verificatie. U kunt andere verificatie typen gebruiken.

>[!NOTE]
>Als u "AzureStorage" type gekoppelde service zijn gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u het gebruik van deze nieuwe 'Azure BLOB Storage"gekoppeld servicetype voortaan worden voorgesteld.

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

**Voor beeld: account sleutel opslaan in Azure Key Vault**

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

### <a name="shared-access-signature-authentication"></a>Shared access signature-verificatie

Een shared access signature biedt gedelegeerde toegang tot resources in uw opslagaccount. U kunt een shared access signature toekennen van dat een client beperkte machtigingen voor objecten in uw storage-account voor een opgegeven periode. U hebt geen delen van de toegangssleutels van uw account. De shared access signature is een URI die in de queryparameters alle informatie die nodig zijn voor geverifieerde toegang tot een opslagresource omvat. Voor toegang tot de storage-resources met de shared access signature, moet de client alleen worden doorgegeven in de handtekening voor gedeelde toegang tot de juiste constructor of methode. Zie voor meer informatie over hand tekeningen voor gedeelde toegang, [Shared Access Signatures: inzicht in het model voor de Shared Access-hand tekening](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Data Factory ondersteunt nu zowel **gedeelde toegangs handtekeningen van services** als **hand tekeningen voor gedeelde toegang van accounts**. Zie voor meer informatie over gedeelde toegangs handtekeningen [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](../storage/common/storage-sas-overview.md).
>- In latere configuratie van de gegevensset is het pad naar het absolute pad vanaf het niveau van de container. U moet een afgestemd op het pad naar uw SAS-URI configureren.

> [!TIP]
> U kunt de volgende PowerShell-opdrachten uitvoeren voor het genereren van een gedeelde-toegangshandtekening van de service voor uw opslagaccount. Vervang de tijdelijke aanduidingen en de benodigde machtiging verlenen.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Voor het gebruik van shared access signature-verificatie, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **AzureBlobStorage** (aanbevolen) of **opslag** (Zie de opmerkingen hieronder). |Ja |
| sasUri | Geef de URI van de Shared Access-hand tekening op voor de opslag resources, zoals BLOB/container. <br/>Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory. U kunt ook SAS-token in Azure Key Vault plaatsen om automatisch te draaien en het token gedeelte te verwijderen. Raadpleeg de volgende voor beelden en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie. |Ja |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt de Azure Integration Runtime of de zelfgehoste Cloudintegratieruntime gebruiken (als het gegevensarchief bevindt zich in een particulier netwerk). Als niet is opgegeven, wordt de standaard Azure Integration Runtime. |Nee |

>[!NOTE]
>Als u "AzureStorage" type gekoppelde service zijn gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u het gebruik van deze nieuwe 'Azure BLOB Storage"gekoppeld servicetype voortaan worden voorgesteld.

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

**Voor beeld: account sleutel opslaan in Azure Key Vault**

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

Wanneer u een shared access signature URI maakt, moet u de volgende punten overwegen:

- Geschikt voor lezen/schrijven-machtigingen instellen voor objecten die zijn gebaseerd op hoe de gekoppelde service (lezen, schrijven, lezen/schrijven) wordt gebruikt in uw data factory.
- Stel de **verloop tijd** op de juiste wijze in. Zorg ervoor dat de toegang tot de Storage-objecten niet binnen een actieve periode van de pijplijn verloopt.
- De URI moet worden gemaakt in de juiste container/Blob op basis van de behoefte. Een shared access signature URI naar een blob kan Data Factory voor toegang tot die specifieke blob. Met een URI voor Shared Access Signature naar een BLOB storage-container kan Data Factory de blobs in die container door lopen. Voor toegang tot meer of minder objecten later of bijwerken van de shared access signature URI, vergeet niet om bij te werken van de gekoppelde service met de nieuwe URI.

### <a name="service-principal-authentication"></a>Verificatie van service-principal

Raadpleeg voor Azure Storage Service-Principal-verificatie in het algemeen het [verifiëren van toegang tot Azure Storage met behulp van Azure Active Directory](../storage/common/storage-auth-aad.md).

Volg deze stappen voor het gebruik van service-principal verificatie:

1. Registreer een toepassings entiteit in Azure Active Directory (Azure AD) door [uw toepassing te registreren bij een Azure AD-Tenant](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Noteer de volgende waarden, die u gebruikt voor het definiëren van de gekoppelde service:

    - Toepassings-id
    - Toepassingssleutel
    - Tenant-id

2. Geef de service principal juiste toestemming in Azure Blob-opslag. Raadpleeg de [toegangs rechten voor het Azure Storage van gegevens beheren met RBAC](../storage/common/storage-auth-aad-rbac.md) met meer informatie over de rollen.

    - Ken **als bron**, in toegangs beheer (IAM), ten minste de rol van **BLOB-gegevens lezer voor opslag** toe.
    - Ken **als Sink**, in toegangs beheer (IAM), ten minste de rol van **BLOB data contributor voor opslag** .

Deze eigenschappen worden ondersteund voor een Azure Blob storage gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **AzureBlobStorage**. |Ja |
| serviceEndpoint | Geef het Azure Blob Storage-service-eind punt op met het patroon van `https://<accountName>.blob.core.windows.net/`. |Ja |
| servicePrincipalId | Opgeven van de toepassing client-ID. | Ja |
| servicePrincipalKey | Geef de sleutel van de toepassing. Markeer dit veld als **SecureString** om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | De tenantgegevens (domain name of tenant-ID) opgeven in uw toepassing zich bevindt. Deze ophalen door de muis in de rechterbovenhoek van de Azure-portal. | Ja |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt Azure Integration Runtime of zelfgehoste Cloudintegratieruntime gebruiken (als uw gegevensopslag in een particulier netwerk is). Als niet is opgegeven, wordt de standaard Azure Integration Runtime. |Nee |

>[!NOTE]
>Service-principal verificatie wordt alleen ondersteund door 'Azure BLOB Storage'-type gekoppelde service, maar niet vorige "AzureStorage" type gekoppelde service.

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

### <a name="managed-identity"></a>Beheerde identiteiten voor Azure-bronnen verificatie

Een data factory kan worden gekoppeld aan een [beheerde identiteit voor Azure-resources](data-factory-service-identity.md)die deze specifieke Data Factory vertegenwoordigt. U kunt deze beheerde identiteit rechtstreeks gebruiken voor de verificatie van Blob-opslag, vergelijkbaar met het gebruik van uw eigen service-principal. Hiermee kunt deze aangewezen factory toegang en gegevens kopiëren van/naar de Blob-opslag.

Raadpleeg [toegang tot Azure Storage verifiëren met behulp van Azure Active Directory](../storage/common/storage-auth-aad.md) voor Azure Storage verificatie in het algemeen. Voor het gebruik van beheerde identiteiten voor verificatie van de Azure-resources, de volgende stappen uit:

1. [Haal Data Factory beheerde identiteits gegevens](data-factory-service-identity.md#retrieve-managed-identity) op door de waarde van de **beheerde ID-object-id** die samen met uw fabriek is gegenereerd, te kopiëren.

2. Verleen de juiste machtiging beheerde identiteit in Azure Blob-opslag. Raadpleeg de [toegangs rechten voor het Azure Storage van gegevens beheren met RBAC](../storage/common/storage-auth-aad-rbac.md) met meer informatie over de rollen.

    - Ken **als bron**, in toegangs beheer (IAM), ten minste de rol van **BLOB-gegevens lezer voor opslag** toe.
    - Ken **als Sink**, in toegangs beheer (IAM), ten minste de rol van **BLOB data contributor voor opslag** .

>[!IMPORTANT]
>Als u poly base gebruikt voor SQL Database het laden van gegevens uit een BLOB (als bron of als fase ring) in SQL Data Warehouse, moet u, wanneer u beheerde identiteits verificatie voor BLOB gebruikt, ervoor zorgen dat u de stappen 1 en 2 in [deze richt lijnen](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) aan de hand van de gegevens van de SQL database server registreert bij Azure Active Directory (Azure AD) en 2). de rest wordt afgehandeld door Data Factory. Als uw Blobopslag is geconfigureerd met een Azure Virtual Network-eind punt, moet u de beheerde identiteits verificatie gebruiken zoals vereist is door poly Base om poly Base te gebruiken voor het laden van gegevens uit de opslag.

Deze eigenschappen worden ondersteund voor een Azure Blob storage gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **AzureBlobStorage**. |Ja |
| serviceEndpoint | Geef het Azure Blob Storage-service-eind punt op met het patroon van `https://<accountName>.blob.core.windows.net/`. |Ja |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt Azure Integration Runtime of zelfgehoste Cloudintegratieruntime gebruiken (als uw gegevensopslag in een particulier netwerk is). Als niet is opgegeven, wordt de standaard Azure Integration Runtime. |Nee |

> [!NOTE]
> Beheerde identiteiten voor Azure-resources verificatie wordt alleen ondersteund door "Azure BLOB Storage" type gekoppelde service, maar niet vorige "AzureStorage" type gekoppelde service. 

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

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor Azure Blob onder `location` instellingen in gegevensset op basis van indeling:

| Eigenschap   | Beschrijving                                                  | Vereist |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | De eigenschap type van de locatie in DataSet moet worden ingesteld op **AzureBlobStorageLocation**. | Ja      |
| container  | De BLOB-container.                                          | Ja      |
| folderPath | Het pad naar de map onder de opgegeven container. Als u een Joker teken wilt gebruiken om de map te filteren, slaat u deze instelling over en geeft u de instellingen voor de activiteit bron op. | Nee       |
| fileName   | De bestands naam onder de opgegeven container + folderPath. Als u Joker tekens wilt gebruiken om bestanden te filteren, slaat u deze instelling over en geeft u de instellingen van de activiteit bron op. | Nee       |

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

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Blob storage-bron en sink.

### <a name="blob-storage-as-a-source-type"></a>BLOB-opslag als een brontype

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor Azure Blob onder `storeSettings` instellingen in op indeling gebaseerde kopie bron:

| Eigenschap                 | Beschrijving                                                  | Vereist                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | De eigenschap type onder `storeSettings` moet zijn ingesteld op **AzureBlobStorageReadSettings**. | Ja                                           |
| recursive                | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen voor de opgegeven map. Houd er rekening mee dat wanneer recursieve is ingesteld op true en de sink is een opslagplaats op basis van bestanden, een lege map of submap is niet gekopieerd of gemaakt in de sink. Toegestane waarden zijn **True** (standaard) en **Onwaar**. | Nee                                            |
| prefix                   | Voor voegsel voor de blobnaam onder de opgegeven container die in de gegevensset is geconfigureerd voor het filteren van bron-blobs. Blobs waarvan de naam begint met dit voor voegsel worden geselecteerd. <br>Is alleen van toepassing als `wildcardFolderPath` en `wildcardFileName` eigenschappen niet zijn opgegeven. | Nee                                                          |
| wildcardFolderPath       | Het mappad met Joker tekens onder de opgegeven container die in de gegevensset is geconfigureerd om bron mappen te filteren. <br>Toegestane joker tekens zijn: `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken). gebruik `^` om te escapen als uw daad werkelijke mapnaam Joker teken of escape-teken bevat. <br>Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). | Nee                                            |
| wildcardFileName         | De naam van het bestand met Joker tekens onder de opgegeven container + folderPath/wildcardFolderPath voor het filteren van bron bestanden. <br>Toegestane joker tekens zijn: `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken). gebruik `^` om te escapen als uw daad werkelijke mapnaam Joker teken of escape-teken bevat.  Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). | Ja als `fileName` niet is opgegeven in de gegevensset |
| modifiedDatetimeStart    | Bestanden filteren op basis van het kenmerk: laatst gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik ligt tussen `modifiedDatetimeStart` en `modifiedDatetimeEnd`. De tijd wordt toegepast op de UTC-tijdzone in de notatie ' 2018-12-01T05:00:00Z '. <br> De eigenschappen is NULL. Dit betekent dat er geen kenmerk bestandsfilter worden toegepast op de gegevensset.  Als `modifiedDatetimeStart` een datum/tijd-waarde heeft, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter is dan of gelijk is aan de datum/tijd-waarde wordt geselecteerd.  Als `modifiedDatetimeEnd` datum/tijd-waarde heeft, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is, kleiner is dan de datum/tijd-waarde wordt geselecteerd. | Nee                                            |
| modifiedDatetimeEnd      | Hetzelfde als hierboven.                                               | Nee                                            |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met opslag archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee                                            |

> [!NOTE]
> Voor een Parquet/gescheiden tekst indeling wordt de bron van de Kopieer activiteit van het type **BlobSource** die in de volgende sectie wordt vermeld, nog steeds ondersteund als-is voor achterwaartse compatibiliteit. U wordt aangeraden dit nieuwe model verder te gebruiken en de gebruikers interface van de ADF-ontwerp functie is overgeschakeld op het genereren van deze nieuwe typen.

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

### <a name="blob-storage-as-a-sink-type"></a>BLOB-opslag als een sink-type

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor Azure-Blob onder `storeSettings` instellingen in op indeling gebaseerde kopie-Sink:

| Eigenschap                 | Beschrijving                                                  | Vereist |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | De eigenschap type onder `storeSettings` moet zijn ingesteld op **AzureBlobStorageWriteSettings**. | Ja      |
| copyBehavior             | Definieert het gedrag kopiëren wanneer de bron bestanden vanuit een bestandsgebaseerde gegevensarchief is.<br/><br/>Toegestane waarden zijn:<br/><b>-PreserveHierarchy (standaard instelling)</b>: behoudt de bestands hiërarchie in de doelmap. Het relatieve pad van het bronbestand voor bronmap is identiek aan het relatieve pad van doelbestand naar doelmap.<br/><b>-FlattenHierarchy</b>: alle bestanden in de bronmap bevinden zich in het eerste niveau van de doelmap. De doelbestanden hebben automatisch gegenereerde namen. <br/><b>-MergeFiles</b>: alle bestanden van de bronmap worden samengevoegd met één bestand. Als de naam van het bestand of de blob is opgegeven, is de naam van het samengevoegde de opgegeven naam. Anders is de naam van een automatisch gegenereerde bestand. | Nee       |
| blockSizeInMB | Geef de blok grootte in MB op die wordt gebruikt voor het schrijven van gegevens voor blok-blobs. Meer informatie [over blok-blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>De toegestane waarde ligt **tussen 4 en 100 MB**. <br/>Standaard bepaalt ADF automatisch de blok grootte op basis van het type en de gegevens van het bron archief. Voor een niet-binaire kopie naar een blob is de standaard blok grootte 100 MB, zodat deze in Maxi maal 4,95 TB gegevens past. Het is mogelijk niet optimaal wanneer uw gegevens niet groot zijn, met name wanneer u een zelf-hostend Integration Runtime gebruikt met een slecht netwerk als gevolg van een time-out voor de bewerking of het prestatie probleem. U kunt expliciet een blok grootte opgeven, terwijl u er zeker van wilt zijn dat blockSizeInMB * 50.000 groot genoeg is om de gegevens op te slaan, anders mislukt het uitvoeren van de Kopieer activiteit. | Nee |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met opslag archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee       |

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

### <a name="folder-and-file-filter-examples"></a>Voor beelden van map-en bestands filter

In deze sectie wordt het resulterende gedrag van het mappad en de bestands naam met Joker teken filters beschreven.

| folderPath | fileName | recursive | De structuur van de bronmap en het filter resultaat ( **vetgedrukte** bestanden worden opgehaald)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (leeg, standaard instelling gebruiken) | onwaar | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**bestand1. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**bestand2. json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. CSV<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. CSV<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. CSV |
| `container/Folder*` | (leeg, standaard instelling gebruiken) | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**bestand1. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**bestand2. json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. CSV |
| `container/Folder*` | `*.csv` | onwaar | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**bestand1. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand2. json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. CSV<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. CSV<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. CSV |
| `container/Folder*` | `*.csv` | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**bestand1. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand2. json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. CSV |

### <a name="some-recursive-and-copybehavior-examples"></a>Enkele voorbeelden van recursieve en copyBehavior

Deze sectie beschrijft het resulterende gedrag van de kopieerbewerking voor de verschillende combinaties van recursieve en copyBehavior waarden.

| recursive | copyBehavior | Structuur van de gegevensbron | Resulterende doel |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | de doelmap Map1 is gemaakt met dezelfde structuur als de bron:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Het doel Map1 is gemaakt met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Het doel Map1 is gemaakt met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1 + Bestand2 + File3 + File4 + File5 wordt de inhoud samengevoegd in één bestand met een automatisch gegenereerde bestands naam. |
| onwaar |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | de doelmap Map1 is gemaakt met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/><br/>Subfolder1 bestand3 File4 en File5 is niet opgehaald. |
| onwaar |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | de doelmap Map1 is gemaakt met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/><br/>Subfolder1 bestand3 File4 en File5 is niet opgehaald. |
| onwaar |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | De doelmap Map1 wordt gemaakt met de volgende structuur<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;bestand1 + Bestand2-inhoud worden samengevoegd in één bestand met een automatisch gegenereerde bestands naam. automatisch gegenereerde naam voor File1<br/><br/>Subfolder1 bestand3 File4 en File5 is niet opgehaald. |

## <a name="preserve-metadata-during-copy"></a>Meta gegevens bewaren tijdens kopiëren

Wanneer u bestanden kopieert van Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 naar Azure Data Lake Storage Gen2/Azure Blob, kunt u ervoor kiezen om de meta gegevens van het bestand te behouden. Meer informatie over het [bewaren van meta gegevens](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Bij het transformeren van gegevens in de toewijzing van gegevens stroom kunt u bestanden lezen en schrijven van Azure Blob Storage in JSON, AVRO, tekst met scheidings tekens of Parquet-indeling. Zie [bron transformatie](data-flow-source.md) en [sink-trans formatie](data-flow-sink.md) in de functie gegevens stroom toewijzen voor meer informatie.

### <a name="source-transformation"></a>Bron transformatie

In de bron transformatie kunt u lezen uit een container, map of afzonderlijk bestand in Azure Blob Storage. Op het tabblad **bron opties** kunt u beheren hoe de bestanden worden gelezen. 

![Bron opties](media/data-flow/sourceOptions1.png "Bron opties")

**Pad met Joker tekens:** Als u een Joker teken gebruikt, wordt ADF geadviseerd om elke overeenkomende map en elk bestand in één bron transformatie te verwerken. Dit is een efficiënte manier om meerdere bestanden binnen één stroom te verwerken. Voeg meerdere Joker teken patronen toe met het plus teken dat wordt weer gegeven bij het aanwijzen van het bestaande Joker teken patroon.

Kies in uw bron container een reeks bestanden die overeenkomen met een patroon. Alleen container kan worden opgegeven in de gegevensset. Het pad naar uw Joker teken moet daarom ook uw mappad van de hoofdmap bevatten.

Voor beelden van joker tekens:

* ```*``` vertegenwoordigt een wille keurige set tekens
* ```**``` staat voor recursieve nesten van mappen
* ```?``` vervangt één teken
* ```[]``` komt overeen met een van de tekens in de vier Kante haken

* ```/data/sales/**/*.csv``` haalt alle CSV-bestanden op/Data/Sales
* ```/data/sales/20??/**``` alle bestanden in de twintigste eeuw ophalen
* ```/data/sales/2004/*/12/[XY]1?.csv``` haalt alle CSV-bestanden in 2004 december op, beginnend met X of Y, voorafgegaan door een getal van twee cijfers

Basispad **:** Als u gepartitioneerde mappen in de bestands bron hebt met een ```key=value```-indeling (bijvoorbeeld Year = 2019), kunt u het hoogste niveau van die partitie mappen structuur toewijzen aan een kolom naam in de gegevens stroom van de gegevensstroom.

Stel eerst een Joker teken in om alle paden op te nemen van de gepartitioneerde mappen plus de blad bestanden die u wilt lezen.

![Bron Bestands instellingen voor partitioneren](media/data-flow/partfile2.png "Instelling voor partitie bestand")

Gebruik de instelling basis pad partitie om te definiëren wat het hoogste niveau van de mappen structuur is. Wanneer u de inhoud van uw gegevens bekijkt via een voor beeld van de gegevens, ziet u dat de opgeloste partities in elk van uw mapniveau worden toegevoegd.

![Basispad voor partitie](media/data-flow/partfile1.png "Voor beeld van basispad")

**Lijst met bestanden:** Dit is een bestandenset. Maak een tekst bestand met een lijst met relatieve padgegevens die moeten worden verwerkt. Wijs dit tekst bestand aan.

**Kolom voor het opslaan van de bestands naam:** Sla de naam van het bron bestand op in een kolom in uw gegevens. Voer hier een nieuwe kolom naam in om de teken reeks voor de bestands naam op te slaan.

**Na voltooiing:** U kunt niets doen met het bron bestand nadat de gegevens stroom is uitgevoerd, het bron bestand te verwijderen of het bron bestand te verplaatsen. De paden voor de verplaatsing zijn relatief.

Als u bron bestanden naar een andere locatie wilt verplaatsen, selecteert u eerst verplaatsen voor bestands bewerking. Stel vervolgens de map uit. Als u geen joker tekens gebruikt voor uw pad, is de instelling van ' van ' dezelfde map als de bronmap.

Als u een bronpad met Joker teken hebt, ziet uw syntaxis er als volgt uit:

```/data/sales/20??/**/*.csv```

U kunt "van" opgeven als

```/data/sales```

En "aan" als

```/backup/priorSales```

In dit geval worden alle bestanden die zijn gebrond onder/Data/Sales verplaatst naar/backup/priorSales.

> [!NOTE]
> Bestands bewerkingen worden alleen uitgevoerd wanneer u de gegevens stroom start vanuit een pijplijn uitvoering (een uitvoering van een pijp lijn of uitvoering) die gebruikmaakt van de activiteit gegevens stroom uitvoeren in een pijp lijn. Bestands bewerkingen *worden niet* uitgevoerd in de modus voor fout opsporing van gegevens stromen.

**Filteren op laatst gewijzigd:** U kunt filteren op de bestanden die u verwerkt door een datum bereik op te geven wanneer ze voor het laatst zijn gewijzigd. Alle datum-tijden zijn in UTC. 

### <a name="sink-properties"></a>Eigenschappen van Sink

In de Sink-trans formatie kunt u schrijven naar een container of map in Azure Blob Storage. op het tabblad **instellingen** kunt u beheren hoe de bestanden worden geschreven.

![Sink-opties](media/data-flow/file-sink-settings.png "Sink-opties")

**De map wissen:** Hiermee wordt bepaald of de doelmap wordt gewist voordat de gegevens worden geschreven.

**Optie Bestands naam:** Hiermee wordt bepaald hoe de doel bestanden in de doelmap worden genoemd. De opties voor de bestands naam zijn:
   * **Standaard**: Spark toestaan om bestanden een naam te geven op basis van de standaard waarden van een deel.
   * **Patroon**: Voer een patroon in voor het inventariseren van de uitvoer bestanden per partitie. Bijvoorbeeld: **leningen [n]. CSV** maken loans1. CSV, loans2. CSV, enzovoort.
   * **Per partitie**: Voer één bestands naam per partitie in.
   * **Als gegevens in kolom**: Stel het uitvoer bestand in op de waarde van een kolom. Het pad is relatief ten opzichte van de container gegevensset, niet de doelmap.
   * **Uitvoer naar één bestand**: de gepartitioneerde uitvoer bestanden combi neren in één bestand met een naam. Het pad is relatief ten opzichte van de map DataSet. Houd er rekening mee dat de samenvoegings bewerking mogelijk kan mislukken op basis van de knooppunt grootte. Deze optie wordt niet aanbevolen voor grote gegevens sets.

**Alle aanhalings tekens:** Hiermee wordt bepaald of alle waarden tussen aanhalings tekens moeten worden inge sloten

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

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de DataSet moet worden ingesteld op **AzureBlob**. |Ja |
| folderPath | Pad naar de container en map in de blob-opslag. <br/><br/>Het Joker teken filter wordt ondersteund voor het pad zonder container naam. Toegestane joker tekens zijn: `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken). gebruik `^` om te escapen als uw daad werkelijke mapnaam Joker teken of escape-teken bevat. <br/><br/>Voor beelden: myblobcontainer/myblobfolder/, meer voor beelden weer geven in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). |Ja voor kopiëren/Lookup-activiteit, niet voor de activiteit GetMetadata |
| fileName | De **naam of het Joker teken filter** voor de BLOB (s) onder de opgegeven folderPath. Als u een waarde voor deze eigenschap niet opgeeft, wordt de gegevensset verwijst naar alle blobs in de map. <br/><br/>Voor het filter zijn toegestane joker tekens: `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken).<br/>-Voor beeld 1: `"fileName": "*.csv"`<br/>-Voor beeld 2: `"fileName": "???20180427.txt"`<br/>Gebruik `^` om te escapen als uw werkelijke bestands naam Joker teken of escape-teken bevat.<br/><br/>Als er geen bestands naam is opgegeven voor een uitvoer-gegevensset en **preserveHierarchy** niet is opgegeven in de Sink van de activiteit, genereert de Kopieer activiteit automatisch de naam van de blob met het volgende patroon: "*gegevens. [ Run ID-GUID van activiteit]. [GUID if FlattenHierarchy]. [indeling indien geconfigureerd]. [compressie indien geconfigureerd]* ', bijvoorbeeld ' data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt. gz '; Als u vanuit tabellaire bron kopieert met behulp van de tabel naam in plaats van een query, is het naam patroon ' *[tabel naam]. [ indeling]. [compressie indien geconfigureerd]* ", bijvoorbeeld" mytable. csv ". |Nee |
| modifiedDatetimeStart | Bestanden filteren op basis van het kenmerk: laatst gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik ligt tussen `modifiedDatetimeStart` en `modifiedDatetimeEnd`. De tijd wordt toegepast op de UTC-tijdzone in de notatie ' 2018-12-01T05:00:00Z '. <br/><br/> Houd er rekening mee dat de prestaties van het verplaatsen van gegevens worden beïnvloed door deze instelling in te scha kelen wanneer u bestands filter van enorme hoeveel heden bestanden wilt uitvoeren. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestands kenmerken wordt toegepast op de gegevensset.  Als `modifiedDatetimeStart` een datum/tijd-waarde heeft, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter is dan of gelijk is aan de datum/tijd-waarde wordt geselecteerd.  Als `modifiedDatetimeEnd` datum/tijd-waarde heeft, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is, kleiner is dan de datum/tijd-waarde wordt geselecteerd.| Nee |
| modifiedDatetimeEnd | Bestanden filteren op basis van het kenmerk: laatst gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik ligt tussen `modifiedDatetimeStart` en `modifiedDatetimeEnd`. De tijd wordt toegepast op de UTC-tijdzone in de notatie ' 2018-12-01T05:00:00Z '. <br/><br/> Houd er rekening mee dat de prestaties van het verplaatsen van gegevens worden beïnvloed door deze instelling in te scha kelen wanneer u bestands filter van enorme hoeveel heden bestanden wilt uitvoeren. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestands kenmerken wordt toegepast op de gegevensset.  Als `modifiedDatetimeStart` een datum/tijd-waarde heeft, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter is dan of gelijk is aan de datum/tijd-waarde wordt geselecteerd.  Als `modifiedDatetimeEnd` datum/tijd-waarde heeft, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is, kleiner is dan de datum/tijd-waarde wordt geselecteerd.| Nee |
| format | Als u wilt kopiëren van bestanden als tussen winkels op basis van bestanden (binaire kopie), moet u de sectie indeling in de invoer en uitvoer gegevenssetdefinities overslaan.<br/><br/>Als u bestanden wilt parseren of genereren met een specifieke indeling, worden de volgende typen bestands indelingen ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**en **ParquetFormat**. Stel de eigenschap **type** onder **indeling** in op een van deze waarden. Zie de secties [tekst indeling](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-indeling](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format)-indeling, [Orc-indeling](supported-file-formats-and-compression-codecs-legacy.md#orc-format)en [Parquet-indeling](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) voor meer informatie. |Nee (alleen voor binaire kopie-scenario) |
| compression | Geef het type en het niveau van compressie voor de gegevens. Zie [ondersteunde bestands indelingen en compressie-codecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)voor meer informatie.<br/>Ondersteunde typen zijn **gzip**, **Deflate**, **bzip2**en **ZipDeflate**.<br/>Ondersteunde niveaus zijn **optimaal** en **snelst**. |Nee |

>[!TIP]
>Als u alle blobs in een map wilt kopiëren, geeft u alleen **FolderPath** op.<br>Als u één blob met een opgegeven naam wilt kopiëren, geeft u **FolderPath** op met een mappictogram en een **Bestands** naam met de bestands namen.<br>Als u een subset van blobs in een map wilt kopiëren, geeft u **FolderPath** op met een mappictogram en een **Bestands naam** met een Joker teken filter. 

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

### <a name="legacy-copy-activity-source-model"></a>Bron model van verouderde Kopieer activiteit

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **BlobSource**. |Ja |
| recursive | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen voor de opgegeven map. Houd er rekening mee dat wanneer recursieve is ingesteld op true en de sink is een opslagplaats op basis van bestanden, een lege map of submap is niet gekopieerd of gemaakt in de sink.<br/>Toegestane waarden zijn **True** (standaard) en **Onwaar**. | Nee |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met opslag archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee |

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

### <a name="legacy-copy-activity-sink-model"></a>Sink model voor verouderde Kopieer activiteit

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op **BlobSink**. |Ja |
| copyBehavior | Definieert het gedrag kopiëren wanneer de bron bestanden vanuit een bestandsgebaseerde gegevensarchief is.<br/><br/>Toegestane waarden zijn:<br/><b>-PreserveHierarchy (standaard instelling)</b>: behoudt de bestands hiërarchie in de doelmap. Het relatieve pad van het bronbestand voor bronmap is identiek aan het relatieve pad van doelbestand naar doelmap.<br/><b>-FlattenHierarchy</b>: alle bestanden in de bronmap bevinden zich in het eerste niveau van de doelmap. De doelbestanden hebben automatisch gegenereerde namen. <br/><b>-MergeFiles</b>: alle bestanden van de bronmap worden samengevoegd met één bestand. Als de naam van het bestand of de blob is opgegeven, is de naam van het samengevoegde de opgegeven naam. Anders is de naam van een automatisch gegenereerde bestand. | Nee |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met opslag archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee |

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

Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in Data Factory.
