---
title: Gegevens van AWS Marketplace kopiëren
description: Meer informatie over het kopiëren van gegevens van Amazon Marketplace Web Service naar ondersteunde sink data stores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 08/01/2018
ms.openlocfilehash: 8cd35131cfa6e919d22033a644a6479af4ee6eb4
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992025"
---
# <a name="copy-data-from-amazon-marketplace-web-service-using-azure-data-factory"></a>Gegevens van Amazon Marketplace Web Service kopiëren met Azure Data Factory

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens van Amazon Marketplace Web Service te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Amazon Marketplace Web Service-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens van Amazon Marketplace Web Service kopiëren naar elk ondersteund sink datastore. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Azure Data Factory biedt een ingebouwd stuurprogramma om connectiviteit mogelijk te maken, daarom hoeft u geen stuurprogramma handmatig te installeren met deze connector.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabrieken te definiëren die specifiek zijn voor de Amazon Marketplace Web Service-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor amazon Marketplace Web Service gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | Het type eigenschap moet worden ingesteld op: **AmazonMWS** | Ja |
| endpoint | Het eindpunt van de Amazon MWS-server, (dat wil zeggen, mws.amazonservices.com)  | Ja |
| marketplaceID | De Amazon Marketplace ID waar u gegevens van wilt ophalen. Als u gegevens uit meerdere Marketplace---------iD's wilt ophalen, scheidt u deze met een komma (`,`). (dat wil zeggen, A2EUQ1WTGCTBG2)  | Ja |
| verkoper-id | De Amazon-verkoper-id.  | Ja |
| mwsAuthToken mwsAuthToken | Het Amazon MWS-verificatietoken. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |
| accessKeyId accessKeyId | De toegangssleutel-id die wordt gebruikt om toegang te krijgen tot gegevens.  | Ja |
| secretKey secretKey | De geheime sleutel die wordt gebruikt om toegang te krijgen tot gegevens. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |
| gebruikVersleutelde eindpunten | Hiermee geeft u op of de eindpunten van de gegevensbron zijn versleuteld met HTTPS. De standaardwaarde is waar.  | Nee |
| useHostVerification | Hiermee geeft u op of de hostnaam in het certificaat van de server moet overeenkomen met de hostnaam van de server wanneer u verbinding maakt via TLS. De standaardwaarde is waar.  | Nee |
| usePeerVerification | Hiermee geeft u op of u de identiteit van de server moet verifiëren wanneer u verbinding maakt via TLS. De standaardwaarde is waar.  | Nee |

**Voorbeeld:**

```json
{
    "name": "AmazonMWSLinkedService",
    "properties": {
        "type": "AmazonMWS",
        "typeProperties": {
            "endpoint" : "mws.amazonservices.com",
            "marketplaceID" : "A2EUQ1WTGCTBG2",
            "sellerID" : "<sellerID>",
            "mwsAuthToken": {
                 "type": "SecureString",
                 "value": "<mwsAuthToken>"
            },
            "accessKeyId" : "<accessKeyId>",
            "secretKey": {
                 "type": "SecureString",
                 "value": "<secretKey>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de gegevensset Amazon Marketplace Web Service.

Als u gegevens van Amazon Marketplace Web Service wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **AmazonMWSObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **AmazonMWSObject** | Ja |
| tableName | Naam van de tabel. | Nee (als 'query' in activiteitsbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "AmazonMWSDataset",
    "properties": {
        "type": "AmazonMWSObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<AmazonMWS linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de bron van Amazon Marketplace Web Service.

### <a name="amazon-mws-as-source"></a>Amazon MWS als bron

Als u gegevens van Amazon Marketplace Web Service wilt kopiëren, stelt u het brontype in de kopieeractiviteit in op **AmazonMWSSource.** De volgende eigenschappen worden ondersteund in de sectie **bron** van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op: **AmazonMWSSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM Orders where  Amazon_Order_Id = 'xx'"`. | Nee (als 'tabelNaam' in de gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromAmazonMWS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AmazonMWS input dataset name>",
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
                "type": "AmazonMWSSource",
                "query": "SELECT * FROM Orders where Amazon_Order_Id = 'xx'"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
