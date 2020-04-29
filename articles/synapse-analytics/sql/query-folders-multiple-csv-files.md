---
title: Query's uitvoeren op mappen en meerdere CSV-bestanden met behulp van SQL op aanvraag (preview)
description: SQL on-demand (preview) ondersteunt het lezen van meerdere bestanden/mappen met behulp van joker tekens, die vergelijkbaar zijn met de joker tekens die in Windows-besturings systeem worden gebruikt.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8f8af7fab7113e38b91c3f5f1bcc41b4e4fba2c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457362"
---
# <a name="query-folders-and-multiple-csv-files"></a>Query mappen en meerdere CSV-bestanden  

In dit artikel leert u hoe u een query schrijft met behulp van SQL on-demand (preview) in azure Synapse Analytics.

SQL on-demand ondersteunt het lezen van meerdere bestanden/mappen met behulp van joker tekens, die vergelijkbaar zijn met de joker tekens die in Windows-besturings systeem worden gebruikt. Er is echter meer flexibiliteit aanwezig omdat meerdere joker tekens zijn toegestaan.

## <a name="prerequisites"></a>Vereisten

Voordat u de rest van dit artikel leest, moet u de onderstaande artikelen door nemen:

- [Eerste keer instellen](query-data-storage.md#first-time-setup)
- [Vereisten](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>Meerdere bestanden in de map lezen

U gebruikt de map *CSV/taxi* om de voorbeeld query's te volgen. Het bevat NYC-Gelee taxi-excursie records van 2016 juli tot juni 2018.

Bestanden in *CSV/taxi* worden na jaar en maand benoemd:

- yellow_tripdata_2016 -07. CSV
- yellow_tripdata_2016 -08. CSV
- yellow_tripdata_2016 -09. CSV
- ...
- yellow_tripdata_2018 -04. CSV
- yellow_tripdata_2018 -05. CSV
- yellow_tripdata_2018 -06. CSV

Elk bestand heeft de volgende structuur:
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>Alle bestanden in de map lezen
    
In het onderstaande voor beeld worden alle NYC Yellow Taxi-gegevens bestanden uit de map *CSV/taxi* gelezen en wordt het totale aantal passagiers en onderdrukkingen per jaar geretourneerd. Ook wordt het gebruik van statistische functies weer gegeven.

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
> Alle bestanden die worden geopend met één OPENROWSET, moeten dezelfde structuur hebben (bijvoorbeeld het aantal kolommen en de bijbehorende gegevens typen).

### <a name="read-subset-of-files-in-folder"></a>Subset van bestanden in de map lezen

In het onderstaande voor beeld worden de 2017 gele taxi-gegevens bestanden uit de map *CSV/taxi* gelezen met behulp van een Joker teken en wordt het totale ritbedrag bedrag per betalings type geretourneerd.

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
> Alle bestanden die worden geopend met één OPENROWSET, moeten dezelfde structuur hebben (bijvoorbeeld het aantal kolommen en de bijbehorende gegevens typen).

## <a name="read-folders"></a>Mappen lezen

Het pad dat u opgeeft voor OPENROWSET, kan ook een pad naar een map zijn. De volgende secties bevatten deze query typen.

### <a name="read-all-files-from-specific-folder"></a>Alle bestanden van een specifieke map lezen

U kunt alle bestanden in een map lezen met behulp van het Joker teken op bestands niveau, zoals wordt weer gegeven in [alle bestanden in de map lezen](#read-all-files-in-folder). Maar er is een manier om een query uit te stellen op een map en alle bestanden in die map te gebruiken.

Als het pad in OpenRowset naar een map verwijst, worden alle bestanden in die map gebruikt als bron voor uw query. Met de volgende query worden alle bestanden in de map *CSV/taxi* gelezen.

> [!NOTE]
> Let op het bestaan van de/aan het einde van het pad in de onderstaande query. Hiermee wordt een map aangeduid. Als de/wordt wegge laten, wordt de query gericht op een bestand met de naam *taxi* .

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
> Alle bestanden die worden geopend met één OPENROWSET, moeten dezelfde structuur hebben (bijvoorbeeld het aantal kolommen en de bijbehorende gegevens typen).

### <a name="read-all-files-from-multiple-folders"></a>Alle bestanden uit meerdere mappen lezen

Het is mogelijk om bestanden uit meerdere mappen te lezen met behulp van een Joker teken. Met de volgende query worden alle bestanden gelezen van alle mappen in de *CSV* -map die namen hebben die beginnen met *t* en eindigen met *i*.

> [!NOTE]
> Let op het bestaan van de/aan het einde van het pad in de onderstaande query. Hiermee wordt een map aangeduid. Als de/wordt wegge laten, wordt in de query het doel bestand met de naam *&ast;i* weer te richten.

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
> Alle bestanden die worden geopend met één OPENROWSET, moeten dezelfde structuur hebben (bijvoorbeeld het aantal kolommen en de bijbehorende gegevens typen).

Omdat u slechts één map hebt die voldoet aan de criteria, is het resultaat van de query hetzelfde als [alle bestanden in de map lezen](#read-all-files-in-folder).

## <a name="multiple-wildcards"></a>Meerdere joker tekens

U kunt meerdere joker tekens gebruiken op verschillende padvariabelen. U kunt bijvoorbeeld een eerdere query verrijken om bestanden met alleen 2017 gegevens te lezen, van alle mappen met een naam die begint met *t* en eindigt met *i*.

> [!NOTE]
> Let op het bestaan van de/aan het einde van het pad in de onderstaande query. Hiermee wordt een map aangeduid. Als de/wordt wegge laten, wordt in de query het doel bestand met de naam *&ast;i* weer te richten.
> Er is een maximum limiet van 10 joker tekens per query.

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
> Alle bestanden die worden geopend met één OPENROWSET, moeten dezelfde structuur hebben (bijvoorbeeld het aantal kolommen en de bijbehorende gegevens typen).

Omdat u slechts één map hebt die voldoet aan de criteria, is het resultaat van de query hetzelfde als de [subset van bestanden in de map lezen](#read-subset-of-files-in-folder) en [alle bestanden uit een specifieke map lezen](#read-all-files-from-specific-folder). Complexere gebruiks scenario's voor joker tekens worden behandeld in [Parquet-bestanden van query's](query-parquet-files.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie vindt u in het artikel [query-specifieke bestanden](query-specific-files.md) .
