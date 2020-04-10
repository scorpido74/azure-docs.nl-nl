---
title: Gegevens van Spark kopiëren
description: Meer informatie over het kopiëren van gegevens van Spark naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: cb628ec73430d542295a83773401c256421605a0
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990868"
---
# <a name="copy-data-from-spark-using-azure-data-factory"></a>Gegevens van Spark kopiëren met Azure Data Factory 

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens van Spark te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Spark-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens van Spark kopiëren naar een ondersteund sink data store. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Azure Data Factory biedt een ingebouwd stuurprogramma om connectiviteit mogelijk te maken, daarom hoeft u geen stuurprogramma handmatig te installeren met deze connector.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor spark-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor spark-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **Spark** | Ja |
| host | IP-adres of hostnaam van de Spark-server  | Ja |
| poort | De TCP-poort die de Spark-server gebruikt om naar clientverbindingen te luisteren. Als u verbinding maakt met Azure HDInsights, geeft u de poort op als 443. | Ja |
| serverType | Het type Spark-server. <br/>Toegestane waarden zijn: **SharkServer,** **SharkServer2,** **SparkThriftServer** | Nee |
| zuinigtransportProtocol | Het transportprotocol voor gebruik in de thrift laag. <br/>Toegestane waarden zijn: **Binair**, **SASL**, **HTTP** | Nee |
| authenticationType | De verificatiemethode die wordt gebruikt om toegang te krijgen tot de Spark-server. <br/>Toegestane waarden zijn: **Anoniem**, **Gebruikersnaam**, **Gebruikersnaam ,Password**, **WindowsAzureHDInsightService** | Ja |
| gebruikersnaam | De gebruikersnaam die u gebruikt om toegang te krijgen tot Spark Server.  | Nee |
| wachtwoord | Het wachtwoord dat overeenkomt met de gebruiker. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Nee |
| httpPath httpPath | De gedeeltelijke URL die overeenkomt met de Spark-server.  | Nee |
| inschakelenSsl | Hiermee geeft u op of de verbindingen met de server zijn versleuteld met TLS. De standaardwaarde is false.  | Nee |
| trustedCertPath | Het volledige pad van het .pem-bestand met vertrouwde CA-certificaten voor het verifiëren van de server bij het maken via TLS. Deze eigenschap kan alleen worden ingesteld wanneer tls wordt gebruikt op zelf gehoste IR. De standaardwaarde is het cacerts.pem-bestand dat bij de IR is geïnstalleerd.  | Nee |
| gebruik vanSystemTrustStore | Hiermee geeft u op of u een CA-certificaat wilt gebruiken in het systeemvertrouwensarchief of uit een opgegeven PEM-bestand. De standaardwaarde is false.  | Nee |
| allowHostNameCNMismatch | Hiermee geeft u op of een door CA uitgegeven TLS/SSL-certificaatnaam moet overeenkomen met de hostnaam van de server wanneer u verbinding maakt via TLS. De standaardwaarde is false.  | Nee |
| allowSelfSignedServerCert | Hiermee geeft u op of zelfondertekende certificaten van de server moeten worden toegestaan. De standaardwaarde is false.  | Nee |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Meer informatie van de sectie [Voorwaarden.](#prerequisites) Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

**Voorbeeld:**

```json
{
    "name": "SparkLinkedService",
    "properties": {
        "type": "Spark",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
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

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Spark-gegevensset.

Als u gegevens uit Spark wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **SparkObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **SparkObject** | Ja |
| schema | Naam van het schema. |Nee (als 'query' in activiteitsbron is opgegeven)  |
| tabel | Naam van de tabel. |Nee (als 'query' in activiteitsbron is opgegeven)  |
| tableName | Naam van de tabel met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` `table` en voor nieuwe werkbelasting. | Nee (als 'query' in activiteitsbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "SparkDataset",
    "properties": {
        "type": "SparkObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Spark linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door Spark-bron.

### <a name="spark-as-source"></a>Vonk als bron

Als u gegevens van Spark wilt kopiëren, stelt u het brontype in de kopieeractiviteit in op **SparkSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van kopieeractiviteit moet zijn ingesteld op: **SparkSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als 'tabelNaam' in de gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSpark",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Spark input dataset name>",
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
                "type": "SparkSource",
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
