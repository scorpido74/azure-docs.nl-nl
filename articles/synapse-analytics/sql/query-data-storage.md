---
title: "Overzicht: Query's uitvoeren op gegevens in de opslag met behulp van SQL on-demand (preview)"
description: Deze sectie bevat voorbeeldquery's die u kunt gebruiken om de SQL-resource on-demand (preview) uit te proberen in Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8501f9d07ffa2d04915d4d1a351317cc145f9844
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118270"
---
# <a name="overview-query-data-in-storage"></a>Overzicht: Query uitvoeren op gegevens in opslag

Deze sectie bevat voorbeeldquery's die u kunt gebruiken om de SQL-resource on-demand (preview) uit te proberen in Azure Synapse Analytics.
Momenteel worden de volgende bestandsindelingen ondersteund:  
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>Vereisten

De hulpprogramma's die u nodig hebt om query's uit te voeren:

- Gewenste SQL-client:
    - Azure Synapse Studio (preview)
    - Azure Data Studio
    - SQL Server Management Studio

Daarnaast zijn de parameters als volgt:

| Parameter                                 | Beschrijving                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adres van SQL on-demand service-eindpunt    | Wordt gebruikt als de servernaam.                                   |
| Regio van SQL on-demand service-eindpunt     | Wordt gebruikt om te bepalen welke opslag wordt gebruikt in de voorbeelden. |
| Gebruikersnaam en wachtwoord voor eindpunttoegang | Wordt gebruikt om toegang te krijgen tot het eindpunt.                               |
| De database die u gaat gebruiken om weergaven te maken     | Deze database wordt gebruikt als uitgangspunt voor de voorbeelden.       |

## <a name="first-time-setup"></a>De eerste installatie

De eerste stap bestaat uit het **maken van een database** waarin u de query's gaat uitvoeren. Initialiseer vervolgens de objecten door een [installatiescript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) uit te voeren op die database. Met dit installatiescript worden de gegevensbronnen, referenties voor het databasebereik en externe bestandsindelingen gemaakt die worden gebruikt om gegevens in deze voorbeelden te lezen.

> [!NOTE]
> Databases worden alleen gebruikt voor het weergeven van metagegevens, niet voor de werkelijke gegevens.  Noteer de naam van de database die u gebruikt. U hebt deze later nodig.

```sql
CREATE DATABASE mydbname;
```

## <a name="provided-demo-data"></a>Beschikbare demogegevens

Demogegevens bevatten de volgende gegevenssets:

- NYC Taxi - Records van Yellow Taxi-ritten - onderdeel van openbare NYC-gegevensset
  - CSV-indeling
  - Parquet-indeling
- Gegevensset populatie
  - CSV-indeling
- Voorbeeld van Parquet-bestanden met geneste kolommen
  - Parquet-indeling
- Boeken JSON
  - JSON-indeling

| Mappad                                                  | Beschrijving                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | Bovenliggende map voor gegevens in CSV-indeling                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Mappen met gegevensbestanden met een populatie in verschillende CSV-indelingen. |
| /csv/taxi/                                                   | Map met openbare NYC-gegevensbestanden in CSV-indeling              |
| /parquet/                                                    | Bovenliggende map voor gegevens in Parquet-indeling                     |
| /parquet/taxi                                                | Openbare gegevensbestanden van NYC in Parquet-indeling, gepartitioneerd per jaar en maand met Hive/Hadoop-partitieschema. |
| /parquet/nested/                                             | Voorbeeld van Parquet-bestanden met geneste kolommen                     |
| /json/                                                       | Bovenliggende map voor gegevens in JSON-indeling                        |
| /json/books/                                                 | JSON-bestanden met boekengegevens                                   |

### <a name="sample-query"></a>Voorbeeldquery

De laatste stap van de validatie is het uitvoeren van de volgende query:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc;
```

De bovenstaande query moet dit getal retourneren: **8945574**.

## <a name="next-steps"></a>Volgende stappen

U kunt nu doorgaan met de volgende Help-artikelen:

- [Query's uitvoeren op één CSV-bestand](query-single-csv-file.md)

- [Query's uitvoeren op mappen en meerdere CSV-bestanden](query-folders-multiple-csv-files.md)

- [Query's uitvoeren op specifieke bestanden](query-specific-files.md)

- [Query's uitvoeren op Parquet-bestanden](query-parquet-files.md)

- [Query uitvoeren op met Parquet geneste typen](query-parquet-nested-types.md)

- [Query's uitvoeren op JSON-bestanden](query-json-files.md)

- [Weergaven maken en gebruiken](create-use-views.md)
