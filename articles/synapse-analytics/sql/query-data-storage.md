---
title: Overzicht - Querygegevens in opslag met SQL on-demand (voorbeeld)
description: Deze sectie bevat voorbeeldquery's die u gebruiken om de SQL on-demand (preview)-bron in Azure Synapse Analytics uit te proberen.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: cdad95b1a910a45629e85bcc716218b272afd9de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424900"
---
# <a name="overview-query-data-in-storage"></a>Overzicht: Querygegevens in de opslag

Deze sectie bevat voorbeeldquery's die u gebruiken om de SQL on-demand (preview)-bron in Azure Synapse Analytics uit te proberen.
Momenteel ondersteunde bestanden zijn: 
- CSV
- Parket
- JSON

## <a name="prerequisites"></a>Vereisten

De hulpprogramma's die u nodig hebt om query's uit te geven:

- SQL-client van uw keuze:
    - Azure Synapse Studio (voorbeeld)
    - Azure Data Studio
    - SQL Server Management Studio

Bovendien zijn de parameters als volgt:

| Parameter                                 | Beschrijving                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL on-demand serviceeindpuntadres    | Wordt gebruikt als servernaam.                                   |
| SQL on-demand serviceendpoint-regio     | Zal worden gebruikt om de opslag die wordt gebruikt in de monsters te bepalen. |
| Gebruikersnaam en wachtwoord voor toegang tot eindpunten | Wordt gebruikt om toegang te krijgen tot het eindpunt.                               |
| De database die u gebruikt om weergaven te maken     | Deze database zal worden gebruikt als uitgangspunt voor de monsters.       |

## <a name="first-time-setup"></a>First-time setup

Voordat u de voorbeelden die later in dit artikel zijn opgenomen, gebruikt, hebt u twee stappen:

- Een database maken voor uw weergaven (voor het geval u weergaven wilt gebruiken)
- Referenties maken die door SQL on-demand kunnen worden gebruikt om toegang te krijgen tot de bestanden in de opslag

### <a name="create-database"></a>Database maken

U hebt een database nodig om weergaven te maken. U gebruikt deze database voor een aantal voorbeeldquery's in deze documentatie.

> [!NOTE]
> Databases worden alleen gebruikt voor het bekijken van metadata, niet voor werkelijke gegevens.  Noteer de databasenaam die u gebruikt, u zult deze later nodig hebben.

```sql
CREATE DATABASE mydbname;
```

### <a name="create-credentials"></a>Referenties maken

U moet referenties maken voordat u query's uitvoeren. Deze referentie wordt gebruikt door SQL on-demand service om toegang te krijgen tot de bestanden in opslag.

> [!NOTE]
> Om How To's in deze sectie succesvol uit te voeren, moet je SAS-token gebruiken.
>
> Om SAS-tokens te gaan gebruiken, moet je de UserIdentity laten vallen die wordt uitgelegd in het volgende [artikel.](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through)
>
> SQL on-demand maakt standaard altijd gebruik van AAD-pass-through.

Voor meer informatie over het beheren van opslagtoegangsbeheer, raadpleegt u deze [koppeling](develop-storage-files-storage-access-control.md).

> [!WARNING]
> U moet referenties maken voor een opslagaccount dat zich in uw eindpuntgebied bevindt. Hoewel SQL on-demand toegang heeft tot opslag vanuit verschillende regio's, biedt opslag en eindpunt in dezelfde regio een betere prestatie-ervaring.

Voer de onderstaande code uit om referenties voor CSV-, JSON- en parketcontainers te maken:

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

## <a name="provided-demo-data"></a>Verstrekte demogegevens

Demogegevens bevatten de volgende gegevenssets:

- NYC Taxi - Yellow Taxi Trip Records - onderdeel van openbare NYC data set
  - CSV-indeling
  - Parquet-indeling
- Bevolkingsgegevensset
  - CSV-indeling
- Voorbeeld van parketbestanden met geneste kolommen
  - Parquet-indeling
- Boeken JSON
  - JSON-indeling

| Mappad                                                  | Beschrijving                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | Bovenliggende map voor gegevens in CSV-indeling                         |
| /csv/bevolking/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Mappen met gegevensbestanden van populatie in verschillende CSV-indelingen. |
| /csv/taxi/                                                   | Map met openbare NYC-gegevensbestanden in CSV-indeling              |
| /parket/                                                    | Bovenliggende map voor gegevens in parketindeling                     |
| /parket/taxi                                                | NYC openbare gegevensbestanden in parketformaat, verdeeld per jaar en maand met behulp van Hive / Hadoop partitionering regeling. |
| /parket/genest/                                             | Voorbeeld van parketbestanden met geneste kolommen                     |
| /json/                                                       | Bovenliggende map voor gegevens in JSON-indeling                        |
| /json/boeken/                                                 | JSON-bestanden met boekengegevens                                   |

## <a name="validation"></a>Validatie

Voer de volgende drie query's uit en controleer of de referenties correct zijn gemaakt.

> [!NOTE]
> Alle URI's in de voorbeeldquery's gebruiken een opslagaccount in de Azure-regio Noord-Europa. Zorg ervoor dat u de juiste referentie hebt gemaakt. Voer de onderstaande query uit en zorg ervoor dat het opslagaccount wordt weergegeven.

```sql
SELECT name
FROM sys.credentials
WHERE
     name IN ( 'https://sqlondemandstorage.blob.core.windows.net/csv',
     'https://sqlondemandstorage.blob.core.windows.net/parquet',
     'https://sqlondemandstorage.blob.core.windows.net/json');
```

Als u de juiste referenties niet vinden, schakelt u [First-time setup in.](#first-time-setup)

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

De bovenstaande query moet dit nummer retourneren: **8945574**.

## <a name="next-steps"></a>Volgende stappen

Je bent nu klaar om verder te gaan met de volgende How To-artikelen:

- [Query enkel CSV-bestand](query-single-csv-file.md)

- [Querymappen en meerdere CSV-bestanden](query-folders-multiple-csv-files.md)

- [Specifieke bestanden opvragen](query-specific-files.md)

- [Parketbestanden voor query's](query-parquet-files.md)

- [Geneste querygenese](query-parquet-nested-types.md)

- [JSON-bestanden opvragen](query-json-files.md)

- [Weergaven maken en gebruiken](create-use-views.md)
