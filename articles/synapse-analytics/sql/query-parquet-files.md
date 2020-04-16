---
title: Query-parketbestanden met SQL on-demand (voorbeeld)
description: In dit artikel leert u hoe u parketbestanden opvragen met SQL on-demand (voorbeeld).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431694"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Query Parquet-bestanden met SQL on-demand (preview) in Azure Synapse Analytics

In dit artikel leert u hoe u een query schrijven met SQL on-demand (preview) waarin parketbestanden worden gelezen.

## <a name="prerequisites"></a>Vereisten

Voordat u de rest van dit artikel leest, leest u de volgende artikelen:

- [First-time setup](query-data-storage.md#first-time-setup)
- [Vereisten](query-data-storage.md#prerequisites)

## <a name="dataset"></a>Gegevensset

U parketbestanden op dezelfde manier opvragen als CSV-bestanden. Het enige verschil is dat de parameter FILEFORMAT moet worden ingesteld op PARQUET. Voorbeelden in dit artikel tonen de specifieke kenmerken van het lezen van Parketbestanden.

> [!NOTE]
> U hoeft geen kolommen op te geven in de OPENROWSET MET-clausule bij het lezen van parketbestanden. SQL on-demand maakt gebruik van metadata in het parketbestand en bindt kolommen bij naam.

U gebruikt het *parket/de taxi* van de map voor de voorbeeldquery's. Het bevat NYC Taxi - Yellow Taxi Trip Records gegevens van juli 2016. tot juni 2018.

Gegevens worden per jaar en maand verdeeld en de mapstructuur is als volgt:

- jaar=2016
  - maand=6
  - ...
  - maand=12
- jaar=2017
  - maand=1
  - ...
  - maand=12
- jaar=2018
  - maand=1
  - ...
  - maand=6

## <a name="query-set-of-parquet-files"></a>Queryset van parketbestanden

U alleen de kolommen opgeven die interessant zijn wanneer u parketbestanden opvraagt.

```sql
SELECT
        YEAR(pickup_datetime),
        passenger_count,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) WITH (
        pickup_datetime DATETIME2,
        passenger_count INT
    ) AS nyc
GROUP BY
    passenger_count,
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime),
    passenger_count;
```

## <a name="automatic-schema-inference"></a>Automatische schema-gevolgtrekking

U hoeft de OPENROWSET WITH-clausule niet te gebruiken bij het lezen van parketbestanden. Kolomnamen en gegevenstypen worden automatisch gelezen uit parketbestanden.

In het onderstaande voorbeeld worden de automatische schema-inferencemogelijkheden voor parketbestanden weergegeven. Het geeft het aantal rijen in september 2017 terug zonder een schema op te geven.

> [!NOTE]
> U hoeft geen kolommen op te geven in de openrowset met-clausule bij het lezen van Parketbestanden. In dat geval maakt sql on-demand queryservice gebruik van metagegevens in het parketbestand en bindt kolommen op naam.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>Gegevens met partitie van query's

De gegevensset in dit voorbeeld is verdeeld (verdeeld) in afzonderlijke submappen. U specifieke partities targeten met de bestandspadfunctie. In dit voorbeeld worden tariefbedragen per jaar, maand en payment_type weergegeven voor de eerste drie maanden van 2017.

> [!NOTE]
> De SQL on-demand Query is compatibel met Hive/Hadoop partitionering scheme.

```sql
SELECT
    nyc.filepath(1) AS [year],
    nyc.filepath(2) AS [month],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND pickup_datetime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type
ORDER BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type;
```

## <a name="type-mapping"></a>Typetoewijzing

Parketbestanden bevatten typebeschrijvingen voor elke kolom. In de volgende tabel wordt beschreven hoe parkettypen worden toegewezen aan SQL-native typen.

| Parkettype | Parket logisch type (annotatie) | SQL-gegevenstype |
| --- | --- | --- |
| Booleaanse | | bit |
| BINAIR / BYTE_ARRAY | | varbinary varbinary |
| Dubbele | | float |
| Float | | real |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binair |
| Binaire |UTF8 |varchar \*(UTF8 collation) |
| Binaire |Tekenreeks |varchar \*(UTF8 collation) |
| Binaire |Enum|varchar \*(UTF8 collation) |
| Binaire |Uuid |uniqueidentifier |
| Binaire |Decimaal |decimal |
| Binaire |JSON |varchar(max) \*(UTF8 collation) |
| Binaire |BSON BSON |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |Decimaal |decimal |
| BYTE_ARRAY |Interval |varchar(max), geserialiseerd in gestandaardiseerd formaat |
| INT32 |INT(8, waar) |smallint |
| INT32 |INT(16, waar) |smallint |
| INT32 |INT(32, waar) |int |
| INT32 |INT(8, false) |tinyint |
| INT32 |INT(16, false) |int |
| INT32 |INT(32, false) |bigint |
| INT32 |DATE |date |
| INT32 |Decimaal |decimal |
| INT32 |TIJD (MILLIS )|tijd |
| INT64 |INT(64, waar) |bigint |
| INT64 |INT(64, false ) |decimaal(20,0) |
| INT64 |Decimaal |decimal |
| INT64 |TIJD (MICROS / NANOS) |tijd |
|INT64 |TIMESTAMP (MILLIS / MICROS / NANOS) |datetime2 |
|[Complex type](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |Lijst |varchar(max), geserialiseerd tot JSON |
|[Complex type](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|Kaart|varchar(max), geserialiseerd tot JSON |

## <a name="next-steps"></a>Volgende stappen

Ga verder naar het volgende artikel om te leren hoe [je parketgenesetypen opvragen.](query-parquet-nested-types.md)
