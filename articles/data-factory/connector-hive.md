---
title: Gegevens kopiëren van Hive met Azure Data Factory | Microsoft Docs
description: Leer hoe u gegevens kopiëren van Hive naar ondersteunde sink-gegevensopslag met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
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
ms.openlocfilehash: 40f97c3b31a7e49c9a5ecc790e3cc762572ecaa3
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70276355"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Gegevens kopiëren van Hive met Azure Data Factory 

In dit artikel bevat een overzicht over het gebruik van de Kopieeractiviteit in Azure Data Factory om gegevens te kopiëren van Hive. Dit is gebaseerd op de [overzicht kopieeractiviteit](copy-activity-overview.md) artikel met daarin een algemeen overzicht van de kopieeractiviteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U kunt gegevens uit Hive kopiëren naar een ondersteunde sink-gegevensopslag. Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen/put door de kopieeractiviteit, de [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats) tabel.

Azure Data Factory biedt een ingebouwde stuurprogramma als connectiviteit wilt inschakelen, dus hoeft u stuurprogramma voor gebruik van deze connector handmatig installeren.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten meer informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten specifieke met Hive-connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor Hive gekoppelde service:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type moet worden ingesteld op: **Hive** | Ja |
| host | IP-adres of de hostnaam de naam van de Hive-server, gescheiden door ';' voor meerdere hosts (alleen bij serviceDiscoveryMode inschakelen is).  | Ja |
| port | De TCP-poort die gebruikmaakt van de Hive-server om te luisteren naar clientverbindingen. Als u verbinding met Azure HDInsights maken, geeft u poort 443. | Ja |
| serverType | Het type van Hive-server. <br/>Toegestane waarden zijn: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | Nee |
| thriftTransportProtocol | Het te gebruiken in de laag Thrift-transportprotocol. <br/>Toegestane waarden zijn: **Binary**, **SASL**, **HTTP** | Nee |
| authenticationType | De verificatiemethode die wordt gebruikt voor toegang tot de Hive-server. <br/>Toegestane waarden zijn: **Anonymous**, **Username**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Ja |
| serviceDiscoveryMode | True om aan te geven met de ZooKeeper-service, ONWAAR niet.  | Nee |
| zooKeeperNameSpace | De naamruimte op ZooKeeper onder welke Hive Server 2 knooppunten worden toegevoegd.  | Nee |
| useNativeQuery | Geeft aan of het stuurprogramma maakt gebruik van systeemeigen HiveQL-query's, of deze naar een equivalente formulier in HiveQL converteert.  | Nee |
| username | De gebruikersnaam die u gebruikt voor toegang tot Hive-Server.  | Nee |
| password | Het wachtwoord dat overeenkomt met de gebruiker. Dit veld markeren als een SecureString Bewaar deze zorgvuldig in Data Factory, of [verwijzen naar een geheim opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). | Nee |
| httpPath | De gedeeltelijke URL overeenkomt met de Hive-server.  | Nee |
| enableSsl | Hiermee geeft u op of de verbindingen met de server zijn versleuteld met behulp van SSL. De standaardwaarde is false.  | Nee |
| trustedCertPath | Het volledige pad van het .pem-bestand met de vertrouwde CA-certificaten voor het controleren van de server wanneer u verbinding maakt via SSL. Deze eigenschap kan alleen worden ingesteld wanneer u SSL op zelf-hostende IR De standaardwaarde is het bestand cacerts.pem is geïnstalleerd met de IR.  | Nee |
| useSystemTrustStore | Hiermee bepaalt u of u een CA-certificaat uit het archief van de vertrouwensrelatie systeem- of uit een opgegeven PEM-bestand. De standaardwaarde is false.  | Nee |
| allowHostNameCNMismatch | Hiermee geeft u op of de naam van een Certificeringsinstantie uitgegeven SSL-certificaat zodat deze overeenkomen met de hostnaam van de server wanneer u verbinding maakt via SSL vereist. De standaardwaarde is false.  | Nee |
| allowSelfSignedServerCert | Hiermee geeft u op of zelfondertekende certificaten van de server is toegestaan. De standaardwaarde is false.  | Nee |
| connectVia | De [Integration Runtime](concepts-integration-runtime.md) moet worden gebruikt verbinding maken met het gegevensarchief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als niet is opgegeven, wordt de standaard Azure Integration Runtime. |Nee |

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

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door Hive-gegevensset.

Om gegevens te kopiëren van de component, stel de eigenschap type van de gegevensset in **HiveObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van de gegevensset moet worden ingesteld op: **HiveObject** | Ja |
| schema | De naam van het schema. |Nee (als 'query' in de activiteitbron is opgegeven)  |
| table | Naam van de tabel. |Nee (als 'query' in de activiteitbron is opgegeven)  |
| tableName | De naam van de tabel met inbegrip van het schema gedeelte. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` en`table`voor nieuwe werk belasting. | Nee (als 'query' in de activiteitbron is opgegeven) |

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

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door Hive-bron.

### <a name="hivesource-as-source"></a>HiveSource als bron

Om gegevens te kopiëren van Hive, stelt u het brontype in de kopieeractiviteit naar **HiveSource**. De volgende eigenschappen worden ondersteund in de kopieeractiviteit **bron** sectie:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van de bron van de Kopieer activiteit moet worden ingesteld op: **HiveSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als de 'tableName' in de gegevensset is opgegeven) |

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

## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats).
