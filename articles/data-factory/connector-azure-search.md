---
title: Gegevens kopiëren naar zoekindex
description: Meer informatie over het pushen of kopiëren van gegevens naar een Azure-zoekindex met behulp van de pijplijn Activiteit kopiëren in een Azure Data Factory-pijplijn.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/13/2019
ms.openlocfilehash: 418026d5569cd7e4a7c5239f99650833b1b9514d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75892943"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Gegevens kopiëren naar een Azure Cognitive Search-index met Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-azure-search-connector.md)
> * [Huidige versie](connector-azure-search.md)

In dit artikel wordt beschreven hoe u activiteit kopiëren in Azure Data Factory gebruikt om gegevens te kopiëren naar azure cognitive search-index. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U gegevens uit elk ondersteund brongegevensarchief kopiëren naar zoekindex. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor azure cognitive search-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor azure cognitive search-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **AzureSearch** | Ja |
| url | URL voor de zoekservice. | Ja |
| sleutel | Beheerderssleutel voor de zoekservice. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. U Azure Integration Runtime of Self-hosted Integration Runtime gebruiken (als uw gegevensarchief zich in een privénetwerk bevindt). Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

> [!IMPORTANT]
> Wanneer u gegevens uit een cloudgegevensarchief kopieert naar zoekindex, moet u in azure cognitive search-gekoppelde service een Azure Integration Runtime doorverwijzen met een expliciete regio in connactVia. Stel de regio in als de regio waar uw zoekservice zich bevindt. Meer informatie over [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

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

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de gegevensset Azure Cognitive Search.

Als u gegevens wilt kopiëren naar Azure Cognitive Search, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **AzureSearchIndex** | Ja |
| indexNaam | Naam van de zoekindex. Data Factory maakt de index niet. De index moet bestaan in Azure Cognitive Search. | Ja |

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
            "referenceName": "<Azure Cognitive Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door azure cognitive search-bron.

### <a name="azure-cognitive-search-as-sink"></a>Azure Cognitive Search als gootsteen

Als u gegevens wilt kopiëren naar Azure Cognitive Search, stelt u het brontype in de kopieeractiviteit in op **AzureSearchIndexSink**. De volgende eigenschappen worden ondersteund in de sectie copy activity **sink:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van kopieeractiviteit moet zijn ingesteld op: **AzureSearchIndexSink** | Ja |
| writeBehavior | Hiermee geeft u op of een document al in de index moet worden samengevoegd of vervangen. Zie de [eigenschap Schrijfgedrag](#writebehavior-property).<br/><br/>Toegestane waarden zijn: **Samenvoegen** (standaard) en **Uploaden**. | Nee |
| writeBatchSize | Uploadt gegevens in de zoekindex wanneer de buffergrootte writeBatchSize bereikt. Zie de [eigenschap WriteBatchSize](#writebatchsize-property) voor meer informatie.<br/><br/>Toegestane waarden zijn: gehele getal 1 tot 1.000; standaard is 1000. | Nee |

### <a name="writebehavior-property"></a>Schrijfgedrag, eigenschap

AzureSearchSink upserts bij het schrijven van gegevens. Met andere woorden, wanneer u een document schrijft, als de documentsleutel al bestaat in de zoekindex, werkt Azure Cognitive Search het bestaande document bij in plaats van een conflictuitzondering te plaatsen.

De AzureSearchSink biedt de volgende twee upsert gedragingen (met behulp van AzureSearch SDK):

- **Samenvoegen:** combineer alle kolommen in het nieuwe document met het bestaande document. Voor kolommen met null-waarde in het nieuwe document blijft de waarde in het bestaande document behouden.
- **Uploaden:** het nieuwe document vervangt het bestaande document. Voor kolommen die niet in het nieuwe document zijn opgegeven, wordt de waarde ingesteld op null of er een niet-null-waarde in het bestaande document is of niet.

Het standaardgedrag is **Samenvoegen**.

### <a name="writebatchsize-property"></a>WriteBatchSize , eigenschap

Azure Cognitive Search-service ondersteunt het schrijven van documenten als een batch. Een batch kan 1 tot 1.000 acties bevatten. Een actie verwerkt één document om de upload-/samenvoegbewerking uit te voeren.

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
                "referenceName": "<Azure Cognitive Search output dataset name>",
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

## <a name="data-type-support"></a>Ondersteuning voor gegevenstype

In de volgende tabel wordt aangegeven of een Azure Cognitive Search-gegevenstype al dan niet wordt ondersteund.

| Gegevenstype Azure Cognitive Search | Ondersteund in Azure Cognitive Search Sink |
| ---------------------- | ------------------------------ |
| Tekenreeks | J |
| Int32 | J |
| Int64 | J |
| Double | J |
| Booleaans | J |
| DataTimeOffset | J |
| Tekenreeksarray | N |
| AardrijkskundePoint | N |

Momenteel worden andere gegevenstypen, zoals ComplexType, niet ondersteund. Zie [Ondersteunde gegevenstypen (Azure Cognitive Search)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)voor een volledige lijst met door Azure Cognitive Search ondersteunde gegevenstypen.

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
