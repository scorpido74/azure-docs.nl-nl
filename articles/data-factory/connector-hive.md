---
title: Gegevens uit een Hive kopiëren met behulp van Azure Data Factory
description: Meer informatie over het kopiëren van gegevens van Hive naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81410443"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Gegevens uit een Hive kopiëren met behulp van Azure Data Factory 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit Hive te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Hive-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Opzoek activiteit](control-flow-lookup-activity.md)

U kunt gegevens van Hive naar elk ondersteund Sink-gegevens archief kopiëren. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Azure Data Factory biedt een ingebouwd stuur programma om connectiviteit mogelijk te maken. u hoeft dus niet hand matig een stuur programma te installeren met behulp van deze connector.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Hive-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de gekoppelde service van het onderdeel:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **Hive** | Ja |
| host | Het IP-adres of de hostnaam van de Hive-server, gescheiden door '; ' voor meerdere hosts (alleen wanneer serviceDiscoveryMode is ingeschakeld).  | Ja |
| poort | De TCP-poort die de Hive-server gebruikt om te Luis teren naar client verbindingen. Als u verbinding maakt met Azure HDInsights, geeft u poort op als 443. | Ja |
| Server type | Het type van de Hive-server. <br/>Toegestane waarden zijn: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | Nee |
| thriftTransportProtocol | Het transport protocol dat in de Thrift-laag moet worden gebruikt. <br/>Toegestane waarden zijn: **binary**, **sasl**, **http** | Nee |
| authenticationType | De verificatie methode die wordt gebruikt voor toegang tot de Hive-server. <br/>Toegestane waarden zijn: **Anonymous**, **username**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Ja |
| serviceDiscoveryMode | True om aan te geven dat de ZooKeeper-service wordt gebruikt, False not.  | Nee |
| zooKeeperNameSpace | De naam ruimte op ZooKeeper waarmee de knoop punten van de Hive-Server 2 worden toegevoegd.  | Nee |
| useNativeQuery | Hiermee geeft u op of het stuur programma systeem eigen HiveQL-query's gebruikt of converteert u deze naar een gelijkwaardig formulier in HiveQL.  | Nee |
| gebruikersnaam | De gebruikers naam die u gebruikt voor toegang tot de Hive-server.  | Nee |
| wachtwoord | Het wacht woord dat bij de gebruiker hoort. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Nee |
| httpPath | De gedeeltelijke URL die overeenkomt met de Hive-server.  | Nee |
| enableSsl | Hiermee geeft u op of de verbindingen met de server met behulp van TLS worden versleuteld. De standaardwaarde is false.  | Nee |
| trustedCertPath | Het volledige pad van het. pem-bestand met vertrouwde CA-certificaten voor het verifiëren van de server bij het maken van verbinding via TLS. Deze eigenschap kan alleen worden ingesteld wanneer TLS op zelf-hostende IR wordt gebruikt. De standaard waarde is het cacerts. pem-bestand dat met de IR is geïnstalleerd.  | Nee |
| useSystemTrustStore | Hiermee geeft u op of u een CA-certificaat wilt gebruiken uit de systeem vertrouwens archief of vanuit een opgegeven PEM-bestand. De standaardwaarde is false.  | Nee |
| allowHostNameCNMismatch | Hiermee geeft u op of een door de certificerings instantie uitgegeven TLS/SSL-certificaat naam moet overeenkomen met de hostnaam van de server bij het maken van verbinding via TLS. De standaardwaarde is false.  | Nee |
| allowSelfSignedServerCert | Hiermee geeft u op of zelfondertekende certificaten van de server mogen worden toegestaan. De standaardwaarde is false.  | Nee |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |Nee |

**Hierbij**

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

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Hive-gegevensset.

Als u gegevens wilt kopiëren uit Hive, stelt u de eigenschap type van de gegevensset in op **HiveObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **HiveObject** | Ja |
| schema | De naam van het schema. |Nee (als "query" in activiteit bron is opgegeven)  |
| tabel | De naam van de tabel. |Nee (als "query" in activiteit bron is opgegeven)  |
| tableName | De naam van de tabel met inbegrip van het schema gedeelte. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` en `table`voor nieuwe werk belasting. | Nee (als "query" in activiteit bron is opgegeven) |

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

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Hive-bron.

### <a name="hivesource-as-source"></a>HiveSource als bron

Als u gegevens wilt kopiëren uit Hive, stelt u het bron type in de Kopieer activiteit in op **HiveSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **HiveSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als ' Tablename ' in gegevensset is opgegeven) |

**Hierbij**

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

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.


## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
