---
title: JSON-bestanden opvragen met SQL on-demand (voorbeeld)
description: In dit gedeelte wordt uitgelegd hoe u JSON-bestanden lezen met SQL on-demand in Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1054328216d0517b3f450ba4fe08f3bef32d68f7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431720"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>JSON-bestanden opvragen met SQL on-demand (preview) in Azure Synapse Analytics

In dit artikel leert u hoe u een query schrijft met SQL on-demand (preview) in Azure Synapse Analytics. Het doel van de query is om JSON-bestanden te lezen.

## <a name="prerequisites"></a>Vereisten

Voordat u de rest van dit artikel leest, leest u de volgende artikelen:

- [First-time setup](query-data-storage.md#first-time-setup)
- [Vereisten](query-data-storage.md#prerequisites)

## <a name="sample-json-files"></a>Voorbeeld van JSON-bestanden

De sectie hieronder bevat voorbeeldscripts om JSON-bestanden te lezen. Bestanden worden opgeslagen in een *json-container,* *mapboeken*en bevatten één boekvermelding met de volgende structuur:

```json
{
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

## <a name="read-json-files"></a>JSON-bestanden lezen

Als u JSON-bestanden wilt verwerken met JSON_VALUE en [JSON_QUERY,](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)moet u het JSON-bestand in één kolom uit de opslag lezen. In het volgende script wordt het *bestand book1.json* als één kolom gelezen:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/book1.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r];
```

> [!NOTE]
> U leest het hele JSON-bestand als één rij of kolom. Veldterminator, FIELDQUOTE en ROWTERMINATOR zijn ingesteld op 0x0b.

## <a name="query-json-files-using-json_value"></a>JSON-bestanden opvragen met JSON_VALUE

De onderstaande query laat zien hoe u [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) gebruiken om scalaire waarden (titel, uitgever) op te halen uit een boek getiteld *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected articles:*

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-json_query"></a>JSON-bestanden opvragen met JSON_QUERY

In de volgende query ziet u hoe u [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) gebruiken om objecten en arrays (auteurs) op te halen uit een boek getiteld *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics:*

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-openjson"></a>JSON-bestanden opvragen met OPENJSON

In de volgende query wordt [OPENJSON gebruikt.](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Het zal ophalen objecten en eigenschappen in een boek getiteld *Probabilistic en statistische methoden in cryptologie, een inleiding door geselecteerde artikelen:*

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent NVARCHAR(4000) --Note that you have to use NVARCHAR(4000) for OPENJSON to work.
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>Volgende stappen

De volgende artikelen in deze serie zal laten zien hoe:

- [Mappen en meerdere bestanden opvragen](query-folders-multiple-csv-files.md)
- [Weergaven maken en gebruiken](create-use-views.md).
