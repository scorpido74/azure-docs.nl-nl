---
title: Gegevens kopiëren van/naar SAP Cloud voor de klant
description: Meer informatie over het kopiëren van gegevens van SAP Cloud for Customer naar ondersteunde sink data stores (of) van ondersteunde brongegevensopslag naar SAP Cloud for Customer met behulp van Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: 0b0352632491dbfc46ed4819653c6cb902eee4ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74923724"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Gegevens uit SAP Cloud for Customer (C4C) kopiëren met Azure Data Factory

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens van/naar SAP Cloud for Customer (C4C) te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

>[!TIP]
>Zie [SAP-gegevensintegratie met azure data factory-whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) met gedetailleerde introductie, vergelijking en richtlijnen voor de algemene ondersteuning van ADF voor de algemene ondersteuning van sap-gegevensintegratie.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze SAP Cloud for Customer-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens van SAP Cloud for Customer kopiëren naar een ondersteund sink datastore of gegevens uit een ondersteunde brongegevenswinkel kopiëren naar SAP Cloud for Customer. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Met name deze connector stelt Azure Data Factory in staat om gegevens van/naar SAP Cloud voor de klant te kopiëren, waaronder de SAP Cloud for Sales, SAP Cloud for Service en SAP Cloud for Social Engagement-oplossingen.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor de SAP Cloud for Customer-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor SAP Cloud for Customer linked service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **SapCloudForCustomer**. | Ja |
| url | De URL van de SAP C4C OData-service. | Ja |
| gebruikersnaam | Geef de gebruikersnaam op om verbinding te maken met de SAP C4C. | Ja |
| wachtwoord | Geef het wachtwoord op voor het gebruikersaccount dat u hebt opgegeven voor de gebruikersnaam. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. | Nee voor bron, Ja voor gootsteen |

>[!IMPORTANT]
>Als u gegevens wilt kopiëren naar SAP Cloud for Customer, maakt u expliciet [een Azure IR](create-azure-integration-runtime.md#create-azure-ir) met een locatie in de buurt van uw SAP-cloud voor de klant en associeert u in de gekoppelde service als het volgende voorbeeld:

**Voorbeeld:**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
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

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de gegevensset SAP Cloud for Customer.

Als u gegevens wilt kopiëren van SAP Cloud voor klant, stelt u de eigenschap type van de gegevensset in op **SapCloudForCustomerResource.** De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **SapCloudForCustomerResource** |Ja |
| path | Geef het pad op van de SAP C4C OData-entiteit. |Ja |

**Voorbeeld:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door SAP Cloud voor klantbron.

### <a name="sap-c4c-as-source"></a>SAP C4C als bron

Als u gegevens van SAP Cloud voor klant wilt kopiëren, stelt u het brontype in de kopieeractiviteit in op **SapCloudForCustomerSource.** De volgende eigenschappen worden ondersteund in de sectie **bron** van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **SapCloudForCustomerSource**  | Ja |
| query | Geef de aangepaste OData-query op om gegevens te lezen. | Nee |

Voorbeeldquery om gegevens voor een bepaalde dag op te halen:`"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>SAP C4C als gootsteen

Als u gegevens wilt kopiëren naar SAP Cloud for Customer, stelt u het sinktype in de kopieeractiviteit in op **SapCloudForCustomerSink.** De volgende eigenschappen worden ondersteund in de sectie copy activity **sink:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **SapCloudForCustomerSink**  | Ja |
| writeBehavior | Het schrijfgedrag van de operatie. Kan "Invoegen", "Bijwerken" zijn. | Nee. Standaard "Invoegen". |
| writeBatchSize | De batchgrootte van de schrijfbewerking. De batchgrootte om de beste prestaties te krijgen, kan verschillen voor verschillende tabel of server. | Nee. Standaard 10. |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "dataIntegrationUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Gegevenstypetoewijzing voor SAP Cloud voor klant

Bij het kopiëren van gegevens van SAP Cloud voor klant worden de volgende toewijzingen gebruikt van SAP Cloud voor klantgegevenstypen naar tijdelijke gegevenstypen van Azure Data Factory. Zie [Schema- en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe kopieeractiviteit het bronschema en het gegevenstype naar de gootsteen brengt.

| SAP C4C-gegevenstype OData | Tussentijds gegevenstype gegevensfabriek |
|:--- |:--- |
| Edm.Binary (Edm.Binary) | Byte |
| Edm.Boolean | Booleaanse waarde |
| Edm.Byte Edm.Byte | Byte |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single (Edm.Single) | Enkel |
| Edm.Guid Edm.Guid | GUID |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte Edm.SByte | Int16 |
| Edm.String | Tekenreeks |
| Edm.Time (Edm.Time) | TimeSpan |
| Edm.DateTimeOffset | Datumtijdverschuiving |


## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
