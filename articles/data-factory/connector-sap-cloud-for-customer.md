---
title: Gegevens kopiëren van/naar SAP-Cloud voor klant met Azure Data Factory | Microsoft Docs
description: Meer informatie over het kopiëren van gegevens uit de SAP-Cloud voor klant naar ondersteunde Sink-gegevens archieven (of) van ondersteunde brongegevens archieven naar SAP-Cloud voor klanten door gebruik te maken van Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: bf92a0b197efaf1d1f88903cfbe9a7c515eaed03
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259072"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Gegevens kopiëren uit de SAP-Cloud voor de klant (C4C) met behulp van Azure Data Factory

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van/naar SAP-Cloud voor klant (C4C). Dit is gebaseerd op de [overzicht kopieeractiviteit](copy-activity-overview.md) artikel met daarin een algemeen overzicht van de kopieeractiviteit.

>[!TIP]
>Zie [SAP Data Integration using Azure Data Factory White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) with introduction, comparsion en guidance (Engelstalig) voor meer informatie over de algemene ondersteuning van de ADF op SAP Data Integration scenario.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U kunt gegevens uit de SAP-Cloud voor de klant kopiëren naar een ondersteunde Sink-gegevens opslag of gegevens kopiëren van elk ondersteund bron gegevens archief naar een SAP-Cloud voor de klant. Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen/put door de kopieeractiviteit, de [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats) tabel.

Met name Azure Data Factory maakt deze connector het mogelijk om gegevens te kopiëren van/naar SAP-Cloud voor klanten, waaronder de SAP-Cloud voor verkoop, SAP Cloud for service en SAP-Cloud voor sociale oplossingen.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor de SAP-Cloud voor de klant connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor SAP-Cloud voor door de klant gekoppelde service:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type moet worden ingesteld op: **SapCloudForCustomer**. | Ja |
| url | De URL van de SAP C4C OData-service. | Ja |
| username | Geef de gebruikers naam op om verbinding te maken met de SAP-C4C. | Ja |
| password | Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory, of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | De [Integration Runtime](concepts-integration-runtime.md) moet worden gebruikt verbinding maken met het gegevensarchief. Als niet is opgegeven, wordt de standaard Azure Integration Runtime. | Nee voor bron, Ja voor sink |

>[!IMPORTANT]
>Als u gegevens wilt kopiëren naar een SAP-Cloud voor de klant, maakt u expliciet [een Azure IR](create-azure-integration-runtime.md#create-azure-ir) met een locatie in de buurt van uw SAP-Cloud voor klant en koppelt u deze aan de gekoppelde service als in het volgende voor beeld:

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

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de SAP-Cloud voor de gegevensset van de klant.

Als u gegevens wilt kopiëren uit de SAP-Cloud voor de klant, stelt u de eigenschap type van de gegevensset in op **SapCloudForCustomerResource**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van de gegevensset moet worden ingesteld op: **SapCloudForCustomerResource** |Ja |
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

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de SAP-Cloud voor de klant bron.

### <a name="sap-c4c-as-source"></a>SAP C4C als bron

Als u gegevens wilt kopiëren uit de SAP-Cloud voor de klant, stelt u het bron type in de Kopieer activiteit in op **SapCloudForCustomerSource**. De volgende eigenschappen worden ondersteund in de kopieeractiviteit **source** sectie:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type moet worden ingesteld op: **SapCloudForCustomerSource**  | Ja |
| query | Geef de aangepaste OData-query op om gegevens te lezen. | Nee |

Voorbeeld query om gegevens op te halen voor een specifieke dag:`"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

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

### <a name="sap-c4c-as-sink"></a>SAP-C4C als Sink

Als u gegevens wilt kopiëren naar een SAP-Cloud voor de klant, stelt u het sink-type in de Kopieer activiteit in op **SapCloudForCustomerSink**. De volgende eigenschappen worden ondersteund in de kopieeractiviteit **sink** sectie:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type moet worden ingesteld op: **SapCloudForCustomerSink**  | Ja |
| writeBehavior | Het schrijf gedrag van de bewerking. Kan ' Insert ', ' Update ' zijn. | Nee. Standaard ' Insert '. |
| writeBatchSize | De Batch grootte van de schrijf bewerking. De Batch grootte voor het verkrijgen van de beste prestaties kan verschillen voor de verschillende tabellen of servers. | Nee. Standaard 10. |

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

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Toewijzing van gegevens type voor SAP-Cloud voor klant

Bij het kopiëren van gegevens uit de SAP-Cloud voor de klant, worden de volgende toewijzingen gebruikt vanuit de SAP-Cloud voor klant gegevens typen voor het Azure Data Factory van tussenliggende gegevens typen. Zie [Schema en gegevens typt toewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe copy activity in het schema en de gegevens van een brontype aan de sink toegewezen.

| SAP C4C OData-gegevens type | Data factory tussentijdse gegevenstype |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| EDM. single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats).
