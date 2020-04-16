---
title: Externe tabellen maken en gebruiken in SQL on-demand (voorbeeld)
description: In deze sectie leert u hoe u externe tabellen maken en gebruiken in SQL on-demand (voorbeeld). Externe tabellen zijn handig wanneer u de toegang tot externe gegevens in SQL On-demand wilt beheren en als u hulpprogramma's wilt gebruiken, zoals Power BI, in combinatie met SQL on-demand.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a708e5e713e62ab5b7ca70b61235a84830b051cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424550"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Externe tabellen maken en gebruiken in SQL on-demand (preview) met Azure Synapse Analytics

In deze sectie leert u hoe u externe tabellen maken en gebruiken in SQL on-demand (voorbeeld). Externe tabellen zijn handig wanneer u de toegang tot externe gegevens in SQL On-demand wilt beheren en als u hulpprogramma's wilt gebruiken, zoals Power BI, in combinatie met SQL on-demand.

## <a name="prerequisites"></a>Vereisten

Uw eerste stap is om de onderstaande artikelen te bekijken en ervoor te zorgen dat u voldoet aan de vereisten voor het maken en gebruiken van SQL on-demand externe tabellen:

- [First-time setup](query-data-storage.md#first-time-setup)
- [Vereisten](query-data-storage.md#prerequisites)

## <a name="create-an-external-table"></a>Een externe tabel maken

U externe tabellen op dezelfde manier maken als gewone externe SQL Server-tabellen. De onderstaande query maakt een externe tabel met het bestand *population.csv.*

> [!NOTE]
> Wijzig de eerste regel in de query, d.w.z. [mydbname], zodat u de database gebruikt die u hebt gemaakt. Als u geen database hebt gemaakt, leest u [First-time setup](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [CsvDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT CSVFileFormat
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT,
    FORMAT_OPTIONS (
        FIELD_TERMINATOR = ',',
        STRING_DELIMITER = '"',
        FIRST_ROW = 2
    )
);
GO

CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
);
WITH (
    LOCATION = 'population/population.csv',
    DATA_SOURCE = CsvDataSource,
    FILE_FORMAT = CSVFileFormat
);
GO
```

## <a name="use-a-external-table"></a>Een externe tabel gebruiken

U externe tabellen in uw query's op dezelfde manier gebruiken als in SQL Server-query's.

In de volgende query wordt de externe *bevolkingstabel* die we hebben gemaakt in [Een externe tabelsectie maken,](#create-an-external-table) aante voeren. Het brengt landnamen met hun bevolking in 2019 in dalende volgorde terug.

> [!NOTE]
> Wijzig de eerste regel in de query, d.w.z. [mydbname], zodat u de database gebruikt die u hebt gemaakt. Als u geen database hebt gemaakt, leest u [First-time setup](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Volgende stappen

Voor informatie over het opslaan van resultaten van een query naar de opslag raadpleegt u de [queryresultaten van de store naar de opslag](../sql/create-external-table-as-select.md).
