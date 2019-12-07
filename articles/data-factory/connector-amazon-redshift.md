---
title: Gegevens kopiëren van Amazon RedShift
description: Meer informatie over het kopiëren van gegevens van Amazon Redshift naar ondersteunde Sink-gegevens archieven met behulp van Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: jingwang
ms.openlocfilehash: 8cb604996f6cde5c55098fd3fdc51d0d061dbcd1
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74891215"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Gegevens kopiëren van Amazon Redshift met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1](v1/data-factory-amazon-redshift-connector.md)
> * [Huidige versie](connector-amazon-redshift.md)


In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van een Amazon Redshift te kopiëren. Dit is gebaseerd op de [overzicht kopieeractiviteit](copy-activity-overview.md) artikel met daarin een algemeen overzicht van de kopieeractiviteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Amazon Redshift-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van Amazon Redshift kopiëren naar elk ondersteund Sink-gegevens archief. Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen/put door de kopieeractiviteit, de [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats) tabel.

Met name deze Amazon Redshift-connector biedt ondersteuning voor het ophalen van gegevens van RedShift met behulp van query of ingebouwde Redshift Unload-ondersteuning.

> [!TIP]
> Voor de beste prestaties bij het kopiëren van grote hoeveel heden gegevens uit Redshift, kunt u overwegen de ingebouwde Redshift te verwijderen via Amazon S3. Zie [Unload gebruiken om gegevens te kopiëren uit het gedeelte Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Als u gegevens kopieert naar een on-premises gegevens opslag met behulp van [zelf-hostende Integration runtime](create-self-hosted-integration-runtime.md), verleent u Integration runtime (gebruik IP-adres van de machine) de toegang tot het Amazon Redshift-cluster. Zie [toegang tot het cluster machtigen](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) voor instructies.
* Als u gegevens naar een Azure-gegevens archief kopieert, raadpleegt u [Azure Data Center IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653) voor het IP-adres van de berekening en SQL-bereiken die worden gebruikt door de Azure-data centers.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor de Amazon Redshift-connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor de gekoppelde service van Amazon redshift:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **AmazonRedshift** | Ja |
| server |Het IP-adres of de hostnaam van de Amazon Redshift-server. |Ja |
| poort |Het nummer van de TCP-poort die de Amazon Redshift-server gebruikt om te Luis teren naar client verbindingen. |Nee, standaard waarde is 5439 |
| database |De naam van de Amazon Redshift-data base. |Ja |
| gebruikersnaam |De naam van de gebruiker die toegang heeft tot de data base. |Ja |
| wachtwoord |Het wacht woord voor het gebruikers account. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory, of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | De [Integration Runtime](concepts-integration-runtime.md) moet worden gebruikt verbinding maken met het gegevensarchief. U kunt Azure Integration Runtime of zelfgehoste Cloudintegratieruntime gebruiken (als het gegevensarchief bevindt zich in een particulier netwerk). Als niet is opgegeven, wordt de standaard Azure Integration Runtime. |Nee |

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

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Amazon Redshift-gegevensset.

De volgende eigenschappen worden ondersteund voor het kopiëren van gegevens uit Amazon redshift:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **AmazonRedshiftTable** | Ja |
| schema | De naam van het schema. |Nee (als 'query' in de activiteitbron is opgegeven)  |
| table | Naam van de tabel. |Nee (als 'query' in de activiteitbron is opgegeven)  |
| tableName | De naam van de tabel met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` en `table` voor nieuwe workloads. | Nee (als 'query' in de activiteitbron is opgegeven) |

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

Als u `RelationalTable` getypte gegevensset gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Amazon Redshift-bron.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift als bron

Als u gegevens wilt kopiëren uit Amazon Redshift, stelt u het bron type in de Kopieer activiteit in op **AmazonRedshiftSource**. De volgende eigenschappen worden ondersteund in de kopieeractiviteit **source** sectie:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **AmazonRedshiftSource** | Ja |
| query |Gebruik de aangepaste query om gegevens te lezen. Bijvoorbeeld: Select * from MyTable. |Nee (als de 'tableName' in de gegevensset is opgegeven) |
| redshiftUnloadSettings | Eigenschaps groep bij het gebruik van Amazon Redshift Unload. | Nee |
| s3LinkedServiceName | Verwijst naar een Amazon S3 dat wordt gebruikt als een tijdelijke opslag door een gekoppelde service naam van het type ' AmazonS3 ' op te geven. | Ja bij gebruik van Unload |
| bucketName | Geef de S3-Bucket op om de tussenliggende gegevens op te slaan. Als u dit niet opgeeft, wordt het automatisch door Data Factory Service gegenereerd.  | Ja bij gebruik van Unload |

**Voor beeld: Amazon Redshift source in copy-activiteit met Unload**

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

Meer informatie over het gebruik van Unload om gegevens te kopiëren van Amazon Redshift efficiënt vanuit de volgende sectie.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>VERWIJDEREN gebruiken om gegevens van Amazon Redshift te kopiëren

[Unload](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) is een mechanisme dat door Amazon Redshift wordt geleverd, waarmee de resultaten van een query kunnen worden verwijderd naar een of meer bestanden in de Amazon Simple Storage-service (Amazon S3). Het is de manier die door Amazon wordt aanbevolen voor het kopiëren van een grote gegevensset vanuit Redshift.

**Voor beeld: gegevens kopiëren van Amazon Redshift naar Azure SQL Data Warehouse met behulp van verwijderen, gefaseerde kopie en poly base**

Voor deze voorbeeld use-case verwijdert u met de Kopieer activiteit gegevens uit Amazon Redshift naar Amazon S3, zoals geconfigureerd in "redshiftUnloadSettings", en kopieert u vervolgens gegevens van Amazon S3 naar Azure Blob zoals opgegeven in "stagingSettings", ten slotte poly Base voor het laden van gegevens in SQL-gegevens Uitslag. Alle tussenliggende indeling wordt verwerkt door de Kopieer activiteit op de juiste manier.

![Redshift naar SQL DW Copy-werk stroom](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

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

## <a name="data-type-mapping-for-amazon-redshift"></a>Toewijzing van gegevens type voor Amazon RedShift

Bij het kopiëren van gegevens uit Amazon Redshift worden de volgende toewijzingen gebruikt vanuit gegevens typen van Amazon Redshift om Azure Data Factory tussenliggende gegevens typen. Zie [Schema en gegevens typt toewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe copy activity in het schema en de gegevens van een brontype aan de sink toegewezen.

| Amazon Redshift-gegevens type | Data factory tussentijdse gegevenstype |
|:--- |:--- |
| BIGINT |Int64 |
| BOOLEAN |Tekenreeks |
| CHAR |Tekenreeks |
| DATE |Datum/tijd |
| DECIMAL |Decimal |
| DOUBLE PRECISION |Double |
| INTEGER |Int32 |
| REAL |Enkelvoudig |
| SMALLINT |Int16 |
| TEXT |Tekenreeks |
| TIMESTAMP |Datum/tijd |
| VARCHAR |Tekenreeks |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md##supported-data-stores-and-formats).
