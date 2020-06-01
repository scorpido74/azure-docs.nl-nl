---
title: Een query uitvoeren op CSV-bestanden met behulp van SQL op aanvraag (preview)
description: In dit artikel leert u hoe u met behulp van SQL on-demand (preview) een query kunt uitvoeren op enkelvoudige CSV-bestanden met verschillende bestands indelingen.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: f264a62428f919fe23797171926ddf63c585c42b
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234134"
---
# <a name="query-csv-files"></a>CSV-bestanden opvragen

In dit artikel leert u hoe u een query kunt uitvoeren op één CSV-bestand met behulp van SQL on-demand (preview) in azure Synapse Analytics. CSV-bestanden hebben mogelijk verschillende indelingen: 

- Met en zonder een veldnamenrij
- Door komma's en tabs gescheiden waarden
- Windows-en UNIX-stijl regel eindigt
- Niet-geciteerde en niet-geciteerde waarden en Escape tekens

Alle bovenstaande variaties worden hieronder besproken.

## <a name="prerequisites"></a>Vereisten

De eerste stap is het **maken van een Data Base** waarin de tabellen worden gemaakt. Initialiseer vervolgens de objecten door [installatie script](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) uit te voeren op die data base. Met dit installatie script worden de gegevens bronnen, referenties voor het data base-bereik en externe bestands indelingen gemaakt die in deze voor beelden worden gebruikt.

## <a name="windows-style-new-line"></a>Windows-stijl nieuwe regel

De volgende query laat zien hoe u een CSV-bestand zonder een koprij, met een nieuwe Windows-stijl en door komma's gescheiden kolommen kunt lezen.

Bestands voorbeeld:

![Eerste tien rijen van het CSV-bestand zonder kop, Windows-stijl nieuwe regel.](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>UNIX-stijl nieuwe regel

De volgende query laat zien hoe u een bestand zonder koprij, met een nieuwe UNIX-stijl en door komma's gescheiden kolommen kunt lezen. Let op de verschillende locatie van het bestand in vergelijking met de andere voor beelden.

Bestands voorbeeld:

![Eerste tien rijen van het CSV-bestand zonder veldnamenrij en met de nieuwe regel UNIX-style.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>Veldnamenrij

De volgende query laat zien hoe een bestand wordt gelezen met een veldnamenrij, met een nieuwe regel in UNIX-stijl en door komma's gescheiden kolommen. Let op de verschillende locatie van het bestand in vergelijking met de andere voor beelden.

Bestands voorbeeld:

![Eerste tien rijen van het CSV-bestand met veldnamenrij en met de nieuwe regel UNIX-style.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>Aangepast aanhalings teken

De volgende query laat zien hoe u een bestand met een veldnamenrij kunt lezen met een nieuwe UNIX-stijl, door komma's gescheiden kolommen en geciteerde waarden. Let op de verschillende locatie van het bestand in vergelijking met de andere voor beelden.

Bestands voorbeeld:

![De eerste tien rijen van het CSV-bestand met de koprij en de nieuwe regel voor de UNIX-stijl en de geciteerde waarden.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> Deze query retourneert dezelfde resultaten als u de para meter FIELDQUOTE hebt wegge laten, omdat de standaard waarde voor FIELDQUOTE een dubbele aanhalings tekens is.

## <a name="escaping-characters"></a>Escape tekens

De volgende query laat zien hoe u een bestand met een veldnamenrij kunt lezen met een nieuwe UNIX-stijl, door komma's gescheiden kolommen en een escape teken dat wordt gebruikt voor het veld scheidings teken (komma) in waarden. Let op de verschillende locatie van het bestand in vergelijking met de andere voor beelden.

Bestands voorbeeld:

![De eerste tien rijen van het CSV-bestand met veldnamenrij en met een nieuwe regel en escape-teken voor de UNIX-stijl die wordt gebruikt voor veld scheiding.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> Deze query mislukt als ESCAPECHAR niet is opgegeven omdat de komma in "slov, enia" als veld scheidings teken wordt beschouwd in plaats van een deel van de naam van het land/de regio. "Slov, enia" worden beschouwd als twee kolommen. Daarom zou de betreffende rij een kolom meer hebben dan de andere rijen en één kolom meer dan u hebt gedefinieerd in de WITH-component.

## <a name="tab-delimited-files"></a>Door tabs gescheiden bestanden

De volgende query laat zien hoe u een bestand met een veldnamenrij, met een nieuwe regel in UNIX-stijl en door tabs gescheiden kolommen kunt lezen. Let op de verschillende locatie van het bestand in vergelijking met de andere voor beelden.

Bestands voorbeeld:

![Eerste tien rijen van het CSV-bestand met veldnamenrij en met de UNIX-stijl nieuwe regel en het scheidings teken.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="returning-subset-of-columns"></a>Subset van kolommen wordt geretourneerd

Tot nu toe hebt u het CSV-bestands schema opgegeven met behulp van en een lijst met alle kolommen. U kunt alleen kolommen opgeven die u daad werkelijk nodig hebt in uw query door gebruik te maken van een rang nummer voor elke gewenste kolom. U kunt ook kolommen zonder interesse weglaten.

Met de volgende query wordt het aantal afzonderlijke land/regio namen in een bestand geretourneerd, waarbij alleen de benodigde kolommen worden opgegeven:

> [!NOTE]
> Bekijk de WITH-component in de query hieronder en houd er rekening mee dat er ' 2 ' (zonder aanhalings tekens) aan het einde van de rij is waar u de kolom *[country_name]* definieert. Dit betekent dat de kolom *[country_name]* de tweede kolom in het bestand is. Met de query worden alle kolommen in het bestand genegeerd, met uitzonde ring van de tweede.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Volgende stappen

In de volgende artikelen ziet u hoe u:

- [Query's uitvoeren op Parquet-bestanden](query-parquet-files.md)
- [Query's uitvoeren op mappen en meerdere bestanden](query-folders-multiple-csv-files.md)
