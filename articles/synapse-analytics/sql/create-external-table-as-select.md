---
title: Queryresultaten opslaan in opslag
description: In dit artikel leert u hoe u queryresultaten opslaat in opslag met SQL on-demand (voorbeeld).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 462185feb2b9cbebd17ce9cba54c2b23deea6c75
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425145"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Queryresultaten opslaan in opslag met SQL on-demand (preview) met Azure Synapse Analytics

In dit artikel leert u hoe u queryresultaten op de opslag opslaan met SQL On-demand (voorbeeld).

## <a name="prerequisites"></a>Vereisten

Uw eerste stap is om de onderstaande artikelen te bekijken en ervoor te zorgen dat u aan de voorwaarden hebt voldaan:

- [First-time setup](query-data-storage.md#first-time-setup)
- [Vereisten](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>Externe tabel maken als selecteren

U de instructie EXTERNE TABEL MAKEN ALS SELECT (CETAS) gebruiken om de queryresultaten op te slaan in de opslag.

> [!NOTE]
> Wijzig de eerste regel in de query, d.w.z. [mydbname], zodat u de database gebruikt die u hebt gemaakt. Als u geen database hebt gemaakt, leest u [First-time setup](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://showdemoweu.dfs.core.windows.net/data/population_csv/population.csv',
        FORMAT='CSV'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-a-external-table-created"></a>Een externe tabel gebruiken die is gemaakt

U externe tabel gebruiken die via CETAS is gemaakt, zoals een gewone externe tabel.

> [!NOTE]
> Wijzig de eerste regel in de query, d.w.z. [mydbname], zodat u de database gebruikt die u hebt gemaakt. Als u geen database hebt gemaakt, leest u [First-time setup](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg het [bestand Query single CSV,](query-single-csv-file.md) [Query Parquet files](query-parquet-files.md)en Query [JSON-bestanden](query-json-files.md) voor informatie over het opvragen van verschillende bestandstypen.
