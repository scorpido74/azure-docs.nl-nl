---
title: Gegevens van SAP ECC kopiëren
description: Meer informatie over het kopiëren van gegevens van SAP ECC naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: ad26fca94527864af10bb0051336c372ea65b3e0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413802"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Gegevens van SAP ECC kopiëren met Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de kopieeractiviteit in Azure Data Factory gebruiken om gegevens van SAP Enterprise Central Component (ECC) te kopiëren. Zie [Overzicht van activiteit kopiëren](copy-activity-overview.md)voor meer informatie .

>[!TIP]
>Zie [SAP-gegevensintegratie met azure data factory-whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) met gedetailleerde introductie, vergelijking en richtlijnen voor de algemene ondersteuning van ADF voor de algemene ondersteuning van sap-gegevensintegratie.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze SAP ECC-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens van SAP ECC kopiëren naar elk ondersteund sink datastore. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen of sinks door de kopieeractiviteit.

Met name deze SAP ECC-connector ondersteunt:

- Gegevens van SAP ECC kopiëren op SAP NetWeaver-versie 7.0 en hoger.
- Gegevens kopiëren van objecten die worden blootgesteld door SAP ECC OData-services, zoals:

  - SAP-tabellen of -weergaven.
  - Zakelijke toepassingsprogrammeringsinterface [BAPI]-objecten.
  - Data-extractors.
  - Gegevens of tussentijdse documenten (IDOCs) die naar SAP Process Integration (PI) worden verzonden en die als OData kunnen worden ontvangen via relatieve adapters.

- Gegevens kopiëren met basisverificatie.

>[!TIP]
>Als u gegevens van SAP ECC wilt kopiëren via een SAP-tabel of -weergave, gebruikt u de [SAP-tabelconnector,](connector-sap-table.md) die sneller en schaalbaarder is.

## <a name="prerequisites"></a>Vereisten

Over het algemeen stelt SAP ECC entiteiten via OData-services bloot via SAP Gateway. Als u deze SAP ECC-connector wilt gebruiken, moet u het:

- **SAP-gateway instellen.** Voor servers met SAP NetWeaver-versies later dan 7.4 is SAP Gateway al geïnstalleerd. Voor eerdere versies moet u de ingesloten SAP Gateway of het SAP Gateway-hubsysteem installeren voordat SAP ECC-gegevens worden blootgesteld via OData-services. Zie de [installatiehandleiding](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm)om SAP Gateway in te stellen.

- **De SAP OData-service activeren en configureren.** U de OData-service binnen enkele seconden activeren via TCODE SICF. U ook configureren welke objecten moeten worden blootgesteld. Zie voor meer informatie de [stapsgewijze richtlijnen.](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om de gegevensfabriekentiteiten te definiëren die specifiek zijn voor de SAP ECC-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de SAP ECC-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| `type` | De `type` accommodatie moet `SapEcc`worden ingesteld op . | Ja |
| `url` | De URL van de SAP ECC OData-service. | Ja |
| `username` | De gebruikersnaam die wordt gebruikt om verbinding te maken met SAP ECC. | Nee |
| `password` | Het plaintext-wachtwoord dat wordt gebruikt om verbinding te maken met SAP ECC. | Nee |
| `connectVia` | De [inburgeringsruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Meer informatie van de sectie [Voorwaarden.](#prerequisites) Als u geen runtime opgeeft, wordt de standaardruntijd voor Azure-integratie gebruikt. | Nee |

### <a name="example"></a>Voorbeeld

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie [Gegevenssets](concepts-datasets-linked-services.md)voor een volledige lijst met de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In de volgende sectie vindt u een lijst met de eigenschappen die worden ondersteund door de SAP ECC-gegevensset.

Als u gegevens van SAP `type` ECC wilt `SapEccResource`kopiëren, stelt u de eigenschap van de gegevensset in op .

De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| `path` | Pad van de SAP ECC OData-entiteit. | Ja |

### <a name="example"></a>Voorbeeld

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie [Pijplijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In de volgende sectie vindt u een lijst van de eigenschappen die worden ondersteund door de SAP ECC-bron.

### <a name="sap-ecc-as-a-source"></a>SAP ECC als bron

Als u gegevens van SAP `type` ECC `source` wilt kopiëren, stelt `SapEccSource`u de eigenschap in het gedeelte van de kopieeractiviteit in op .

De volgende eigenschappen worden ondersteund in `source` de sectie kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| `type` | De `type` eigenschap van de `source` sectie van de `SapEccSource`kopieeractiviteit moet worden ingesteld op . | Ja |
| `query` | De queryopties van OData om de gegevens te filteren. Bijvoorbeeld:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>De SAP ECC-connector kopieert gegevens uit de gecombineerde URL:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Zie [OData URL-componenten](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)voor meer informatie . | Nee |

### <a name="example"></a>Voorbeeld

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>Gegevenstypetoewijzingen voor SAP ECC

Wanneer u gegevens van SAP ECC kopieert, worden de volgende toewijzingen gebruikt van OData-gegevenstypen voor SAP ECC-gegevens naar tijdelijke gegevenstypen van Azure Data Factory. Zie [Schema en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de kopieeractiviteit het bronschema en het gegevenstype aan de gootsteen toebrengt.

| OData-gegevenstype | Tussentijds gegevenstype Data Factory |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> Complexe gegevenstypen worden momenteel niet ondersteund.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Volgende stappen

Zie [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met de gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
