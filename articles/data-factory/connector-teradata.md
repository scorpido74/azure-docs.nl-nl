---
title: Gegevens van Teradata-Vantage kopiëren met behulp van Azure Data Factory
description: Met de Teradata-connector van de Data Factory-service kunt u gegevens kopiëren van een Teradata-Vantage naar gegevens archieven die door Data Factory worden ondersteund als Sinks.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 5a41d5653de0d8a9f674009904756892ac343609
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358478"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Gegevens van Teradata-Vantage kopiëren met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
>
> * [Versie 1](v1/data-factory-onprem-teradata-connector.md)
> * [Huidige versie](connector-teradata.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit Teradata Vantage te kopiëren. Het is gebaseerd op het overzicht van de [Kopieer activiteit](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Teradata-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van Teradata-Vantage kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Deze Teradata-connector ondersteunt met name:

- Teradata- **versie 14,10, 15,0, 15,10, 16,0, 16,10 en 16,20**.
- Kopiëren van gegevens met behulp van **basis** -of **Windows** -verificatie.
- Parallelle kopieën van een Teradata-bron. Zie de sectie [parallelle kopie van Teradata](#parallel-copy-from-teradata) voor meer informatie.

> [!NOTE]
>
> Na de release van zelf-hostende Integration runtime v 3.18, Azure Data Factory de Teradata-connector bijgewerkt. Een bestaande werk belasting die gebruikmaakt van de vorige Teradata-connector, wordt nog steeds ondersteund. Voor nieuwe workloads is het echter een goed idee om het nieuwe werk belasting te gebruiken. Houd er rekening mee dat voor het nieuwe pad een andere set gekoppelde service, gegevensset en kopieer bron is vereist. Zie de volgende secties voor meer informatie over de configuratie.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration runtime biedt een ingebouwd Teradata-stuur programma vanaf versie 3,18. U hoeft geen stuur Programma's hand matig te installeren. Het stuur programma vereist ' C++ Visual Redistributable 2012 update 4 ' op de zelf-hostende Integration runtime-computer. Als u de app nog niet hebt geïnstalleerd, kunt u deze [hier](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)downloaden.

Voor een zelf-hostende Integration runtime-versie die ouder is dan 3,18, installeert u de [.net-gegevens provider voor Teradata](https://go.microsoft.com/fwlink/?LinkId=278886), versie 14 of hoger op de computer met de Integration runtime. 

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor de Teradata-connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De gekoppelde Teradata-service ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **Teradata**. | Ja |
| connectionString | Hiermee geeft u de gegevens op die nodig zijn om verbinding te maken met het Teradata-exemplaar. Raadpleeg de volgende voor beelden.<br/>U kunt ook een wacht woord in Azure Key Vault plaatsen en de `password` configuratie uit de connection string halen. Raadpleeg [referenties opslaan in azure Key Vault](store-credentials-in-key-vault.md) met meer informatie. | Ja |
| gebruikersnaam | Geef een gebruikers naam op om verbinding te maken met Teradata. Van toepassing wanneer u Windows-verificatie gebruikt. | Nee |
| wachtwoord | Geef een wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. U kunt er ook voor kiezen om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). <br>Is van toepassing wanneer u Windows-verificatie gebruikt of een verwijzing naar een wacht woord in Key Vault voor basis verificatie. | Nee |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als niet is opgegeven, wordt de standaard Azure Integration Runtime. |Ja |

Meer verbindings eigenschappen die u in connection string per case kunt instellen:

| Eigenschap | Beschrijving | Standaardwaarde |
|:--- |:--- |:--- |
| CharacterSet | De tekenset die moet worden gebruikt voor de sessie. Bijvoorbeeld `CharacterSet=UTF16`.<br><br/>Deze waarde kan een door de gebruiker gedefinieerde tekenset of een van de volgende vooraf gedefinieerde teken sets zijn: <br/>-ASCII<br/>-UTF8<br/>-UTF16<br/>-LATIN1252_0A<br/>-LATIN9_0A<br/>-LATIN1_0A<br/>-Shift-JIS (Windows, DOS-compatibel, KANJISJIS_0S)<br/>-EUC (compatibel met UNIX, KANJIEC_0U)<br/>-IBM mainframe (KANJIEBCDIC5035_0I)<br/>-KANJI932_1S0<br/>-BIG5 (TCHBIG5_1R0)<br/>-GB (SCHGB2312_1T0)<br/>-SCHINESE936_6R0<br/>-TCHINESE950_8R0<br/>-NetworkKorean (HANGULKSC5601_2R4)<br/>-HANGUL949_7R0<br/>-ARABIC1256_6A0<br/>-CYRILLIC1251_2A0<br/>-HEBREW1255_5A0<br/>-LATIN1250_1A0<br/>-LATIN1254_7A0<br/>-LATIN1258_8A0<br/>-THAI874_4A0 | De standaard waarde is `ASCII`. |
| MaxRespSize |De maximale grootte van de antwoord buffer voor SQL-aanvragen, in kilo bytes (Kb's). Bijvoorbeeld `MaxRespSize=‭10485760‬`.<br/><br/>Voor de Teradata-Data Base versie 16,00 of hoger is de maximum waarde 7361536. De maximum waarde is 1048576 voor verbindingen die gebruikmaken van eerdere versies. | De standaard waarde is `65536`. |

**Voor beeld van basis verificatie**

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

**Voor beeld van het gebruik van Windows-verificatie**

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
> De volgende Payload wordt nog steeds ondersteund. U moet de nieuwe echter gebruiken.

**Vorige nettolading:**

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

Deze sectie bevat een lijst met eigenschappen die door de Teradata-gegevensset worden ondersteund. Zie [gegevens sets](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets.

Als u gegevens wilt kopiëren uit Teradata, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de DataSet moet worden ingesteld op `TeradataTable`. | Ja |
| enddatabase | De naam van het Teradata-exemplaar. | Nee (als 'query' in de activiteitbron is opgegeven) |
| table | De naam van de tabel in het Teradata-exemplaar. | Nee (als 'query' in de activiteitbron is opgegeven) |

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
> de gegevensset van het `RelationalTable` type wordt nog steeds ondersteund. We raden u echter aan de nieuwe gegevensset te gebruiken.

**Vorige nettolading:**

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

Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Teradata-bron. Voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, Zie [pijp lijnen](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Teradata als bron

>[!TIP]
>Als u gegevens op efficiënte wijze wilt laden met behulp van gegevens partitioneren, kunt u meer informatie vinden in het gedeelte [parallel kopiëren vanuit Teradata](#parallel-copy-from-teradata) .

Als u gegevens wilt kopiëren uit Teradata, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet worden ingesteld op `TeradataSource`. | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Een voorbeeld is `"SELECT * FROM MyTable"`.<br>Wanneer u gepartitioneerde belasting inschakelt, moet u alle bijbehorende ingebouwde partitie parameters in uw query koppelen. Zie de sectie [parallelle kopie van Teradata](#parallel-copy-from-teradata) voor voor beelden. | Nee (als de tabel in de gegevensset is opgegeven) |
| partitionOptions | Hiermee geeft u de opties voor gegevens partities op die worden gebruikt voor het laden van gegevens uit Teradata. <br>Toegestane waarden zijn: **geen** (standaard), **hash** -en **DynamicRange**.<br>Wanneer een partitie optie is ingeschakeld (dat wil zeggen, niet `None`), is de mate van parallelle uitvoering om gegevens te laden vanuit Teradata, bepaald door de [`parallelCopies`](copy-activity-performance.md#parallel-copy) instelling van de Kopieer activiteit. | Nee |
| partitionSettings | Geef de groep van de instellingen voor het partitioneren van gegevens op. <br>Toep assen als de partitie optie niet `None`is. | Nee |
| partitionColumnName | Geef de naam op van de bron kolom die wordt gebruikt door de bereik partitie of hash-partitie voor parallelle kopieën. Als u niets opgeeft, wordt de primaire index van de tabel automatisch gedetecteerd en gebruikt als partitie kolom. <br>Toep assen wanneer de partitie optie `Hash` of `DynamicRange`is. Als u een query gebruikt om de bron gegevens op te halen, Hook `?AdfHashPartitionCondition` of `?AdfRangePartitionColumnName` in de component WHERE. Zie voor beeld in [parallelle kopie van](#parallel-copy-from-teradata) de sectie Teradata. | Nee |
| partitionUpperBound | De maximum waarde van de partitie kolom waaruit de gegevens moeten worden gekopieerd. <br>Toep assen als de partitie optie `DynamicRange`is. Als u query gebruikt om bron gegevens op te halen, Hook `?AdfRangePartitionUpbound` in de component WHERE. Zie de sectie [parallelle kopie van Teradata](#parallel-copy-from-teradata) voor een voor beeld. | Nee |
| partitionLowerBound | De minimum waarde van de partitie kolom waaruit de gegevens moeten worden gekopieerd. <br>Toep assen wanneer de partitie optie is `DynamicRange`. Als u een query gebruikt om de bron gegevens op te halen, Hook `?AdfRangePartitionLowbound` in de component WHERE. Zie de sectie [parallelle kopie van Teradata](#parallel-copy-from-teradata) voor een voor beeld. | Nee |

> [!NOTE]
>
> de Kopieer bron van `RelationalSource` type wordt nog steeds ondersteund, maar biedt geen ondersteuning voor de nieuwe ingebouwde parallelle belasting vanuit Teradata (partitie opties). We raden u echter aan de nieuwe gegevensset te gebruiken.

**Voor beeld: gegevens kopiëren met behulp van een basis query zonder partitie**

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

De Data Factory Teradata-connector biedt ingebouwde gegevens partities voor het parallel kopiëren van gegevens van Teradata. U kunt opties voor gegevens partities vinden in de **bron** tabel van de Kopieer activiteit.

![Scherm opname van partitie opties](./media/connector-teradata/connector-teradata-partition-options.png)

Wanneer u gepartitioneerde kopie inschakelt, voert Data Factory parallelle query's uit op uw Teradata-bron om gegevens op partities te laden. De parallelle graad wordt bepaald door de instelling [`parallelCopies`](copy-activity-performance.md#parallel-copy) op de Kopieer activiteit. Als u bijvoorbeeld `parallelCopies` instelt op vier, worden met Data Factory gelijktijdig vier query's gegenereerd en uitgevoerd op basis van uw opgegeven partitie optie en instellingen, en elke query haalt een deel van de gegevens op uit uw Teradata.

U wordt aangeraden om parallelle kopieën in te scha kelen met gegevens partities met name wanneer u grote hoeveel heden gegevens uit uw Teradata laadt. Hieronder vindt u de aanbevolen configuraties voor verschillende scenario's. Bij het kopiëren van gegevens naar gegevens opslag op basis van een bestand, is het opnieuw opdracht om naar een map te schrijven als meerdere bestanden (Geef alleen de mapnaam op). in dat geval is de prestaties beter dan het schrijven naar één bestand.

| Scenario                                                     | Voorgestelde instellingen                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Volledige belasting van een grote tabel.                                   | **Partitie optie**: hash. <br><br/>Tijdens de uitvoering van Data Factory detecteert automatisch de kolom PK, wordt er een hash op toegepast en worden gegevens gekopieerd op partities. |
| Laad grote hoeveelheid gegevens met behulp van een aangepaste query.                 | **Partitie optie**: hash.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Partitie kolom**: Geef de kolom op die wordt gebruikt voor het Toep assen van de hash-partitie. Als u dit niet opgeeft, detecteert Data Factory automatisch de PK-kolom van de tabel die u in de Teradata-gegevensset hebt opgegeven.<br><br>Tijdens de uitvoering van Data Factory worden `?AdfHashPartitionCondition` vervangen door de hash-partitie logica en verzonden naar Teradata. |
| Laad een grote hoeveelheid gegevens met behulp van een aangepaste query, met een kolom met gehele getallen met een gelijkmatig gedistribueerde waarde voor bereik partitionering. | **Partitie opties**: partitie met dynamisch bereik.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Partitie kolom**: Geef de kolom op die wordt gebruikt om gegevens te partitioneren. U kunt de kolom met het gegevens type geheel getal partitioneren.<br>**Bovengrens van partities** en partities met een **ondergrens**: Geef op of u wilt filteren op de partitie kolom om alleen gegevens tussen het onderste en het bovenste bereik op te halen.<br><br>Tijdens de uitvoering Data Factory worden `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`en `?AdfRangePartitionLowbound` vervangen door de werkelijke kolom naam en het waardebereik voor elke partitie, en verzonden naar Teradata. <br>Als uw partitie kolom "ID" bijvoorbeeld is ingesteld met de ondergrens als 1 en de bovengrens als 80, met een parallelle kopie ingesteld als 4, Data Factory worden gegevens opgehaald met vier partities. Hun Id's liggen respectievelijk tussen [1, 20], [21, 40], [41, 60] en [61, 80]. |

**Voor beeld: query met hash-partitie**

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

**Voor beeld: query met een dynamische bereik partitie**

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

## <a name="data-type-mapping-for-teradata"></a>Toewijzing van gegevens type voor Teradata

Wanneer u gegevens uit Teradata kopieert, zijn de volgende toewijzingen van toepassing. Zie [schema en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de Kopieer activiteit het bron schema en het gegevens type aan de Sink toewijst.

| Teradata-gegevens type | Data Factory tussentijdse gegevenstype |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| char |Tekenreeks |
| Clob |Tekenreeks |
| Date |DateTime |
| decimaal |decimaal |
| Double-waarde |Double-waarde |
| Graphic |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Geheel getal |Int32 |
| Interval dag |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval Day To Hour |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval Day To Minute |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval Day To Second |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval Hour |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval Hour To Minute |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval Hour To Second |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval Minute |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval Minute To Second |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval Month |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval Second |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval Year |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval Year To Month |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Aantal |Double-waarde |
| Periode (datum) |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Periode (tijd) |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Periode (tijd met tijd zone) |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Periode (tijds tempel) |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Periode (tijds tempel met tijd zone) |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| Tijdstempel |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |Tekenreeks |
| VarGraphic |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| XML |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |


## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.


## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in Data Factory.
