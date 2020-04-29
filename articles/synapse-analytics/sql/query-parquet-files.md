---
title: Een query uitvoeren op Parquet-bestanden met behulp van SQL op aanvraag (preview)
description: In dit artikel leert u hoe u een query kunt uitvoeren op Parquet-bestanden met behulp van SQL op aanvraag (preview).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431694"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Een query uitvoeren op Parquet-bestanden met behulp van SQL on-demand (preview) in azure Synapse Analytics

In dit artikel leert u hoe u een query schrijft met behulp van SQL on-demand (preview) waarmee Parquet-bestanden worden gelezen.

## <a name="prerequisites"></a>Vereisten

Lees de volgende artikelen voordat u de rest van dit artikel leest:

- [Eerste keer instellen](query-data-storage.md#first-time-setup)
- [Vereisten](query-data-storage.md#prerequisites)

## <a name="dataset"></a>Gegevensset

U kunt een query uitvoeren op Parquet-bestanden op dezelfde manier als bij het lezen van CSV-bestanden. Het enige verschil is dat de File Format-para meter moet worden ingesteld op PARQUET. In de voor beelden in dit artikel ziet u de specifieke informatie over het lezen van Parquet-bestanden.

> [!NOTE]
> U hoeft geen kolommen op te geven in de component OPENROWSET WITH bij het lezen van Parquet-bestanden. SQL on demand maakt gebruik van meta gegevens in het Parquet-bestand en verbindt kolommen op naam.

U gebruikt de map *Parquet/taxi* voor de voorbeeld query's. Het bevat NYC-gele taxi-excursie records van 2016 juli. tot juni 2018.

De gegevens worden gepartitioneerd per jaar en maand en de mapstructuur is als volgt:

- Year = 2016
  - maand = 6
  - ...
  - maand = 12
- Year = 2017
  - maand = 1
  - ...
  - maand = 12
- Year = 2018
  - maand = 1
  - ...
  - maand = 6

## <a name="query-set-of-parquet-files"></a>Query set van Parquet-bestanden

U kunt alleen de interessante kolommen opgeven wanneer u Parquet-bestanden opvraagt.

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

## <a name="automatic-schema-inference"></a>Automatische schema-deinterferentie

U hoeft de component OPENROWSET WITH niet te gebruiken bij het lezen van Parquet-bestanden. Kolom namen en gegevens typen worden automatisch uit Parquet-bestanden gelezen.

In het onderstaande voor beeld ziet u de mogelijkheden voor het automatisch afnemen van schema's voor Parquet-bestanden. Het retourneert het aantal rijen in september 2017 zonder een schema op te geven.

> [!NOTE]
> U hoeft geen kolommen op te geven in de component OPENROWSET WITH bij het lezen van Parquet-bestanden. In dat geval worden meta gegevens in de SQL-query service op aanvraag gebruikt in het Parquet-bestand en worden kolommen op naam gebonden.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>Gepartitioneerde gegevens opvragen

De gegevensset die in dit voor beeld wordt gegeven, wordt in afzonderlijke submappen verdeeld (gepartitioneerd). U kunt met behulp van de functie filepath specifieke partities richten. In dit voor beeld worden de ritbedrag bedragen per jaar, maand en payment_type voor de eerste drie maanden van 2017 weer gegeven.

> [!NOTE]
> De SQL op aanvraag-query is compatibel met hive/Hadoop-partitie schema.

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

## <a name="type-mapping"></a>Type toewijzing

Parquet-bestanden bevatten type beschrijvingen voor elke kolom. In de volgende tabel wordt beschreven hoe Parquet types worden toegewezen aan systeem eigen SQL-typen.

| Type Parquet | Parquet logische type (annotatie) | SQL-gegevens type |
| --- | --- | --- |
| True | | bit |
| BINARY/BYTE_ARRAY | | varbinary |
| DUBBELKLIK | | float |
| FLOAT | | werkelijk |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binair |
| WAARDE |UTF8 |varchar \*(UTF8-sortering) |
| WAARDE |TEKENREEKSEXPRESSIE |varchar \*(UTF8-sortering) |
| WAARDE |VASTE|varchar \*(UTF8-sortering) |
| WAARDE |MEE |uniqueidentifier |
| WAARDE |KOMMA |decimal |
| WAARDE |JSON |varchar (max) \*(UTF8-sortering) |
| WAARDE |BSON |varbinary (max) |
| FIXED_LEN_BYTE_ARRAY |KOMMA |decimal |
| BYTE_ARRAY |BEREIK |varchar (max), geserialiseerd in gestandaardiseerde indeling |
| INT32 |INT (8, True) |smallint |
| INT32 |INT (16, True) |smallint |
| INT32 |INT (32, True) |int |
| INT32 |INT (8, false) |tinyint |
| INT32 |INT (16, false) |int |
| INT32 |INT (32, false) |bigint |
| INT32 |DATE |date |
| INT32 |KOMMA |decimal |
| INT32 |TIJD (MILLIS)|tijd |
| INT64 |INT (64, True) |bigint |
| INT64 |INT (64, false) |decimaal (20, 0) |
| INT64 |KOMMA |decimal |
| INT64 |TIJD (MICRON/NANOS) |tijd |
|INT64 |TIJDS TEMPEL (MILLIS/MICRON/NANOS) |datetime2 |
|[Complex type](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |ORDERVERZAMELLIJST |varchar (max), geserialiseerd in JSON |
|[Complex type](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|DIAGRAM|varchar (max), geserialiseerd in JSON |

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het [opvragen van Parquet-geneste typen](query-parquet-nested-types.md).
