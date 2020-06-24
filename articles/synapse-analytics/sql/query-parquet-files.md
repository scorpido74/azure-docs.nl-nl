---
title: Een query uitvoeren op Parquet-bestanden met behulp van SQL op aanvraag (preview)
description: In dit artikel leert u hoe u een query kunt uitvoeren op Parquet-bestanden met behulp van SQL op aanvraag (preview).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 4bab1ef4588a705f0dd6cdb34be8272868f826e9
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85207563"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Een query uitvoeren op Parquet-bestanden met behulp van SQL on-demand (preview) in azure Synapse Analytics

In dit artikel leert u hoe u een query schrijft met behulp van SQL on-demand (preview) waarmee Parquet-bestanden worden gelezen.

## <a name="prerequisites"></a>Vereisten

De eerste stap bestaat uit het **maken van een Data Base** met een gegevens bron die verwijst naar een [NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) -opslag account. Initialiseer vervolgens de objecten door een [installatiescript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) uit te voeren op die database. Met dit installatie script worden de gegevens bronnen, referenties voor het data base-bereik en externe bestands indelingen gemaakt die in deze voor beelden worden gebruikt.

## <a name="dataset"></a>Gegevensset

In dit voor beeld wordt een [NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) -gegevensset gebruikt. U kunt een query uitvoeren op Parquet-bestanden op dezelfde manier als bij het [lezen van CSV-bestanden](query-parquet-files.md). Het enige verschil is dat de `FILEFORMAT` para meter moet worden ingesteld op `PARQUET` . In de voor beelden in dit artikel ziet u de specifieke informatie over het lezen van Parquet-bestanden.

## <a name="query-set-of-parquet-files"></a>Query set van Parquet-bestanden

U kunt alleen de interessante kolommen opgeven wanneer u Parquet-bestanden opvraagt.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>Automatische schemadeductie

U hoeft de component OPENROWSET WITH niet te gebruiken bij het lezen van Parquet-bestanden. Kolom namen en gegevens typen worden automatisch uit Parquet-bestanden gelezen.

In het onderstaande voor beeld ziet u de mogelijkheden voor het automatisch afnemen van schema's voor Parquet-bestanden. Het retourneert het aantal rijen in september 2017 zonder een schema op te geven.

> [!NOTE]
> U hoeft geen kolommen op te geven in de component OPENROWSET WITH bij het lezen van Parquet-bestanden. In dat geval worden meta gegevens in de SQL-query service op aanvraag gebruikt in het Parquet-bestand en worden kolommen op naam gebonden.

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>Gepartitioneerde gegevens opvragen

De gegevensset die in dit voor beeld wordt gegeven, wordt in afzonderlijke submappen verdeeld (gepartitioneerd). U kunt met behulp van de functie filepath specifieke partities richten. In dit voor beeld worden de ritbedrag bedragen per jaar, maand en payment_type voor de eerste drie maanden van 2017 weer gegeven.

> [!NOTE]
> De SQL op aanvraag-query is compatibel met hive/Hadoop-partitie schema.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="type-mapping"></a>Type toewijzing

Parquet-bestanden bevatten type beschrijvingen voor elke kolom. In de volgende tabel wordt beschreven hoe Parquet types worden toegewezen aan systeem eigen SQL-typen.

| Type Parquet | Parquet logische type (annotatie) | SQL-gegevenstype |
| --- | --- | --- |
| True | | bit |
| BINARY/BYTE_ARRAY | | varbinary |
| DUBBELKLIK | | float |
| FLOAT | | werkelijk |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binair |
| WAARDE |UTF8 |varchar \* (UTF8-sortering) |
| WAARDE |TEKENREEKSEXPRESSIE |varchar \* (UTF8-sortering) |
| WAARDE |VASTE|varchar \* (UTF8-sortering) |
| WAARDE |MEE |uniqueidentifier |
| WAARDE |KOMMA |decimal |
| WAARDE |JSON |varchar (max) \* (UTF8-sortering) |
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
