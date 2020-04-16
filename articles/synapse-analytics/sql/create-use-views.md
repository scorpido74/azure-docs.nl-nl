---
title: Weergaven maken en gebruiken in SQL on-demand (voorbeeld)
description: In deze sectie leert u hoe u weergaven maken en gebruiken om SQL on-demand (preview)-query's te verpakken. Met weergaven u deze query's opnieuw gebruiken. Weergaven zijn ook nodig als u tools, zoals Power BI, wilt gebruiken in combinatie met SQL on-demand.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0f5323193706fdd00739be6c71a4fe12cfedf21b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424536"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Weergaven maken en gebruiken in SQL on-demand (preview) met Azure Synapse Analytics

In deze sectie leert u hoe u weergaven maken en gebruiken om SQL on-demand (preview)-query's te verpakken. Met weergaven u deze query's opnieuw gebruiken. Weergaven zijn ook nodig als u tools, zoals Power BI, wilt gebruiken in combinatie met SQL on-demand.

## <a name="prerequisites"></a>Vereisten

Uw eerste stap is om de onderstaande artikelen te bekijken en ervoor te zorgen dat u voldoet aan de vereisten voor het maken en gebruiken van SQL on-demand weergaven:

- [First-time setup](query-data-storage.md#first-time-setup)
- [Vereisten](query-data-storage.md#prerequisites)

## <a name="create-a-view"></a>Een weergave maken

U weergaven maken op dezelfde manier als u normale SQL Server-weergaven maakt. De onderstaande query maakt een weergave met het leesbestand *population.csv.*

> [!NOTE]
> Wijzig de eerste regel in de query, d.w.z. [mydbname], zodat u de database gebruikt die u hebt gemaakt. Als u geen database hebt gemaakt, leest u [First-time setup](query-data-storage.md#first-time-setup).

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

## <a name="use-a-view"></a>Een weergave gebruiken

U weergaven in uw query's op dezelfde manier gebruiken als weergaven in SQL Server-query's.

De volgende query toont aan met behulp van de *population_csv* weergave die we hebben gemaakt in [Een weergave maken](#create-a-view). Het brengt landnamen met hun bevolking in 2019 in dalende volgorde terug.

> [!NOTE]
> Wijzig de eerste regel in de query, d.w.z. [mydbname], zodat u de database gebruikt die u hebt gemaakt. Als u geen database hebt gemaakt, leest u [First-time setup](query-data-storage.md#first-time-setup).

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

Raadpleeg het [bestand Query single CSV,](query-single-csv-file.md) [Query Parquet files](query-parquet-files.md)en Query [JSON-bestanden](query-json-files.md) voor informatie over het opvragen van verschillende bestandstypen.
