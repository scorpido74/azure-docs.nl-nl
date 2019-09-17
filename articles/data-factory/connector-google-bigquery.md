---
title: Gegevens uit Google BigQuery kopiëren met behulp van Azure Data Factory | Microsoft Docs
description: Meer informatie over het kopiëren van gegevens uit Google BigQuery naar ondersteunde sink-gegevensopslag met behulp van een kopieeractiviteit in een data factory-pijplijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 70eb70d7c63d66601aebf8e05a2b14b582058296
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009244"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Gegevens uit Google BigQuery kopiëren met behulp van Azure Data Factory

In dit artikel bevat een overzicht over het gebruik van de Kopieeractiviteit in Azure Data Factory om gegevens te kopiëren uit Google BigQuery. Dit is gebaseerd op de [overzicht van Kopieeractiviteit](copy-activity-overview.md) artikel met daarin een algemeen overzicht van de kopieeractiviteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Google BigQuery-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit Google BigQuery kopiëren naar een ondersteunde sink-gegevensopslag. Zie voor een lijst met gegevensarchieven die worden ondersteund als gegevensbronnen of PUT voor de kopieeractiviteit, de [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats) tabel.

Data Factory biedt een ingebouwde stuurprogramma als connectiviteit wilt inschakelen. Daarom moet u niet handmatig een stuurprogramma voor het gebruik van deze connector hebt geïnstalleerd.

>[!NOTE]
>Deze Google BigQuery-connector is gebaseerd op de APIs BigQuery. Houd er rekening mee dat BigQuery limieten voor de maximale snelheid van inkomende aanvragen en dwingt juiste quota op basis van per-project, verwijzen naar [quota en limieten - API-aanvragen](https://cloud.google.com/bigquery/quotas#api_requests). Zorg ervoor dat u te veel gelijktijdige aanvragen voor het account niet activeren.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten meer informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten specifieke naar de Google BigQuery-connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor de Google BigQuery service gekoppelde.

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **GoogleBigQuery**. | Ja |
| project | De project-ID van het project standaard BigQuery-query op.  | Ja |
| additionalProjects | Een door komma's gescheiden lijst van project-id's van de openbare BigQuery projecten toegang krijgen tot.  | Nee |
| requestGoogleDriveScope | Hiermee geeft u op of u voor het aanvragen van toegang tot Google Drive. Google Drive-toegang toe te staan, schakelt u ondersteuning voor federatieve-tabellen BigQuery-gegevens met gegevens uit Google Drive combineren. De standaardwaarde is **false**.  | Nee |
| authenticationType | Het OAuth 2.0-verificatiemechanisme voor verificatie gebruikt. ServiceAuthentication kan alleen worden gebruikt voor zelfgehoste Cloudintegratieruntime. <br/>Toegestane waarden zijn **UserAuthentication** en **ServiceAuthentication**. Zie de secties onder deze tabel op meer eigenschappen en JSON-voorbeelden voor deze verificatietypen respectievelijk. | Ja |

### <a name="using-user-authentication"></a>Met behulp van verificatie van de gebruiker

Stel de eigenschap 'authenticationType' in op **UserAuthentication**, en geeft u de volgende eigenschappen samen met de algemene eigenschappen die worden beschreven in de vorige sectie:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| clientId | ID van de toepassing die wordt gebruikt voor het genereren van het vernieuwingstoken. | Nee |
| clientSecret | Geheim van de toepassing die wordt gebruikt voor het genereren van het vernieuwingstoken. Dit veld markeren als een SecureString Bewaar deze zorgvuldig in Data Factory, of [verwijzen naar een geheim opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). | Nee |
| refreshToken | Het vernieuwingstoken dat is verkregen van Google gebruikt voor de autorisatie van toegang tot BigQuery. Informatie over het verkrijgen van [het verkrijgen van OAuth 2.0-toegangstokens](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) en [deze communityblog](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Dit veld markeren als een SecureString Bewaar deze zorgvuldig in Data Factory, of [verwijzen naar een geheim opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). | Nee |

**Voorbeeld:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>Met behulp van service-verificatie

Stel de eigenschap 'authenticationType' in op **ServiceAuthentication**, en geeft u de volgende eigenschappen samen met de algemene eigenschappen die worden beschreven in de vorige sectie. Dit verificatietype kan alleen worden gebruikt voor zelfgehoste Cloudintegratieruntime.

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| email | De service-account e-mail-ID die wordt gebruikt voor ServiceAuthentication. Het kan alleen worden gebruikt voor zelfgehoste Cloudintegratieruntime.  | Nee |
| keyFilePath | Het volledige pad naar het .p12-sleutelbestand die wordt gebruikt voor verificatie van het e-mailadres van de service-account. | Nee |
| trustedCertPath | Het volledige pad van het .pem-bestand met vertrouwde CA-certificaten gebruikt om te controleren of de server wanneer u verbinding via SSL maakt. Deze eigenschap kan alleen worden ingesteld als u SSL op zelfgehoste Cloudintegratieruntime. De standaardwaarde is de cacerts.pem-bestand met de integratieruntime geïnstalleerd.  | Nee |
| useSystemTrustStore | Hiermee geeft u op of u wilt gebruiken van een CA-certificaat uit het archief van de vertrouwensrelatie system of vanuit een opgegeven .pem-bestand. De standaardwaarde is **false**.  | Nee |

**Voorbeeld:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Google BigQuery-gegevensset.

Als u wilt kopiëren van gegevens uit Google BigQuery, stel de eigenschap type van de gegevensset in **GoogleBigQueryObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van de gegevensset moet worden ingesteld op: **GoogleBigQueryObject** | Ja |
| sets | De naam van de Google BigQuery-gegevensset. |Nee (als 'query' in de activiteitbron is opgegeven)  |
| table | Naam van de tabel. |Nee (als 'query' in de activiteitbron is opgegeven)  |
| tableName | Naam van de tabel. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `dataset` en`table`voor nieuwe werk belasting. | Nee (als 'query' in de activiteitbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door het brontype Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource als een brontype

Om gegevens te kopiëren uit Google BigQuery, stelt u het brontype in de kopieeractiviteit naar **GoogleBigQuerySource**. De volgende eigenschappen worden ondersteund in de kopieeractiviteit **bron** sectie.

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron voor kopiëren-activiteit moet worden ingesteld op **GoogleBigQuerySource**. | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Een voorbeeld is `"SELECT * FROM MyTable"`. | Nee (als de 'tableName' in de gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats).
