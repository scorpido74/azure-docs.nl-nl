---
title: Gegevens kopiëren van en naar Azure Database for PostgreSQL met behulp van Azure Data Factory | Microsoft Docs
description: Informatie over het kopiëren van gegevens van en naar Azure Database for PostgreSQL met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: jingwang
ms.openlocfilehash: b92177b162f4649f253bf74372b175fc16130af6
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300395"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Gegevens kopiëren van en naar Azure Database for PostgreSQL met behulp van Azure Data Factory

In dit artikel wordt beschreven hoe u de functie Copy activity in Azure Data Factory gebruikt om gegevens te kopiëren van Azure Database for PostgreSQL. Het is gebaseerd op de [Kopieer activiteit in azure Data Factory](copy-activity-overview.md) artikel, waarin een algemeen overzicht van de Kopieer activiteit wordt weer gegeven.

Deze connector is speciaal bedoeld voor de [Azure database for PostgreSQL-service](../postgresql/overview.md). Als u gegevens wilt kopiëren vanuit een algemene PostgreSQL-data base die zich on-premises of in de Cloud bevindt, gebruikt u de [postgresql-connector](connector-postgresql.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Database for PostgreSQL-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit een Azure Database for PostgreSQL kopiëren naar een ondersteunde sink-gegevensopslag. U kunt ook gegevens van elk ondersteund brongegevens archief kopiëren naar Azure Database for PostgreSQL. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en Sinks.

Azure Data Factory biedt een ingebouwde stuurprogramma als connectiviteit wilt inschakelen. Daarom hoeft u geen stuur Programma's hand matig te installeren om deze connector te gebruiken.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bieden informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Azure Database for PostgreSQL-connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor de Azure Database for PostgreSQL gekoppelde service:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **AzurePostgreSql**. | Ja |
| connectionString | Een ODBC-verbindingsreeks verbinding maakt met Azure Database voor PostgreSQL.<br/>Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory. U kunt ook een wacht woord in azure Key Vault plaatsen en de `password` configuratie uit de Connection String halen. Raadpleeg de volgende voor beelden en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) voor meer informatie. | Ja |
| connectVia | Deze eigenschap vertegenwoordigt de [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt Azure Integration Runtime of zelfgehoste Cloudintegratieruntime gebruiken (als het gegevensarchief bevindt zich in een particulier netwerk). Als niet is opgegeven, wordt de standaard Azure Integration Runtime. |Nee |

Een gebruikelijke verbindingsreeks is `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Hier vindt u meer eigenschappen die u per case kunt instellen:

| Eigenschap | Description | Opties | Vereist |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| De methode voor het stuurprogramma wordt gebruikt voor het versleutelen van gegevens die worden verzonden tussen het stuurprogramma en de database-server. Bijvoorbeeld:`EncryptionMethod=<0/1/6>;`| 0 (geen versleuteling) **(standaard)** / 1 (SSL) / 6 (RequestSSL) | Nee |
| ValidateServerCertificate (VSC) | Hiermee wordt bepaald of het stuur programma het certificaat valideert dat door de database server wordt verzonden wanneer SSL-versleuteling is ingeschakeld (versleutelings methode = 1). Bijvoorbeeld:`ValidateServerCertificate=<0/1>;`| 0 (uitgeschakeld) **(standaard)** / 1 (ingeschakeld) | Nee |

**Voor beeld**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
            }
        }
    }
}
```

**Voor beeld**:

***Wacht woord opslaan in Azure Key Vault***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie [gegevens sets in azure Data Factory](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. In deze sectie vindt u een lijst met eigenschappen die Azure Database for PostgreSQL in gegevens sets ondersteunt.

Om gegevens te kopiëren uit een Azure Database for PostgreSQL, stel de eigenschap type van de gegevensset in **AzurePostgreSqlTable**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de DataSet moet worden ingesteld op **AzurePostgreSqlTable** | Ja |
| tableName | Naam van de tabel | Nee (als 'query' in de activiteitbron is opgegeven) |

**Voor beeld**:

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, Zie [pijp lijnen en activiteiten in azure Data Factory](concepts-pipelines-activities.md). Deze sectie bevat een lijst met eigenschappen die worden ondersteund door een Azure Database for PostgreSQL bron.

### <a name="azure-database-for-postgresql-as-source"></a>Azure Database voor PostgreSql als bron

Om gegevens te kopiëren uit een Azure Database for PostgreSQL, stelt u het brontype in de kopieeractiviteit naar **AzurePostgreSqlSource**. De volgende eigenschappen worden ondersteund in de kopieeractiviteit **bron** sectie:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **AzurePostgreSqlSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"` | Nee (als de eigenschap TableName in de gegevensset is opgegeven) |

**Voor beeld**:

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Azure Database for PostgreSQL als Sink

Als u gegevens wilt kopiëren naar Azure Database for PostgreSQL, worden de volgende eigenschappen ondersteund in het gedeelte **sink** van Kopieer activiteit:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op **AzurePostgreSQLSink**. | Ja |
| preCopyScript | Geef een SQL-query op voor de Kopieer activiteit die moet worden uitgevoerd voordat u in elke uitvoering gegevens in Azure Database for PostgreSQL schrijft. U kunt deze eigenschap gebruiken om de vooraf geladen gegevens op te schonen. | Nee |
| writeBatchSize | Hiermee worden gegevens in de Azure Database for PostgreSQL tabel ingevoegd wanneer de buffer grootte writeBatchSize bereikt.<br>Toegestane waarde is een geheel getal dat het aantal rijen vertegenwoordigt. | Nee (de standaard waarde is 10.000) |
| writeBatchTimeout | Wacht tijd voordat de batch INSERT-bewerking is voltooid voordat er een time-out optreedt.<br>Toegestane waarden zijn time span-teken reeksen. Een voor beeld is 00:30:00 (30 minuten). | Nee (de standaard waarde is 00:00:30) |

**Voor beeld**:

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Zie voor meer informatie over de eigenschappen [lookup-activiteit in azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
