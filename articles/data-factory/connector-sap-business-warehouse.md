---
title: Gegevens kopiëren van SAP BW met behulp van Azure Data Factory | Microsoft Docs
description: Informatie over het kopiëren van gegevens van SAP Business Warehouse naar ondersteunde Sink-gegevens archieven door gebruik te maken van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
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
ms.openlocfilehash: 4ee9048202a5953ff27ea83cfb39cb1af0739e7a
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277511"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Gegevens van SAP Business Warehouse kopiëren met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-sap-business-warehouse-connector.md)
> * [Huidige versie](connector-sap-business-warehouse.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een SAP Business Warehouse (BW) te kopiëren. Dit is gebaseerd op de [overzicht kopieeractiviteit](copy-activity-overview.md) artikel met daarin een algemeen overzicht van de kopieeractiviteit.

>[!TIP]
>Zie [SAP Data Integration using Azure Data Factory White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) with introduction, comparsion en guidance (Engelstalig) voor meer informatie over de algemene ondersteuning van de ADF op SAP Data Integration scenario.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U kunt gegevens van SAP Business Warehouse kopiëren naar een ondersteunde Sink-gegevens opslag. Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen/put door de kopieeractiviteit, de [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats) tabel.

Deze SAP Business Warehouse-connector ondersteunt met name:

- SAP Business Warehouse **versie 7. x**.
- Gegevens kopiëren van **InfoCubes en QueryCubes** (inclusief Bex-query's) met behulp van MDX-query's.
- Kopiëren van gegevens met behulp van basis verificatie.

## <a name="prerequisites"></a>Vereisten

Als u deze SAP Business Warehouse-connector wilt gebruiken, moet u het volgende doen:

- Stel een zelf-hostende Integration Runtime in. Zie [zelfgehoste Cloudintegratieruntime](create-self-hosted-integration-runtime.md) artikel voor meer informatie.
- Installeer de **SAP NetWeaver-bibliotheek** op de Integration runtime computer. U kunt de SAP NetWeaver-bibliotheek ophalen van uw SAP-beheerder of rechtstreeks vanuit het [SAP-software Download centrum](https://support.sap.com/swdc). Zoek naar de **SAP-notitie #1025361** om de download locatie voor de meest recente versie op te halen. Zorg ervoor dat u de **64-bits** SAP NetWeaver-bibliotheek hebt gekozen die overeenkomt met uw Integration runtime-installatie. Installeer vervolgens alle bestanden die zijn opgenomen in de SAP NetWeaver RFC SDK volgens de SAP-opmerking. De SAP NetWeaver-bibliotheek is ook opgenomen in de installatie van de SAP-client Hulpprogramma's.

>[!TIP]
>Als u verbindings problemen wilt oplossen met SAP BW, controleert u het volgende:
>- Alle afhankelijkheids bibliotheken die zijn geëxtraheerd uit de coweaver RFC SDK zijn aanwezig in de map%windir%\System32. Normaal gesp roken heeft het icudt34. dll, icuin34. dll, icuuc34. dll, libicudecnumber. dll, librfc32. dll, libsapucum. dll, sapcrypto. dll, sapcryto_old. dll, sapnwrfc. dll.
>- De benodigde poorten die worden gebruikt om verbinding te maken met SAP server worden ingeschakeld op de zelf-hostende IR-computer, die meestal poort 3300 en 3201 is.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor SAP Business Warehouse connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor de gekoppelde service SAP Business Warehouse (BW):

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type moet worden ingesteld op: **SapBw** | Ja |
| server | De naam van de server waarop het SAP BW-exemplaar zich bevindt. | Ja |
| systemNumber | Systeem nummer van het SAP BW systeem.<br/>Toegestane waarde: decimaal getal van twee cijfers dat wordt weer gegeven als een teken reeks. | Ja |
| clientId | Client-ID van de client in het SAP W-systeem.<br/>Toegestane waarde: decimaal getal met drie cijfers dat wordt weer gegeven als een teken reeks. | Ja |
| userName | De naam van de gebruiker die toegang heeft tot de SAP-server. | Ja |
| password | Het wachtwoord voor de gebruiker. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory, of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | De [Integration Runtime](concepts-integration-runtime.md) moet worden gebruikt verbinding maken met het gegevensarchief. Een zelf-hostende Integration Runtime is vereist zoals vermeld in de [vereisten](#prerequisites). |Ja |

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

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. Deze sectie bevat een lijst met eigenschappen die door SAP BW DataSet worden ondersteund.

Als u gegevens wilt kopiëren uit SAP BW, stelt u de eigenschap type van de gegevensset in op **SapBwCube**. Hoewel er geen type-specifieke eigenschappen worden ondersteund voor de SAP BW-gegevensset van het type RelationalTable.

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

Als u getypte gegevensset gebruikt `RelationalTable` , wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. Deze sectie bevat een lijst met eigenschappen die door SAP BW bron worden ondersteund.

### <a name="sap-bw-as-source"></a>SAP BW als bron

Als u gegevens wilt kopiëren uit SAP BW, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van de bron van de Kopieer activiteit moet worden ingesteld op: **SapBwSource** | Ja |
| query | Hiermee geeft u de MDX-query op die gegevens uit het SAP BW exemplaar moet lezen. | Ja |

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

Als u getypte bron gebruikt `RelationalSource` , wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

## <a name="data-type-mapping-for-sap-bw"></a>Toewijzing van gegevens type voor SAP BW

Bij het kopiëren van gegevens uit SAP BW worden de volgende toewijzingen gebruikt van SAP BW gegevens typen om tussenliggende gegevens typen te Azure Data Factory. Zie [Schema en gegevens typt toewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe copy activity in het schema en de gegevens van een brontype aan de sink toegewezen.

| SAP BW gegevens type | Data factory tussentijdse gegevenstype |
|:--- |:--- |
| ACCP | Int |
| CHAR | String |
| CLNT | String |
| CURR | Decimal |
| CUKY | String |
| DEC | Decimal |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | Int |
| LANG | String |
| LCHR | String |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | String |
| UNIT | String |
| DATS | String |
| NUMC | String |
| WISSEN | String |


## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats).
