---
title: Gegevens uit Google AdWords kopiëren
description: Meer informatie over het kopiëren van gegevens van Google AdWords naar ondersteunde sink-gegevensarchieven met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/25/2019
ms.openlocfilehash: b01dcad71747da6b7aa770e3993cb82892ae55fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929441"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory"></a>Gegevens uit Google AdWords kopiëren met Azure Data Factory

In dit artikel wordt beschreven hoe u activiteit kopiëren in Azure Data Factory gebruikt om gegevens van Google AdWords te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Google AdWords-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)


U gegevens van Google AdWords kopiëren naar elk ondersteund sink-gegevensarchief. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Azure Data Factory biedt een ingebouwd stuurprogramma om connectiviteit mogelijk te maken, daarom hoeft u geen stuurprogramma handmatig te installeren met deze connector.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor de AdWords-connector van Google.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de gekoppelde Service van Google AdWords:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **GoogleAdWords** | Ja |
| klantCustomerID | De klant-id van de klant van het AdWords-account waarvoor u rapportgegevens wilt ophalen.  | Ja |
| developerToken | Het ontwikkelaarstoken dat is gekoppeld aan het beheeraccount dat u gebruikt om toegang te verlenen tot de AdWords-API.  U ervoor kiezen om dit veld te markeren als een SecureString om het veilig op te slaan in ADF, of wachtwoord op te slaan in Azure Key Vault en ADF activiteit van daaruit te laten kopiëren bij het uitvoeren van gegevenskopie - meer informatie over [storereferenties in Key Vault](store-credentials-in-key-vault.md). | Ja |
| authenticationType | Het OAuth 2.0-verificatiemechanisme dat wordt gebruikt voor verificatie. ServiceAuthentication kan alleen worden gebruikt op zelf gehoste IR. <br/>Toegestane waarden zijn: **ServiceAuthentication,** **UserAuthentication** | Ja |
| refreshToken | Het vernieuwingstoken dat van Google is verkregen voor het toestaan van toegang tot AdWords voor UserAuthentication. U ervoor kiezen om dit veld te markeren als een SecureString om het veilig op te slaan in ADF, of wachtwoord op te slaan in Azure Key Vault en ADF activiteit van daaruit te laten kopiëren bij het uitvoeren van gegevenskopie - meer informatie over [storereferenties in Key Vault](store-credentials-in-key-vault.md). | Nee |
| clientId | De client-id van de Google-toepassing die wordt gebruikt om het vernieuwingstoken te verkrijgen. U ervoor kiezen om dit veld te markeren als een SecureString om het veilig op te slaan in ADF, of wachtwoord op te slaan in Azure Key Vault en ADF activiteit van daaruit te laten kopiëren bij het uitvoeren van gegevenskopie - meer informatie over [storereferenties in Key Vault](store-credentials-in-key-vault.md). | Nee |
| clientGeheim | Het clientgeheim van de google-toepassing die wordt gebruikt om het vernieuwingstoken te verkrijgen. U ervoor kiezen om dit veld te markeren als een SecureString om het veilig op te slaan in ADF, of wachtwoord op te slaan in Azure Key Vault en ADF activiteit van daaruit te laten kopiëren bij het uitvoeren van gegevenskopie - meer informatie over [storereferenties in Key Vault](store-credentials-in-key-vault.md). | Nee |
| e-mail | De e-mail-ID van het serviceaccount die wordt gebruikt voor ServiceAuthentication en alleen kan worden gebruikt op zelf gehoste IR.  | Nee |
| keyFilePath | Het volledige pad naar het .p12-sleutelbestand dat wordt gebruikt om het e-mailadres van het serviceaccount te verifiëren en kan alleen worden gebruikt op zelf gehoste IR.  | Nee |
| trustedCertPath | Het volledige pad van het .pem-bestand met vertrouwde CA-certificaten voor het verifiëren van de server bij het maken via SSL. Deze eigenschap kan alleen worden ingesteld wanneer ssl wordt gebruikt op zelf gehoste IR. De standaardwaarde is het cacerts.pem-bestand dat bij de IR is geïnstalleerd.  | Nee |
| gebruik vanSystemTrustStore | Hiermee geeft u op of u een CA-certificaat wilt gebruiken in het systeemvertrouwensarchief of uit een opgegeven PEM-bestand. De standaardwaarde is false.  | Nee |

**Voorbeeld:**

```json
{
    "name": "GoogleAdWordsLinkedService",
    "properties": {
        "type": "GoogleAdWords",
        "typeProperties": {
            "clientCustomerID" : "<clientCustomerID>",
            "developerToken": {
                "type": "SecureString",
                "value": "<developerToken>"
            },
            "authenticationType" : "ServiceAuthentication",
            "refreshToken": {
                "type": "SecureString",
                "value": "<refreshToken>"
            },
            "clientId": {
                "type": "SecureString",
                "value": "<clientId>"
            },
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            },
            "email" : "<email>",
            "keyFilePath" : "<keyFilePath>",
            "trustedCertPath" : "<trustedCertPath>",
            "useSystemTrustStore" : true,
        }
    }
}

```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Google AdWords-gegevensset.

Als u gegevens uit Google AdWords wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **GoogleAdWordsObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **GoogleAdWordsObject** | Ja |
| tableName | Naam van de tabel. | Nee (als 'query' in activiteitsbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Google AdWords-bron.

### <a name="google-adwords-as-source"></a>Google AdWords als bron

Als u gegevens uit Google AdWords wilt kopiëren, stelt u het brontype in de kopieeractiviteit in op **GoogleAdWordsSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron voor kopieeractiviteit moet zijn ingesteld op: **GoogleAdWordsSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als 'tabelNaam' in de gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromGoogleAdWords",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleAdWords input dataset name>",
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
                "type": "GoogleAdWordsSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
