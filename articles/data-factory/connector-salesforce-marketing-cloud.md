---
title: Gegevens kopiëren van Salesforce Marketing Cloud
description: Meer informatie over het kopiëren van gegevens uit Salesforce Marketing Cloud naar ondersteunde sink data stores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
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
ms.date: 10/25/2019
ms.openlocfilehash: 1a5a2682198f9ce9f5cb39f21e244c723ca513d9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416650"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory"></a>Gegevens uit Salesforce Marketing Cloud kopiëren met Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens uit Salesforce Marketing Cloud te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Salesforce Marketing Cloud-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens van Salesforce Marketing Cloud kopiëren naar elk ondersteund sink-gegevensarchief. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

De Salesforce Marketing Cloud-connector ondersteunt OAuth 2-verificatie. Het is gebouwd op de top van de [Salesforce Marketing Cloud REST API.](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm)

>[!NOTE]
>Deze connector biedt geen ondersteuning voor het ophalen van aangepaste objecten of aangepaste gegevensextensies.

## <a name="getting-started"></a>Aan de slag

U een pijplijn maken met kopieeractiviteit met behulp van .NET SDK, Python SDK, Azure PowerShell, REST API of Azure Resource Manager-sjabloon. Zie [Zelfstudie voor activiteit kopiëren](quickstart-create-data-factory-dot-net.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken.

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor de Salesforce Marketing Cloud-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor salesforce marketing cloud gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **SalesforceMarketingCloud** | Ja |
| clientId | De client-id die is gekoppeld aan de Salesforce Marketing Cloud-toepassing.  | Ja |
| clientGeheim | Het klantgeheim dat is gekoppeld aan de Salesforce Marketing Cloud-toepassing. U ervoor kiezen om dit veld te markeren als een SecureString om het veilig op te slaan in ADF, of wachtwoord op te slaan in Azure Key Vault en ADF activiteit van daaruit te laten kopiëren bij het uitvoeren van gegevenskopie - meer informatie over [storereferenties in Key Vault](store-credentials-in-key-vault.md). | Ja |
| gebruikVersleutelde eindpunten | Hiermee geeft u op of de eindpunten van de gegevensbron zijn versleuteld met HTTPS. De standaardwaarde is waar.  | Nee |
| useHostVerification | Hiermee geeft u op of de hostnaam in het certificaat van de server moet overeenkomen met de hostnaam van de server wanneer u verbinding maakt via TLS. De standaardwaarde is waar.  | Nee |
| usePeerVerification | Hiermee geeft u op of u de identiteit van de server moet verifiëren wanneer u verbinding maakt via TLS. De standaardwaarde is waar.  | Nee |

**Voorbeeld:**

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints" : true,
            "useHostVerification" : true,
            "usePeerVerification" : true
        }
    }
}

```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Salesforce Marketing Cloud-gegevensset.

Als u gegevens uit Salesforce Marketing Cloud wilt kopiëren, stelt u de eigenschap typeeigenschap van de gegevensset in op **SalesforceMarketingCloud.** De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **SalesforceMarketingCloudObject** | Ja |
| tableName | Naam van de tabel. | Nee (als 'query' in activiteitsbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Salesforce Marketing Cloud-bron.

### <a name="salesforce-marketing-cloud-as-source"></a>Salesforce Marketing Cloud als bron

Als u gegevens uit Salesforce Marketing Cloud wilt kopiëren, stelt u het brontype in de kopieeractiviteit in op **SalesforceMarketingCloud.** De volgende eigenschappen worden ondersteund in de sectie **bron** van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron voor kopieeractiviteit moet zijn ingesteld op: **SalesforceMarketingCloudSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als 'tabelNaam' in de gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceMarketingCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SalesforceMarketingCloud input dataset name>",
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
                "type": "SalesforceMarketingCloudSource",
                "query": "SELECT * FROM MyTable"
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
