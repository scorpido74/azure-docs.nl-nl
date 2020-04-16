---
title: Zelfstudie gegevensanalist - SQL on-demand (voorbeeld) gebruiken om Azure Open Datasets in Azure Synapse Studio te analyseren (voorbeeld)
description: In deze zelfstudie leert u hoe u eenvoudig verkennende gegevensanalyse uitvoeren waarbij verschillende Azure Open Datasets worden gecombineerd met SQL on-demand (preview) en de resultaten in Azure Synapse Studio kunnen visualiseren.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1080001cb222f91503080914d7fb253e5ee82626
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423227"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>SQL on-demand (preview) gebruiken om Azure Open Datasets te analyseren en de resultaten in Azure Synapse Studio te visualiseren (preview)

In deze zelfstudie leert u hoe u verkennende gegevensanalyse uitvoert door verschillende Azure Open Datasets te combineren met SQL on-demand en vervolgens de resultaten in Azure Synapse Studio te visualiseren.

In het bijzonder analyseert u de [taxigegevensset Van New York City (NYC)](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) die ophaal- en inleverdatums/-tijden, ophaal- en afleverlocaties, reisafstanden, gespecificeerde tarieven, tarieftypen, betalingstypen en door de chauffeur gerapporteerde passagierstellingen omvat.

De focus van de analyse is het vinden van trends in veranderingen in het aantal taxiritten in de tijd. U analyseert twee andere Azure Open Datasets[(feestdagen](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) en [weergegevens)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)om inzicht te krijgen in de uitschieters in het aantal taxiritten.

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

## <a name="automatic-schema-inference"></a>Automatische schema-gevolgtrekking

Aangezien gegevens worden opgeslagen in de indeling van het parketbestand, is automatische schema-inference beschikbaar, zodat men de gegevens gemakkelijk kan opvragen zonder dat u de gegevenstypen van alle kolommen in de bestanden hoeft op te sommen. Bovendien kan men gebruik maken van virtuele kolom mechanisme en filepath functie te filteren uit een bepaalde subset van bestanden.

Laten we eerst vertrouwd raken met de NYC Taxi-gegevens door de volgende query uit te voeren:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

Het volgende toont het resultaatfragment voor de NYC Taxi-gegevens:

![resultaatfragment](./media/tutorial-data-analyst/1.png)

Op dezelfde manier kunnen we de gegevensset feestdagen opvragen met de volgende query:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Het volgende toont het resultaatfragment voor de gegevensset feestdagen:

![resultaatfragment 2](./media/tutorial-data-analyst/2.png)

Tot slot kunnen we ook de weersset opvragen met de volgende query:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Het volgende toont het resultaatfragment voor de weergegevensset:

![resultaatfragment 3](./media/tutorial-data-analyst/3.png)

U meer informatie over de betekenis van de afzonderlijke kolommen in de beschrijvingen van de [NYC Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/), [Feestdagen](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/), en [Weergegevens](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) datasets.

## <a name="time-series-seasonality-and-outlier-analysis"></a>Tijdreeksen, seizoensinvloeden en uitschietersanalyse

U eenvoudig een overzicht geven van het jaarlijkse aantal taxiritten met behulp van de volgende query:

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

Het volgende toont het resultaatfragment voor het jaarlijkse aantal taxiritten:

![resultaatfragment 4](./media/tutorial-data-analyst/4.png)

De gegevens kunnen worden gevisualiseerd in Synapse Studio door over te schakelen van tabel naar grafiekweergave. U kiezen uit verschillende grafiektypen (gebied, balk, kolom, lijn, cirkel en spreiding). Laten we in dit geval het kolomdiagram uitzetten met kolom Categorie ingesteld op 'current_year':

![resultaatvisualisatie 5](./media/tutorial-data-analyst/5.png)

Uit deze visualisatie, een trend van een dalend aantal ritten over jaren kan duidelijk worden gezien, vermoedelijk te wijten aan een recente toegenomen populariteit van ride sharing bedrijven.

> [!NOTE]
> Op het moment van schrijven van deze tutorial zijn de gegevens voor 2019 onvolledig, dus er is een enorme daling in een aantal ritten voor dat jaar.

Laten we onze analyse vervolgens richten op één jaar, bijvoorbeeld 2016. Met de volgende query wordt het dagelijkse aantal ritten in dat jaar geretourneerd:

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

Het volgende toont het resultaatfragment voor deze query:

![resultaatfragment 6](./media/tutorial-data-analyst/6.png)

Nogmaals, we kunnen eenvoudig gegevens visualiseren door kolomdiagram in kaart te brengen met kolom categorie "current_day" en Legenda (serie) kolom "rides_per_day".

![resultaatvisualisatie 7](./media/tutorial-data-analyst/7.png)

Uit de plot, kan worden waargenomen dat er een wekelijks patroon, met de piek van de zaterdag. Tijdens de zomermaanden zijn er minder taxiritten vanwege de vakantieperiode. Er zijn echter ook enkele significante dalingen in het aantal taxiritten zonder een duidelijk patroon wanneer en waarom ze zich voordoen.

Vervolgens, laten we eens kijken of deze druppels zijn potentieel gecorreleerd met feestdagen door toetreding NYC taxiritten met de feestdagen dataset:

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

![resultaatvisualisatie 8](./media/tutorial-data-analyst/8.png)

Deze keer willen we het aantal taxiritten tijdens feestdagen benadrukken. Daarvoor kiezen we 'geen' voor categoriekolom en 'rides_per_day' en 'vakantie' als kolommen legenda (serie).

![resultaatvisualisatie 9](./media/tutorial-data-analyst/9.png)

Uit het perceel blijkt duidelijk dat tijdens feestdagen een aantal taxiritten lager is. Echter, er is nog steeds een onverklaarbare enorme daling op 23 januari. Laten we het weer in NYC op die dag controleren door de weergegevensset op te vragen:

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

![resultaatvisualisatie 10](./media/tutorial-data-analyst/10.png)

De resultaten van de query geven aan dat de daling van een aantal taxiritten te wijten was aan de:

- sneeuwstorm op die dag in NYC, want er was zware sneeuw (~ 30 cm)
- het was koud (temperatuur onder nul graden Celsius)
- en winderig (~ 10m/s)

Deze zelfstudie laat zien hoe gegevensanalist snel verkennende gegevensanalyse kan uitvoeren, eenvoudig verschillende gegevenssets kan combineren met SQL on-demand en de resultaten kan visualiseren met Azure Synapse Studio.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [On-demand Connect NAAR Power BI Desktop & rapportartikel maken](tutorial-connect-power-bi-desktop.md) om te leren hoe u SQL on-demand verbinden met Power BI Desktop en rapporten maken.
 
