---
title: QUERY CSV-bestanden met SQL on-demand (voorbeeld)
description: In dit artikel leert u hoe u afzonderlijke CSV-bestanden met verschillende bestandsindelingen opvragen met SQL on-demand (preview).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 3d09692c06bcdffbb070f545950092592e417838
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431590"
---
# <a name="query-csv-files"></a>CSV-bestanden van query's

In dit artikel leert u hoe u één CSV-bestand opvraagt met SQL on-demand (preview) in Azure Synapse Analytics. CSV-bestanden kunnen verschillende indelingen hebben: 

- Met en zonder koptekstrij
- Komma- en tabgedefinieerde waarden
- Line-eindes in Windows- en Unix-stijl
- Niet-geciteerde en geciteerde waarden en ontsnappende tekens

Alle bovenstaande variaties worden hieronder behandeld.

## <a name="prerequisites"></a>Vereisten

Voordat u de rest van dit artikel leest, leest u de volgende artikelen:

- [First-time setup](query-data-storage.md#first-time-setup)
- [Vereisten](query-data-storage.md#prerequisites)

## <a name="windows-style-new-line"></a>Windows-stijl nieuwe lijn

In de volgende query ziet u hoe u een CSV-bestand leest zonder koptekstrij, met een nieuwe regel in Windows-stijl en kolommen met komma's.

Voorbeeld van bestand:

![Eerste 10 rijen van het CSV-bestand zonder koptekst, Windows-stijl nieuwe lijn.](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
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

## <a name="unix-style-new-line"></a>Unix-stijl nieuwe lijn

In de volgende query ziet u hoe u een bestand lezen zonder koptekstrij, met een nieuwe regel in Unix-stijl en kolommen met komma's. Let op de verschillende locatie van het bestand in vergelijking met de andere voorbeelden.

Voorbeeld van bestand:

![Eerste 10 rijen van het CSV-bestand zonder koptekstrij en met nieuwe regel Unix-Stijl.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT = 'CSV',
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

In de volgende query ziet u hoe u een leesbestand met een koptekstrij, met een nieuwe regel in Unix-stijl en kolommen met komma's. Let op de verschillende locatie van het bestand in vergelijking met de andere voorbeelden.

Voorbeeld van bestand:

![Eerste 10 rijen van het CSV-bestand met koptekstrij en met nieuwe regel Unix-Stijl.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr/population.csv',
        FORMAT = 'CSV',
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

## <a name="custom-quote-character"></a>Aangepast aanhalingsteken

In de volgende query ziet u hoe u een bestand met een koptekstrij leest, met een nieuwe regel in Unix-stijl, kolommen met komma's en geciteerde waarden. Let op de verschillende locatie van het bestand in vergelijking met de andere voorbeelden.

Voorbeeld van bestand:

![Eerste 10 rijen van het CSV-bestand met koptekstrij en met nieuwe regel- en geciteerde waarden in Unix-stijl.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-quoted/population.csv',
        FORMAT = 'CSV',
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
> Deze query zou dezelfde resultaten opleveren als u de parameter FIELDQUOTE hebt weggelaten, omdat de standaardwaarde voor FIELDQUOTE een dubbele aanhalingslijst is.

## <a name="escaping-characters"></a>Ontsnappende personages

In de volgende query ziet u hoe u een bestand met een koptekstrij lezen, met een nieuwe regel in Unix-stijl, kolommen met komma's en een escape char die wordt gebruikt voor de veldafbakening (komma) binnen waarden. Let op de verschillende locatie van het bestand in vergelijking met de andere voorbeelden.

Voorbeeld van bestand:

![Eerste 10 rijen van het CSV-bestand met koptekstrij en met Unix-Style nieuwe lijn en escape char gebruikt voor veldafbakening.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-escape/population.csv',
        FORMAT = 'CSV',
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
    country_name = 'Slov,enia';
```

> [!NOTE]
> Deze query zou mislukken als ESCAPECHAR niet is opgegeven, omdat de komma in "Slov,enia" zou worden behandeld als veldscheidingsteken in plaats van een deel van de landnaam. "Slov, enia" zou worden behandeld als twee kolommen. Daarom zou de specifieke rij een kolom meer hebben dan de andere rijen en één kolom meer dan u hebt gedefinieerd in de MET-component.

## <a name="tab-delimited-files"></a>Met tabbladen afgebakende bestanden

In de volgende query ziet u hoe u een bestand met een koptekstrij leest, met een nieuwe regel in Unix-stijl en kolommen met tabbladen. Let op de verschillende locatie van het bestand in vergelijking met de andere voorbeelden.

Voorbeeld van bestand:

![Eerste 10 rijen van het CSV-bestand met koptekstrij en met nieuwe regel- en tabscheidingsteken in Unix-stijl.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-tsv/population.csv',
        FORMAT = 'CSV',
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

## <a name="returning-subset-of-columns"></a>Subset van kolommen retourneren

Tot nu toe hebt u het CSV-bestandsschema opgegeven met behulp van MET en alle kolommen vermeld. U alleen kolommen opgeven die u daadwerkelijk nodig hebt in uw query met behulp van een ordinaal nummer voor elke kolom die nodig is. U zult ook kolommen van geen belang weglaten.

Met de volgende query wordt het aantal verschillende landnamen in een bestand geretourneerd en wordt alleen de kolommen opgegeven die nodig zijn:

> [!NOTE]
> Neem een kijkje op de MET-clausule in de query hieronder en merk op dat er "2" (zonder aanhalingstekens) aan het einde van de rij waar u de *[country_name]* kolom te definiëren. Dit betekent dat de *kolom [country_name]* de tweede kolom in het bestand is. De query negeert alle kolommen in het bestand, behalve de tweede.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Volgende stappen

De volgende artikelen laten zien hoe je:

- [Parketbestanden opvragen](query-parquet-files.md)
- [Mappen en meerdere bestanden opvragen](query-folders-multiple-csv-files.md)
