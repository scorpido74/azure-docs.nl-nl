---
title: 'Zelf studie voor data analisten: SQL on-demand gebruiken (preview) voor het analyseren van Azure open gegevens sets in azure Synapse Studio (preview)'
description: In deze zelf studie leert u hoe u eenvoudig experimentele gegevens analyse kunt uitvoeren met behulp van SQL op aanvraag (preview) en visualiseren van de resultaten in azure Synapse Studio.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1080001cb222f91503080914d7fb253e5ee82626
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81423227"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>SQL op aanvraag (preview) gebruiken voor het analyseren van Azure open gegevens sets en het visualiseren van de resultaten in azure Synapse Studio (preview)

In deze zelf studie leert u hoe u experimentele gegevens analyse kunt uitvoeren door verschillende Azure open gegevens sets te combi neren met SQL op aanvraag en vervolgens de resultaten te visualiseren in azure Synapse Studio.

In het bijzonder kunt u de [NYC-gegevensset (New York City)](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) analyseren, met inbegrip van de ophaal-en verwijderings datums/-tijden, de ophaal-en uitval locaties, de bevestigingen, de gespecificeerde tarieven, de betalings typen en de door het stuur programma gemelde passagiers aantallen.

De focus van de analyse is het vinden van trends in wijzigingen van het aantal taxi-overschrijvingen in de loop van de tijd. U kunt twee andere Azure open gegevens sets ([open bare feest dagen](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) en [weers gegevens](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)) analyseren om inzicht te krijgen in de uitschieters van het aantal taxi-onderdrukkingen.

## <a name="create-credentials"></a>Referenties maken

```sql
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/nyctlc]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO
```

## <a name="automatic-schema-inference"></a>Automatische schema-deinterferentie

Omdat de gegevens zijn opgeslagen in de Parquet-bestands indeling, is automatische schema-interferentie beschikbaar, zodat de gegevens eenvoudig kunnen worden opgevraagd door de gegevens typen van alle kolommen in de bestanden. Daarnaast kunt u met een virtueel kolom mechanisme en de functie filepath een bepaalde subset van bestanden filteren.

Laten we eerst de NYC-gegevens over de taxi bekijken door de volgende query uit te voeren:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

Hieronder ziet u het resultaat fragment voor de NYC taxi-gegevens:

![resultaat fragment](./media/tutorial-data-analyst/1.png)

Op dezelfde manier kunnen we een query uitvoeren op de gegevensset van de open bare feest dagen met de volgende query:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Hieronder ziet u het resultaat fragment voor de gegevensset van de open bare feest dagen:

![resultaat fragment 2](./media/tutorial-data-analyst/2.png)

Ten slotte kunnen we ook een query uitvoeren op de weer gegevensset met behulp van de volgende query:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Hieronder ziet u het resultaat fragment voor de weer gegevensset:

![resultaat fragment 3](./media/tutorial-data-analyst/3.png)

Meer informatie over de betekenis van de afzonderlijke kolommen vindt u in de beschrijvingen van de [NYC-taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/), [open bare feest dagen](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)en [weer gegevens](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) sets.

## <a name="time-series-seasonality-and-outlier-analysis"></a>Tijd reeks, seizoensgebondenheid en uitschieter analyse

Met de volgende query kunt u heel eenvoudig een samen vatting van elk jaarlijks aantal Taxis maken:

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

Hieronder ziet u het resultaat fragment voor het jaarlijkse aantal taxi-onderdrukkingen:

![resultaat fragment 4](./media/tutorial-data-analyst/4.png)

De gegevens kunnen worden gevisualiseerd in Synapse Studio door over te scha kelen van de tabel naar de grafiek weergave. U kunt kiezen uit verschillende grafiek typen (vlak, staaf, kolom, lijn, cirkel en sprei ding). In dit geval wordt de kolom diagram met de categorie kolom ingesteld op ' current_year ':

![resultaat visualisatie 5](./media/tutorial-data-analyst/5.png)

Vanuit deze visualisatie kan een trend van een afnemend aantal onderdrukkingen in jaren duidelijk worden gezien als gevolg van een recente verhoogde populariteit van het delen van bedrijven.

> [!NOTE]
> Op het moment van het schrijven van deze zelf studie zijn de gegevens voor 2019 onvolledig, waardoor er een groot aantal onderdrukkingen is voor dat jaar.

We gaan nu onze analyse over één jaar richten, bijvoorbeeld 2016. De volgende query retourneert een dagelijks aantal onderdrukkingen gedurende dat jaar:

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

Hieronder ziet u het resultaat fragment voor deze query:

![resultaat fragment 6](./media/tutorial-data-analyst/6.png)

Ook hier kunnen we eenvoudig gegevens visualiseren door kolom diagram te tekenen met categorie kolom "current_day" en legenda (reeks) kolom "rides_per_day".

![resultaat visualisatie 7](./media/tutorial-data-analyst/7.png)

Vanuit het waarnemings punt kan worden geobserveerd dat er een wekelijks patroon is, met de piek van zaterdag. Tijdens de zomer maanden zijn er minder Taxis die worden gezien door de vakantie periode. Er zijn echter ook aanzienlijke dalingen in het aantal taxi-overschrijvingen zonder duidelijke patroon wanneer en waarom ze optreden.

Laten we eens kijken of die drup pels mogelijk met open bare feest dagen kunnen worden gecorreleerd door NYCe taxi-onderdrukkingen samen te voegen met de gegevensset van de open bare feest dagen:

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![resultaat visualisatie 8](./media/tutorial-data-analyst/8.png)

Deze keer willen we het aantal taxi-overschrijvingen markeren tijdens open bare feest dagen. Hiervoor kiezen we "geen" voor categorie kolom en "rides_per_day" en "vakantie" als legenda kolommen (reeks).

![resultaat visualisatie 9](./media/tutorial-data-analyst/9.png)

Vanuit het waarnemings punt kan het duidelijk worden gezien dat er tijdens open bare feest dagen een aantal van de verschillende Taxis wordt verlaagd. Er is echter nog steeds een onduidelijke, enorme daling op 23 januari. Laten we het weer in NYC op die dag controleren door de weers gegevensset te doorzoeken:

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![resultaat visualisatie 10](./media/tutorial-data-analyst/10.png)

De resultaten van de query geven aan dat de verwijdering van een aantal taxi-overschrijvingen als gevolg van de volgende:

- Blizzard op die dag in NYC, omdat er sprake is van zware sneeuw (ongeveer 30 cm)
- het was koud (de Tempe ratuur onder nul graden Celsius)
- en wikkeling (~ 10 miljoen/s)

In deze zelf studie hebt u geleerd hoe gegevens analist snel experimentele gegevens analyse kan uitvoeren, en kunt u eenvoudig verschillende gegevens sets combi neren met behulp van SQL op aanvraag en de resultaten visualiseren met behulp van Azure Synapse Studio.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [BIND SQL on-demand to Power BI Desktop & rapport artikel maken](tutorial-connect-power-bi-desktop.md) voor meer informatie over het verbinden van SQL op aanvraag met Power bi Desktop en het maken van rapporten.
 
