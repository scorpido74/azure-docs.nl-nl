---
title: SQL on-demand gebruiken (voorbeeld)
description: In deze quickstart ziet en leert u hoe eenvoudig het is om verschillende soorten bestanden op te vragen met SQL on-demand (preview).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 0d543abc88c1e45f2c1f5503473d8e92566fc582
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457379"
---
# <a name="quickstart-using-sql-on-demand"></a>Snelstart: SQL on-demand gebruiken

Synapse SQL on-demand (preview) is een serverloze queryservice waarmee u de SQL-query's uitvoeren op uw bestanden die in Azure Storage zijn geplaatst. In deze quickstart leert u hoe u verschillende soorten bestanden opvragen met SQL on-demand.

De volgende bestandstypen worden ondersteund: JSON, CSV, Apache Parket

## <a name="prerequisites"></a>Vereisten

Kies een SQL-client om query's uit te geven:

- [Azure Synapse Studio](quickstart-synapse-studio.md) is een webtool die u gebruiken om door bestanden in opslag te bladeren en SQL-query's te maken.
- [Azure Data Studio](sql/get-started-azure-data-studio.md) is een clienttool waarmee u SQL-query's en notitieblokken uitvoeren in uw on-demand database.
- [SQL Server Management Studio](sql/get-started-ssms.md) is een clienttool waarmee u SQL-query's uitvoeren op uw on-demand database.

Parameters voor snel starten:

| Parameter                                 | Beschrijving                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL on-demand serviceeindpuntadres    | Gebruikt als servernaam                                   |
| SQL on-demand serviceendpoint-regio     | Wordt gebruikt om te bepalen welke opslag we in monsters zullen gebruiken |
| Gebruikersnaam en wachtwoord voor toegang tot eindpunten | Wordt gebruikt om toegang te krijgen tot eindpunt                               |
| De database die wordt gebruikt om weergaven te maken         | Database die als startpunt in monsters wordt gebruikt       |

## <a name="first-time-setup"></a>First-time setup

Voorafgaand aan het gebruik van monsters:

- Database maken voor uw weergaven (voor het geval u weergaven wilt gebruiken)
- Referenties maken die door SQL on-demand kunnen worden gebruikt om toegang te krijgen tot bestanden in opslag

### <a name="create-database"></a>Database maken

Maak je eigen database voor demodoeleinden. Dit is de database waarin u uw weergaven maakt. Gebruik deze database in de voorbeeldquery's in dit artikel.

> [!NOTE]
> De databases worden alleen gebruikt voor weergavemetagegevens, niet voor werkelijke gegevens.
>
> Schrijf de databasenaam op die u later in de Quickstart gebruikt.

Gebruik de volgende `mydbname` query en wijzig een naam naar keuze:

```sql
CREATE DATABASE mydbname
```

### <a name="create-credentials"></a>Referenties maken

Als u query's wilt uitvoeren met SQL on-demand, maakt u referenties voor SQL on-demand om toegang te krijgen tot bestanden in de opslag.

> [!NOTE]
> Houd er rekening mee dat u referenties moet maken voor toegang tot het opslagaccount. Hoewel SQL on-demand toegang heeft tot opslag vanuit verschillende regio's, biedt opslag en Azure Synapse-werkruimte in dezelfde regio een betere prestatie-ervaring.

Wijzig het volgende codefragment om referenties voor CSV-, JSON- en Parketcontainers te maken:

```sql
-- create credentials for containers in our demo storage account
IF EXISTS
   (SELECT * FROM sys.credentials
   WHERE name = 'https://sqlondemandstorage.blob.core.windows.net')
   DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
```

## <a name="querying-csv-files"></a>CSV-bestanden opvragen

De volgende afbeelding is een voorbeeld van het bestand dat moet worden opgevraagd:

![Eerste 10 rijen van het CSV-bestand zonder koptekst, Windows-stijl nieuwe lijn.](./sql/media/query-single-csv-file/population.png)

In de volgende query wordt uitgelegd hoe u een CSV-bestand leest dat geen koptekstrij bevat, met nieuwe regel in Windows-stijl en kolommen met komma's:

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/*.csv'
    , FORMAT = 'CSV'
  )
WITH
  (
      country_code VARCHAR (5)
    , country_name VARCHAR (100)
    , year smallint
    , population bigint
  ) AS r
WHERE
  country_name = 'Luxembourg' AND year = 2017
```

U het schema opgeven op de verzameltijd van query's.
Zie voor meer voorbeelden hoe u [CSV-bestand opvragen](sql/query-single-csv-file.md).

## <a name="querying-parquet-files"></a>Parketbestanden opvragen

In het volgende voorbeeld worden de automatische opties voor schema-inferenctrekking weergegeven voor het opvragen van parketbestanden. Het geeft het aantal rijen in september 2017 terug zonder het schema op te geven.

> [!NOTE]
> U hoeft geen kolommen `OPENROWSET WITH` in de clausule op te geven bij het lezen van parketbestanden. In dat geval maakt SQL on-demand gebruik van metagegevens in het parketbestand en bindt kolommen op naam.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet'
    , FORMAT='PARQUET'
  ) AS nyc
```

Meer informatie over [het opvragen van parketbestanden].](sql/query-parquet-files.md)

## <a name="querying-json-files"></a>JSON-bestanden opvragen

### <a name="json-sample-file"></a>JSON-voorbeeldbestand

Bestanden worden opgeslagen in *json-container,* *mapboeken*en bevatten enkele boekinvoer met de volgende structuur:

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

### <a name="querying-json-files"></a>JSON-bestanden opvragen

Volgende query toont hoe [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) te gebruiken om scalaire waarden (titel, uitgever) ophalen uit een boek met de titel *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected artikelen:*

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json'
    , FORMAT='CSV'
    , FIELDTERMINATOR ='0x0b'
    , FIELDQUOTE = '0x0b'
    , ROWTERMINATOR = '0x0b'
  )
WITH
  ( jsonContent varchar(8000) ) AS [r]
WHERE
  JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics'
```

> [!IMPORTANT]
> We lezen het hele JSON-bestand als single row/column dus FIELDTERMINATOR, FIELDQUOTE en ROWTERMINATOR zijn ingesteld op 0x0b omdat we niet verwachten dat we het in het bestand zullen vinden.

## <a name="next-steps"></a>Volgende stappen

Nu bent u klaar om te beginnen met het volgen van Quickstart-artikelen:

- [Query enkel CSV-bestand](sql/query-single-csv-file.md)
- [Querymappen en meerdere CSV-bestanden](sql/query-folders-multiple-csv-files.md)
- [Specifieke bestanden opvragen](sql/query-specific-files.md)
- [Parketbestanden voor query's](sql/query-parquet-files.md)
- [Geneste querygenese](sql/query-parquet-nested-types.md)
- [JSON-bestanden opvragen](sql/query-json-files.md)
- [Weergaven maken en gebruiken](sql/create-use-views.md)
- [Externe tabellen maken en gebruiken](sql/create-use-external-tables.md)
- [Queryresultaat blijven uitvoeren naar Azure-opslag](sql/create-external-table-as-select.md)

Ga door naar het volgende artikel voor meer informatie over het opvragen van één CSV-bestand.
> [!div class="nextstepaction"]
> [Query enkel CSV-bestand](sql/query-single-csv-file.md)
