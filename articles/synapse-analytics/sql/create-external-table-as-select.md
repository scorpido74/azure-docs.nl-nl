---
title: Queryresultaten opslaan in opslag
description: In dit artikel leert u hoe u queryresultaten opslaat in de opslag met behulp van SQL op aanvraag (preview).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: cbf6d42f3b1d130a6bf89f07bd3a7009ff0e8fa8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647515"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Queryresultaten opslaan in opslag met behulp van SQL op aanvraag (preview) met behulp van Azure Synapse Analytics

In dit artikel leert u hoe u queryresultaten opslaat in de opslag met behulp van SQL op aanvraag (preview).

## <a name="prerequisites"></a>Vereisten

De eerste stap is om de onderstaande artikelen door te nemen en te controleren of u aan de vereisten voldoet:

- [De eerste installatie](query-data-storage.md#first-time-setup)
- [Vereisten](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>Create external table as select

U kunt de instructie CREATE EXTERNAL TABLE AS SELECT (CETAS) gebruiken om de queryresultaten op te slaan in de opslag.

> [!NOTE]
> Wijzig de eerste regel in de query, d.w.z. [mydbname], zodat u de database gebruikt die u hebt gemaakt. Als u nog geen database hebt gemaakt, lees dan [De eerste installatie](query-data-storage.md#first-time-setup). U moet de LOCATION voor de externe gegevensbron MyDataSource wijzigen zodat deze verwijst naar een locatie waarvoor u schrijfmachtigingen hebt. 

```sql
USE [mydbname];
GO

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL [SasTokenWrite]
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
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT='CSV'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-the-external-table"></a>De externe tabel gebruiken

U kunt de externe tabel die is gemaakt via CETAS gebruiken als een gewone externe tabel.

> [!NOTE]
> Wijzig de eerste regel in de query, d.w.z. [mydbname], zodat u de database gebruikt die u hebt gemaakt. Als u nog geen database hebt gemaakt, lees dan [De eerste installatie](query-data-storage.md#first-time-setup).

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

Raadpleeg voor meer informatie over het uitvoeren van een query op verschillende bestandstypen de artikelen [Query's uitvoeren op één CSV-bestand](query-single-csv-file.md), [Query uitvoeren op Parquet-bestanden](query-parquet-files.md)en [Query's uitvoeren op JSON-bestanden](query-json-files.md).
