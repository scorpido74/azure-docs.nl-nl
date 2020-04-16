---
title: Gegevens van SAP BW kopiëren
description: Meer informatie over het kopiëren van gegevens uit SAP Business Warehouse naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
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
ms.date: 09/04/2019
ms.openlocfilehash: 2f8406038be10ba3bdc207bf447fecb86a376fe8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418062"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Gegevens uit SAP Business Warehouse kopiëren met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Huidige versie](connector-sap-business-warehouse.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens uit een BW (SAP Business Warehouse) te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

>[!TIP]
>Zie [SAP-gegevensintegratie met azure data factory-whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) met gedetailleerde introductie, vergelijking en richtlijnen voor de algemene ondersteuning van ADF voor de algemene ondersteuning van sap-gegevensintegratie.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze SAP Business Warehouse-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens uit SAP Business Warehouse kopiëren naar elk ondersteund sink datastore. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Met name deze SAP Business Warehouse-connector ondersteunt:

- SAP Business Warehouse **versie 7.x**.
- Gegevens kopiëren van **InfoCubes en QueryCubes** (inclusief BEx-query's) met Behulp van MDX-query's.
- Gegevens kopiëren met basisverificatie.

## <a name="prerequisites"></a>Vereisten

Als u deze SAP Business Warehouse-connector wilt gebruiken, moet u het:

- Stel een Self-hosted Integration Runtime in. Zie [artikel Self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) voor meer informatie.
- Installeer de **SAP NetWeaver-bibliotheek** op de runtime-machine integratie. U de SAP Netweaver-bibliotheek ophalen bij uw SAP-beheerder of rechtstreeks van het [SAP Software Download Center.](https://support.sap.com/swdc) Zoek naar de **SAP Note #1025361** om de downloadlocatie voor de meest recente versie te krijgen. Zorg ervoor dat u de **64-bits** SAP NetWeaver-bibliotheek kiest die overeenkomt met uw integratieruntime-installatie. Installeer vervolgens alle bestanden die zijn opgenomen in de SAP NetWeaver RFC SDK volgens de SAP Note. De SAP NetWeaver-bibliotheek is ook opgenomen in de SAP Client Tools-installatie.

>[!TIP]
>Als u het verbindingsprobleem met SAP BW wilt oplossen, moet u het als belangrijkste uitvoeren:
>- Alle afhankelijkheidsbibliotheken die uit de NetWeaver RFC SDK zijn geëxtraheerd, zijn op hun plaats in de map %windir%\system32. Meestal heeft icudt34.dll, icuin34.dll, icuuc34.dll, libicudecnumber.dll, librfc32.dll, libsapucum.dll, sapcrypto.dll, sapcryto_old.dll, sapnwrfc.dll.
>- De benodigde poorten die worden gebruikt om verbinding te maken met SAP Server zijn ingeschakeld op de self-hosted IR-machine, die meestal poort 3300 en 3201 zijn.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor de SAP Business Warehouse-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de gekoppelde service van SAP Business Warehouse (BW):

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | Het type eigenschap moet worden ingesteld op: **SapBw** | Ja |
| server | Naam van de server waarop de SAP BW-instantie zich bevindt. | Ja |
| systeemAantal | Systeemnummer van het SAP BW-systeem.<br/>Toegestane waarde: tweecijferig decimaal getal weergegeven als een tekenreeks. | Ja |
| clientId | Client-ID van de client in het SAP W-systeem.<br/>Toegestane waarde: driecijferig decimaal getal weergegeven als een tekenreeks. | Ja |
| userName | Naam van de gebruiker die toegang heeft tot de SAP-server. | Ja |
| wachtwoord | Het wachtwoord voor de gebruiker. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Een Self-hosted Integration Runtime is vereist zoals vermeld in [Voorwaarden](#prerequisites). |Ja |

**Voorbeeld:**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de SAP BW-gegevensset.

Als u gegevens van SAP BW wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **SapBwCube**. Hoewel er geen typespecifieke eigenschappen worden ondersteund voor de SAP BW-gegevensset van type RelationalTable.

**Voorbeeld:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "SapBwCube",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Als u `RelationalTable` getypte gegevensset gebruikt, wordt deze nog steeds ondersteund als deze is, terwijl u wordt voorgesteld om de nieuwe in de toekomst te gebruiken.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door SAP BW-bron.

### <a name="sap-bw-as-source"></a>SAP BW als bron

Als u gegevens van SAP BW wilt kopiëren, worden de volgende eigenschappen ondersteund in de **sectie** bron van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op: **SapBwSource** | Ja |
| query | Hiermee geeft u de MDX-query op om gegevens uit de instantie SAP BW te lezen. | Ja |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "type": "SapBwSource",
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Als u `RelationalSource` getypte bron gebruikt, wordt deze nog steeds ondersteund als is, terwijl u wordt voorgesteld om de nieuwe in de toekomst te gebruiken.

## <a name="data-type-mapping-for-sap-bw"></a>Gegevenstypetoewijzing voor SAP BW

Bij het kopiëren van gegevens van SAP BW worden de volgende toewijzingen gebruikt van SAP BW-gegevenstypen naar tijdelijke gegevenstypen van Azure Data Factory. Zie [Schema- en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe kopieeractiviteit het bronschema en het gegevenstype naar de gootsteen brengt.

| SAP BW-gegevenstype | Tussentijds gegevenstype gegevensfabriek |
|:--- |:--- |
| ACCP (ACCP) | Int |
| Char | Tekenreeks |
| Clnt | Tekenreeks |
| CURR (CURR) | Decimal |
| CUKY CUKY | Tekenreeks |
| Dec | Decimal |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | Int |
| LANG LANG | Tekenreeks |
| LCHR (LCHR) | Tekenreeks |
| LRAW (LRAW) | Byte |
| PREC (PREC) | Int16 |
| Quan | Decimal |
| Raw | Byte |
| RAWSTRING RAWSTRING | Byte |
| Tekenreeks | Tekenreeks |
| Eenheid | Tekenreeks |
| DATS (DATS) | Tekenreeks |
| NUMC (NUMC) | Tekenreeks |
| TIMS TIMS | Tekenreeks |


## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
