---
title: Een query uitvoeren op JSON-bestanden met behulp van SQL op aanvraag (preview)
description: In deze sectie wordt uitgelegd hoe u JSON-bestanden kunt lezen met behulp van SQL op aanvraag in azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 5d02736e9cb0a612e434dc5a79a73d7a62785728
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85207648"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Een query uitvoeren op JSON-bestanden met behulp van SQL on-demand (preview) in azure Synapse Analytics

In dit artikel leert u hoe u een query schrijft met behulp van SQL on-demand (preview) in azure Synapse Analytics. Het doel van de query is het lezen van JSON-bestanden. De ondersteunde indelingen worden weergegeven in [OPENROWSET](develop-openrowset.md).

## <a name="prerequisites"></a>Vereisten

De eerste stap bestaat uit het **maken van een database** waarin u de query's gaat uitvoeren. Initialiseer vervolgens de objecten door een [installatiescript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) uit te voeren op die database. Met dit installatie script worden de gegevens bronnen, referenties voor het data base-bereik en externe bestands indelingen gemaakt die in deze voor beelden worden gebruikt.

## <a name="sample-json-files"></a>Voor beeld van JSON-bestanden

De volgende sectie bevat voorbeeld scripts voor het lezen van JSON-bestanden. Bestanden worden opgeslagen in een *JSON* -container, in mappen *boeken*en bevatten één boek vermelding met de volgende structuur:

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

Als u JSON-bestanden wilt verwerken met JSON_VALUE en [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), moet u het JSON-bestand lezen uit de opslag als één kolom. Met het volgende script wordt debook1.jsals een enkele kolom gelezen *in* het bestand:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'json/books/book1.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
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
> U leest het hele JSON-bestand als één rij of kolom. So, FIELDTERMINATOR, FIELDQUOTE en ROWTERMINATOR zijn ingesteld op 0x0B.

## <a name="query-json-files-using-json_value"></a>Een query uitvoeren op JSON-bestanden met JSON_VALUE

De onderstaande query laat zien hoe u [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) kunt gebruiken om scalaire waarden (titel, uitgever) op te halen uit een boek met de naam *Probabilistic en statistische methoden in Cryptology, een inleiding op de geselecteerde onderwerpen*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
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

## <a name="query-json-files-using-json_query"></a>Een query uitvoeren op JSON-bestanden met JSON_QUERY

De volgende query laat zien hoe u [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) kunt gebruiken om objecten en matrices (auteurs) op te halen uit een boek met de titel *Probabilistic en statistische methoden in Cryptology, een inleiding op de geselecteerde onderwerpen*:

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
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

## <a name="query-json-files-using-openjson"></a>Een query uitvoeren op JSON-bestanden met openjson

De volgende query maakt gebruik van [openjson](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Er worden objecten en eigenschappen opgehaald binnen een boek met de naam *Probabilistic en statistische methoden in Cryptology, een inleiding op de geselecteerde onderwerpen*:

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent NVARCHAR(max) -- Use appropriate length. Make sure JSON fits. 
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>Volgende stappen

In de volgende artikelen in deze reeks wordt uitgelegd hoe u:

- [Query's uitvoeren op mappen en meerdere bestanden](query-folders-multiple-csv-files.md)
- [Weergaven maken en gebruiken](create-use-views.md)
