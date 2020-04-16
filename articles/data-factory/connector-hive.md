---
title: Gegevens uit Hive kopiëren met Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit Hive naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
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
ms.openlocfilehash: 3720d917d71fa4e8c5a14bb60fdc7c405be4bfdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410443"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Gegevens uit Hive kopiëren met Azure Data Factory 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens uit Hive te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Hive-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens van Hive kopiëren naar een ondersteund sink data store. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Azure Data Factory biedt een ingebouwd stuurprogramma om connectiviteit mogelijk te maken, daarom hoeft u geen stuurprogramma handmatig te installeren met deze connector.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor de Hive-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor Hive linked service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **Hive** | Ja |
| host | IP-adres of hostnaam van de Hive-server, gescheiden door ';' voor meerdere hosts (alleen wanneer serviceDiscoveryMode is ingeschakeld).  | Ja |
| poort | De TCP-poort die de Hive-server gebruikt om naar clientverbindingen te luisteren. Als u verbinding maakt met Azure HDInsights, geeft u de poort op als 443. | Ja |
| serverType | Het type Hive-server. <br/>Toegestane waarden zijn: **HiveServer1,** **HiveServer2**, **HiveThriftServer** | Nee |
| zuinigtransportProtocol | Het transportprotocol voor gebruik in de thrift laag. <br/>Toegestane waarden zijn: **Binair**, **SASL**, **HTTP** | Nee |
| authenticationType | De verificatiemethode die wordt gebruikt om toegang te krijgen tot de Hive-server. <br/>Toegestane waarden zijn: **Anoniem**, **Gebruikersnaam**, **Gebruikersnaam ,Password**, **WindowsAzureHDInsightService** | Ja |
| serviceDiscoveryMode | true om aan te geven dat u de ZooKeeper-service gebruikt, niet vals.  | Nee |
| zooKeeperNameSpace | De naamruimte op ZooKeeper waaronder Hive Server 2-knooppunten worden toegevoegd.  | Nee |
| gebruikNativeQuery | Hiermee geeft u op of het stuurprogramma native HiveQL-query's gebruikt of converteert in een gelijkwaardige vorm in HiveQL.  | Nee |
| gebruikersnaam | De gebruikersnaam die u gebruikt om toegang te krijgen tot Hive Server.  | Nee |
| wachtwoord | Het wachtwoord dat overeenkomt met de gebruiker. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Nee |
| httpPath httpPath | De gedeeltelijke URL die overeenkomt met de Hive-server.  | Nee |
| inschakelenSsl | Hiermee geeft u op of de verbindingen met de server zijn versleuteld met TLS. De standaardwaarde is false.  | Nee |
| trustedCertPath | Het volledige pad van het .pem-bestand met vertrouwde CA-certificaten voor het verifiëren van de server bij het maken via TLS. Deze eigenschap kan alleen worden ingesteld wanneer tls wordt gebruikt op zelf gehoste IR. De standaardwaarde is het cacerts.pem-bestand dat bij de IR is geïnstalleerd.  | Nee |
| gebruik vanSystemTrustStore | Hiermee geeft u op of u een CA-certificaat wilt gebruiken in het systeemvertrouwensarchief of uit een opgegeven PEM-bestand. De standaardwaarde is false.  | Nee |
| allowHostNameCNMismatch | Hiermee geeft u op of een door CA uitgegeven TLS/SSL-certificaatnaam moet overeenkomen met de hostnaam van de server wanneer u verbinding maakt via TLS. De standaardwaarde is false.  | Nee |
| allowSelfSignedServerCert | Hiermee geeft u op of zelfondertekende certificaten van de server moeten worden toegestaan. De standaardwaarde is false.  | Nee |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Meer informatie van de sectie [Voorwaarden.](#prerequisites) Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

**Voorbeeld:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
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

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de hive-gegevensset.

Als u gegevens uit Hive wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **HiveObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **HiveObject** | Ja |
| schema | Naam van het schema. |Nee (als 'query' in activiteitsbron is opgegeven)  |
| tabel | Naam van de tabel. |Nee (als 'query' in activiteitsbron is opgegeven)  |
| tableName | Naam van de tabel inclusief schemaonderdeel. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Voor nieuwe werkbelasting, gebruik `schema` en `table`. | Nee (als 'query' in activiteitsbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door Hive-bron.

### <a name="hivesource-as-source"></a>HiveSource als bron

Als u gegevens uit Hive wilt kopiëren, stelt u het brontype in de kopieeractiviteit in op **HiveSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de kopieeractiviteit moet zijn ingesteld op: **HiveSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als 'tabelNaam' in de gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
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
