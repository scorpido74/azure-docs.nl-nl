---
title: 'Zelfstudie gegevensanalyse: SQL on-demand (preview) gebruiken voor het analyseren van Azure Open Datasets in Azure Synapse Studio (preview)'
description: In deze zelfstudie leert u hoe u eenvoudig verkennende gegevensanalyse kunt uitvoeren door verschillende Azure Open Datasets te combineren met behulp van SQL on-demand en de resultaten in Azure Synapse Studio kunt analyseren.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: b2fe4dea27564b96c5ef1734dc16ca4525011d17
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745645"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>Gebruik SQL on-demand (preview) voor het analyseren van Azure Open Datasets en visualiseer resultaten in Azure Synapse Studio (preview)

In deze zelfstudie leert u hoe u verkennende gegevensanalyse kunt uitvoeren door verschillende Azure Open Datasets te combineren met behulp van SQL on-demand en de resultaten in Azure Synapse Studio kunt analyseren.

U analyseert met name de [New York City (NYC) taxi-gegevensset](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) die datums/-tijden van ophalen en afzetten, locaties van ophalen en afzetten, reisafstanden, gespecificeerde tarieven, tarieftypen, betalingstypen en door de chauffeur gemelde passagiersaantallen bevat.

De focus van de analyse is het vinden van trends in wijzigingen van het aantal taxiritten in de loop van de tijd. U kunt twee andere Azure Open Datasets ([Openbare feestdagen](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) en [weersgegevens](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)) analyseren om inzicht te krijgen in de uitschieters in de hoeveelheid taxiritten.

## <a name="create-data-source"></a>Gegevensbron maken

Gegevensbronobject wordt gebruikt om te verwijzen naar een Azure-opslagaccount waar u gegevens moet analyseren. Voor openbaar beschikbare opslag is geen referentie vereist voor toegang tot de opslag.

```sql
-- There is no credential in data surce. We are using public storage account which doesn't need a credential.
CREATE EXTERNAL DATA SOURCE AzureOpenData
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/')
```

## <a name="automatic-schema-inference"></a>Automatische schemadeductie

Omdat de gegevens zijn opgeslagen in de bestandsindeling Parquet, is automatische schemadeductie beschikbaar, zodat de gegevens eenvoudig kunnen worden opgevraagd zonder dat de gegevenstypen van alle kolommen in de bestanden moeten worden genoemd. Daarnaast kunt u met een virtueel kolommechanisme en de functie bestandspad een bepaalde subset van bestanden filteren.

Eerst bekijken we de gegevens van NYC Taxi door de volgende query uit te voeren:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [nyc]
```

Hieronder ziet u het resultaatsfragment voor de gegevens van NYC Taxi:

![resultaatsfragment](./media/tutorial-data-analyst/1.png)

Op dezelfde manier kunnen we een query uitvoeren op de gegevensset van de openbare feestdagen met de volgende query:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'holidaydatacontainer/Processed/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Hieronder ziet u het resultaatsfragment voor de gegevensset van de openbare feestdagen:

![resultaatsfragment 2](./media/tutorial-data-analyst/2.png)

Tot slot kunnen we ook een query uitvoeren op de gegevensset over het weer met behulp van de volgende query:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [weather]
```

Hieronder ziet u het resultaatsfragment voor de gegevensset over het weer:

![resultaatsfragment 3](./media/tutorial-data-analyst/3.png)

Meer informatie over de betekenis van de afzonderlijke kolommen vindt u in de beschrijvingen van de gegevenssets [NYC Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/), [openbare feestdagen](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) en [Weergegevens](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/).

## <a name="time-series-seasonality-and-outlier-analysis"></a>Analyse van tijdreeks, seizoensgebondenheid en uitbijter

Met de volgende query kunt u heel eenvoudig een samenvatting van het jaarlijks aantal taxiritten maken:

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

Hieronder ziet u het resultaatsfragment voor het jaarlijkse aantal taxiritten:

![resultaatsfragment 4](./media/tutorial-data-analyst/4.png)

De gegevens kunnen worden gevisualiseerd in Synapse Studio door over te schakelen van de Tabel- naar de Grafiekweergave. U kunt kiezen uit verschillende grafiektypen (gebied, staaf, kolom, lijn, cirkel en spreiding). In dit geval wordt de kolomdiagram met de categoriekolom ingesteld op current_year:

![visualisatie van resultaat 5](./media/tutorial-data-analyst/5.png)

Vanuit deze visualisatie kan een trend van een afnemend aantal ritten over de jaren duidelijk worden gezien als gevolg van een recente verhoogde populariteit van bedrijven voor het delen van taxiritten.

> [!NOTE]
> Op het moment van het schrijven van deze zelfstudie zijn de gegevens voor 2019 onvolledig, waardoor er een grote vermindering is in het aantal ritten voor dat jaar.

We gaan nu onze analyse op één jaar richten, bijvoorbeeld 2016. De volgende query retourneert een dagelijks aantal ritten gedurende dat jaar:

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

Hieronder ziet u het resultaatsfragment voor deze query:

![resultaatsfragment 6](./media/tutorial-data-analyst/6.png)

Ook hier kunnen we eenvoudig gegevens visualiseren door een kolomdiagram te tekenen met categoriekolom current_day en legenda (reeks)-kolom rides_per_day.

![visualisatie van resultaat 7](./media/tutorial-data-analyst/7.png)

Vanuit de plot kan worden gezien dat er een wekelijks patroon is, met de piek op zaterdag. Tijdens de zomermaanden zijn er minder taxiritten, vanwege de vakantieperiode. Er zijn echter ook aanzienlijke dalingen in het aantal taxiritten zonder duidelijk patroon wanneer en waarom ze optreden.

Laten we eens kijken of die dalingen mogelijk met openbare feestdagen kunnen worden gecorreleerd door ritten van NYC Taxi samen te voegen met de gegevensset van de openbare feestdagen:

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            DATA_SOURCE = 'AzureOpenData',
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
            BULK 'holidaydatacontainer/Processed/*.parquet',
            DATA_SOURCE = 'AzureOpenData',
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

![visualisatie van resultaat 8](./media/tutorial-data-analyst/8.png)

Deze keer willen we het aantal taxiritten tijdens openbare feestdagen markeren. Hiervoor kiezen we 'geen' voor categoriekolom en 'rides_per_day' en 'feestdag' als legenda (reeks)-kolommen.

![visualisatie van resultaat 9](./media/tutorial-data-analyst/9.png)

Vanuit de plot kan het duidelijk worden gezien dat er tijdens openbare feestdagen minder taxiritten zijn. Er is echter nog steeds een onduidelijke, enorme daling op 23 januari. Laten we het weer in NYC op die dag controleren door de weersgegevensset te doorzoeken:

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
        BULK 'isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![visualisatie van resultaat 10](./media/tutorial-data-analyst/10.png)

De resultaten van de query geven aan dat de daling van een aantal taxiritten kwam door het volgende:

- er was die dag een storm in NYC, met zware sneeuw (ongeveer 30 cm)
- het was koud (temperatuur onder nul graden Celsius)
- en het waaide hard (ongeveer 10 m/s)

In deze zelfstudie hebt u geleerd hoe gegevensanalist snel verkennende gegevensanalyse kan uitvoeren, eenvoudig verschillende gegevenssets kan combineren met behulp van SQL on-demand en de resultaten kan visualiseren met behulp van Azure Synapse Studio.

## <a name="next-steps"></a>Volgende stappen

Bekijk het artikel [Verbinding maken met SQL on-demand voor Power BI Desktop & rapport maken](tutorial-connect-power-bi-desktop.md) voor meer informatie over het verbinden van SQL on-demand met Power BI Desktop en het maken van rapporten.
 
