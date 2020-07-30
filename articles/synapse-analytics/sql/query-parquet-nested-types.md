---
title: Parquet geneste typen doorzoeken met behulp van SQL op aanvraag (preview)
description: In dit artikel leert u hoe u een query kunt uitvoeren op Parquet-geneste typen.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 60f2a1992750950b50995fcf36513e44e377004d
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386602"
---
# <a name="query-nested-types-in-parquet-and-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Een query uitvoeren op geneste typen in Parquet-en JSON-bestanden met behulp van SQL on-demand (preview) in azure Synapse Analytics

In dit artikel leert u hoe u een query schrijft met behulp van SQL on-demand (preview) in azure Synapse Analytics. Met deze query worden geneste Parquet-typen gelezen.
Geneste typen zijn complexe structuren die objecten of matrices vertegenwoordigen. Geneste typen kunnen worden opgeslagen in: 
- [PARQUET](query-parquet-files.md) waar u meerdere complexe kolommen kunt hebben die matrices en objecten bevatten.
- Hiërarchische [json-bestanden](query-json-files.md) waarin u complexe JSON-documenten kunt lezen als één kolom.
- CosmosDB-verzameling waarbij elk document complexe geneste eigenschappen kan bevatten (momenteel in de open bare preview-fase).

Synapse SQL on-demand formatteert alle geneste typen als JSON-objecten en matrices, zodat u [complexe objecten kunt uitpakken of wijzigen met behulp van JSON-functies](https://docs.microsoft.com/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) of [JSON-gegevens kan parseren met de openjson-functie](https://docs.microsoft.com/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server). 

Hieronder ziet u een voor beeld van een query waarmee scalaire en objecten waarden worden geëxtraheerd uit [het JSON-bestand COVID-19 open Research dataset](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) met geneste objecten. 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

`JSON_VALUE`de functie retourneert een scalaire waarde uit het veld op het opgegeven pad. `JSON_QUERY`functie retourneert een object dat als JSON is opgemaakt vanuit het veld op het opgegeven pad.

> [!IMPORTANT]
> In dit voor beeld wordt een bestand gebruikt van [COVID-19 open-gegevensset voor onderzoek](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). Zie deze-licentie en de structuur van de gegevens op deze pagina.

## <a name="prerequisites"></a>Vereisten

De eerste stap bestaat uit het **maken van een Data Base** met een gegevens bron waarnaar wordt verwezen. Initialiseer vervolgens de objecten door een [installatiescript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) uit te voeren op die database. Met dit installatie script worden de gegevens bronnen, referenties voor het data base-bereik en externe bestands indelingen gemaakt die in deze voor beelden worden gebruikt.

## <a name="project-nested-or-repeated-data"></a>Geneste of herhaalde gegevens projecteren

Het PARQUET-bestand kan meerdere kolommen met complexe typen bevatten. De waarden van deze kolommen zijn opgemaakt als JSON-tekst en geretourneerd als VARCHAR-kolom. Met de volgende query leest u het bestand *structExample. Parquet* en ziet u hoe u de waarden van de geneste kolommen kunt lezen: 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

Met deze query wordt het volgende resultaat geretourneerd waarbij de inhoud van elk genest object als JSON-tekst wordt geretourneerd:

| DateStruct    | TimeStruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Datum": "2009-04-25"}| {"Tijd": "20:51:54.3598000"}|    {"Tijds tempel": "5501-04-08 12:13:57.4821000"}|    {"Decimaal": 11143412.25350}| {"Float": 0,5}|
|{"Datum": "1916-04-29"}| {"Tijd": "00:16:04.6778000"}|    {"Tijds tempel": "1990-06-30 20:50:52.6828000"}|    {"Decimaal": 1963545.62800}|  {"Float":-2,125}|

Met de volgende query wordt het bestand *justSimpleArray. Parquet* gelezen. Hiermee worden alle kolommen uit het Parquet-bestand, inclusief geneste of herhaalde gegevens, geprojecteerd.

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Met deze query wordt het volgende resultaat geretourneerd:

|SimpleArray|
| --- |
|[11, 12, 13]|
|[21, 22, 23]|

## <a name="read-properties-from-nested-object-columns"></a>Eigenschappen van geneste object kolommen lezen

`JSON_VALUE`met de functie kunt u waarden van een kolom die is opgemaakt als JSON-tekst retour neren:

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

Het resultaat wordt weer gegeven in de volgende tabel:

|titel  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| Aanvullende informatie een eco-epidemiolo... | Julien   | -Afbeelding S1: Phylogeny van... | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

In tegens telling tot JSON-bestanden die in de meeste gevallen één kolom met een complex JSON-object retour neren. PARQUET-bestanden kunnen meerdere ingewikkelde zijn. U kunt de eigenschappen van een geneste kolom lezen met behulp `JSON_VALUE` van de functie voor elke kolom. `OPENROWSET`Hiermee kunt u rechtstreeks de paden van de geneste eigenschappen in `WITH` component opgeven. Paden kunnen worden ingesteld als een naam van de kolom of u kunt een [JSON-padexpressie](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server) toevoegen na het kolom Type.

Met de volgende query wordt het bestand *structExample. Parquet* gelezen en wordt weer gegeven hoe u de Opper vlakken van een geneste kolom laat zien. U hebt twee manieren om te verwijzen naar een geneste waarde:
- Opgeven van de geneste waarde Path-expressie na type specificatie.
- De naam van de kolom wordt opgemaakt als genest pad met behulp van '. ' om naar de velden te verwijzen.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Elementen benaderen vanuit herhaalde kolommen

De volgende query leest het bestand *justSimpleArray. Parquet* en gebruikt [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) om een **scalair** element op te halen in een herhaalde kolom, zoals een matrix of kaart:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Het resultaat wordt weer gegeven in de volgende tabel:

|SimpleArray    | FirstElement  | SecondElement | ThirdElement |
| --- | --- | --- | --- |
| [11, 12, 13] | 11   | 12 | 13 |
| [21, 22, 23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>Onderliggende objecten openen vanuit complexe kolommen

Met de volgende query wordt het bestand *mapExample. Parquet* gelezen en wordt [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) gebruikt om een **niet-scalair** element op te halen uit een herhaalde kolom, zoals een matrix of kaart:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

U kunt ook expliciet verwijzen naar de kolommen die u wilt retour neren in de `WITH` component:

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

De structuur `MapOfPersons` wordt geretourneerd als `VARCHAR` kolom en OPGEMAAKT als JSON-teken reeks.

## <a name="projecting-values-from-repeated-columns"></a>Waarden van herhaalde kolommen projecteren

Als u een matrix met scalaire waarden (bijvoorbeeld `[1,2,3]` ) in sommige kolommen hebt, kunt u deze eenvoudig uitbreiden en aan de rij toevoegen met behulp van het volgende script:

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>Volgende stappen

In het volgende artikel ziet u hoe u een [query uitvoert voor json-bestanden](query-json-files.md).
