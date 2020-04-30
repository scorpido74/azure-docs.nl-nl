---
title: Queryresultaten opslaan in opslag
description: In dit artikel leert u hoe u query resultaten opslaat in de opslag met behulp van SQL op aanvraag (preview).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 462185feb2b9cbebd17ce9cba54c2b23deea6c75
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81425145"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Query resultaten opslaan in opslag met behulp van SQL on-demand (preview) met behulp van Azure Synapse Analytics

In dit artikel leert u hoe u query resultaten opslaat in de opslag met behulp van SQL op aanvraag (preview).

## <a name="prerequisites"></a>Vereisten

De eerste stap is om de onderstaande artikelen te controleren en te controleren of u aan de vereisten voldoet:

- [Eerste keer instellen](query-data-storage.md#first-time-setup)
- [Vereisten](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>Externe tabel maken als selecteren

U kunt de instructie CREATE EXTERNAL TABLE AS SELECT (CETAS) gebruiken om de query resultaten op te slaan in de opslag.

> [!NOTE]
> Wijzig de eerste regel in de query, bijvoorbeeld [mydbname], zodat u de data base gebruikt die u hebt gemaakt. Als u nog geen data base hebt gemaakt, lees dan [eerst de installatie](query-data-storage.md#first-time-setup).

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

U kunt externe tabellen die zijn gemaakt via CETAS gebruiken als een gewone externe tabel.

> [!NOTE]
> Wijzig de eerste regel in de query, bijvoorbeeld [mydbname], zodat u de data base gebruikt die u hebt gemaakt. Als u nog geen data base hebt gemaakt, lees dan [eerst de installatie](query-data-storage.md#first-time-setup).

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

Raadpleeg voor meer informatie over het uitvoeren van een query op verschillende bestands typen de artikelen [query ENKELVOUDIG CSV-bestand](query-single-csv-file.md), [query uitvoeren op Parquet-bestanden](query-parquet-files.md)en JSON- [bestanden opvragen](query-json-files.md) .
