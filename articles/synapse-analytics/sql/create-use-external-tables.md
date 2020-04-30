---
title: Externe tabellen maken en gebruiken in SQL op aanvraag (preview)
description: In deze sectie leert u hoe u externe tabellen kunt maken en gebruiken in SQL op aanvraag (preview). Externe tabellen zijn handig als u de toegang tot externe gegevens in SQL on-demand wilt beheren en als u hulpprogram ma's, zoals Power BI, wilt gebruiken in combi natie met SQL op aanvraag.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a708e5e713e62ab5b7ca70b61235a84830b051cd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424550"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Externe tabellen maken en gebruiken in SQL on-demand (preview) met behulp van Azure Synapse Analytics

In deze sectie leert u hoe u externe tabellen kunt maken en gebruiken in SQL op aanvraag (preview). Externe tabellen zijn handig als u de toegang tot externe gegevens in SQL on-demand wilt beheren en als u hulpprogram ma's, zoals Power BI, wilt gebruiken in combi natie met SQL op aanvraag.

## <a name="prerequisites"></a>Vereisten

De eerste stap is om de onderstaande artikelen te controleren en ervoor te zorgen dat u voldoet aan de vereisten voor het maken en gebruiken van externe SQL on-demand-tabellen:

- [Eerste keer instellen](query-data-storage.md#first-time-setup)
- [Vereisten](query-data-storage.md#prerequisites)

## <a name="create-an-external-table"></a>Een externe tabel maken

U kunt externe tabellen op dezelfde manier maken als gewone SQL Server externe tabellen. Met de onderstaande query maakt u een externe tabel die het bestand *populatie. CSV* leest.

> [!NOTE]
> Wijzig de eerste regel in de query, bijvoorbeeld [mydbname], zodat u de data base gebruikt die u hebt gemaakt. Als u nog geen data base hebt gemaakt, lees dan [eerst de installatie](query-data-storage.md#first-time-setup).

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

U kunt externe tabellen in uw query's op dezelfde manier gebruiken als u ze gebruikt in SQL Server query's.

De volgende query illustreert het gebruik van de externe tabel *populatie* die we hebben gemaakt in de sectie [een externe tabel maken](#create-an-external-table) . Het retourneert land namen met hun populatie in 2019 in aflopende volg orde.

> [!NOTE]
> Wijzig de eerste regel in de query, bijvoorbeeld [mydbname], zodat u de data base gebruikt die u hebt gemaakt. Als u nog geen data base hebt gemaakt, lees dan [eerst de installatie](query-data-storage.md#first-time-setup).

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

Voor informatie over het opslaan van resultaten van een query in de opslag raadpleegt u de [query resultaten opslaan in de opslag](../sql/create-external-table-as-select.md).
