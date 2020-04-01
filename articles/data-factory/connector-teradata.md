---
title: Gegevens van Teradata Vantage kopiëren met Azure Data Factory
description: Met de Teradata Connector van de Data Factory-service u gegevens van een Teradata Vantage kopiëren naar gegevensopslag die door Data Factory worden ondersteund als putten.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: jingwang
ms.openlocfilehash: 1e1d7cc4bb7762d3ebd29e349467f3e33c0887f9
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421221"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Gegevens van Teradata Vantage kopiëren met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
>
> * [Versie 1](v1/data-factory-onprem-teradata-connector.md)
> * [Huidige versie](connector-teradata.md)

In dit artikel wordt beschreven hoe u de kopieeractiviteit in Azure Data Factory gebruiken om gegevens van Teradata Vantage te kopiëren. Het bouwt voort op het [overzicht van de kopieeractiviteit](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Teradata-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens van Teradata Vantage kopiëren naar elk ondersteund sink data store. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Met name deze Teradata-connector ondersteunt:

- Teradata **versie 14.10, 15.0, 15.10, 16.0, 16.10 en 16.20**.
- Gegevens kopiëren met basis- of **Windows-verificatie.** **Windows**
- Parallel kopiëren vanuit een Teradata-bron. Zie de [parallel kopie van Teradata](#parallel-copy-from-teradata) sectie voor meer informatie.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Als u Self-hosted Integration Runtime gebruikt, wordt er rekening mee gehouden dat het een ingebouwd Teradata-stuurprogramma biedt vanaf versie 3.18. U hoeft geen stuurprogramma handmatig te installeren. Het stuurprogramma vereist "Visual C++ Redistributable 2012 Update 4" op de zelf gehoste runtime machine voor integratie. Als u nog niet hebt geïnstalleerd, download het vanaf [hier](https://www.microsoft.com/en-sg/download/details.aspx?id=30679).

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor de Teradata-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De teradata gekoppelde service ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **Teradata**. | Ja |
| Connectionstring | Hiermee geeft u de informatie op die nodig is om verbinding te maken met het instantie-instantie Teradata. Raadpleeg de volgende voorbeelden.<br/>U ook een wachtwoord in Azure `password` Key Vault plaatsen en de configuratie uit de verbindingstekenreeks halen. Raadpleeg [storereferenties in Azure Key Vault](store-credentials-in-key-vault.md) met meer details. | Ja |
| gebruikersnaam | Geef een gebruikersnaam op om verbinding te maken met Teradata. Geldt wanneer u Windows-verificatie gebruikt. | Nee |
| wachtwoord | Geef een wachtwoord op voor het gebruikersaccount dat u hebt opgegeven voor de gebruikersnaam. U er ook voor kiezen om [te verwijzen naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) <br>Geldt wanneer u Windows-verificatie gebruikt of verwijst naar een wachtwoord in Key Vault voor basisverificatie. | Nee |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Meer informatie van de sectie [Voorwaarden.](#prerequisites) Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

Meer verbindingseigenschappen die u per aanvraag instellen in verbindingstekenreeks:

| Eigenschap | Beschrijving | Standaardwaarde |
|:--- |:--- |:--- |
| Tekenset | Het teken dat voor de sessie wordt gebruikt. B.v., `CharacterSet=UTF16`.<br><br/>Deze waarde kan een door de gebruiker gedefinieerde tekenset zijn of een van de volgende vooraf gedefinieerde tekensets: <br/>- ASCII<br/>- UTF8<br/>- UTF16<br/>- LATIN1252_0A<br/>- LATIN9_0A<br/>- LATIN1_0A<br/>- Shift-JIS (Windows, DOS compatibel, KANJISJIS_0S)<br/>- EUC (Unix compatible, KANJIEC_0U)<br/>- IBM Mainframe (KANJIEBCDIC5035_0I)<br/>- KANJI932_1S0<br/>- BIG5 (TCHBIG5_1R0)<br/>- GB (SCHGB2312_1T0)<br/>- SCHINESE936_6R0<br/>- TCHINESE950_8R0<br/>- NetworkKorean (HANGULKSC5601_2R4)<br/>- HANGUL949_7R0<br/>- ARABIC1256_6A0<br/>- CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | Standaardwaarde `ASCII`is . |
| MaxRespSize (Max. |De maximale grootte van de antwoordbuffer voor SQL-aanvragen, in kilobytes (KB's). B.v., `MaxRespSize=‭10485760‬`.<br/><br/>Voor Teradata Database versie 16.00 of hoger is de maximale waarde 7361536. Voor verbindingen die eerdere versies gebruiken, is de maximale waarde 1048576. | Standaardwaarde `65536`is . |

**Voorbeeld van basisverificatie**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voorbeeld van Windows-verificatie**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> De volgende payload wordt nog steeds ondersteund. In de toekomst moet u echter de nieuwe gebruiken.

**Vorige payload:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
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

In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de gegevensset Teradata. Zie [Gegevenssets](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets.

Als u gegevens uit Teradata wilt kopiëren, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de `TeradataTable`gegevensset moet worden ingesteld op . | Ja |
| database | De naam van het exemplaar Teradata. | Nee (als 'query' in activiteitsbron is opgegeven) |
| tabel | De naam van de tabel in het exemplaar Teradata. | Nee (als 'query' in activiteitsbron is opgegeven) |

**Voorbeeld:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable`typegegevensset wordt nog steeds ondersteund. We raden u echter aan de nieuwe gegevensset te gebruiken.

**Vorige payload:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Teradata-bron. Zie [Pijplijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. 

### <a name="teradata-as-source"></a>Teradata als bron

>[!TIP]
>Als u gegevens van Teradata efficiënt wilt laden door gebruik te maken van gegevenspartitionering, leest u meer over parallelle kopie van de sectie [Teradata.](#parallel-copy-from-teradata)

Als u gegevens uit Teradata wilt kopiëren, worden de volgende eigenschappen ondersteund in de **sectie** bron van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron `TeradataSource`van de kopieeractiviteit moet worden ingesteld op . | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Een voorbeeld is `"SELECT * FROM MyTable"`.<br>Wanneer u partitioned load inschakelt, moet u alle bijbehorende ingebouwde partitieparameters in uw query aansluiten. Zie bijvoorbeeld de [sectie Parallelle kopie van Teradata.](#parallel-copy-from-teradata) | Nee (als de tabel in de gegevensset is opgegeven) |
| partitieOpties | Hiermee geeft u de opties voor gegevenspartitionering op die worden gebruikt om gegevens van Teradata te laden. <br>Waarden toestaan zijn: **Geen** (standaard), **Hash** en **DynamicRange**.<br>Wanneer een partitieoptie is ingeschakeld (dat wil zeggen, niet), `None`wordt de mate van parallellisme om tegelijkertijd gegevens van Teradata te laden, gecontroleerd door de [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) instelling voor de kopieeractiviteit. | Nee |
| partitieInstellingen | Geef de groep van de instellingen voor gegevenspartitionering op. <br>Toepassen wanneer de partitieoptie niet `None`is . | Nee |
| partitionColumnName | Geef de naam op van de bronkolom die wordt gebruikt door bereikpartitie of Hash-partitie voor parallelle kopie. Als dit niet is opgegeven, wordt de primaire index van de tabel automatisch gedetecteerd en gebruikt als partitiekolom. <br>Toepassen wanneer de `Hash` partitieoptie is of `DynamicRange`. Als u een query gebruikt om `?AdfHashPartitionCondition` de `?AdfRangePartitionColumnName` brongegevens, haak of in WHERE-clausule op te halen. Zie voorbeeld in Parallelle kopie van de sectie [Teradata.](#parallel-copy-from-teradata) | Nee |
| partitieUpperBound | De maximale waarde van de partitiekolom om gegevens uit te kopiëren. <br>Toepassen wanneer partitieoptie is `DynamicRange`. Als u query's gebruikt `?AdfRangePartitionUpbound` om brongegevens op te halen, haakt u de WHERE-component aan. Zie bijvoorbeeld de [sectie Parallel van Teradata.](#parallel-copy-from-teradata) | Nee |
| partitionLowerBound | De minimumwaarde van de partitiekolom om gegevens uit te kopiëren. <br>Toepassen wanneer de `DynamicRange`partitieoptie is . Als u een query gebruikt om `?AdfRangePartitionLowbound` de brongegevens op te halen, haakt u de WHERE-component aan. Zie bijvoorbeeld de [sectie Parallel van Teradata.](#parallel-copy-from-teradata) | Nee |

> [!NOTE]
>
> `RelationalSource`type kopiebron wordt nog steeds ondersteund, maar ondersteunt niet de nieuwe ingebouwde parallelle belasting van Teradata (partitieopties). We raden u echter aan de nieuwe gegevensset te gebruiken.

**Voorbeeld: gegevens kopiëren met behulp van een basisquery zonder partitie**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Parallelle kopie van Teradata

De Data Factory Teradata-connector biedt ingebouwde gegevenspartitionering om gegevens van Teradata parallel te kopiëren. U opties voor gegevenspartitionering vinden in de **brontabel** van de kopieeractiviteit.

![Schermafbeelding van partitieopties](./media/connector-teradata/connector-teradata-partition-options.png)

Wanneer u gepartitioneerde kopie inschakelt, voert Data Factory parallelle query's uit op uw Teradata-bron om gegevens te laden door partities. De parallelle graad wordt [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) gecontroleerd door de instelling voor de kopieeractiviteit. Als u bijvoorbeeld `parallelCopies` op vier instelt, genereert en voert Data Factory tegelijkertijd vier query's uit op basis van de opgegeven partitieoptie en -instellingen en haalt elke query een gedeelte gegevens op uit uw Teradata.

U wordt voorgesteld om parallelle kopiëren met gegevenspartitionering in te schakelen, vooral wanneer u grote hoeveelheden gegevens van uw Teradata laadt. De volgende zijn voorgestelde configuraties voor verschillende scenario's. Bij het kopiëren van gegevens naar het gegevensarchief in bestanden wordt aanbevolen om naar een map te schrijven als meerdere bestanden (geef alleen de naam van de map op), in welk geval de prestaties beter zijn dan naar één bestand te schrijven.

| Scenario                                                     | Aanbevolen instellingen                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Volledige lading van grote tafel.                                   | **Partitieoptie**: Hash. <br><br/>Tijdens de uitvoering detecteert Data Factory automatisch de PK-kolom, past een hash tegen deze code toe en kopieert gegevens door partities. |
| Laad grote hoeveelheid gegevens met behulp van een aangepaste query.                 | **Partitieoptie**: Hash.<br>**Query** `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`: .<br>**Partitiekolom**: Geef de kolom op die wordt gebruikt voor het toepassen van hashpartitie. Als dit niet is opgegeven, detecteert Data Factory automatisch de PK-kolom van de tabel die u hebt opgegeven in de gegevensset Teradata.<br><br>Tijdens de uitvoering `?AdfHashPartitionCondition` vervangt Data Factory de hashpartitielogica en verzendt naar Teradata. |
| Laad grote hoeveelheid gegevens met behulp van een aangepaste query, met een gehele kolom met gelijkmatig verdeelde waarde voor bereikverdeling. | **Partitieopties**: Dynamische bereikpartitie.<br>**Query** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`: .<br>**Partitiekolom**: Geef de kolom op die wordt gebruikt om gegevens te partitioneren. U partitioneren tegen de kolom met integer gegevenstype.<br>**Hoofdgrens** en **partitie ondergrens**: Geef op als u wilt filteren op de partitiekolom om alleen gegevens op te halen tussen het onderste en bovenste bereik.<br><br>Tijdens de uitvoering `?AdfRangePartitionColumnName`vervangt `?AdfRangePartitionUpbound`Data `?AdfRangePartitionLowbound` Factory , en met de werkelijke kolomnaam en waardebereiken voor elke partitie, en verzendt naar Teradata. <br>Als uw partitiekolom 'ID' bijvoorbeeld is ingesteld met de ondergrens als 1 en de bovengrens als 80, met parallelle kopie ingesteld als 4, haalt Data Factory gegevens op met 4 partities. Hun ID's liggen tussen [1,20], [21, 40], [41, 60] en [61, 80], respectievelijk. |

**Voorbeeld: query met hashpartitie**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Voorbeeld: query met dynamische bereikpartitie**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Gegevenstypetoewijzing voor Teradata

Wanneer u gegevens uit Teradata kopieert, zijn de volgende toewijzingen van toepassing. Zie [Schema en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de kopieeractiviteit het bronschema en het gegevenstype aan de gootsteen toebrengt.

| Gegevenstype Teradata | Tussentijds gegevenstype Data Factory |
|:--- |:--- |
| Bigint |Int64 |
| Blob |Byte |
| Byte |Byte |
| ByteInt ByteInt |Int16 |
| Char |Tekenreeks |
| Clob Clob |Tekenreeks |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Grafische |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Geheel getal |Int32 |
| Intervaldag |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Interval van dag tot uur |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Interval van dag tot minuut |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Intervaldag naar tweede |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Intervaluur |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Interval uur tot minuut |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Intervaluur naar seconde |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Intervalminuten |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Interval minuut tot seconde |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Intervalmaand |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Interval Tweede |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Intervaljaar |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Interval jaar tot maand |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Aantal |Double |
| Periode (datum) |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Periode (Tijd) |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Periode (tijd met tijdzone) |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Periode (tijdstempel) |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Periode (tijdstempel met tijdzone) |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Smallint |Int16 |
| Time |TimeSpan |
| Tijd met tijdzone |TimeSpan |
| Tijdstempel |DateTime |
| Tijdstempel met tijdzone |DateTime |
| VarByte (VarByte) |Byte |
| Varchar |Tekenreeks |
| VarGrafisch |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |
| Xml |Wordt niet ondersteund. Expliciete cast toepassen in bronquery. |


## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Volgende stappen
Zie [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Data Factory.
