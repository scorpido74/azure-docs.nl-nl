---
title: Gegevens van Google BigQuery kopiëren met Azure Data Factory
description: Meer informatie over het kopiëren van gegevens van Google BigQuery naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een pijplijn voor gegevensfabrieken.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 6751f64706444176f0df8f8fc0c6132e76b39b2d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417326"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Gegevens van Google BigQuery kopiëren met Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u Activiteit kopiëren in Azure Data Factory gebruiken om gegevens van Google BigQuery te kopiëren. Het bouwt voort op het [overzichtsartikel Van activiteit kopiëren](copy-activity-overview.md) dat een algemeen overzicht van de kopieeractiviteit weergeeft.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Google BigQuery-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens van Google BigQuery kopiëren naar elk ondersteund sink data store. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen of sinks door de kopieeractiviteit.

Data Factory biedt een ingebouwd stuurprogramma om connectiviteit mogelijk te maken. Daarom hoeft u geen stuurprogramma handmatig te installeren om deze connector te gebruiken.

>[!NOTE]
>Deze Google BigQuery-connector is gebouwd bovenop de BigQuery API's. Houd er rekening mee dat BigQuery het maximale percentage inkomende aanvragen beperkt en de juiste quota per project afdwingt, verwijzen naar [Quota & Limits - API-aanvragen](https://cloud.google.com/bigquery/quotas#api_requests). Zorg ervoor dat u niet te veel gelijktijdige aanvragen naar het account activeert.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabrieken te definiëren die specifiek zijn voor de Google BigQuery-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de gekoppelde Google BigQuery-service.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **GoogleBigQuery**. | Ja |
| project | De project-ID van het standaard BigQuery-project om tegen op te vragen.  | Ja |
| extraProjecten | Een door komma's gescheiden lijst met project-id's van openbare BigQuery-projecten om toegang te krijgen.  | Nee |
| verzoekGoogleDriveScope | Of u toegang tot Google Drive wilt aanvragen. Door Toegang tot Google Drive toe te staan, u ondersteuning bieden voor federatieve tabellen die BigQuery-gegevens combineren met gegevens van Google Drive. De standaardwaarde is **onwaar**.  | Nee |
| authenticationType | Het OAuth 2.0-verificatiemechanisme dat wordt gebruikt voor verificatie. ServiceAuthentication kan alleen worden gebruikt op Self-hosted Integration Runtime. <br/>Toegestane waarden zijn **UserAuthentication** en **ServiceAuthentication**. Raadpleeg secties onder deze tabel over meer eigenschappen en JSON-voorbeelden voor deze verificatietypen. | Ja |

### <a name="using-user-authentication"></a>Gebruikersverificatie gebruiken

Stel de eigenschap 'authenticationType' in **op UserAuthentication**en geef de volgende eigenschappen op, samen met algemene eigenschappen die in de vorige sectie zijn beschreven:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| clientId | ID van de toepassing die wordt gebruikt om het vernieuwingstoken te genereren. | Nee |
| clientGeheim | Geheim van de toepassing die wordt gebruikt om het vernieuwingstoken te genereren. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Nee |
| refreshToken | Het vernieuwingstoken dat van Google is verkregen, heeft de toegang tot BigQuery geautoriseerd. Meer informatie over hoe je er een krijgen van [Het verkrijgen van OAuth 2.0-toegangstokens](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) en [deze communityblog.](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59) Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Nee |

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

### <a name="using-service-authentication"></a>Serviceverificatie gebruiken

Stel de eigenschap 'authenticationType' in op **ServiceAuthentication**en geef de volgende eigenschappen op, samen met algemene eigenschappen die in de vorige sectie zijn beschreven. Dit verificatietype kan alleen worden gebruikt op Runtime voor zelfgehoste integratie.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| e-mail | De e-mail-id van het serviceaccount die wordt gebruikt voor ServiceAuthentication. Het kan alleen worden gebruikt op Self-hosted Integration Runtime.  | Nee |
| keyFilePath | Het volledige pad naar het .p12-sleutelbestand dat wordt gebruikt om het e-mailadres van het serviceaccount te verifiëren. | Nee |
| trustedCertPath | Het volledige pad van het .pem-bestand dat vertrouwde CA-certificaten bevat die worden gebruikt om de server te verifiëren wanneer u verbinding maakt via TLS. Deze eigenschap kan alleen worden ingesteld wanneer u TLS gebruikt op Self-hosted Integration Runtime. De standaardwaarde is het cacerts.pem-bestand dat is geïnstalleerd met de inburgeringsruntime.  | Nee |
| gebruik vanSystemTrustStore | Hiermee geeft u op of u een CA-certificaat wilt gebruiken in het systeemvertrouwensarchief of uit een opgegeven .pem-bestand. De standaardwaarde is **onwaar**.  | Nee |

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

Zie het artikel [Gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Google BigQuery-gegevensset.

Als u gegevens van Google BigQuery wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **GoogleBigQueryObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **GoogleBigQueryObject** | Ja |
| Dataset | Naam van de Google BigQuery-gegevensset. |Nee (als 'query' in activiteitsbron is opgegeven)  |
| tabel | Naam van de tabel. |Nee (als 'query' in activiteitsbron is opgegeven)  |
| tableName | Naam van de tabel. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Voor nieuwe werkbelasting, gebruik `dataset` en `table`. | Nee (als 'query' in activiteitsbron is opgegeven) |

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

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door het brontype Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource als brontype

Als u gegevens van Google BigQuery wilt kopiëren, stelt u het brontype in de kopieeractiviteit in op **GoogleBigQuerySource.** De volgende eigenschappen worden ondersteund in de **sectie** bron van kopieeractiviteit.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron voor kopieeractiviteit moet worden ingesteld op **GoogleBigQuerySource**. | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Een voorbeeld is `"SELECT * FROM MyTable"`. | Nee (als 'tabelNaam' in de gegevensset is opgegeven) |

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

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Volgende stappen
Zie [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Data Factory.
