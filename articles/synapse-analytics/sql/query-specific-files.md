---
title: Meta gegevens van bestanden in query's gebruiken
description: De functie OPENROWSET bevat bestands-en padgegevens over elk bestand dat in de query wordt gebruikt om gegevens te filteren of te analyseren op basis van de bestands naam en/of het pad van de map.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431551"
---
# <a name="using-file-metadata-in-queries"></a>Meta gegevens van bestanden in query's gebruiken

De SQL on-demand-query service kan worden uitgebreid naar meerdere bestanden en mappen, zoals beschreven in het artikel [query mappen en meerdere bestanden](query-folders-multiple-csv-files.md) . In dit artikel vindt u informatie over het gebruik van meta gegevens over bestands-en mapnamen in de query's.

Het kan voor komen dat u moet weten welk bestands-of bronmap-bron overeenkomt met een specifieke rij in de resultatenset.

U kunt de functie `filepath` gebruiken `filename` en bestands namen en/of het pad in de resultatenset retour neren. U kunt ze ook gebruiken om gegevens te filteren op basis van de bestands naam en/of het pad van de map. Deze functies worden beschreven in de syntaxis sectie [Bestands naam functie](develop-storage-files-overview.md#filename-function) en [filepath-functie](develop-storage-files-overview.md#filepath-function). Hieronder vindt u een korte beschrijving van de voor beelden.

## <a name="prerequisites"></a>Vereisten

Lees de volgende vereisten voordat u de rest van dit artikel leest:

- [Eerste keer instellen](query-data-storage.md#first-time-setup)
- [Vereisten](query-data-storage.md#prerequisites)

## <a name="functions"></a>Functions

### <a name="filename"></a>Bestands

Deze functie retourneert de naam van het bestand waaruit de rij afkomstig is.

In het volgende voor beeld worden de NYC Yellow Taxi-gegevens bestanden voor de laatste drie maanden van 2017 gelezen en wordt het aantal onderdrukkingen per bestand geretourneerd. Het gedeelte OPENROWSET van de query geeft aan welke bestanden worden gelezen.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET') AS [r]
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

In het volgende voor beeld ziet u hoe *filename ()* kan worden gebruikt in de component WHERE om de bestanden te filteren die moeten worden gelezen. Het opent de volledige map in het onderdeel OPENROWSET van de query en filtert bestanden in de component WHERE.

De resultaten zijn hetzelfde als in het vorige voor beeld.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
    FORMAT='PARQUET') AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.parquet', 'yellow_tripdata_2017-11.parquet', 'yellow_tripdata_2017-12.parquet')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Bestandspad

De functie filepath retourneert een volledig of gedeeltelijk pad:

- Als u zonder para meter aangeroepen, wordt het volledige bestandspad geretourneerd waaruit de rij afkomstig is.
- Als deze wordt aangeroepen met een para meter, wordt een deel van het pad geretourneerd dat overeenkomt met het Joker teken op de positie die is opgegeven in de para meter. Zo retourneert parameter waarde 1 een deel van het pad dat overeenkomt met het eerste Joker teken.

In het volgende voor beeld worden NYC Yellow Taxi-gegevens bestanden voor de laatste drie maanden van 2017 gelezen. Hiermee wordt het aantal onderdrukkingen per bestandspad geretourneerd. Het gedeelte OPENROWSET van de query geeft aan welke bestanden worden gelezen.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-1*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT,
        pickup_datetime DATETIME2,
        dropoff_datetime DATETIME2,
        passenger_count SMALLINT,
        trip_distance FLOAT,
        rate_code SMALLINT,
        store_and_fwd_flag SMALLINT,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type SMALLINT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

In het volgende voor beeld ziet u hoe *filepath ()* kan worden gebruikt in de component WHERE om de bestanden te filteren die moeten worden gelezen.

U kunt de joker tekens gebruiken in het deel van de query OPENROWSET en de bestanden in de component WHERE filteren. De resultaten zijn hetzelfde als in het vorige voor beeld.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_*-*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
WITH (
    vendor_id INT,
    pickup_datetime DATETIME2,
    dropoff_datetime DATETIME2,
    passenger_count SMALLINT,
    trip_distance FLOAT,
    rate_code SMALLINT,
    store_and_fwd_flag SMALLINT,
    pickup_location_id INT,
    dropoff_location_id INT,
    payment_type SMALLINT,
    fare_amount FLOAT,
    extra FLOAT,
    mta_tax FLOAT,
    tip_amount FLOAT,
    tolls_amount FLOAT,
    improvement_surcharge FLOAT,
    total_amount FLOAT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>Volgende stappen

In het volgende artikel vindt u informatie over het uitvoeren van een [query op Parquet-bestanden](query-parquet-files.md).
