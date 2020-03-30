---
title: Gegevens uit een SAP-tabel kopiëren
description: Meer informatie over het kopiëren van gegevens uit een SAP-tabel naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
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
ms.openlocfilehash: e98ff7fd914bb86cae256bb1bf6c19086758d463
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371549"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Gegevens uit een SAP-tabel kopiëren met Azure Data Factory

In dit artikel wordt beschreven hoe u de kopieeractiviteit in Azure Data Factory gebruiken om gegevens uit een SAP-tabel te kopiëren. Zie [Overzicht van activiteit kopiëren](copy-activity-overview.md)voor meer informatie .

>[!TIP]
>Zie [SAP-gegevensintegratie met azure data factory-whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) met gedetailleerde introductie, vergelijking en richtlijnen voor de algemene ondersteuning van ADF voor de algemene ondersteuning van sap-gegevensintegratie.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze SAP-tabelconnector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens uit een SAP-tabel kopiëren naar een ondersteund sinkdataarchief. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslagdie wordt ondersteund als bronnen of zinkt door de kopieeractiviteit.

Met name deze SAP-tabelconnector ondersteunt:

- Gegevens uit een SAP-tabel kopiëren in:

  - SAP ERP Central Component (SAP ECC) versie 7.01 of hoger (in een recente SAP Support Package Stack uitgebracht na 2015).
  - SAP Business Warehouse (SAP BW) versie 7.01 of hoger (in een recente SAP Support Package Stack uitgebracht na 2015).
  - SAP S/4HANA.
  - Andere producten in SAP Business Suite versie 7.01 of hoger (in een recente SAP Support Package Stack uitgebracht na 2015).

- Gegevens kopiëren uit zowel een door SAP transparante tabel, een samengevoegde tabel, een geclusterde tabel en een weergave.
- Gegevens kopiëren met basisverificatie of Secure Network Communications (SNC), als SNC is geconfigureerd.
- Verbinding maken met een SAP-toepassingsserver of SAP-berichtenserver.

## <a name="prerequisites"></a>Vereisten

Als u deze SAP-tabelconnector wilt gebruiken, moet u het:

- Stel een self-hosted integration runtime in (versie 3.17 of hoger). Zie [Een zelf gehoste nawerking van de integratie maken en configureren](create-self-hosted-integration-runtime.md)voor meer informatie.

- Download de 64-bits [SAP Connector voor Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) van de website van SAP en installeer deze op de zelf gehoste runtime-machine voor integratie. Controleer tijdens de installatie of u de optie **Assemblages installeren op GAC** selecteert in het venster **Optionele installatiestappen.**

  ![SAP-connector voor .NET installeren](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- De SAP-gebruiker die wordt gebruikt in de SAP-tabelconnector gegevensfabriek, moet de volgende machtigingen hebben:

  - Autorisatie voor het gebruik van RFC-bestemmingen (Remote Function Call).
  - Machtigingen voor de activiteit Uitvoeren van het S_SDSAUTH autorisatieobject.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om de gegevensfabriekentiteiten te definiëren die specifiek zijn voor de SAP-tabelconnector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de SAP BW Open Hub gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| `type` | De `type` accommodatie moet `SapTable`worden ingesteld op . | Ja |
| `server` | De naam van de server waarop de SAP-instantie zich bevindt.<br/>Met deze functie u verbinding maken met een SAP-toepassingsserver. | Nee |
| `systemNumber` | Het systeemnummer van het SAP-systeem.<br/>Met deze functie u verbinding maken met een SAP-toepassingsserver.<br/>Toegestane waarde: een tweecijferig decimaal getal dat wordt weergegeven als een tekenreeks. | Nee |
| `messageServer` | De hostnaam van de SAP-berichtenserver.<br/>Maak verbinding met een SAP-berichtenserver. | Nee |
| `messageServerService` | De servicenaam of het poortnummer van de berichtenserver.<br/>Maak verbinding met een SAP-berichtenserver. | Nee |
| `systemId` | De ID van het SAP-systeem waar de tabel zich bevindt.<br/>Maak verbinding met een SAP-berichtenserver. | Nee |
| `logonGroup` | De aanmeldingsgroep voor het SAP-systeem.<br/>Maak verbinding met een SAP-berichtenserver. | Nee |
| `clientId` | De ID van de client in het SAP-systeem.<br/>Toegestane waarde: een driecijferig decimaal getal dat wordt weergegeven als een tekenreeks. | Ja |
| `language` | De taal die het SAP-systeem gebruikt.<br/>Standaardwaarde `EN`is .| Nee |
| `userName` | De naam van de gebruiker die toegang heeft tot de SAP-server. | Ja |
| `password` | Het wachtwoord voor de gebruiker. Markeer dit `SecureString` veld met het type om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |
| `sncMode` | De SNC-activeringsindicator voor toegang tot de SAP-server waar de tabel zich bevindt.<br/>Gebruik als u SNC wilt gebruiken om verbinding te maken met de SAP-server.<br/>Toegestane waarden `0` zijn (uitgeschakeld, de `1` standaard) of (aan). | Nee |
| `sncMyName` | De SNC-naam van de initiator om toegang te krijgen tot de SAP-server waar de tabel zich bevindt.<br/>Geldt `sncMode` wanneer dit is ingeschakeld. | Nee |
| `sncPartnerName` | De SNC-naam van de communicatiepartner om toegang te krijgen tot de SAP-server waar de tabel zich bevindt.<br/>Geldt `sncMode` wanneer dit is ingeschakeld. | Nee |
| `sncLibraryPath` | De bibliotheek van het externe beveiligingsproduct om toegang te krijgen tot de SAP-server waar de tabel zich bevindt.<br/>Geldt `sncMode` wanneer dit is ingeschakeld. | Nee |
| `sncQop` | Het SNC Quality of Protection-niveau dat van toepassing is.<br/>Geldt `sncMode` wanneer is ingeschakeld. <br/>Toegestane waarden `1` zijn (Verificatie), `2` `3` (Integriteit), `8` (Privacy), (Standaard), `9` (Maximum). | Nee |
| `connectVia` | De [inburgeringsruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Een self-hosted integratie runtime is vereist, zoals eerder vermeld in [Voorwaarden](#prerequisites). |Ja |

**Voorbeeld 1: Verbinding maken met een SAP-toepassingsserver**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
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

### <a name="example-2-connect-to-an-sap-message-server"></a>Voorbeeld 2: Verbinding maken met een SAP-berichtenserver

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>Voorbeeld 3: Verbinding maken met SNC

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie [Gegevenssets](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen voor het definiëren van gegevenssets. In de volgende sectie vindt u een lijst met de eigenschappen die worden ondersteund door de gegevensset SAP-tabel.

Als u gegevens van en naar de SAP BW Open Hub-gekoppelde service wilt kopiëren, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| `type` | De `type` accommodatie moet `SapTableResource`worden ingesteld op . | Ja |
| `tableName` | De naam van de SAP-tabel om gegevens van te kopiëren. | Ja |

### <a name="example"></a>Voorbeeld

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie [Pijplijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen voor het definiëren van activiteiten. In de volgende sectie vindt u een lijst met de eigenschappen die worden ondersteund door de SAP-tabelbron.

### <a name="sap-table-as-source"></a>SAP-tabel als bron

Als u gegevens uit een SAP-tabel wilt kopiëren, worden de volgende eigenschappen ondersteund:

| Eigenschap                         | Beschrijving                                                  | Vereist |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | De `type` accommodatie moet `SapTableSource`worden ingesteld op .         | Ja      |
| `rowCount`                         | Het aantal rijen dat moet worden opgehaald.                              | Nee       |
| `rfcTableFields`                   | De velden (kolommen) die u wilt kopiëren vanuit de SAP-tabel. Bijvoorbeeld `column0, column1`. | Nee       |
| `rfcTableOptions`                  | De opties om de rijen in een SAP-tabel te filteren. Bijvoorbeeld `COLUMN0 EQ 'SOMEVALUE'`. Zie ook de tabel met de SAP-queryoperator later in dit artikel. | Nee       |
| `customRfcReadTableFunctionModule` | Een aangepaste RFC-functiemodule die kan worden gebruikt om gegevens uit een SAP-tabel te lezen.<br>U een aangepaste RFC-functiemodule gebruiken om te bepalen hoe de gegevens uit uw SAP-systeem worden opgehaald en naar Gegevensfabriek worden teruggestuurd. De aangepaste functiemodule moet een interface hebben geïmplementeerd (importeren, exporteren, tabellen) die vergelijkbaar `/SAPDS/RFC_READ_TABLE2`is met , wat de standaardinterface is die wordt gebruikt door Data Factory. | Nee       |
| `partitionOption`                  | Het partitiemechanisme om uit een SAP-tabel te lezen. De ondersteunde opties zijn onder andere: <ul><li>`None`</li><li>`PartitionOnInt`(normale gehele getal- of gehele waarden met `0000012345`nul vulling aan de linkerkant, zoals )</li><li>`PartitionOnCalendarYear`(4 cijfers in het formaat "YYYY")</li><li>`PartitionOnCalendarMonth`(6 cijfers in het formaat "YYYYMM")</li><li>`PartitionOnCalendarDate`(8 cijfers in het formaat "YYYYMMDD")</li></ul> | Nee       |
| `partitionColumnName`              | De naam van de kolom die wordt gebruikt om de gegevens te partitioneren.                | Nee       |
| `partitionUpperBound`              | De maximale waarde van `partitionColumnName` de kolom die daarin is opgegeven, wordt gebruikt om verder te gaan met partitioneren. | Nee       |
| `partitionLowerBound`              | De minimumwaarde van de `partitionColumnName` kolom die daarin is opgegeven, wordt gebruikt om verder te gaan met partitioneren. (Opmerking: `partitionLowerBound` kan niet "0" `PartitionOnInt`zijn wanneer partitieoptie is) | Nee       |
| `maxPartitionsNumber`              | Het maximum aantal partities om de gegevens in te splitsen.     | Nee       |

>[!TIP]
>Als uw SAP-tabel een groot volume aan gegevens `partitionOption` heeft, zoals enkele miljardenrijen, gebruikt en `partitionSetting` splitst u de gegevens op in kleinere partities. In dit geval worden de gegevens per partitie gelezen en wordt elke gegevenspartitie via één RFC-gesprek van uw SAP-server opgehaald.<br/>
<br/>
>`partitionOnInt` Als `partitionOption` voorbeeld wordt het aantal rijen in elke partitie berekend met deze `partitionUpperBound` formule: (totale rijen die tussen en `partitionLowerBound`)/`maxPartitionsNumber`vallen.<br/>
<br/>
>Als u gegevenspartities parallel wilt laden om de kopie [`parallelCopies`](copy-activity-performance.md#parallel-copy) te versnellen, wordt de parallelle graad gecontroleerd door de instelling voor de kopieeractiviteit. Als u bijvoorbeeld `parallelCopies` op vier instelt, genereert en voert Data Factory tegelijkertijd vier query's uit op basis van de opgegeven partitieoptie en -instellingen en haalt elke query een gedeelte gegevens uit uw SAP-tabel op. Wij raden `maxPartitionsNumber` ten zeerste aan `parallelCopies` om een veelvoud van de waarde van het pand te maken. Bij het kopiëren van gegevens naar het gegevensarchief in bestanden, wordt het ook aanbevolen om naar een map te schrijven als meerdere bestanden (geef alleen de naam van de map op), in welk geval de prestaties beter zijn dan het schrijven naar één bestand.

In `rfcTableOptions`u de volgende algemene SAP-queryoperatoren gebruiken om de rijen te filteren:

| Operator | Beschrijving |
| :------- | :------- |
| `EQ` | Gelijk aan |
| `NE` | Niet gelijk aan |
| `LT` | Kleiner dan |
| `LE` | Kleiner dan of gelijk aan |
| `GT` | Groter dan |
| `GE` | Groter dan of gelijk aan |
| `LIKE` | Zoals in`LIKE 'Emma%'` |

### <a name="example"></a>Voorbeeld

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Toewijzingen van gegevenstypen voor een SAP-tabel

Wanneer u gegevens uit een SAP-tabel kopieert, worden de volgende toewijzingen gebruikt van de gegevenstypen van de SAP-tabel naar de tijdelijke gegevenstypen van Azure Data Factory. Zie [Schema en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de kopieeractiviteit het bronschema en het gegevenstype aan de gootsteen toebrengt.

| SAP ABAP-type | Tussentijds gegevenstype Data Factory |
|:--- |:--- |
| `C`(Tekenreeks) | `String` |
| `I`(Geheel getal) | `Int32` |
| `F`(Float) | `Double` |
| `D`(Datum) | `String` |
| `T`(Tijd) | `String` |
| `P`(BCD Packed, Valuta, Decimal, Qty) | `Decimal` |
| `N`(Numeriek) | `String` |
| `X`(Binair en Rauw) | `String` |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Volgende stappen

Zie [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met de gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
