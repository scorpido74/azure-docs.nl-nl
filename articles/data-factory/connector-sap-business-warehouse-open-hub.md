---
title: Gegevens uit SAP Business Warehouse kopiëren via Open Hub
description: Meer informatie over het kopiëren van gegevens uit SAP Business Warehouse (BW) via Open Hub naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
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
ms.date: 03/24/2020
ms.openlocfilehash: ff3b4799f42e85ad3df62ef18469a26120ae3021
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418079"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Gegevens uit SAP Business Warehouse kopiëren via Open Hub met Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens uit een SAP Business Warehouse (BW) te kopiëren via Open Hub. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

>[!TIP]
>Zie [SAP-gegevensintegratie met azure data factory-whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) met gedetailleerde introductie, vergelijking en richtlijnen voor de algemene ondersteuning van ADF voor de algemene ondersteuning van sap-gegevensintegratie.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Dit SAP Business Warehouse via Open Hub connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens uit SAP Business Warehouse kopiëren via Open Hub naar elk ondersteund sink data store. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Met name deze SAP Business Warehouse Open Hub-connector ondersteunt:

- SAP Business Warehouse **versie 7.01 of hoger (in een recente SAP Support Package Stack uitgebracht na het jaar 2015)**.
- Het kopiëren van gegevens via de lokale tabel Open Hub Destination die hieronder dso, infocube, multiprovider, datasource, enz.
- Gegevens kopiëren met basisverificatie.
- Verbinding maken met application server.

## <a name="sap-bw-open-hub-integration"></a>SAP BW Open Hub Integratie 

[SAP BW Open Hub Service](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) is een efficiënte manier om gegevens uit SAP BW te halen. Het volgende diagram toont een van de typische stromen die klanten in hun SAP-systeem hebben, in welk geval gegevensstromen van SAP ECC-> PSA-> DSO-> Cube.

SAP BW Open Hub Destination (OHD) definieert het doel waaraan de SAP-gegevens worden doorgegeven. Alle objecten die worden ondersteund door SAP Data Transfer Process (DTP) kunnen worden gebruikt als open hub-gegevensbronnen, bijvoorbeeld DSO, InfoCube, DataSource, enz. Het type Hub-doel openen - waarbij de doorgegeven gegevens worden opgeslagen - kunnen databasetabellen (lokaal of extern) en platte bestanden zijn. Deze SAP BW Open Hub-connector ondersteunt het kopiëren van gegevens van ohd-lokale tabel in BW. Als u andere typen gebruikt, u rechtstreeks verbinding maken met de database of het bestandssysteem via andere connectors.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Delta-extractiestroom

ADF SAP BW Open Hub Connector `excludeLastRequest` `baseRequestId` biedt twee optionele eigenschappen: en die kunnen worden gebruikt om deltabelasting vanuit Open Hub te verwerken. 

- **excludeLastRequestId**: Of de records van de laatste aanvraag moeten worden uitgesloten. De standaardwaarde is waar. 
- **baseRequestId**: De ID van aanvraag voor deltaladen. Zodra deze is ingesteld, worden alleen gegevens met requestId opgehaald die groter zijn dan de waarde van deze eigenschap. 

Over het algemeen bestaat de extractie van SAP InfoProviders naar Azure Data Factory (ADF) uit 2 stappen: 

1. **SAP BW Data Transfer Process (DTP)** Met deze stap worden de gegevens van een SAP BW InfoProvider gekopieerd naar een SAP BW Open Hub-tabel 

1. **ADF-gegevenskopie** In deze stap wordt de open hub-tabel gelezen door de ADF-connector 

![Delta-extractiestroom](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

In de eerste stap wordt een DTP uitgevoerd. Elke uitvoering maakt een nieuwe SAP-aanvraag-id. De aanvraag-id wordt opgeslagen in de open hub-tabel en wordt vervolgens gebruikt door de ADF-connector om de delta te identificeren. De twee stappen worden asynchroon uitgevoerd: de DTP wordt geactiveerd door SAP en de ADF-gegevenskopie wordt geactiveerd via ADF. 

AdF leest standaard niet de nieuwste delta uit de Open Hub-tabel (optie 'Laatste aanvraag uitsluiten' is waar). Hierdoor zijn de gegevens in ADF niet 100% up-to-date met de gegevens in de Open Hub-tabel (de laatste delta ontbreekt). In ruil daarvoor zorgt deze procedure ervoor dat er geen rijen verloren gaan als gevolg van de asynchrone extractie. Het werkt prima, zelfs wanneer ADF is het lezen van de Open Hub tafel, terwijl de DTP is nog steeds schrijven in dezelfde tabel. 

U slaat doorgaans de maximaal gekopieerde aanvraag-id op in de laatste run door ADF in een faseringsgegevensarchief (zoals Azure Blob in bovendiagram). Daarom wordt dezelfde aanvraag in de volgende run geen tweede keer door ADF gelezen. Houd er ondertussen rekening mee dat de gegevens niet automatisch worden verwijderd uit de open hub-tabel.

Voor een goede deltaafhandeling is het niet toegestaan om aanvraag-id's van verschillende DTP's in dezelfde Open Hub-tabel te hebben. Daarom mag u niet meer dan één DTP maken voor elke Open Hub-bestemming (OHD). Wanneer u Volledige en Delta-extractie van dezelfde InfoProvider nodig hebt, moet u twee OHD's maken voor dezelfde InfoProvider. 

## <a name="prerequisites"></a>Vereisten

Als u deze SAP Business Warehouse Open Hub-connector wilt gebruiken, moet u het:

- Stel een Self-hosted Integration Runtime in met versie 3.13 of hoger. Zie [artikel Self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) voor meer informatie.

- Download de **64-bits [SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html) ** van de website van SAP en installeer deze op de zelfgehoste IR-machine. Controleer bij het installeren in het venster optionele installatiestappen of u de optie **Assemblages installeren op GAC** selecteert, zoals in de volgende afbeelding wordt weergegeven. 

    ![SAP .NET-connector installeren](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- SAP-gebruiker die wordt gebruikt in de Bw-connector van de gegevensfabriek, moet de volgende machtigingen hebben: 

    - Autorisatie voor RFC en SAP BW. 
    - Machtigingen voor de activiteit "Uitvoeren" van autorisatieobject "S_SDSAUTH".

- Sap Open Hub-doeltype maken als **databasetabel** met de optie 'Technische sleutel'.  Het wordt ook aanbevolen om de deletinggegevens van tabel als ongecontroleerd achter te laten, hoewel dit niet vereist is. Maak gebruik van de DTP (direct uitvoeren of integreren in bestaande procesketen) om gegevens uit het bronobject (zoals kubus) te landen die u hebt gekozen voor de open hub-doeltabel.

## <a name="getting-started"></a>Aan de slag

> [!TIP]
>
> Zie [Gegevens laden van SAP Business Warehouse (BW) voor](load-sap-bw-data.md)een walkthrough van het gebruik van SAP BW Open Hub-connector.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor de SAP Business Warehouse Open Hub-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor sap business warehouse open hub gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **SapOpenHub** | Ja |
| server | Naam van de server waarop de SAP BW-instantie zich bevindt. | Ja |
| systeemAantal | Systeemnummer van het SAP BW-systeem.<br/>Toegestane waarde: tweecijferig decimaal getal weergegeven als een tekenreeks. | Ja |
| clientId | Client-ID van de client in het SAP W-systeem.<br/>Toegestane waarde: driecijferig decimaal getal weergegeven als een tekenreeks. | Ja |
| language | Taal die het SAP-systeem gebruikt. | Nee (standaardwaarde is **NL)**|
| userName | Naam van de gebruiker die toegang heeft tot de SAP-server. | Ja |
| wachtwoord | Het wachtwoord voor de gebruiker. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Een Self-hosted Integration Runtime is vereist zoals vermeld in [Voorwaarden](#prerequisites). |Ja |

**Voorbeeld:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
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

Zie het artikel [Gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de SAP BW Open Hub-gegevensset.

Als u gegevens wilt kopiëren van en naar SAP BW Open Hub, stelt u de eigenschap type van de gegevensset in op **SapOpenHubTable**. De volgende eigenschappen worden ondersteund.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op **SapOpenHubTable**.  | Ja |
| openHubDestinationName | De naam van de Open Hub-bestemming om gegevens van te kopiëren. | Ja |

Als u `excludeLastRequest` instelling `baseRequestId` en in de gegevensset, het wordt nog steeds ondersteund as-is, terwijl u wordt voorgesteld om het nieuwe model te gebruiken in activiteit bron in de toekomst.

**Voorbeeld:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door SAP BW Open Hub-bron.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW Open Hub als bron

Als u gegevens uit SAP BW Open Hub wilt kopiëren, worden de volgende eigenschappen ondersteund in de **sectie** bron van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de bron van de kopieeractiviteit moet zijn ingesteld op **SapOpenHubSource**. | Ja |
| excludeLastRequest | Of de records van het laatste verzoek uit te sluiten. | Nee (standaard is **waar)** |
| baseRequestId | De ID van het verzoek voor delta laden. Zodra deze is ingesteld, worden alleen gegevens met requestId opgehaald die groter zijn **dan** de waarde van deze eigenschap.  | Nee |

>[!TIP]
>Als uw open hub-tabel alleen de gegevens bevat die worden gegenereerd door single request ID, bijvoorbeeld, u altijd de volledige belasting uitvoert en de bestaande gegevens in de tabel overschrijft, of als u de DTP slechts één keer uitvoert voor de test, vergeet dan niet de optie 'excludeLastRequest' uit te vinken om de gegevens uit te kopiëren.

Als u het laden van [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) gegevens wilt versnellen, u de kopieeractiviteit instellen om gegevens van SAP BW Open Hub parallel te laden. Als u bijvoorbeeld `parallelCopies` op vier instelt, voert Data Factory tegelijkertijd vier RFC-aanroepen uit en haalt elke RFC-oproep een deel van de gegevens op uit uw SAP BW Open Hub-tabel die is verdeeld door de DTP-aanvraag-id en pakket-ID. Dit geldt wanneer het aantal unieke DTP-aanvraag-ID + `parallelCopies`pakket-ID groter is dan de waarde van . Bij het kopiëren van gegevens naar het gegevensarchief in bestanden, wordt het ook aanbevolen om naar een map te schrijven als meerdere bestanden (geef alleen de naam van de map op), in welk geval de prestaties beter zijn dan het schrijven naar één bestand.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource",
                "excludeLastRequest": true
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Gegevenstypetoewijzing voor SAP BW Open Hub

Bij het kopiëren van gegevens van SAP BW Open Hub worden de volgende toewijzingen gebruikt van SAP BW-gegevenstypen naar tijdelijke gegevenstypen van Azure Data Factory. Zie [Schema- en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe kopieeractiviteit het bronschema en het gegevenstype naar de gootsteen brengt.

| SAP ABAP-type | Tussentijds gegevenstype gegevensfabriek |
|:--- |:--- |
| C (tekenreeks) | Tekenreeks |
| I (geheel getal) | Int32 |
| F (VLotter) | Double |
| D (datum) | Tekenreeks |
| T (Tijd) | Tekenreeks |
| P (BCD Packed, Valuta, Decimal, Qty) | Decimal |
| N (Numc) | Tekenreeks |
| X (binair en rauw) | Tekenreeks |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

**Symptomen:** Als u SAP BW op HANA uitvoert en alleen subset van gegevens observeert die wordt gekopieerd met behulp van ADF-kopieeractiviteit (1 miljoen rijen), is de mogelijke oorzaak dat u de optie "SAP HANA-uitvoering" in schakelt in uw DTP, in welk geval ADF alleen de eerste batch gegevens kan ophalen.

**Oplossing:** Schakel de optie "SAP HANA Execution" in DTP uit, verwerk de gegevens opnieuw en probeer de kopieeractiviteit opnieuw uit te voeren.

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
