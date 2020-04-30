---
title: "Overzicht: Query's uitvoeren op gegevens in de opslag met behulp van SQL op aanvraag (preview)"
description: Deze sectie bevat voorbeeld query's die u kunt gebruiken om de SQL on-demand (preview)-resource in azure Synapse Analytics uit te proberen.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: e18fc765385e6d703e735a1ca15c539c32f36e93
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82116244"
---
# <a name="overview-query-data-in-storage"></a>Overzicht: gegevens in opslag opvragen

Deze sectie bevat voorbeeld query's die u kunt gebruiken om de SQL on-demand (preview)-resource in azure Synapse Analytics uit te proberen.
Momenteel ondersteunde bestanden zijn: 
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>Vereisten

De hulpprogram ma's die u nodig hebt om query's uit te voeren:

- Gewenste SQL-client:
    - Azure Synapse Studio (preview-versie)
    - Azure Data Studio
    - SQL Server Management Studio

Daarnaast zijn de para meters als volgt:

| Parameter                                 | Beschrijving                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL on-demand service-eindpunt adres    | Wordt gebruikt als de server naam.                                   |
| SQL on-demand service-eindpunt regio     | Wordt gebruikt om te bepalen welke opslag wordt gebruikt in de voor beelden. |
| Gebruikers naam en wacht woord voor endpoint Access | Wordt gebruikt voor toegang tot het eind punt.                               |
| De data base die u gebruikt voor het maken van weer gaven     | Deze data base wordt gebruikt als uitgangs punt voor de voor beelden.       |

## <a name="first-time-setup"></a>Eerste keer instellen

Voordat u de voor beelden verderop in dit artikel gebruikt, hebt u twee stappen:

- Een Data Base voor uw weer gaven maken (als u weer gaven wilt gebruiken)
- Referenties maken voor gebruik door SQL op aanvraag om toegang te krijgen tot de bestanden in de opslag

### <a name="create-database"></a>Database maken

U hebt een data base nodig om weer gaven te maken. U gebruikt deze data base voor enkele van de voorbeeld query's in deze documentatie.

> [!NOTE]
> Data bases worden alleen gebruikt voor het weer geven van meta gegevens, niet voor werkelijke gegevens.  Noteer de naam van de data base die u gebruikt. u hebt deze later nodig.

```sql
CREATE DATABASE mydbname;
```

### <a name="create-credentials"></a>Referenties maken

U moet referenties maken voordat u query's kunt uitvoeren. Deze referentie wordt gebruikt door de SQL-service op aanvraag om toegang te krijgen tot de bestanden in de opslag.

> [!NOTE]
> Als u de procedure in deze sectie wilt uitvoeren, moet u een SAS-token gebruiken.
>
> Als u SAS-tokens wilt gebruiken, moet u de UserIdentity verwijderen die wordt beschreven in het volgende [artikel](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through).
>
> SQL on-demand standaard maakt altijd gebruik van AAD Pass-through.

Raadpleeg deze [koppeling](develop-storage-files-storage-access-control.md)voor meer informatie over het beheren van toegangs beheer voor opslag.

Voer de volgende code uit om referenties te maken voor de CSV-, JSON-en Parquet-containers:

```sql
-- create credentials for CSV container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/csv')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for JSON container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/json')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for PARQUET container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/parquet')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO
```

## <a name="provided-demo-data"></a>Gegeven demo gegevens

Demo gegevens bevatten de volgende gegevens sets:

- NYC-gelede taxi-reis records-onderdeel van een open bare NYC-gegevensset
  - CSV-indeling
  - Parquet-indeling
- Gegevens verzameling in populatie
  - CSV-indeling
- Voor beeld van Parquet-bestanden met geneste kolommen
  - Parquet-indeling
- Rapporten JSON
  - JSON-indeling

| Mappad                                                  | Beschrijving                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| bestand                                                        | Bovenliggende map voor gegevens in CSV-indeling                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Mappen met gegevens bestanden met een populatie in verschillende CSV-indelingen. |
| /csv/taxi/                                                   | Map met open bare NYC-gegevens bestanden in CSV-indeling              |
| Parquet                                                    | Bovenliggende map voor de gegevens in de Parquet-indeling                     |
| /parquet/taxi                                                | NYC open bare gegevens bestanden in Parquet-indeling, gepartitioneerd per jaar en maand met behulp van Hive/Hadoop-partitie schema. |
| /parquet/nested/                                             | Voor beeld van Parquet-bestanden met geneste kolommen                     |
| JSON                                                       | Bovenliggende map voor gegevens in JSON-indeling                        |
| /json/books/                                                 | JSON-bestanden met boeken gegevens                                   |

## <a name="validation"></a>Validatie

Voer de volgende drie query's uit en controleer of de referenties correct zijn gemaakt.

> [!NOTE]
> Alle Uri's in de voorbeeld query's gebruiken een opslag account in de Azure-regio Europa-noord. Zorg ervoor dat u de juiste referentie hebt gemaakt. Voer de onderstaande query uit en controleer of het opslag account wordt vermeld.

```sql
SELECT name
FROM sys.credentials
WHERE
     name IN ( 'https://sqlondemandstorage.blob.core.windows.net/csv',
     'https://sqlondemandstorage.blob.core.windows.net/parquet',
     'https://sqlondemandstorage.blob.core.windows.net/json');
```

Als u de juiste referentie niet kunt vinden, controleert u de [eerste keer](#first-time-setup)dat u de installatie uitvoert.

### <a name="sample-query"></a>Voorbeeldquery

De laatste stap van de validatie is het uitvoeren van de volgende query:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

De bovenstaande query moet dit nummer retour neren: **8945574**.

## <a name="next-steps"></a>Volgende stappen

U kunt nu door gaan met de volgende artikelen:

- [Query uitvoeren op één CSV-bestand](query-single-csv-file.md)

- [Query mappen en meerdere CSV-bestanden](query-folders-multiple-csv-files.md)

- [Query's uitvoeren op specifieke bestanden](query-specific-files.md)

- [Query uitvoeren op Parquet-bestanden](query-parquet-files.md)

- [Query uitvoeren op met Parquet geneste typen](query-parquet-nested-types.md)

- [Query uitvoeren op JSON-bestanden](query-json-files.md)

- [Weer gaven maken en gebruiken](create-use-views.md)
