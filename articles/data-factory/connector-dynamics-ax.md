---
title: Gegevens kopiëren van Dynamics AX
description: Meer informatie over het kopiëren van gegevens uit Dynamics AX naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
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
ms.date: 08/01/2019
ms.openlocfilehash: 4acad5e2de55211b6c4492513f331c36286ed852
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75892781"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Gegevens uit Dynamics AX kopiëren met Azure Data Factory

In dit artikel wordt beschreven hoe u Activiteit kopiëren in Azure Data Factory gebruikt om gegevens uit Dynamics AX-bron te kopiëren. Het artikel bouwt voort op [Kopieeractiviteit in Azure Data Factory](copy-activity-overview.md), dat een algemeen overzicht van Kopieeractiviteit weergeeft.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Dynamics AX-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens van Dynamics AX kopiëren naar elk ondersteund sinkdataarchief. Zie [Ondersteunde gegevensopslag en -indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die Activiteit kopiëren als bronnen en sinks ondersteunt.

Deze Dynamics AX-connector ondersteunt specifiek het kopiëren van gegevens van Dynamics AX met **odata-protocol** met **Service Principal-verificatie.**

>[!TIP]
>U deze connector ook gebruiken om gegevens van **Dynamics 365 Finance and Operations**te kopiëren. Raadpleeg de [OData-ondersteunings-](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) en [verificatiemethode van](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication)Dynamics 365.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die u gebruiken om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor Dynamics AX-connector.

## <a name="prerequisites"></a>Vereisten

Voer de volgende stappen uit om serviceprincipal-verificatie te gebruiken:

1. Registreer een toepassingsentiteit in Azure Active Directory (Azure AD) door Uw toepassing te registreren [bij een Azure AD-tenant.](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:

    - Toepassings-id
    - Toepassingssleutel
    - Tenant-id

2. Ga naar Dynamics AX en geef deze serviceprincipal de juiste toestemming om toegang te krijgen tot uw Dynamics AX.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor Dynamics AX-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet zijn ingesteld op **DynamicsAX**. |Ja |
| url | Het OData-eindpunt Dynamics AX (of Dynamics 365 Finance and Operations). |Ja |
| servicePrincipalId | Geef de client-id van de toepassing op. | Ja |
| servicePrincipalKey | Geef de sleutel van de toepassing op. Markeer dit veld als een **SecureString** om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |
| tenant | Geef de tenantgegevens op (domeinnaam of tenant-id) waaronder uw toepassing zich bevindt. Haal deze op door met de muis in de rechterbovenhoek van de Azure-portal te zweven. | Ja |
| aadResourceId | Geef de AAD-bron op die u om autorisatie vraagt. Als uw URL Dynamics `https://sampledynamics.sandbox.operations.dynamics.com/data/`bijvoorbeeld is, is `https://sampledynamics.sandbox.operations.dynamics.com`de bijbehorende AAD-bron meestal . | Ja |
| connectVia | De [runtime integratie](concepts-integration-runtime.md) om verbinding te maken met het gegevensarchief. U Azure Integration Runtime of een self-hosted Integration Runtime kiezen (als uw gegevensarchief zich in een privénetwerk bevindt). Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

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

In deze sectie vindt u een lijst met eigenschappen die de Dynamica AX-gegevensset ondersteunt.

Zie [Gegevenssets en gekoppelde services](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. 

Als u gegevens uit Dynamics AX wilt kopiëren, stelt u de **eigenschap type** van de gegevensset in op **DynamicsAXResource**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de gegevensset moet worden ingesteld op **DynamicsAXResource**. | Ja |
| path | Het pad naar de entiteit Dynamics AX OData. | Ja |

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

## <a name="copy-activity-properties"></a>Activiteitseigenschappen kopiëren

In deze sectie vindt u een lijst met eigenschappen die de Dynamics AX-bron ondersteunt.

Zie [Pijplijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. 

### <a name="dynamics-ax-as-source"></a>Dynamics AX als bron

Als u gegevens uit Dynamics AX wilt kopiëren, stelt u het **brontype** in Activiteit kopiëren in **DynamicsAXSource**. De volgende eigenschappen worden ondersteund in de sectie Activiteit **kopiëren:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de bron Activiteit kopiëren moet zijn ingesteld op **DynamicsAXSource**. | Ja |
| query | OData-queryopties voor het filteren van gegevens. Bijvoorbeeld: `"?$select=Name,Description&$top=5"`.<br/><br/>**Opmerking:** De connector kopieert gegevens `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`uit de gecombineerde URL: . Zie [OData URL-componenten](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)voor meer informatie . | Nee |

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


## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Volgende stappen

Zie [Ondersteunde gegevensopslag en -indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die activiteit kopiëren als bronnen en sinks in Azure Data Factory ondersteunt.
