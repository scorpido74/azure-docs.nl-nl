---
title: Weer gaven maken en gebruiken in SQL op aanvraag (preview)
description: In deze sectie leert u hoe u weer gaven maakt en gebruikt om query's op aanvraag (preview) in SQL te laten teruglopen. Met weer gaven kunt u deze query's opnieuw gebruiken. Er zijn ook weer gaven nodig als u hulpprogram ma's, zoals Power BI, wilt gebruiken in combi natie met SQL op aanvraag.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0f5323193706fdd00739be6c71a4fe12cfedf21b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424536"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Weer gaven maken en gebruiken in SQL op aanvraag (preview) met behulp van Azure Synapse Analytics

In deze sectie leert u hoe u weer gaven maakt en gebruikt om query's op aanvraag (preview) in SQL te laten teruglopen. Met weer gaven kunt u deze query's opnieuw gebruiken. Er zijn ook weer gaven nodig als u hulpprogram ma's, zoals Power BI, wilt gebruiken in combi natie met SQL op aanvraag.

## <a name="prerequisites"></a>Vereisten

De eerste stap is om de onderstaande artikelen te controleren en te controleren of u voldoet aan de vereisten voor het maken en gebruiken van SQL on-demand-weer gaven:

- [Eerste keer instellen](query-data-storage.md#first-time-setup)
- [Vereisten](query-data-storage.md#prerequisites)

## <a name="create-a-view"></a>Een weergave maken

U kunt weer gaven op dezelfde manier maken als normale SQL Server weergaven. De onderstaande query maakt weer gave die het bestand *populatie. CSV* leest.

> [!NOTE]
> Wijzig de eerste regel in de query, bijvoorbeeld [mydbname], zodat u de data base gebruikt die u hebt gemaakt. Als u nog geen data base hebt gemaakt, lees dan [eerst de installatie](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV', 
        FIELDTERMINATOR =',', 
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r];
```

## <a name="use-a-view"></a>Een weer gave gebruiken

U kunt weer gaven in uw query's op dezelfde manier gebruiken als bij het gebruik van weer gaven in SQL Server query's.

Met de volgende query wordt gedemonstreerd met behulp van de *population_csve* weer gave die u in [een weer gave maken](#create-a-view)hebt gemaakt. Het retourneert land namen met hun populatie in 2019 in aflopende volg orde.

> [!NOTE]
> Wijzig de eerste regel in de query, bijvoorbeeld [mydbname], zodat u de data base gebruikt die u hebt gemaakt. Als u nog geen data base hebt gemaakt, lees dan [eerst de installatie](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie over het uitvoeren van een query op verschillende bestands typen de artikelen [query ENKELVOUDIG CSV-bestand](query-single-csv-file.md), [query uitvoeren op Parquet-bestanden](query-parquet-files.md)en JSON- [bestanden opvragen](query-json-files.md) .
