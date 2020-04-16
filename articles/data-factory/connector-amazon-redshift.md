---
title: Gegevens van Amazon Redshift kopiëren
description: Meer informatie over het kopiëren van gegevens van Amazon Redshift naar ondersteunde sink data stores met Behulp van Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: ce63da745fb84ebccd57b246fc934f595dd7cda1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418249"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Gegevens van Amazon Redshift kopiëren met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-amazon-redshift-connector.md)
> * [Huidige versie](connector-amazon-redshift.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens van een Amazon Redshift te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Amazon Redshift-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens van Amazon Redshift kopiëren naar elk ondersteund sink data store. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Met name deze Amazon Redshift-connector ondersteunt het ophalen van gegevens van Redshift met behulp van query of ingebouwde Redshift UNLOAD-ondersteuning.

> [!TIP]
> Om de beste prestaties te bereiken bij het kopiëren van grote hoeveelheden gegevens van Redshift, u overwegen de ingebouwde Redshift UNLOAD via Amazon S3 te gebruiken. Zie UNLOAD gebruiken om gegevens van de sectie [Amazon Redshift te kopiëren](#use-unload-to-copy-data-from-amazon-redshift) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Als u gegevens kopieert naar een on-premises gegevensarchief met [Self-hosted Integration Runtime,](create-self-hosted-integration-runtime.md)verleent u Integration Runtime (gebruik IP-adres van de machine) de toegang tot het Amazon Redshift-cluster. Zie [Toegang tot het cluster autoriseren](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) voor instructies.
* Zie [Azure Data Center IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653) voor het IP-adres compute en SQL-bereiken die door de Azure-datacenters worden gebruikt als u gegevens kopieert naar een Azure-gegevensarchief.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor amazon Redshift-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor Amazon Redshift gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **AmazonRedshift** | Ja |
| server |IP-adres of hostnaam van de Amazon Redshift-server. |Ja |
| poort |Het nummer van de TCP-poort dat de Amazon Redshift-server gebruikt om naar clientverbindingen te luisteren. |Nee, standaard is 5439 |
| database |Naam van de Amazon Redshift database. |Ja |
| gebruikersnaam |Naam van de gebruiker die toegang heeft tot de database. |Ja |
| wachtwoord |Wachtwoord voor het gebruikersaccount. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) |Ja |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. U Azure Integration Runtime of Self-hosted Integration Runtime gebruiken (als uw gegevensarchief zich in een privénetwerk bevindt). Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

**Voorbeeld:**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "<server name>",
            "database": "<database name>",
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

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Amazon Redshift-gegevensset.

Als u gegevens van Amazon Redshift wilt kopiëren, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **AmazonRedshiftTable** | Ja |
| schema | Naam van het schema. |Nee (als 'query' in activiteitsbron is opgegeven)  |
| tabel | Naam van de tabel. |Nee (als 'query' in activiteitsbron is opgegeven)  |
| tableName | Naam van de tabel met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` `table` en voor nieuwe werkbelasting. | Nee (als 'query' in activiteitsbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "AmazonRedshiftTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Als u `RelationalTable` getypte gegevensset gebruikt, wordt deze nog steeds ondersteund als deze is, terwijl u wordt voorgesteld om de nieuwe in de toekomst te gebruiken.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door amazon Redshift-bron.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift als bron

Als u gegevens van Amazon Redshift wilt kopiëren, stelt u het brontype in de kopieeractiviteit in op **AmazonRedshiftSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op: **AmazonRedshiftSource** | Ja |
| query |Gebruik de aangepaste query om gegevens te lezen. Selecteer bijvoorbeeld * in MyTable. |Nee (als 'tabelNaam' in de gegevensset is opgegeven) |
| instellingen voor roodschakelen | Eigenschappengroep bij het gebruik van Amazon Redshift UNLOAD. | Nee |
| s3LinkedServiceName | Verwijst naar een Amazon S3 te gebruiken als een tussentijdse winkel door het opgeven van een gekoppelde servicenaam van "AmazonS3" type. | Ja als u UNLOAD gebruikt |
| bucketName | Geef de S3-bucket aan om de tussentijdse gegevens op te slaan. Als deze niet wordt geleverd, genereert de Data Factory-service deze automatisch.  | Ja als u UNLOAD gebruikt |

**Voorbeeld: Amazon Redshift bron in kopieeractiviteit met UNLOAD**

```json
"source": {
    "type": "AmazonRedshiftSource",
    "query": "<SQL query>",
    "redshiftUnloadSettings": {
        "s3LinkedServiceName": {
            "referenceName": "<Amazon S3 linked service>",
            "type": "LinkedServiceReference"
        },
        "bucketName": "bucketForUnload"
    }
}
```

Meer informatie over het efficiënt kopiëren van gegevens van Amazon Redshift uit de volgende sectie.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>UNLOAD gebruiken om gegevens van Amazon Redshift te kopiëren

[UNLOAD](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) is een mechanisme van Amazon Redshift, dat de resultaten van een query kan uitladen naar een of meer bestanden op Amazon Simple Storage Service (Amazon S3). Het is de manier die door Amazon voor het kopiëren van grote gegevensreeks van Redshift wordt geadviseerd.

**Voorbeeld: kopieer gegevens van Amazon Redshift naar Azure SQL Data Warehouse met UNLOAD, gefaseerde kopie en PolyBase**

Voor deze voorbeeldgebruikscase worden gegevens van Amazon Redshift naar Amazon S3 gelost zoals geconfigureerd in 'redshiftUnloadSettings' en worden gegevens van Amazon S3 naar Azure Blob gekopieerd, zoals gespecificeerd in 'stagingSettings', ten slotte gebruikt u PolyBase om gegevens in SQL Data Warehouse te laden. Alle tussentijdse nota wordt behandeld door kopie activiteit goed.

![Werktu shift naar SQL DW-kopieerwerk](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

```json
"activities":[
    {
        "name": "CopyFromAmazonRedshiftToSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "AmazonRedshiftDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRedshiftSource",
                "query": "select * from MyTable",
                "redshiftUnloadSettings": {
                    "s3LinkedServiceName": {
                        "referenceName": "AmazonS3LinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "bucketName": "bucketForUnload"
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "AzureStorageLinkedService",
                "path": "adfstagingcopydata"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Gegevenstypetoewijzing voor Amazon Redshift

Bij het kopiëren van gegevens van Amazon Redshift worden de volgende toewijzingen gebruikt van Amazon Redshift-gegevenstypen naar tijdelijke gegevenstypen van Azure Data Factory. Zie [Schema- en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe kopieeractiviteit het bronschema en het gegevenstype naar de gootsteen brengt.

| Amazon Redshift-gegevenstype | Tussentijds gegevenstype gegevensfabriek |
|:--- |:--- |
| Bigint |Int64 |
| Booleaanse |Tekenreeks |
| Char |Tekenreeks |
| DATE |DateTime |
| Decimaal |Decimal |
| DUBBELE PRECISIE |Double |
| INTEGER |Int32 |
| REAL |Enkel |
| Smallint |Int16 |
| TEXT |Tekenreeks |
| Tijdstempel |DateTime |
| Varchar |Tekenreeks |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
