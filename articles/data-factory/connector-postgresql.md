---
title: Gegevens kopiëren van PostgreSQL met behulp van Azure Data Factory | Microsoft Docs
description: Meer informatie over het kopiëren van gegevens uit PostgreSQL naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
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
ms.openlocfilehash: 84a82e5fae7c56a13aeb4603079e9378b38785cb
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277564"
---
# <a name="copy-data-from-postgresql-by-using-azure-data-factory"></a>Gegevens kopiëren van PostgreSQL met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-onprem-postgresql-connector.md)
> * [Huidige versie](connector-postgresql.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een PostgreSQL-data base te kopiëren. Dit is gebaseerd op de [overzicht kopieeractiviteit](copy-activity-overview.md) artikel met daarin een algemeen overzicht van de kopieeractiviteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U kunt gegevens uit de PostgreSQL-data base kopiëren naar elk ondersteund Sink-gegevens archief. Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen/put door de kopieeractiviteit, de [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats) tabel.

Met name deze PostgreSQL-connector ondersteunt PostgreSQL **versie 7,4 en hoger**.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Voor een zelf-hostende IR-versie lager dan 3,7, moet u de [Ngpsql-gegevens provider voor postgresql](https://go.microsoft.com/fwlink/?linkid=282716) met de versie tussen 2.0.12 en 3.1.9 op de Integration runtime machine installeren.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten die specifiek zijn voor PostgreSQL-connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor PostgreSQL gekoppelde service:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type moet worden ingesteld op: **PostgreSql** | Ja |
| connectionString | Een ODBC-verbindingsreeks verbinding maakt met Azure Database voor PostgreSQL. <br/>Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory. U kunt ook wacht woord in azure Key Vault plaatsen en de `password` configuratie uit de Connection String halen. Raadpleeg de volgende voor beelden en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie. | Ja |
| connectVia | De [Integration Runtime](concepts-integration-runtime.md) moet worden gebruikt verbinding maken met het gegevensarchief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als niet is opgegeven, wordt de standaard Azure Integration Runtime. |Nee |

Een gebruikelijke verbindingsreeks is `Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Meer eigenschappen die u per uw situatie instellen kunt:

| Eigenschap | Description | Opties | Vereist |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| De methode voor het stuurprogramma wordt gebruikt voor het versleutelen van gegevens die worden verzonden tussen het stuurprogramma en de database-server. Bijvoorbeeld `EncryptionMethod=<0/1/6>;`| 0 (geen versleuteling) **(standaard)** / 1 (SSL) / 6 (RequestSSL) | Nee |
| ValidateServerCertificate (VSC) | Hiermee bepaalt u of het certificaat dat door de database-server wordt verzonden wanneer de SSL-versleuteling is ingeschakeld wordt gevalideerd door het stuurprogramma (versleutelingsmethode = 1). Bijvoorbeeld `ValidateServerCertificate=<0/1>;`| 0 (uitgeschakeld) **(standaard)** / 1 (ingeschakeld) | Nee |

**Voorbeeld:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld: wacht woord opslaan in Azure Key Vault**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Als u PostgreSQL gekoppelde service met de volgende Payload gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

**Vorige nettolading:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "username": "<username>",
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

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de PostgreSQL-gegevensset.

De volgende eigenschappen worden ondersteund voor het kopiëren van gegevens uit PostgreSQL:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van de gegevensset moet worden ingesteld op: **PostgreSqlTable** | Ja |
| schema | De naam van het schema. |Nee (als 'query' in de activiteitbron is opgegeven)  |
| table | Naam van de tabel. |Nee (als 'query' in de activiteitbron is opgegeven)  |
| tableName | De naam van de tabel met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` en`table` voor nieuwe werk belasting. | Nee (als 'query' in de activiteitbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "PostgreSQLDataset",
    "properties":
    {
        "type": "PostgreSqlTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<PostgreSQL linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Als u getypte gegevensset gebruikt `RelationalTable` , wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. Deze sectie bevat een lijst met eigenschappen die door PostgreSQL-bron worden ondersteund.

### <a name="postgresql-as-source"></a>PostgreSQL als bron

Als u gegevens wilt kopiëren uit PostgreSQL, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van de bron van de Kopieer activiteit moet worden ingesteld op: **PostgreSqlSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"query": "SELECT * FROM \"MySchema\".\"MyTable\""`. | Nee (als de 'tableName' in de gegevensset is opgegeven) |

> [!NOTE]
> Schema-en tabel namen zijn hoofdletter gevoelig. Plaats deze in `""` (dubbele aanhalings tekens) in de query.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<PostgreSQL input dataset name>",
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
                "type": "PostgreSqlSource",
                "query": "SELECT * FROM \"MySchema\".\"MyTable\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Als u getypte bron gebruikt `RelationalSource` , wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md##supported-data-stores-and-formats).
