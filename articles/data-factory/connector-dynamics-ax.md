---
title: Gegevens kopiëren van Dynamics AX
description: Meer informatie over het kopiëren van gegevens van Dynamics AX naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 555293492a37b9c635cb11087c08d52aa4b4d8c4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74218626"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Gegevens uit Dynamics AX kopiëren met behulp van Azure Data Factory

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit de Dynamics AX-bron te kopiëren. Het artikel bouwt voort op de [Kopieer activiteit in azure Data Factory](copy-activity-overview.md), waarin een algemeen overzicht van de Kopieer activiteit wordt weer gegeven.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Dynamics AX-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van Dynamics AX kopiëren naar elk ondersteund Sink-gegevens archief. Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en Sinks.

Met name deze Dynamics AX-connector ondersteunt het kopiëren van gegevens van Dynamics AX met het **OData-protocol** met service- **Principal-verificatie**.

>[!TIP]
>U kunt deze connector ook gebruiken om gegevens te kopiëren uit **Dynamics 365-Financiën en-bewerkingen**. Raadpleeg de [OData-ondersteuning](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) en [verificatie methode](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication)van Dynamics 365.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die u kunt gebruiken voor het definiëren van Data Factory entiteiten die specifiek zijn voor de Dynamics AX-connector.

## <a name="prerequisites"></a>Vereisten

Volg deze stappen voor het gebruik van service-principal verificatie:

1. Registreer een toepassings entiteit in Azure Active Directory (Azure AD) door [uw toepassing te registreren bij een Azure AD-Tenant](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Noteer de volgende waarden, die u gebruikt voor het definiëren van de gekoppelde service:

    - Toepassings-id
    - Toepassingssleutel
    - Tenant-id

2. Ga naar Dynamics AX en verleen deze service-principal de juiste machtigingen voor toegang tot uw Dynamics AX.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor een gekoppelde Dynamics AX-service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet worden ingesteld op **DynamicsAX**. |Ja |
| url | Het OData-eind punt van het exemplaar van Dynamics AX (of Dynamics 365 Finance and Operations). |Ja |
| servicePrincipalId | Opgeven van de toepassing client-ID. | Ja |
| servicePrincipalKey | Geef de sleutel van de toepassing. Markeer dit veld als **SecureString** om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | De tenantgegevens (domain name of tenant-ID) opgeven in uw toepassing zich bevindt. Deze ophalen door de muis in de rechterbovenhoek van de Azure-portal. | Ja |
| aadResourceId | Geef de AAD-resource op die u aanvraagt voor autorisatie. Als uw Dynamics-URL bijvoorbeeld `https://sampledynamics.sandbox.operations.dynamics.com/data/`is, wordt de bijbehorende AAD-resource meestal `https://sampledynamics.sandbox.operations.dynamics.com`. | Ja |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt kiezen Azure Integration Runtime of een zelf-hostend Integration Runtime (als uw gegevens archief zich in een particulier netwerk bevindt). Indien niet opgegeven, wordt de standaard Azure Integration Runtime wordt gebruikt. |Nee |

**Voorbeeld**

```json
{
    "name": "DynamicsAXLinkedService",
    "properties": {
        "type": "DynamicsAX",
        "typeProperties": {
            "url": "<Dynamics AX instance OData endpoint>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource, e.g. https://sampledynamics.sandbox.operations.dynamics.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}

```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

In deze sectie vindt u een lijst met eigenschappen die door de Dynamics AX-gegevensset worden ondersteund.

Zie [gegevens sets en gekoppelde services](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. 

Als u gegevens wilt kopiëren uit Dynamics AX, stelt u de eigenschap **type** van de gegevensset in op **DynamicsAXResource**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de DataSet moet worden ingesteld op **DynamicsAXResource**. | Ja |
| pad | Het pad naar de Dynamics AX OData-entiteit. | Ja |

**Voorbeeld**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

In deze sectie vindt u een lijst met eigenschappen die door de Dynamics AX-bron worden ondersteund.

Zie [pijp lijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. 

### <a name="dynamics-ax-as-source"></a>Dynamics AX als bron

Als u gegevens wilt kopiëren uit Dynamics AX, stelt u het **bron** type in de Kopieer activiteit in op **DynamicsAXSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op **DynamicsAXSource**. | Ja |
| query | OData-query opties voor het filteren van gegevens. Voor beeld: `"?$select=Name,Description&$top=5"`.<br/><br/>**Opmerking**: de connector kopieert gegevens van de gecombineerde URL: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Zie [ODATA URL Components](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)(Engelstalig) voor meer informatie. | Nee |

**Voorbeeld**

```json
"activities":[
    {
        "name": "CopyFromDynamicsAX",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics AX input dataset name>",
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
                "type": "DynamicsAXSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md##supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en sinks in azure Data Factory.
