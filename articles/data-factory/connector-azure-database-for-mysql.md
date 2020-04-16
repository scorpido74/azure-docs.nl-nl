---
title: Gegevens van en naar Azure Database voor MySQL kopiëren
description: Meer informatie over het kopiëren van gegevens van en naar Azure Database voor MySQL met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/25/2019
ms.openlocfilehash: bbb4aed8ca10fcf7c15e7442ee7067b2e3f8087d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410710"
---
# <a name="copy-data-to-and-from-azure-database-for-mysql-using-azure-data-factory"></a>Gegevens van en naar Azure Database voor MySQL kopiëren met Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens uit Azure Database voor MySQL te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

Deze connector is gespecialiseerd voor [Azure Database for MySQL-service.](../mysql/overview.md) Als u gegevens wilt kopiëren uit een algemene MySQL-database die zich on-premises of in de cloud bevindt, gebruikt u [MySQL-connector](connector-mysql.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Database for MySQL-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens uit Azure Database voor MySQL kopiëren naar elk ondersteund sinkdataarchief. U ook gegevens uit elk ondersteund brongegevensarchief kopiëren naar Azure Database voor MySQL. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Azure Data Factory biedt een ingebouwd stuurprogramma om connectiviteit mogelijk te maken, daarom hoeft u geen stuurprogramma handmatig te installeren met deze connector.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor Azure Database voor MySQL-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor Azure Database voor MySQL-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **AzureMySql** | Ja |
| Connectionstring | Geef informatie op die nodig is om verbinding te maken met de Azure Database voor MySQL-instantie. <br/> U ook wachtwoord in Azure `password` Key Vault plaatsen en de configuratie uit de verbindingstekenreeks halen. Raadpleeg de volgende voorbeelden en [store-referenties in het Azure Key Vault-artikel](store-credentials-in-key-vault.md) met meer details. | Ja |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. U Azure Integration Runtime of Self-hosted Integration Runtime gebruiken (als uw gegevensarchief zich in een privénetwerk bevindt). Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

Een typische verbindingstekenreeks is `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Meer eigenschappen die u per aanvraag instellen:

| Eigenschap | Beschrijving | Opties | Vereist |
|:--- |:--- |:--- |:--- |
| SSLMode | Deze optie geeft aan of het stuurprogramma TLS-versleuteling en -verificatie gebruikt bij het maken van verbinding met MySQL. Bijvoorbeeld `SSLMode=<0/1/2/3/4>`| UITGESCHAKELD (0) / VOORKEUR (1) **(Standaard)** / VEREIST (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Nee |
| GebruikSystemTrustStore | Met deze optie geeft u aan of u een CA-certificaat wilt gebruiken in het systeemvertrouwensarchief of uit een opgegeven PEM-bestand. Bijvoorbeeld `UseSystemTrustStore=<0/1>;`| Ingeschakeld (1) / Uitgeschakeld (0) **(standaard)** | Nee |

**Voorbeeld:**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voorbeeld: wachtwoord opslaan in Azure Key Vault**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;",
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

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door Azure Database voor MySQL-gegevensset.

Als u gegevens uit Azure Database voor MySQL wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **AzureMySqlTable**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **AzureMySqlTable** | Ja |
| tableName | Naam van de tabel in de MySQL-database. | Nee (als 'query' in activiteitsbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door Azure Database voor MySQL-bron en -sink.

### <a name="azure-database-for-mysql-as-source"></a>Azure Database voor MySQL als bron

Als u gegevens uit Azure Database voor MySQL wilt kopiëren, worden de volgende eigenschappen ondersteund in de **sectie** bron van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de kopieeractiviteitsbron moet zijn ingesteld op: **AzureMySqlSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als 'tabelNaam' in de gegevensset is opgegeven) |
| queryCommandTime-out | De wachttijd voordat de queryaanvraag een time-out heeft. Standaard is 120 minuten (02:00:00) | Nee |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>Azure Database voor MySQL als sink

Als u gegevens wilt kopiëren naar Azure Database voor MySQL, worden de volgende eigenschappen ondersteund in de sectie **logboeken voor kopieeractiviteit:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de inhoudsgoot voor kopiëren moet worden ingesteld op: **AzureMySqlSink** | Ja |
| preCopyScript | Geef een SQL-query op voor de kopieeractiviteit die moet worden uitgevoerd voordat u in elke uitvoering gegevens in Azure Database voor MySQL schrijft. U deze eigenschap gebruiken om de vooraf geladen gegevens op te schonen. | Nee |
| writeBatchSize | Hiermee voegt u gegevens in de Azure Database voor MySQL-tabel in wanneer de buffergrootte writeBatchSize bereikt.<br>De toegestane waarde is integer en vertegenwoordigt het aantal rijen. | Nee (standaard is 10.000) |
| writeBatchTimeout | Wacht de tijd voordat de batchinvoegbewerking is voltooid voordat deze een time-out heeft.<br>Toegestane waarden zijn Timespan. Een voorbeeld is 00:30:00 (30 minuten). | Nee (standaard is 00:00:30) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Toewijzing van gegevenstype voor Azure Database voor MySQL

Bij het kopiëren van gegevens uit Azure Database voor MySQL worden de volgende toewijzingen gebruikt van MySQL-gegevenstypen naar tijdelijke gegevenstypen van Azure Data Factory. Zie [Schema- en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe kopieeractiviteit het bronschema en het gegevenstype naar de gootsteen brengt.

| Azure Database voor MySQL-gegevenstype | Tussentijds gegevenstype gegevensfabriek |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int32` |

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
