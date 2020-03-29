---
title: Gegevens uit OData-bronnen kopiëren met Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit OData-bronnen naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 17b78e03e330e342e9d558dd3ca5d9071bcd3c2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163927"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Gegevens uit een OData-bron kopiëren met Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-odata-connector.md)
> * [Huidige versie](connector-odata.md)

In dit artikel wordt beschreven hoe u Activiteit kopiëren in Azure Data Factory gebruikt om gegevens uit een OData-bron te kopiëren. Het artikel bouwt voort op [Kopieeractiviteit in Azure Data Factory](copy-activity-overview.md), dat een algemeen overzicht van Kopieeractiviteit weergeeft.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze OData-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens uit een OData-bron kopiëren naar een ondersteund sinkdataarchief. Zie [Ondersteunde gegevensopslag en -indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die Activiteit kopiëren als bronnen en sinks ondersteunt.

Met name deze OData-connector ondersteunt:

- OData versie 3.0 en 4.0.
- Gegevens kopiëren met behulp van een van de volgende verificaties: **Anoniem,** **Basic**, **Windows**en **AAD serviceprincipal**.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die u gebruiken om entiteiten in gegevensfabrieken te definiëren die specifiek zijn voor een OData-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor een oData-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet worden ingesteld op **OData**. |Ja |
| url | De hoofd-URL van de OData-service. |Ja |
| authenticationType | Het type verificatie dat wordt gebruikt om verbinding te maken met de OData-bron. Toegestane waarden zijn **Anoniem,** **Basic,** **Windows**en **AadServicePrincipal**. Gebruikersgebaseerde OAuth wordt niet ondersteund. | Ja |
| userName | Geef **userName** op als u basis- of Windows-verificatie gebruikt. | Nee |
| wachtwoord | Geef **een wachtwoord** op voor het gebruikersaccount dat u hebt opgegeven voor **userName.** Markeer dit veld als een **SecureString-type** om het veilig op te slaan in Gegevensfabriek. U ook [verwijzen naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Nee |
| servicePrincipalId | Geef de client-id van de Azure Active Directory-toepassing op. | Nee |
| aadServicePrincipalCredentialType | Geef het referentietype op dat moet worden gebruikt voor serviceprincipal-verificatie. Toegestane waarden `ServicePrincipalKey` zijn: `ServicePrincipalCert`of . | Nee |
| servicePrincipalKey | Geef de sleutel van de Azure Active Directory-toepassing op. Markeer dit veld als een **SecureString** om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Nee |
| servicePrincipalEmbeddedCert | Geef het basiscertificaat van 64 op dat is geregistreerd in Azure Active Directory. Markeer dit veld als een **SecureString** om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Nee |
| servicePrincipalEmbeddedCertPassword | Geef het wachtwoord van uw certificaat op als uw certificaat is beveiligd met een wachtwoord. Markeer dit veld als een **SecureString** om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md)  | Nee|
| tenant | Geef de tenantgegevens op (domeinnaam of tenant-id) waaronder uw toepassing zich bevindt. Haal deze op door met de muis in de rechterbovenhoek van de Azure-portal te zweven. | Nee |
| aadResourceId | Geef de AAD-bron op die u om autorisatie vraagt.| Nee |
| connectVia | De [runtime integratie](concepts-integration-runtime.md) om verbinding te maken met het gegevensarchief. Meer informatie van de sectie [Voorwaarden.](#prerequisites) Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

**Voorbeeld 1: Anonieme verificatie gebruiken**

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

**Voorbeeld 2: Basisverificatie gebruiken**

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

**Voorbeeld 3: Windows-verificatie gebruiken**

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

**Voorbeeld 4: Serviceprincipal key authentication gebruiken**

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

**Voorbeeld 5: Service principal cert-verificatie gebruiken**

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

In deze sectie vindt u een lijst met eigenschappen die de OData-gegevensset ondersteunt.

Zie [Gegevenssets en gekoppelde services](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. 

Als u gegevens van OData wilt kopiëren, stelt u de **eigenschap type** van de gegevensset in op **ODataResource**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de gegevensset moet worden ingesteld op **ODataResource**. | Ja |
| path | Het pad naar de OData-bron. | Ja |

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

## <a name="copy-activity-properties"></a>Activiteitseigenschappen kopiëren

In deze sectie vindt u een lijst met eigenschappen die de OData-bron ondersteunt.

Zie [Pijplijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. 

### <a name="odata-as-source"></a>OData als bron

Als u gegevens van OData wilt kopiëren, worden de volgende eigenschappen ondersteund in de **sectie** Activiteit kopiëren:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de bron Activiteit kopiëren moet zijn ingesteld op **ODataSource**. | Ja |
| query | OData-queryopties voor het filteren van gegevens. Bijvoorbeeld: `"$select=Name,Description&$top=5"`.<br/><br/>**Opmerking:** De OData-connector kopieert `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]`gegevens uit de gecombineerde URL: . Zie [OData URL-componenten](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)voor meer informatie . | Nee |

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

Als u `RelationalSource` getypte bron gebruikt, wordt deze nog steeds ondersteund als is, terwijl u wordt voorgesteld om de nieuwe in de toekomst te gebruiken.

## <a name="data-type-mapping-for-odata"></a>Gegevenstypetoewijzing voor OData

Wanneer u gegevens van OData kopieert, worden de volgende toewijzingen gebruikt tussen OData-gegevenstypen en tijdelijke gegevenstypen van Azure Data Factory. Zie [Schema en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe Activiteit kopiëren het bronschema en het gegevenstype aan de gootsteen toebrengt.

| OData-gegevenstype | Tussentijds gegevenstype Data Factory |
|:--- |:--- |
| Edm.Binary (Edm.Binary) | Byte |
| Edm.Boolean | Booleaanse waarde |
| Edm.Byte Edm.Byte | Byte |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single (Edm.Single) | Enkel |
| Edm.Guid Edm.Guid | GUID |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte Edm.SByte | Int16 |
| Edm.String | Tekenreeks |
| Edm.Time (Edm.Time) | TimeSpan |
| Edm.DateTimeOffset | Datumtijdverschuiving |

> [!NOTE]
> Complexe gegevenstypen van OData (zoals **Object)** worden niet ondersteund.


## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Volgende stappen

Zie [Ondersteunde gegevensopslag en -indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die activiteit kopiëren als bronnen en sinks in Azure Data Factory ondersteunt.