---
title: Gegevens kopiëren van/naar sneeuw
description: Meer informatie over het kopiëren van gegevens van en naar sneeuw met behulp van Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: fd067a0a0e99291dd4cea924a15c1157bc3b9f9f
ms.sourcegitcommit: c052c99fd0ddd1171a08077388d221482026cd58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84425500"
---
# <a name="copy-data-from-and-to-snowflake-by-using-azure-data-factory"></a>Gegevens van en naar sneeuw kopiëren met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van en naar sneeuw te kopiëren. Lees het [artikel Inleiding](introduction.md)voor meer informatie over Azure Data Factory.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze sneeuw connector wordt ondersteund voor de volgende activiteiten:

- De tabel [copy-activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Opzoek activiteit](control-flow-lookup-activity.md)

Deze sneeuw connector ondersteunt de volgende functies voor kopieer activiteiten:

- Kopieer gegevens van sneeuw die gebruikmaken van de kopie van sneeuw in de opdracht [[locatie]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) om de beste prestaties te krijgen.
- Kopieer gegevens naar sneeuw die gebruikmaken van de kopie van sneeuw in de opdracht [[Table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) om de beste prestaties te krijgen. Het ondersteunt sneeuw op Azure.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die Data Factory entiteiten definiëren die specifiek zijn voor een sneeuw-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor een gekoppelde service met sneeuw vlokken:

| Eigenschap         | Beschrijving                                                  | Vereist |
| :--------------- | :----------------------------------------------------------- | :------- |
| type             | De eigenschap type moet worden ingesteld op **sneeuw vlokken**.              | Ja      |
| Verbindings | Configureer de [volledige account naam](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name) (inclusief aanvullende segmenten die de regio en het Cloud platform identificeren), de gebruikers naam, het wacht woord, de data base en het magazijn. Geef de JDBC-connection string op om verbinding te maken met het sneeuw-exemplaar. U kunt ook wacht woord in Azure Key Vault plaatsen. Raadpleeg de voor beelden onder de tabel en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie.| Ja      |
| connectVia       | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt Azure Integration Runtime of een zelf-hostende Integration runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | Nee       |

**Voorbeeld:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&password=<password>&db=<database>&warehouse=<warehouse>(optional)"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Wacht woord in Azure Key Vault:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>(optional)",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                }, 
                "secretName": "<secretName>"
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

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. 

De volgende eigenschappen worden ondersteund voor sneeuw-gegevensset:

| Eigenschap  | Beschrijving                                                  | Vereist                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | De eigenschap type van de DataSet moet worden ingesteld op **SnowflakeTable**. | Ja                         |
| schema | De naam van het schema. |Nee voor bron, ja voor Sink  |
| tabel | De naam van de tabel/weer gave. |Nee voor bron, ja voor Sink  |

**Voorbeeld:**

```json
{
    "name": "SnowflakeDataset",
    "properties": {
        "type": "SnowflakeTable",
        "typeProperties": {
            "schema": "<Schema name for your Snowflake database>",
            "table": "<Table name for your Snowflake database>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van Kopieer activiteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de sneeuw-bron en Sink.

### <a name="snowflake-as-the-source"></a>Sneeuw als bron

Sneeuw connector maakt gebruik van de kopie van het sneeuw punt in de onderstaande opdracht [[locatie]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) om de beste prestaties te krijgen.

* Als de Sink-gegevens opslag en-indeling systeem eigen worden ondersteund door de sneeuw COPY-opdracht, kunt u Kopieer activiteit gebruiken om rechtstreeks te kopiëren van sneeuw naar sink. Zie voor meer informatie [direct kopiëren van sneeuw](#direct-copy-from-snowflake).
* Gebruik anders de ingebouwde, [gefaseerde kopie van sneeuw](#staged-copy-from-snowflake).

Als u gegevens van sneeuw vlokken wilt kopiëren, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap                     | Beschrijving                                                  | Vereist |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **SnowflakeSource**. | Ja      |
| query          | Hiermee geeft u de SQL-query op voor het lezen van gegevens van sneeuw. | Nee       |
| exportSettings | Geavanceerde instellingen voor het ophalen van gegevens uit sneeuw. U kunt de bestanden configureren die worden ondersteund door kopiëren naar de opdracht die door de ADF wordt door gegeven wanneer de instructie wordt aangeroepen. | Nee       |
| ***Onder `exportSettings` :*** |  |  |
| type | Het type export opdracht, ingesteld op **SnowflakeExportCopyCommand**. | Ja |
| additionalCopyOptions | Aanvullende kopieer opties, gegeven als een woorden lijst met sleutel-waardeparen. Voor beelden: MAX_FILE_SIZE, OVERSCHRIJVEN. Meer informatie vindt u in de [Kopieer opties van sneeuw vlokken](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions). | Nee |
| additionalFormatOptions | Aanvullende opties voor de bestands indeling die worden meegeleverd met de opdracht COPY, gegeven als een woorden lijst met sleutel-waardeparen. Voor beelden: DATE_FORMAT, TIME_FORMAT TIMESTAMP_FORMAT. Meer informatie over [Opties voor type sneeuw-indeling](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions). | Nee |

#### <a name="direct-copy-from-snowflake"></a>Directe kopie van sneeuw

Als uw Sink-gegevens archief en-indeling voldoen aan de criteria die in deze sectie zijn beschreven, kunt u de Kopieer activiteit gebruiken om rechtstreeks van sneeuw naar sink te kopiëren. Azure Data Factory controleert de instellingen en mislukt de uitvoering van de Kopieer activiteit als niet aan de criteria wordt voldaan.

1. De aan **sink gekoppelde service** is een [**Azure Blob**](connector-azure-blob-storage.md) -type met verificatie van de **Shared Access-hand tekening** .

2. De **sink-gegevens indeling** is **Parquet** of **gescheiden tekst**, met de volgende configuraties:

   - Voor de indeling **Parquet** is de compressie-codec **geen**, **Snappy**of **lzo**.
   - Voor **tekst indeling met scheidings tekens** :
     - `rowDelimiter`is **\r\n**of een wille keurig teken.
     - `compression`kan **geen compressie**, **gzip**, **bzip2**of **Deflate**zijn.
     - `encodingName`is standaard ingesteld op **UTF-8**.
     - `quoteChar`is **dubbele aanhalings**tekens, **enkele aanhalings** tekens of **lege teken reeks** (geen aanhalings teken).
3. De bron van de Kopieer activiteit `additionalColumns` is niet opgegeven.
4. Er is geen kolom toewijzing opgegeven.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable",
                "exportSettings": {
                    "type": "SnowflakeExportCopyCommand",
                    "additionalCopyOptions": {
                        "MAX_FILE_SIZE": "64000000",
                        "OVERWRITE": true
                    },
                    "additionalFormatOptions": {
                        "DATE_FORMAT": "'MM/DD/YYYY'"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-snowflake"></a>Gefaseerde kopie van sneeuw

Als uw Sink-gegevens archief of-indeling niet systeem eigen compatibel is met de opdracht sneeuw COPY, zoals vermeld in de laatste sectie, schakelt u de ingebouwde gefaseerde kopie in via een tussentijds Azure Blob Storage-exemplaar. De functie voor gefaseerd kopiëren biedt u ook een betere door Voer Data Factory het exporteren van gegevens van sneeuw naar staging-opslag, en het kopiëren van gegevens naar sink, ten slotte het opschonen van uw tijdelijke gegevens uit de faserings opslag. Zie [gefaseerde kopie](copy-activity-performance-features.md#staged-copy) voor informatie over het kopiëren van gegevens via een fase ring.

Als u deze functie wilt gebruiken, maakt u een [gekoppelde azure Blob Storage-service](connector-azure-blob-storage.md#linked-service-properties) die verwijst naar het Azure Storage-account als tijdelijke fase ring. Geef vervolgens de `enableStaging` `stagingSettings` Eigenschappen en op in Kopieer activiteit.

> [!NOTE]
>
> De gekoppelde Azure Blob-service voor de fase ring moet gebruikmaken van verificatie van de hand tekening voor gedeelde toegang zoals vereist is door de opdracht voor sneeuw kopiëren. 

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="snowflake-as-sink"></a>Sneeuw vlokken als Sink

Sneeuw connector maakt gebruik van de [kopie van sneeuw in [Table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) om de beste prestaties te krijgen. Het biedt ondersteuning voor het schrijven van gegevens naar sneeuw vlokken op Azure.

* Als het brongegevens archief en de indeling systeem eigen worden ondersteund door de sneeuw COPY-opdracht, kunt u Kopieer activiteit gebruiken om rechtstreeks te kopiëren van bron naar sneeuw vlokken. Zie [direct kopiëren naar sneeuw vlokken](#direct-copy-to-snowflake)voor meer informatie.
* Gebruik anders de ingebouwde [gefaseerde kopie naar sneeuw vlokken](#staged-copy-to-snowflake).

Als u gegevens wilt kopiëren naar sneeuw vlokken, worden de volgende eigenschappen ondersteund in het gedeelte **sink** van Kopieer activiteit:

| Eigenschap          | Beschrijving                                                  | Vereist                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op **SnowflakeSink**. | Ja                                           |
| preCopyScript     | Geef een SQL-query op voor het uitvoeren van de Kopieer activiteit voordat u gegevens naar sneeuw schrijft in elke uitvoering. Gebruik deze eigenschap om de vooraf geladen gegevens op te schonen. | Nee                                            |
| importSettings | *Geavanceerde instellingen voor het schrijven van gegevens naar sneeuw. U kunt de bestanden configureren die worden ondersteund door kopiëren naar de opdracht die door de ADF wordt door gegeven wanneer de instructie wordt aangeroepen.* | *Nee* |
| ***Onder `importSettings` :*** |                                                              |  |
| type | Het type van de opdracht importeren, ingesteld op **SnowflakeImportCopyCommand**. | Ja |
| additionalCopyOptions | Aanvullende kopieer opties, gegeven als een woorden lijst met sleutel-waardeparen. Voor beelden: ON_ERROR, FORCEren, LOAD_UNCERTAIN_FILES. Meer informatie vindt u in de [Kopieer opties van sneeuw vlokken](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions). | Nee |
| additionalFormatOptions | Aanvullende opties voor de bestands indeling die worden meegeleverd met de Kopieer opdracht, als een woorden lijst met sleutel-waardeparen. Voor beelden: DATE_FORMAT, TIME_FORMAT TIMESTAMP_FORMAT. Meer informatie over [Opties voor type sneeuw-indeling](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions). | Nee |

#### <a name="direct-copy-to-snowflake"></a>Directe kopie naar sneeuw vlokken

Als uw brongegevens opslag en-indeling voldoen aan de criteria die in deze sectie zijn beschreven, kunt u de Kopieer activiteit gebruiken om rechtstreeks te kopiëren van bron naar sneeuw vlokken. Azure Data Factory controleert de instellingen en mislukt de uitvoering van de Kopieer activiteit als niet aan de criteria wordt voldaan.

1. De **gekoppelde bron service** is het type [**Azure-Blob**](connector-azure-blob-storage.md) met verificatie van de **Shared Access-hand tekening** .

2. De **indeling van de bron gegevens** is **Parquet** of **gescheiden tekst** met de volgende configuraties:

   - Voor de indeling **Parquet** is de compressie-codec **geen**of **Snappy**.

   - Voor **tekst indeling met scheidings tekens** :
     - `rowDelimiter`is **\r\n**of een wille keurig teken. Als het scheidings teken van de rij niet ' \r\n ' is, `firstRowAsHeader` moet **Onwaar**zijn en `skipLineCount` niet opgegeven.
     - `compression`kan **geen compressie**, **gzip**, **bzip2**of **Deflate**zijn.
     - `encodingName`is standaard ingesteld op ' UTF-8 ', ' UTF-16 ', ' UTF-16BE ', ' UTF-32 ', ' UTF-32BE ', ' BIG5 ', ' EUC-JP ', ' EUC-KR ', ' GB18030 ', ' ISO-2022-JP ', ' ISO-2022-KR ', ' ISO-8859-1 "," ISO-8859-2 "," ISO-8859-5 "," ISO-8859-6 "," ISO-8859-7 "," ISO-8859-8 "," ISO-8859-9 "," WINDOWS-1250 "," WINDOWS-1251 "," WINDOWS-1252 "," WINDOWS-1253 "," WINDOWS-1254 "," WINDOWS-1255 ".
     - `quoteChar`is **dubbele aanhalings**tekens, **enkele aanhalings** tekens of **lege teken reeks** (geen aanhalings teken).

3. In bron van Kopieer activiteit, 

   -  `additionalColumns`is niet opgegeven.
   - Als uw bron een map is, `recursive` moet deze zijn ingesteld op waar.
   - `prefix`, `modifiedDateTimeStart` , `modifiedDateTimeEnd` zijn niet opgegeven.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink",
                "importSettings": {
                    "type": "SnowflakeImportCopyCommand",
                    "copyOptions": {
                        "FORCE": "TRUE",
                        "ON_ERROR": "SKIP_FILE",
                    },
                    "fileFormatOptions": {
                        "DATE_FORMAT": "YYYY-MM-DD",
                    }
                }
            }
        }
    }
]
```

#### <a name="staged-copy-to-snowflake"></a>Gefaseerde kopie naar sneeuw vlokken

Als uw Sink-gegevens archief of-indeling niet systeem eigen compatibel is met de opdracht sneeuw COPY, zoals vermeld in de laatste sectie, schakelt u de ingebouwde gefaseerde kopie in via een tussentijds Azure Blob Storage-exemplaar. Met de functie voor gefaseerd kopiëren kunt u ook een betere door Voer Data Factory de gegevens automatisch converteren om te voldoen aan de vereisten voor gegevens formaat van sneeuw vlokken. Vervolgens wordt de Kopieer opdracht voor het laden van gegevens in sneeuw geactiveerd. Ten slotte ruimt de app de tijdelijke gegevens op uit de Blob-opslag. Zie [gefaseerde kopie](copy-activity-performance-features.md#staged-copy) voor informatie over het kopiëren van gegevens via een fase ring.

Als u deze functie wilt gebruiken, maakt u een [gekoppelde azure Blob Storage-service](connector-azure-blob-storage.md#linked-service-properties) die verwijst naar het Azure Storage-account als tijdelijke fase ring. Geef vervolgens de `enableStaging` `stagingSettings` Eigenschappen en op in Kopieer activiteit.

> [!NOTE]
>
> De gekoppelde Azure Blob-service voor de fase ring moet gebruikmaken van verificatie van de hand tekening voor gedeelde toegang zoals vereist is door de opdracht voor sneeuw kopiëren.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```


## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
