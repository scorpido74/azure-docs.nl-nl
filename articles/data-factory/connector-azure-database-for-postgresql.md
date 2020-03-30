---
title: Gegevens van en naar Azure Database kopiëren voor PostgreSQL
description: Meer informatie over het kopiëren van gegevens van en naar Azure Database voor PostgreSQL met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/16/2019
ms.openlocfilehash: 67d59e3f733efe5a248e6763f46402302496d437
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444376"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Gegevens van en naar Azure Database voor PostgreSQL kopiëren met Azure Data Factory

In dit artikel wordt beschreven hoe u de functie kopieeractiviteit in Azure Data Factory gebruiken om gegevens uit Azure Database voor PostgreSQL te kopiëren. Het bouwt voort op het artikel [Kopieeractiviteit in Azure Data Factory,](copy-activity-overview.md) dat een algemeen overzicht van kopieeractiviteit weergeeft.

Deze connector is gespecialiseerd voor de [Azure Database for PostgreSQL-service.](../postgresql/overview.md) Als u gegevens wilt kopiëren uit een algemene PostgreSQL-database die zich on-premises of in de cloud bevindt, gebruikt u de [PostgreSQL-connector.](connector-postgresql.md)

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Database voor PostgreSQL-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met een [ondersteunde bron/gootsteenmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens uit Azure Database voor PostgreSQL kopiëren naar elk ondersteund sinkdataarchief. U ook gegevens uit elk ondersteund brongegevensarchief kopiëren naar Azure Database voor PostgreSQL. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die de kopieeractiviteit als bronnen en zinkt ondersteunt.

Azure Data Factory biedt een ingebouwd stuurprogramma om connectiviteit mogelijk te maken. Daarom hoeft u geen stuurprogramma handmatig te installeren om deze connector te gebruiken.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties worden details gegeven over eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor Azure Database voor PostgreSQL-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de Azure Database for PostgreSQL-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **AzurePostgreSql**. | Ja |
| Connectionstring | Een ODBC-verbindingstekenreeks om verbinding te maken met Azure Database voor PostgreSQL.<br/>U ook een wachtwoord in Azure `password` Key Vault plaatsen en de configuratie uit de verbindingstekenreeks halen. Zie de volgende voorbeelden en [storereferenties in Azure Key Vault](store-credentials-in-key-vault.md) voor meer informatie. | Ja |
| connectVia | Deze eigenschap vertegenwoordigt de [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. U Azure Integration Runtime of Self-hosted Integration Runtime gebruiken (als uw gegevensarchief zich in een privénetwerk bevindt). Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

Een typische verbindingstekenreeks is `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Hier volgen meer eigenschappen die u per aanvraag instellen:

| Eigenschap | Beschrijving | Opties | Vereist |
|:--- |:--- |:--- |:--- |
| Encryptiemethode (EM)| De methode die het stuurprogramma gebruikt om gegevens te versleutelen die tussen het stuurprogramma en de databaseserver worden verzonden. Bijvoorbeeld,`EncryptionMethod=<0/1/6>;`| 0 (Geen versleuteling) **(standaard)** / 1 (SSL) / 6 (RequestSSL) | Nee |
| ValidateServerCertificaat (VSC) | Hiermee bepaalt u of het stuurprogramma het certificaat valideert dat door de databaseserver wordt verzonden wanneer SSL-versleuteling is ingeschakeld (Versleutelingsmethode=1). Bijvoorbeeld,`ValidateServerCertificate=<0/1>;`| 0 (uitgeschakeld) **(standaard)** / 1 (ingeschakeld) | Nee |

**Voorbeeld**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**Voorbeeld**:

***Wachtwoord opslaan in Azure Key Vault***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
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

Zie [Gegevenssets in Azure Data Factory](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die Azure Database voor PostgreSQL ondersteunt in gegevenssets.

Als u gegevens uit Azure Database voor PostgreSQL wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **AzurePostgreSqlTable**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op **AzurePostgreSqlTable** | Ja |
| tableName | Naam van de tabel | Nee (als 'query' in activiteitsbron is opgegeven) |

**Voorbeeld**:

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

Zie [Pijplijnen en activiteiten in Azure Data Factory](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door een Azure Database voor PostgreSQL-bron.

### <a name="azure-database-for-postgresql-as-source"></a>Azure Database voor PostgreSql als bron

Als u gegevens uit Azure Database voor PostgreSQL wilt kopiëren, stelt u het brontype in de kopieeractiviteit in op **AzurePostgreSqlSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de kopieeractiviteitsbron moet zijn ingesteld op **AzurePostgreSqlSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"` | Nee (als de eigenschap tableName in de gegevensset is opgegeven) |

**Voorbeeld**:

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

### <a name="azure-database-for-postgresql-as-sink"></a>Azure Database voor PostgreSQL als sink

Als u gegevens wilt kopiëren naar Azure Database voor PostgreSQL, worden de volgende eigenschappen ondersteund in de sectie **logboeken voor kopieeractiviteit:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de inhoudsgroep voor kopiëren moet worden ingesteld op **AzurePostgreSQLSink**. | Ja |
| preCopyScript | Geef een SQL-query op voor de kopieeractiviteit die moet worden uitgevoerd voordat u in elke uitvoering gegevens in Azure Database voor PostgreSQL schrijft. U deze eigenschap gebruiken om de vooraf geladen gegevens op te schonen. | Nee |
| writeBatchSize | Hiermee voegt u gegevens in de Azure Database voor PostgreSQL-tabel in wanneer de buffergrootte writeBatchSize bereikt.<br>Toegestane waarde is een geheel getal dat het aantal rijen vertegenwoordigt. | Nee (standaard is 10.000) |
| writeBatchTimeout | Wacht de tijd voordat de batchinvoegbewerking is voltooid voordat deze een time-out heeft.<br>Toegestane waarden zijn Tijdspannetekenreeksen. Een voorbeeld is 00:30:00 (30 minuten). | Nee (standaard is 00:00:30) |

**Voorbeeld**:

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

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Zie [Opzoekactiviteit in Azure Data Factory](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen
Zie [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
