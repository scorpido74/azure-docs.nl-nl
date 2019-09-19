---
title: Gegevens kopiëren van OData-bronnen met behulp van Azure Data Factory | Microsoft Docs
description: Informatie over het kopiëren van gegevens van OData-bronnen naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: f3afccd2414ae4acbd31452d989e9540d36d512d
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090001"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Gegevens kopiëren van een OData-bron met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-odata-connector.md)
> * [Huidige versie](connector-odata.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een OData-bron te kopiëren. Het artikel is gebaseerd op [Kopieeractiviteit in Azure Data Factory](copy-activity-overview.md), die een algemeen overzicht van Kopieeractiviteit geeft.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze OData-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit een OData-bron kopiëren naar elk ondersteund Sink-gegevens archief. Zie voor een lijst met gegevens opslaat of Kopieeractiviteit als bronnen en sinks ondersteunt, [ondersteunde gegevensarchieven en indelingen](copy-activity-overview.md#supported-data-stores-and-formats).

Deze OData-connector ondersteunt met name:

- OData-versie 3,0 en 4,0.
- Gegevens kopiëren met behulp van een van de volgende authenticaties: **Anonieme**, **basis**, **Windows**, **Aad-Service-Principal**en **beheerde identiteiten voor Azure-resources**.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die u kunt gebruiken voor het definiëren van Data Factory entiteiten die specifiek zijn voor een OData-connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor een gekoppelde OData-service:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet worden ingesteld op **OData**. |Ja |
| url | De basis-URL van de OData-service. |Ja |
| authenticationType | Het type verificatie dat wordt gebruikt om verbinding te maken met de OData-bron. Toegestane waarden zijn **anoniem**, **basis**, **Windows**, **AadServicePrincipal**en **ManagedServiceIdentity**. OAuth op basis van de gebruiker wordt niet ondersteund. | Ja |
| userName | Geef **userName** als u basisverificatie of Windows-verificatie gebruikt. | Nee |
| password | Geef **password** voor de gebruiker-account u hebt opgegeven voor **gebruikersnaam**. Dit veld als markeert een **SecureString** type voor het veilig opslaan in Data Factory. U kunt ook [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Nee |
| servicePrincipalId | Geef de client-ID van de Azure Active Directory toepassing op. | Nee |
| aadServicePrincipalCredentialType | Geef het referentie type op dat moet worden gebruikt voor Service-Principal-verificatie. Toegestane waarden zijn: `ServicePrincipalKey` of `ServicePrincipalCert`. | Nee |
| servicePrincipalKey | Geef de sleutel van de Azure Active Directory toepassing op. Dit veld als markeert een **SecureString** voor het veilig opslaan in de Data Factory of [verwijzen naar een geheim opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). | Nee |
| servicePrincipalEmbeddedCert | Geef het met base64 gecodeerde certificaat op van uw toepassing die is geregistreerd in Azure Active Directory. Dit veld als markeert een **SecureString** voor het veilig opslaan in de Data Factory of [verwijzen naar een geheim opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). | Nee |
| servicePrincipalEmbeddedCertPassword | Geef het wacht woord van uw certificaat op als uw certificaat is beveiligd met een wacht woord. Dit veld als markeert een **SecureString** voor het veilig opslaan in de Data Factory of [verwijzen naar een geheim opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md).  | Nee|
| tenant | De tenantgegevens (domain name of tenant-ID) opgeven in uw toepassing zich bevindt. Deze ophalen door de muis in de rechterbovenhoek van de Azure-portal. | Nee |
| aadResourceId | Geef de AAD-resource op die u aanvraagt voor autorisatie.| Nee |
| connectVia | De [Integration Runtime](concepts-integration-runtime.md) gebruiken om te verbinden met het gegevensarchief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Indien niet opgegeven, wordt de standaard Azure Integration Runtime wordt gebruikt. |Nee |

**Voor beeld 1: Anonieme verificatie gebruiken**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld 2: Basis verificatie gebruiken**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
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

**Voor beeld 3: Windows-verificatie gebruiken**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
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

**Voor beeld 4: Service Principal Key-verificatie gebruiken**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**Voor beeld 5: Verificatie van Service-Principal-certificaten gebruiken**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

In deze sectie vindt u een lijst met eigenschappen die door de OData-gegevensset worden ondersteund.

Zie voor een volledige lijst van eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets en secties, [gegevenssets en gekoppelde services](concepts-datasets-linked-services.md). 

Als u gegevens wilt kopiëren uit OData, stelt u de eigenschap **type** van de gegevensset in op **ODataResource**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de DataSet moet worden ingesteld op **ODataResource**. | Ja |
| path | Het pad naar de OData-resource. | Ja |

**Voorbeeld**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

In deze sectie vindt u een lijst met eigenschappen die door de OData-bron worden ondersteund.

Zie voor een volledige lijst van eigenschappen die beschikbaar zijn voor het definiëren van activiteiten en secties, [pijplijnen](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData als bron

Als u gegevens wilt kopiëren uit OData, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op **ODataSource**. | Ja |
| query | OData-query opties voor het filteren van gegevens. Voorbeeld: `"$select=Name,Description&$top=5"`.<br/><br/>**Opmerking**: De OData-connector kopieert gegevens van de gecombineerde URL `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]`:. Zie [ODATA URL Components](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)(Engelstalig) voor meer informatie. | Nee |

**Voorbeeld**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "ODataSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Als u getypte bron gebruikt `RelationalSource` , wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

## <a name="data-type-mapping-for-odata"></a>Toewijzing van gegevens type voor OData

Wanneer u gegevens van OData kopieert, worden de volgende toewijzingen gebruikt tussen OData-gegevens typen en Azure Data Factory tussenliggende gegevens typen. Zie [schema en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over de manier waarop Kopieer activiteiten het bron schema en het gegevens type aan de Sink toewijzen.

| OData-gegevens type | Data Factory tussentijdse gegevenstype |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| EDM. single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Complexe OData-gegevens typen (zoals **object**) worden niet ondersteund.


## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen

Zie voor een lijst met gegevensarchieven die Kopieeractiviteit ondersteunt als bronnen en sinks in Azure Data Factory, [ondersteunde gegevensarchieven en indelingen](copy-activity-overview.md##supported-data-stores-and-formats).