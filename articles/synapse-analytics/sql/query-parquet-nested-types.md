---
title: Geneste querytypen met SQL on-demand (voorbeeld)
description: In dit artikel leert u hoe u parketgenesetypen opvragen.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a1e3d3c7494aa75b3f6d481d12135316791772d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431655"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Geneste querytypen met SQL on-demand (preview) in Azure Synapse Analytics

In dit artikel leert u hoe u een query schrijft met SQL on-demand (preview) in Azure Synapse Analytics.  In deze query worden parketgenesetypen gelezen.

## <a name="prerequisites"></a>Vereisten

Voordat u de rest van dit artikel leest, leest u de volgende artikelen:

- [First-time setup](query-data-storage.md#first-time-setup)
- [Vereisten](query-data-storage.md#prerequisites)

## <a name="project-nested-or-repeated-data"></a>Projectgenese of herhaalde gegevens

In de volgende query wordt het *parketbestand justSimpleArray.parquet* gelezen. Het projecteert alle kolommen uit het parketbestand, inclusief geneste of herhaalde gegevens.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>Toegang tot elementen uit geneste kolommen

In de volgende query wordt het bestand *structExample.parquet* gelezen en wordt weergegeven hoe elementen van een geneste kolom kunnen worden weergegeven:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/structExample.parquet',
        FORMAT='PARQUET'
    )
    WITH (
        -- you can see original n"sted columns values by uncommenting lines below
        --DateStruct VARCHAR(8000),
        [DateStruct.Date] DATE,
        --TimeStruct VARCHAR(8000),
        [TimeStruct.Time] TIME,
        --TimestampStruct VARCHAR(8000),
        [TimestampStruct.Timestamp] DATETIME2,
        --DecimalStruct VARCHAR(8000),
        [DecimalStruct.Decimal] DECIMAL(18, 5),
        --FloatStruct VARCHAR(8000),
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Toegang tot elementen uit herhaalde kolommen

In de volgende query wordt het *parketbestand justSimpleArray.parquet* gelezen en wordt [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) gebruikt om een **scalaire** element op te halen vanuit een herhaalde kolom, zoals een array of kaart:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

De volgende query leest het *mapExample.parketbestand* en gebruikt [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) om een **niet-scalair** element op te halen vanuit een herhaalde kolom, zoals een array of kaart:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/mapExample.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="next-steps"></a>Volgende stappen

In het volgende artikel ziet u hoe u [JSON-bestanden opvragen.](query-json-files.md)
