---
title: Gegevens van SAP HANA kopiëren
description: Meer informatie over het kopiëren van gegevens van SAP HANA naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/17/2020
ms.openlocfilehash: e1a3ff32956e8a8530684ba7f300f06d0c032227
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421116"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Gegevens van SAP HANA kopiëren met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-sap-hana-connector.md)
> * [Huidige versie](connector-sap-hana.md)

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens uit een SAP HANA-database te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

>[!TIP]
>Zie [SAP-gegevensintegratie met azure data factory-whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) met gedetailleerde introductie, vergelijking en richtlijnen voor de algemene ondersteuning van ADF voor de algemene ondersteuning van sap-gegevensintegratie.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze SAP HANA-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens uit de SAP HANA-database kopiëren naar elk ondersteund sinkdataarchief. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die worden ondersteund als bronnen/sinks door de kopieeractiviteit.

Specifiek ondersteunt deze SAP HANA connector:

- Gegevens kopiëren uit elke versie van de SAP HANA-database.
- Gegevens kopiëren van **HANA-informatiemodellen** (zoals analytische en berekeningsweergaven) en **rij-/kolomtabellen**.
- Gegevens kopiëren met **basis-** of **Windows-verificatie.**
- Parallel kopiëren vanuit een SAP HANA bron. Zie de [Parallel-kopie van SAP HANA-sectie](#parallel-copy-from-sap-hana) voor meer informatie.

> [!TIP]
> Als u gegevens wilt kopiëren **naar** SAP HANA-gegevensarchief, gebruikt u een generieke ODBC-connector. Zie [SAP HANA zinken](connector-odbc.md#sap-hana-sink) met details. Let op: de gekoppelde services voor SAP HANA-connector en ODBC-connector zijn van verschillend type en kunnen dus niet opnieuw worden gebruikt.

## <a name="prerequisites"></a>Vereisten

Als u deze SAP HANA-connector wilt gebruiken, moet u:

- Stel een Self-hosted Integration Runtime in. Zie [artikel Self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) voor meer informatie.
- Installeer het SAP HANA ODBC-stuurprogramma op de Integration Runtime-machine. U kunt het SAP HANA ODBC-stuurprogramma downloaden via het [SAP Software Download Center](https://support.sap.com/swdc). Zoeken met het zoekwoord **SAP HANA CLIENT voor Windows**.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor sap HANA-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor SAP HANA-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | Het type eigenschap moet worden ingesteld op: **SapHana** | Ja |
| Connectionstring | Geef informatie op die nodig is om verbinding te maken met de SAP HANA met behulp van **basisverificatie** of **Windows-verificatie.** Raadpleeg de volgende voorbeelden.<br>In verbindingstekenreeks is server/poort verplicht (standaardpoort is 30015) en gebruikersnaam en wachtwoord zijn verplicht bij het gebruik van basisverificatie. Raadpleeg voor aanvullende geavanceerde instellingen [SAP HANA ODBC Connection Properties](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>U ook wachtwoord in Azure Key Vault plaatsen en de wachtwoordconfiguratie uit de verbindingstekenreeks halen. Raadpleeg [storereferenties in het Azure Key Vault-artikel](store-credentials-in-key-vault.md) met meer details. | Ja |
| userName | Geef de gebruikersnaam op bij het gebruik van Windows-verificatie. Voorbeeld: `user@domain.com` | Nee |
| wachtwoord | Geef het wachtwoord voor het gebruikersaccount op. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Nee |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Een Self-hosted Integration Runtime is vereist zoals vermeld in [Voorwaarden](#prerequisites). |Ja |

**Voorbeeld: basisverificatie gebruiken**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voorbeeld: Windows-verificatie gebruiken**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
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

Als u sap HANA gekoppelde service met de volgende payload, het is nog steeds ondersteund as-is, terwijl u wordt voorgesteld om de nieuwe te gebruiken in de toekomst.

**Voorbeeld:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de SAP HANA-gegevensset.

Als u gegevens van SAP HANA wilt kopiëren, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **SapHanaTable** | Ja |
| schema | Naam van het schema in de SAP HANA-database. | Nee (als 'query' in activiteitsbron is opgegeven) |
| tabel | Naam van de tabel in de SAP HANA-database. | Nee (als 'query' in activiteitsbron is opgegeven) |

**Voorbeeld:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Als u `RelationalTable` getypte gegevensset gebruikt, wordt deze nog steeds ondersteund als deze is, terwijl u wordt voorgesteld om de nieuwe in de toekomst te gebruiken.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door SAP HANA-bron.

### <a name="sap-hana-as-source"></a>SAP HANA als bron

>[!TIP]
>Als u gegevens van SAP HANA efficiënt wilt innemen door gebruik te maken van gegevenspartitionering, u meer leren van [Parallel copy van SAP HANA](#parallel-copy-from-sap-hana) sectie.

Als u gegevens van SAP HANA wilt kopiëren, worden de volgende eigenschappen ondersteund in de **sectie** bron van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op: **SapHanaSource** | Ja |
| query | Hiermee geeft u de SQL-query op om gegevens uit de SAP HANA-instantie te lezen. | Ja |
| partitieOpties | Hiermee geeft u de opties voor gegevenspartitionering op die worden gebruikt om gegevens van SAP HANA in te nemen. Lees meer van [Parallel copy van SAP HANA](#parallel-copy-from-sap-hana) sectie.<br>Waarden toestaan zijn: **Geen** (standaard), **PhysicalPartitionsOfTable**, **SapHanaDynamicRange**. Lees meer van [Parallel copy van SAP HANA](#parallel-copy-from-sap-hana) sectie. `PhysicalPartitionsOfTable`kan alleen worden gebruikt bij het kopiëren van gegevens uit een tabel, maar niet query. <br>Wanneer een partitieoptie is ingeschakeld (dat wil zeggen, niet), `None`wordt de mate van parallellisme om gelijktijdig gegevens van SAP HANA te laden, gecontroleerd door de [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) instelling voor de kopieeractiviteit. | False |
| partitieInstellingen | Geef de groep van de instellingen voor gegevenspartitionering op.<br>Toepassen wanneer partitieoptie is `SapHanaDynamicRange`. | False |
| partitionColumnName | Geef de naam op van de bronkolom die door partitie wordt gebruikt voor parallelle kopie. Als dit niet is opgegeven, wordt de index of de primaire sleutel van de tabel automatisch gedetecteerd en gebruikt als partitiekolom.<br>Toepassen wanneer de `SapHanaDynamicRange`partitieoptie is . Als u een query gebruikt om `?AdfHanaDynamicRangePartitionCondition` de brongegevens op te halen, haakt u de WHERE-clausule aan. Zie voorbeeld in [Parallelle kopie van sap HANA](#parallel-copy-from-sap-hana) sectie. | Ja bij `SapHanaDynamicRange` het gebruik van partitie. |
| packetSize | Hiermee geeft u de grootte van het netwerkpakket (in Kilobytes) op om gegevens in meerdere blokken te splitsen. Als u een grote hoeveelheid gegevens moet kopiëren, kan het vergroten van de pakketgrootte in de meeste gevallen de leessnelheid van SAP HANA verhogen. Prestatietests worden aanbevolen bij het aanpassen van de pakketgrootte. | Nee.<br>Standaardwaarde is 2048 (2 MB). |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Als u `RelationalSource` getypte kopiebron gebruikt, wordt deze nog steeds ondersteund als is, terwijl u wordt voorgesteld om de nieuwe in de toekomst te gebruiken.

## <a name="parallel-copy-from-sap-hana"></a>Parallelle kopie van SAP HANA

De Data Factory SAP HANA-connector biedt ingebouwde gegevenspartitionering om gegevens van SAP HANA parallel te kopiëren. U opties voor gegevenspartitionering vinden in de **brontabel** van de kopieeractiviteit.

![Schermafbeelding van partitieopties](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

Wanneer u gepartitioneerde kopie inschakelt, voert Data Factory parallelle query's uit tegen uw SAP HANA-bron om gegevens op te halen door partities. De parallelle graad wordt [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) gecontroleerd door de instelling voor de kopieeractiviteit. Als u bijvoorbeeld `parallelCopies` op vier instelt, genereert en voert Data Factory tegelijkertijd vier query's uit op basis van de opgegeven partitieoptie en -instellingen en haalt elke query een deel van de gegevens op uit uw SAP HANA.

U wordt voorgesteld om parallelle kopiëren met gegevenspartitionering in te schakelen, vooral wanneer u grote hoeveelheden gegevens van uw SAP HANA inneemt. De volgende zijn voorgestelde configuraties voor verschillende scenario's. Bij het kopiëren van gegevens naar het gegevensarchief in bestanden, wordt aanbevolen om naar een map te schrijven als meerdere bestanden (geef alleen de naam van de map op), in welk geval de prestaties beter zijn dan naar één bestand te schrijven.

| Scenario                                           | Aanbevolen instellingen                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Volledige lading van grote tafel.                        | **Partitieoptie**: Fysieke partities van tabel. <br><br/>Tijdens de uitvoering detecteert Data Factory automatisch het fysieke partitietype van de opgegeven SAP HANA-tabel en kiest u de bijbehorende partitiestrategie:<br>- **Bereikpartitionering**: Haal de partitiekolom en partitiebereiken gedefinieerd voor de tabel en kopieer de gegevens op bereik. <br>- **Hash Partitioning**: Gebruik hash partitiesleutel als partitiekolom, vervolgens partitie en kopieer de gegevens op basis van ADF berekende bereiken. <br>- **Round-Robin Partitioning** or **No Partition**: Gebruik de primaire sleutel als partitiekolom en partitie en kopieer de gegevens op basis van ADF berekende bereiken. |
| Laad grote hoeveelheid gegevens met behulp van een aangepaste query. | **Partitieoptie**: Dynamische bereikpartitie.<br>**Query** `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`: .<br>**Partitiekolom**: Geef de kolom op die wordt gebruikt om de dynamische bereikpartitie toe te passen. <br><br>Tijdens de uitvoering berekent Data Factory eerst de waardebereiken van de opgegeven partitiekolom, door de rijen gelijkmatig te verdelen in een aantal buckets op basis van het aantal afzonderlijke partitiekolomwaarden en adf-parallelle kopieerinstelling, vervangt `?AdfHanaDynamicRangePartitionCondition` vervolgens het waardebereik van de partitiekolom voor elke partitie en verzendt naar SAP HANA.<br><br>Als u meerdere kolommen als partitiekolom wilt gebruiken, u de waarden van elke kolom als één kolom in `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition`de query als één kolom en deze opgeven als partitiekolom in ADF, zoals. |

**Voorbeeld: query met fysieke partities van een tabel**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Voorbeeld: query met dynamische bereikpartitie**

```json
"source": {
    "type": "SapHanaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "SapHanaDynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<Partition_column_name>"
    }
}
```

## <a name="data-type-mapping-for-sap-hana"></a>Gegevenstypetoewijzing voor SAP HANA

Bij het kopiëren van gegevens van SAP HANA worden de volgende toewijzingen gebruikt van SAP HANA-gegevenstypen naar tijdelijke gegevenstypen van Azure Data Factory. Zie [Schema- en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe kopieeractiviteit het bronschema en het gegevenstype naar de gootsteen brengt.

| SAP HANA-gegevenstype | Tussentijds gegevenstype gegevensfabriek |
| ------------------ | ------------------------------ |
| ALFAGETAL           | Tekenreeks                         |
| Bigint             | Int64                          |
| Binaire             | Byte                         |
| BINTEXT            | Tekenreeks                         |
| Blob               | Byte                         |
| Bool               | Byte                           |
| CLOB (CLOB)               | Tekenreeks                         |
| DATE               | DateTime                       |
| Decimaal            | Decimal                        |
| Dubbele             | Double                         |
| Float              | Double                         |
| INTEGER            | Int32                          |
| NCLOB (NCLOB)              | Tekenreeks                         |
| Nvarchar           | Tekenreeks                         |
| REAL               | Enkel                         |
| TWEEDEDATUM         | DateTime                       |
| KORTETEKST          | Tekenreeks                         |
| SMALLDECIMAL       | Decimal                        |
| Smallint           | Int16                          |
| STGEOMETRYTYPE     | Byte                         |
| STPOINTTYPE        | Byte                         |
| TEXT               | Tekenreeks                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| Varchar            | Tekenreeks                         |
| Tijdstempel          | DateTime                       |
| VARBINARY (VARBINARY)          | Byte                         |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
