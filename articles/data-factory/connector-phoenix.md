---
title: Gegevens uit Phoenix kopiëren met Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit Phoenix naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
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
ms.openlocfilehash: d8f63984a5ad3717b470657aba02224794122cd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930840"
---
# <a name="copy-data-from-phoenix-using-azure-data-factory"></a>Gegevens uit Phoenix kopiëren met Azure Data Factory 

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens uit Phoenix te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Phoenix-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens uit Phoenix kopiëren naar een ondersteund sink data store. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Azure Data Factory biedt een ingebouwd stuurprogramma om connectiviteit mogelijk te maken, daarom hoeft u geen stuurprogramma handmatig te installeren met deze connector.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor phoenix-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor phoenix gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **Phoenix** | Ja |
| host | Het IP-adres of de hostnaam van de Phoenix-server. (dat wil zeggen, 192.168.222.160)  | Ja |
| poort | De TCP-poort die de Phoenix-server gebruikt om naar clientverbindingen te luisteren. De standaardwaarde is 8765. Als u verbinding maakt met Azure HDInsights, geeft u de poort op als 443. | Nee |
| httpPath httpPath | De gedeeltelijke URL die overeenkomt met de Phoenix-server. (dat wil zeggen, /gateway/sandbox/phoenix/versie). Geef `/hbasephoenix0` op of u het HDInsights-cluster gebruikt.  | Nee |
| authenticationType | Het verificatiemechanisme dat wordt gebruikt om verbinding te maken met de Phoenix-server. <br/>Toegestane waarden zijn: **Anoniem**, **GebruikersnaamEnPassword**, **WindowsAzureHDInsightService** | Ja |
| gebruikersnaam | De gebruikersnaam die wordt gebruikt om verbinding te maken met de Phoenix-server.  | Nee |
| wachtwoord | Het wachtwoord dat overeenkomt met de gebruikersnaam. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Nee |
| inschakelenSsl | Hiermee geeft u op of de verbindingen met de server zijn versleuteld met SSL. De standaardwaarde is false.  | Nee |
| trustedCertPath | Het volledige pad van het .pem-bestand met vertrouwde CA-certificaten voor het verifiëren van de server bij het maken via SSL. Deze eigenschap kan alleen worden ingesteld wanneer ssl wordt gebruikt op zelf gehoste IR. De standaardwaarde is het cacerts.pem-bestand dat bij de IR is geïnstalleerd.  | Nee |
| gebruik vanSystemTrustStore | Hiermee geeft u op of u een CA-certificaat wilt gebruiken in het systeemvertrouwensarchief of uit een opgegeven PEM-bestand. De standaardwaarde is false.  | Nee |
| allowHostNameCNMismatch | Hiermee geeft u op of een door CA uitgegeven SSL-certificaatnaam moet overeenkomen met de hostnaam van de server wanneer deze verbinding maakt via SSL. De standaardwaarde is false.  | Nee |
| allowSelfSignedServerCert | Hiermee geeft u op of zelfondertekende certificaten van de server moeten worden toegestaan. De standaardwaarde is false.  | Nee |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Meer informatie van de sectie [Voorwaarden.](#prerequisites) Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

>[!NOTE]
>Als uw cluster geen ondersteuning biedt voor plaksessie, bijvoorbeeld HDInsight, voegt u expliciet knooppuntindex toe aan `/hbasephoenix0` het `/hbasephoenix`einde van de http-padinstelling, bijvoorbeeld opgeven in plaats van .

**Voorbeeld:**

```json
{
    "name": "PhoenixLinkedService",
    "properties": {
        "type": "Phoenix",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "443",
            "httpPath" : "/hbasephoenix0",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de gegevensset Phoenix.

Als u gegevens uit Phoenix wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **PhoenixObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **PhoenixObject** | Ja |
| schema | Naam van het schema. |Nee (als 'query' in activiteitsbron is opgegeven)  |
| tabel | Naam van de tabel. |Nee (als 'query' in activiteitsbron is opgegeven)  |
| tableName | Naam van de tabel met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` `table` en voor nieuwe werkbelasting. | Nee (als 'query' in activiteitsbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "PhoenixDataset",
    "properties": {
        "type": "PhoenixObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Phoenix linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door phoenixbron.

### <a name="phoenix-as-source"></a>Phoenix als bron

Als u gegevens uit Phoenix wilt kopiëren, stelt u het brontype in de kopieeractiviteit in op **PhoenixSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op: **PhoenixSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als 'tabelNaam' in de gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromPhoenix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Phoenix input dataset name>",
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
                "type": "PhoenixSource",
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
