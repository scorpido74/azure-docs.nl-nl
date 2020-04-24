---
title: SQL op aanvraag gebruiken (preview-versie)
description: In deze Quick start ziet u en leert u hoe eenvoudig verschillende typen bestanden kunnen worden opgevraagd met behulp van SQL op aanvraag (preview).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: d49918fc67a45419e5c7ca123642c48e689a1496
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113779"
---
# <a name="quickstart-using-sql-on-demand"></a>Snelstartgids: SQL on-demand gebruiken

Synapse SQL on-demand (preview) is een serverloze query service waarmee u de SQL-query's kunt uitvoeren op uw bestanden die in Azure Storage worden geplaatst. In deze Quick Start leert u hoe u verschillende typen bestanden kunt zoeken met behulp van SQL op aanvraag.

De volgende bestands typen worden ondersteund: JSON, CSV, Apache Parquet

## <a name="prerequisites"></a>Vereisten

Kies een SQL-client om query's uit te geven:

- [Azure Synapse Studio](quickstart-synapse-studio.md) is een hulp programma dat u kunt gebruiken om door bestanden in de opslag te bladeren en SQL-query's te maken.
- [Azure Data Studio](sql/get-started-azure-data-studio.md) is een client hulpprogramma waarmee u SQL-query's en-notitie blokken kunt uitvoeren op uw Data Base op aanvraag.
- [SQL Server Management Studio](sql/get-started-ssms.md) is een client hulpprogramma waarmee u SQL-query's kunt uitvoeren op uw Data Base op aanvraag.

Para meters voor Quick Start:

| Parameter                                 | Beschrijving                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL on-demand service-eindpunt adres    | Gebruikt als server naam                                   |
| SQL on-demand service-eindpunt regio     | Wordt gebruikt om te bepalen welke opslag wordt gebruikt in voor beelden |
| Gebruikers naam en wacht woord voor endpoint Access | Gebruikt voor toegang tot het eind punt                               |
| De data base die wordt gebruikt voor het maken van weer gaven         | Data base gebruikt als uitgangs punt in voor beelden       |

## <a name="first-time-setup"></a>Eerste keer instellen

Voorafgaand aan het gebruik van voor beelden:

- Maak een Data Base voor uw weer gaven (voor het geval u weer gaven wilt gebruiken)
- Referenties maken voor gebruik door SQL op aanvraag om toegang te krijgen tot bestanden in de opslag

### <a name="create-database"></a>Database maken

Maak uw eigen Data Base voor demonstratie doeleinden. Dit is de Data Base waarin u uw weer gaven maakt. Gebruik deze data base in de voorbeeld query's in dit artikel.

> [!NOTE]
> De data bases worden alleen gebruikt voor het weer geven van meta gegevens, niet voor werkelijke gegevens.
>
> Noteer de naam van de data base die u voor later in de Quick Start gebruikt.

Gebruik de volgende query en wijzig `mydbname` de naam van uw keuze:

```sql
CREATE DATABASE mydbname
```

### <a name="create-credentials"></a>Referenties maken

Als u query's wilt uitvoeren met behulp van SQL op aanvraag, maakt u referenties voor SQL on-demand om te gebruiken voor toegang tot bestanden in de opslag.

> [!NOTE]
> Als u voor beelden in deze sectie wilt uitvoeren, moet u een SAS-token gebruiken.
>
> Als u SAS-tokens wilt gebruiken, moet u de UserIdentity verwijderen die wordt beschreven in het volgende [artikel](sql/develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through).
>
> SQL on-demand standaard maakt altijd gebruik van AAD Pass-through.

Raadpleeg deze [koppeling](sql/develop-storage-files-storage-access-control.md)voor meer informatie over het beheren van toegangs beheer voor opslag.

Voer het volgende code fragment uit om referentie te maken die wordt gebruikt in voor beelden in deze sectie:

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

## <a name="querying-csv-files"></a>Query's uitvoeren op CSV-bestanden

De volgende afbeelding is een voor beeld van het bestand waarin een query moet worden uitgevoerd:

![Eerste tien rijen van het CSV-bestand zonder kop, Windows-stijl nieuwe regel.](./sql/media/query-single-csv-file/population.png)

De volgende query laat zien hoe u een CSV-bestand leest dat geen koprij bevat, met Windows-stijl nieuwe regel en door komma's gescheiden kolommen:

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

U kunt het schema opgeven bij het compileren van de query.
Zie How to [query CSV file](sql/query-single-csv-file.md)(Engelstalig) voor meer voor beelden.

## <a name="querying-parquet-files"></a>Query's uitvoeren op Parquet-bestanden

In het volgende voor beeld ziet u de mogelijkheden voor het automatisch afnemen van schema's voor het uitvoeren van query's op Parquet-bestanden. Het retourneert het aantal rijen in september van 2017 zonder schema op te geven.

> [!NOTE]
> U hoeft geen columns in-component `OPENROWSET WITH` op te geven bij het lezen van Parquet-bestanden. In dat geval maakt SQL op aanvraag gebruik van meta gegevens in het Parquet-bestand en bindt kolommen op naam.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet'
    , FORMAT='PARQUET'
  ) AS nyc
```

Meer informatie over het [uitvoeren van query's op Parquet-bestanden](sql/query-parquet-files.md)].

## <a name="querying-json-files"></a>JSON-bestanden opvragen

### <a name="json-sample-file"></a>JSON-voorbeeld bestand

Bestanden worden opgeslagen in *JSON* -container, in mappen *boeken*en bevatten één boek vermelding met de volgende structuur:

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

De volgende query laat zien hoe u [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) kunt gebruiken om scalaire waarden (titel, uitgever) op te halen uit een boek met de titel *Probabilistic en statistische methoden in Cryptology, een inleiding op geselecteerde artikelen*:

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
> Het hele JSON-bestand wordt gelezen als één rij/kolom, dus FIELDTERMINATOR, FIELDQUOTE en ROWTERMINATOR zijn ingesteld op 0x0B, omdat we niet verwachten dat deze in het bestand worden gevonden.

## <a name="next-steps"></a>Volgende stappen

U bent nu klaar om te beginnen met de volgende Quick Start-artikelen:

- [Query uitvoeren op één CSV-bestand](sql/query-single-csv-file.md)
- [Query mappen en meerdere CSV-bestanden](sql/query-folders-multiple-csv-files.md)
- [Query's uitvoeren op specifieke bestanden](sql/query-specific-files.md)
- [Query uitvoeren op Parquet-bestanden](sql/query-parquet-files.md)
- [Query uitvoeren op met Parquet geneste typen](sql/query-parquet-nested-types.md)
- [Query uitvoeren op JSON-bestanden](sql/query-json-files.md)
- [Weer gaven maken en gebruiken](sql/create-use-views.md)
- [Externe tabellen maken en gebruiken](sql/create-use-external-tables.md)
- [Query resultaten persistent maken naar Azure Storage](sql/create-external-table-as-select.md)

Ga naar het volgende artikel voor meer informatie over het uitvoeren van een query op één CSV-bestand.
> [!div class="nextstepaction"]
> [Query uitvoeren op één CSV-bestand](sql/query-single-csv-file.md)
