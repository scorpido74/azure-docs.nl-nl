---
title: Gegevens kopiëren naar zoek index met behulp van Azure Data Factory | Microsoft Docs
description: Meer informatie over het pushen of kopiëren van gegevens naar een Azure search-index met behulp van de Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: jingwang
ms.openlocfilehash: 703dcb5fee0c31328eee91c9673e62653d7aa698
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286336"
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Gegevens kopiëren naar een Azure Search-index met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1](v1/data-factory-azure-search-connector.md)
> * [Huidige versie](connector-azure-search.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren naar Azure Search index. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U kunt gegevens uit elk ondersteund brongegevens archief kopiëren naar Azure Search index. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Azure Search-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor Azure Search gekoppelde service:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **AzureSearch** | Ja |
| url | De URL voor de Azure Search-service. | Ja |
| sleutel | De beheerders sleutel voor de Azure Search-service. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |Nee |

> [!IMPORTANT]
> Wanneer u gegevens uit een gegevens archief in de Cloud naar Azure Search index kopieert, moet u in Azure Search gekoppelde service een Azure Integration Runtime met een expliciete regio in connactVia. Stel de regio in als uw Azure Search zich bevindt. Meer informatie vindt u in [Azure Integration runtime](concepts-integration-runtime.md#azure-integration-runtime).

**Voorbeeld:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die door Azure Search DataSet worden ondersteund.

De volgende eigenschappen worden ondersteund voor het kopiëren van gegevens naar Azure Search:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **AzureSearchIndex** | Ja |
| indexName | De naam van de Azure Search index. Data Factory maakt de index niet. De index moet bestaan in Azure Search. | Ja |

**Voorbeeld:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "typeProperties" : {
            "indexName": "products"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die door Azure Search bron worden ondersteund.

### <a name="azure-search-as-sink"></a>Azure Search als Sink

Als u gegevens wilt kopiëren naar Azure Search, stelt u het bron type in de Kopieer activiteit in op **AzureSearchIndexSink**. De volgende eigenschappen worden ondersteund in het gedeelte **sink** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **AzureSearchIndexSink** | Ja |
| WriteBehavior | Hiermee geeft u op of u wilt samen voegen of vervangen wanneer een document al aanwezig is in de index. Zie de [eigenschap WriteBehavior](#writebehavior-property).<br/><br/>Toegestane waarden zijn: **samen voegen** (standaard) en **uploaden**. | Nee |
| writeBatchSize | Hiermee worden gegevens geüpload naar de Azure Search-index wanneer de buffer grootte writeBatchSize bereikt. Zie de [eigenschap WriteBatchSize](#writebatchsize-property) voor meer informatie.<br/><br/>Toegestane waarden zijn: integer 1 tot 1.000; de standaard waarde is 1000. | Nee |

### <a name="writebehavior-property"></a>Eigenschap WriteBehavior

AzureSearchSink upsert bij het schrijven van gegevens. Met andere woorden, wanneer u een document schrijft en de document sleutel al aanwezig is in de index van de Azure Search, Azure Search het bestaande document bijwerken in plaats van een conflict uitzondering uit te voeren.

De AzureSearchSink biedt de volgende twee upsert-gedragingen (met behulp van AzureSearch SDK):

- **Samen voegen**: alle kolommen in het nieuwe document combi neren met de bestaande. Voor kolommen met een null-waarde in het nieuwe document, blijft de waarde in de bestaande.
- **Uploaden**: het nieuwe document vervangt de bestaande. Voor kolommen die niet in het nieuwe document zijn opgegeven, wordt de waarde ingesteld op NULL, ongeacht of er een andere waarde dan Null is in het bestaande document of niet.

Het standaard gedrag is **samen voegen**.

### <a name="writebatchsize-property"></a>Eigenschap WriteBatchSize

Azure Search-service ondersteunt het schrijven van documenten als een batch. Een batch kan 1 tot 1.000 acties bevatten. Een actie behandelt één document om de upload/samenvoeg bewerking uit te voeren.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

## <a name="data-type-support"></a>Ondersteuning voor gegevens typen

In de volgende tabel wordt aangegeven of een Azure Search gegevens type wordt ondersteund.

| Azure Search gegevens type | Ondersteund in Azure Search Sink |
| ---------------------- | ------------------------------ |
| Tekenreeks | J |
| Int32 | J |
| Int64 | J |
| Dubbelklik | J |
| Booleaans | J |
| DataTimeOffset | J |
| Teken reeks matrix | N |
| GeographyPoint | N |

Momenteel worden andere gegevens typen, zoals complex type, niet ondersteund. Zie [ondersteunde gegevens typen (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)voor een volledige lijst met Azure Search ondersteund gegevens type.

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md##supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
