---
title: Gegevens uit Dynamics AX kopiëren met behulp van Azure Data Factory (preview) | Microsoft Docs
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
ms.openlocfilehash: 39c03058970a412a9bc312f99c239377898e5073
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71092105"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory-preview"></a>Gegevens uit Dynamics AX kopiëren met behulp van Azure Data Factory (preview-versie)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit de Dynamics AX-bron te kopiëren. Het artikel is gebaseerd op [Kopieeractiviteit in Azure Data Factory](copy-activity-overview.md), die een algemeen overzicht van Kopieeractiviteit geeft.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Dynamics AX-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van Dynamics AX kopiëren naar elk ondersteund Sink-gegevens archief. Zie voor een lijst met gegevens opslaat of Kopieeractiviteit als bronnen en sinks ondersteunt, [ondersteunde gegevensarchieven en indelingen](copy-activity-overview.md#supported-data-stores-and-formats).

Met name deze Dynamics AX-connector ondersteunt het kopiëren van gegevens van Dynamics AX met het **OData-protocol** met service- **Principal-verificatie**.

>[!TIP]
>U kunt deze connector ook gebruiken om gegevens te kopiëren uit **Dynamics 365-Financiën en-bewerkingen**. Raadpleeg de [OData-ondersteuning](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) en [verificatie methode](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication)van Dynamics 365.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die u kunt gebruiken voor het definiëren van Data Factory entiteiten die specifiek zijn voor de Dynamics AX-connector.

## <a name="prerequisites"></a>Vereisten

Volg deze stappen voor het gebruik van service-principal verificatie:

1. Registreren van een Toepassingsentiteit in Azure Active Directory (Azure AD) door [uw toepassing registreren bij een Azure AD-tenant](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Noteer de volgende waarden, die u gebruikt voor het definiëren van de gekoppelde service:

    - Toepassings-id
    - Toepassingssleutel
    - Tenant-id

2. Ga naar Dynamics AX en verleen deze service-principal de juiste machtigingen voor toegang tot uw Dynamics AX.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor een gekoppelde Dynamics AX-service:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet worden ingesteld op **DynamicsAX**. |Ja |
| url | Het OData-eind punt van het exemplaar van Dynamics AX (of Dynamics 365 Finance and Operations). |Ja |
| servicePrincipalId | Opgeven van de toepassing client-ID. | Ja |
| servicePrincipalKey | Geef de sleutel van de toepassing. Dit veld als markeert een **SecureString** voor het veilig opslaan in de Data Factory of [verwijzen naar een geheim opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | De tenantgegevens (domain name of tenant-ID) opgeven in uw toepassing zich bevindt. Deze ophalen door de muis in de rechterbovenhoek van de Azure-portal. | Ja |
| aadResourceId | Geef de AAD-resource op die u aanvraagt voor autorisatie. Als uw Dynamics-URL bijvoorbeeld is `https://sampledynamics.sandbox.operations.dynamics.com/data/`, is de bijbehorende Aad-resource meestal. `https://sampledynamics.sandbox.operations.dynamics.com` | Ja |
| connectVia | De [Integration Runtime](concepts-integration-runtime.md) gebruiken om te verbinden met het gegevensarchief. U kunt kiezen Azure Integration Runtime of een zelf-hostend Integration Runtime (als uw gegevens archief zich in een particulier netwerk bevindt). Indien niet opgegeven, wordt de standaard Azure Integration Runtime wordt gebruikt. |Nee |

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

Zie voor een volledige lijst van eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets en secties, [gegevenssets en gekoppelde services](concepts-datasets-linked-services.md). 

Als u gegevens wilt kopiëren uit Dynamics AX, stelt u de eigenschap **type** van de gegevensset in op **DynamicsAXResource**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de DataSet moet worden ingesteld op **DynamicsAXResource**. | Ja |
| path | Het pad naar de Dynamics AX OData-entiteit. | Ja |

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

Zie voor een volledige lijst van eigenschappen die beschikbaar zijn voor het definiëren van activiteiten en secties, [pijplijnen](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Dynamics AX als bron

Als u gegevens wilt kopiëren uit Dynamics AX, stelt u het **bron** type in de Kopieer activiteit in op **DynamicsAXSource**. De volgende eigenschappen worden ondersteund in de Kopieeractiviteit **bron** sectie:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op **DynamicsAXSource**. | Ja |
| query | OData-query opties voor het filteren van gegevens. Voorbeeld: `"?$select=Name,Description&$top=5"`.<br/><br/>**Opmerking**: De connector kopieert gegevens van de gecombineerde URL: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Zie [ODATA URL Components](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)(Engelstalig) voor meer informatie. | Nee |

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

Zie voor een lijst met gegevensarchieven die Kopieeractiviteit ondersteunt als bronnen en sinks in Azure Data Factory, [ondersteunde gegevensarchieven en indelingen](copy-activity-overview.md##supported-data-stores-and-formats).
