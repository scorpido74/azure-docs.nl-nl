---
title: Metagegevens van bestanden gebruiken in query's
description: OpenROWSET- functie bevat bestands- en padgegevens over elk bestand dat in de query wordt gebruikt om gegevens te filteren of te analyseren op basis van bestandsnaam en/of mappad.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431551"
---
# <a name="using-file-metadata-in-queries"></a>Metagegevens van bestanden gebruiken in query's

De SQL on-demand Query-service kan meerdere bestanden en mappen aanspreken zoals beschreven in de querymappen en het artikel [over meerdere bestanden.](query-folders-multiple-csv-files.md) In dit artikel leert u hoe u metagegevens over bestands- en mapnamen in de query's gebruiken.

Soms moet u weten welke bestands- of mapbron correleert met een specifieke rij in de resultaatset.

U `filepath` de `filename` functie gebruiken en bestandsnamen en/of het pad in de resultaatset retourneren. U ze ook gebruiken om gegevens te filteren op basis van de bestandsnaam en/of het mappad. Deze functies worden beschreven in de [functie bestandsnaam](develop-storage-files-overview.md#filename-function) syntaxissectie en [bestandspadfunctie](develop-storage-files-overview.md#filepath-function). Hieronder vindt u korte beschrijvingen langs voorbeelden.

## <a name="prerequisites"></a>Vereisten

Voordat u de rest van dit artikel leest, bekijkt u de volgende vereisten:

- [First-time setup](query-data-storage.md#first-time-setup)
- [Vereisten](query-data-storage.md#prerequisites)

## <a name="functions"></a>Functions

### <a name="filename"></a>Bestandsnaam

Met deze functie wordt de bestandsnaam geretourneerd waar de rij vandaan komt.

In het volgende voorbeeld worden de NYC Yellow Taxi-gegevensbestanden van de laatste drie maanden van 2017 gelezen en wordt het aantal ritten per bestand geretourneerd. Het OPENROWSET-gedeelte van de query geeft aan welke bestanden worden gelezen.

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

In het volgende voorbeeld ziet u hoe *bestandsnaam()* kan worden gebruikt in de WHERE-component om de te lezen bestanden te filteren. Het toegang tot de gehele map in het OPENROWSET-deel van de query en filtert bestanden in de WHERE-component.

Uw resultaten zullen hetzelfde zijn als het vorige voorbeeld.

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

### <a name="filepath"></a>Filepath

De functie bestandspad retourneert een volledig of gedeeltelijk pad:

- Wanneer deze zonder parameter wordt aangeroepen, wordt het volledige bestandspad geretourneerd waarvan de rij afkomstig is.
- Wanneer deze wordt aangeroepen met een parameter, wordt een deel van het pad geretourneerd dat overeenkomt met de wildcard op de positie die in de parameter is opgegeven. Parameterwaarde 1 geeft bijvoorbeeld een deel van het pad terug dat overeenkomt met de eerste wildcard.

De volgende steekproef leest NYC Yellow Taxi databestanden voor de laatste drie maanden van 2017. Hiermee wordt het aantal ritten per bestandspad geretourneerd. Het OPENROWSET-gedeelte van de query geeft aan welke bestanden worden gelezen.

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

In het volgende voorbeeld ziet u hoe *filepath()* kan worden gebruikt in de WHERE-component om de te lezen bestanden te filteren.

U de jokertekens in het OPENROWSET-gedeelte van de query gebruiken en de bestanden filteren in de WHERE-component. Uw resultaten zullen hetzelfde zijn als het vorige voorbeeld.

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

In het volgende artikel leert u hoe u parketbestanden [opvragen.](query-parquet-files.md)
