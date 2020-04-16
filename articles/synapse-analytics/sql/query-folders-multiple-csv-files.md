---
title: Querymappen en meerdere CSV-bestanden met SQL on-demand (voorbeeld)
description: SQL on-demand (preview) ondersteunt het lezen van meerdere bestanden/mappen met behulp van jokers, die vergelijkbaar zijn met de wildcards die in Windows OS worden gebruikt.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 070574f0eec40c27eb05cec3bf0a84282b85050f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431798"
---
# <a name="query-folders-and-multiple-csv-files"></a>Querymappen en meerdere CSV-bestanden  

In dit artikel leert u hoe u een query schrijft met SQL on-demand (preview) in Azure Synapse Analytics.

SQL on-demand ondersteunt het lezen van meerdere bestanden/mappen met jokertekens, die vergelijkbaar zijn met de wildcards die in Windows OS worden gebruikt. Er is echter meer flexibiliteit aanwezig omdat meerdere wildcards zijn toegestaan.

## <a name="prerequisites"></a>Vereisten

Voordat u de rest van dit artikel leest, moet u de onderstaande artikelen bekijken:

- [First-time setup](query-data-storage.md#first-time-setup)
- [Vereisten](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>Meerdere bestanden in map lezen

U gebruikt de map *csv/taxi* om de voorbeeldquery's te volgen. Het bevat nyc taxi - Yellow Taxi Trip Records gegevens van juli 2016 tot juni 2018.

Bestanden in *csv/taxi* worden vernoemd naar jaar en maand:

- yellow_tripdata_2016-07.csv
- yellow_tripdata_2016-08.csv
- yellow_tripdata_2016-09.csv
- ...
- yellow_tripdata_2018-04.csv
- yellow_tripdata_2018-05.csv
- yellow_tripdata_2018-06.csv

Elk bestand heeft de volgende structuur:
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>Alle bestanden in de map lezen
    
In het onderstaande voorbeeld leest u alle gegevensbestanden van NYC Yellow Taxi uit de *csv/taximap* en retourneert het totale aantal passagiers en ritten per jaar. Het toont ook het gebruik van geaggregeerde functies.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/*.*',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
           trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
           pickup_location_id INT,'
        dropoff_location_id INT,
           payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alle bestanden die met de afzonderlijke OPENROWSET worden geopend, moeten dezelfde structuur hebben (d.w.z. het aantal kolommen en hun gegevenstypen).

### <a name="read-subset-of-files-in-folder"></a>Subset van bestanden in map lezen

In het onderstaande voorbeeld leest u de gegevensbestanden van NYC Yellow Taxi 2017 uit de *csv/taximap* met behulp van een wildcard en retourneert het totale tariefbedrag per betalingstype.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Alle bestanden die met de afzonderlijke OPENROWSET worden geopend, moeten dezelfde structuur hebben (d.w.z. het aantal kolommen en hun gegevenstypen).

## <a name="read-folders"></a>Mappen lezen

Het pad dat u aan OPENROWSET geeft, kan ook een pad naar een map zijn. De volgende secties bevatten deze querytypen.

### <a name="read-all-files-from-specific-folder"></a>Alle bestanden uit een specifieke map lezen

U alle bestanden in een map lezen met de wildcard op bestandsniveau zoals weergegeven in [Alle bestanden in de map lezen.](#read-all-files-in-folder) Er is echter een manier om een map op te vragen en alle bestanden in die map te gebruiken.

Als het pad in OPENROWSET naar een map verwijst, worden alle bestanden in die map gebruikt als bron voor uw query. In de volgende query worden alle bestanden in de *csv/taximap* gelezen.

> [!NOTE]
> Let op het bestaan van de / aan het einde van het pad in de query hieronder. Het duidt een map aan. Als de / wordt weggelaten, zal de query in plaats daarvan gericht zijn op een bestand met de naam *taxi.*

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alle bestanden die met de afzonderlijke OPENROWSET worden geopend, moeten dezelfde structuur hebben (d.w.z. het aantal kolommen en hun gegevenstypen).

### <a name="read-all-files-from-multiple-folders"></a>Alle bestanden uit meerdere mappen lezen

Het is mogelijk om bestanden uit meerdere mappen te lezen met behulp van een wildcard. In de volgende query worden alle bestanden uit alle mappen in de *csv-map* met namen gelezen die beginnen met *t* en eindigend met *i*.

> [!NOTE]
> Let op het bestaan van de / aan het einde van het pad in de query hieronder. Het duidt een map aan. Als de / wordt weggelaten, zal de query richten op bestanden met de naam *t&ast;i* in plaats daarvan.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/', 
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alle bestanden die met de afzonderlijke OPENROWSET worden geopend, moeten dezelfde structuur hebben (d.w.z. het aantal kolommen en hun gegevenstypen).

Aangezien u slechts één map hebt die aan de criteria voldoet, is het queryresultaat hetzelfde als [Alle bestanden in de map lezen.](#read-all-files-in-folder)

## <a name="multiple-wildcards"></a>Meerdere wildcards

U meerdere jokertekens gebruiken op verschillende padniveaus. U bijvoorbeeld eerdere query's verrijken om alleen bestanden met gegevens uit 2017 te lezen, uit alle mappen die met *t* en eindigen met *i*.

> [!NOTE]
> Let op het bestaan van de / aan het einde van het pad in de query hieronder. Het duidt een map aan. Als de / wordt weggelaten, zal de query richten op bestanden met de naam *t&ast;i* in plaats daarvan.
> Er is een maximumlimiet van 10 wildcards per query.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alle bestanden die met de afzonderlijke OPENROWSET worden geopend, moeten dezelfde structuur hebben (d.w.z. het aantal kolommen en hun gegevenstypen).

Aangezien u slechts één map hebt die aan de criteria voldoet, is het queryresultaat hetzelfde als [De subset van bestanden in map lezen](#read-subset-of-files-in-folder) en Alle bestanden uit een specifieke map [lezen.](#read-all-files-from-specific-folder) Complexere scenario's voor het gebruik van jokertekens worden behandeld in [queryparketbestanden](query-parquet-files.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie is te vinden in het artikel in [Query specific files.](query-specific-files.md)
