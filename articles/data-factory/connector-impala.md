---
title: Gegevens uit Impala kopiëren met Azure Data Factory
description: Meer informatie over het kopiëren van gegevens van Impala naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een pijplijn voor gegevensfabrieken.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: b70db03e03ce914ea1d81d94cd2803a36eccfc88
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418215"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory"></a>Gegevens uit Impala kopiëren met Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u Activiteit kopiëren in Azure Data Factory gebruikt om gegevens van Impala te kopiëren. Het bouwt voort op het [overzichtsartikel Van activiteit kopiëren](copy-activity-overview.md) dat een algemeen overzicht van de kopieeractiviteit weergeeft.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Impala-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens van Impala kopiëren naar een ondersteund sink data store. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen of sinks door de kopieeractiviteit.

Data Factory biedt een ingebouwd stuurprogramma om connectiviteit mogelijk te maken. Daarom hoeft u geen stuurprogramma handmatig te installeren om deze connector te gebruiken.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor de Impala-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor impala gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **Impala**. | Ja |
| host | Het IP-adres of de hostnaam van de Impala-server (dat wil zeggen 192.168.222.160).  | Ja |
| poort | De TCP-poort die de Impala-server gebruikt om naar clientverbindingen te luisteren. De standaardwaarde is 21050.  | Nee |
| authenticationType | Het te gebruiken verificatietype. <br/>Toegestane waarden zijn **Anoniem,** **SASLUsername**en **GebruikersnaamEnPassword**. | Ja |
| gebruikersnaam | De gebruikersnaam die wordt gebruikt om toegang te krijgen tot de Impala-server. De standaardwaarde is anoniem wanneer u SASLUsername gebruikt.  | Nee |
| wachtwoord | Het wachtwoord dat overeenkomt met de gebruikersnaam wanneer u Gebruikersnaam EnWachtwoord gebruikt. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Nee |
| inschakelenSsl | Hiermee geeft u op of de verbindingen met de server zijn versleuteld met TLS. De standaardwaarde is **onwaar**.  | Nee |
| trustedCertPath | Het volledige pad van het .pem-bestand dat vertrouwde CA-certificaten bevat die worden gebruikt om de server te verifiëren wanneer u verbinding maakt via TLS. Deze eigenschap kan alleen worden ingesteld wanneer u TLS gebruikt op Self-hosted Integration Runtime. De standaardwaarde is het cacerts.pem-bestand dat is geïnstalleerd met de inburgeringsruntime.  | Nee |
| gebruik vanSystemTrustStore | Hiermee geeft u op of u een CA-certificaat wilt gebruiken in het systeemvertrouwensarchief of uit een opgegeven PEM-bestand. De standaardwaarde is **onwaar**.  | Nee |
| allowHostNameCNMismatch | Hiermee geeft u op of een door CA uitgegeven TLS/SSL-certificaatnaam moet overeenkomen met de hostnaam van de server wanneer u verbinding maakt via TLS. De standaardwaarde is **onwaar**.  | Nee |
| allowSelfSignedServerCert | Hiermee geeft u op of zelfondertekende certificaten van de server moeten worden toegestaan. De standaardwaarde is **onwaar**.  | Nee |
| connectVia | De [inburgeringsruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Meer informatie van de sectie [Voorwaarden.](#prerequisites) Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

**Voorbeeld:**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
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

Zie het artikel [Gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Impala-gegevensset.

Als u gegevens uit Impala wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **ImpalaObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **ImpalaObject** | Ja |
| schema | Naam van het schema. |Nee (als 'query' in activiteitsbron is opgegeven)  |
| tabel | Naam van de tabel. |Nee (als 'query' in activiteitsbron is opgegeven)  |
| tableName | Naam van de tabel met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` `table` en voor nieuwe werkbelasting. | Nee (als 'query' in activiteitsbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door het type Impala-bron.

### <a name="impala-as-a-source-type"></a>Impala als brontype

Als u gegevens uit Impala wilt kopiëren, stelt u het brontype in de kopieeractiviteit in op **ImpalaSource**. De volgende eigenschappen worden ondersteund in de **sectie** bron van kopieeractiviteit.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op **ImpalaSource**. | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Een voorbeeld is `"SELECT * FROM MyTable"`. | Nee (als 'tabelNaam' in de gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
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
