---
title: Door de gebruiker gedefinieerde schema's gebruiken
description: Tips voor het gebruik van Door T-SQL door de gebruiker gedefinieerde schema's om oplossingen te ontwikkelen in Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7144fa75d156ca7aed9d8215592f89c167cfb221
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633454"
---
# <a name="user-defined-schemas-in-synapse-sql-pool"></a>Door de gebruiker gedefinieerde schema's in de Synapse SQL-groep
Dit artikel richt zich op het geven van verschillende tips voor het gebruik van Door T-SQL door de gebruiker gedefinieerde schema's om oplossingen te ontwikkelen in Synapse SQL-pool.

## <a name="schemas-for-application-boundaries"></a>Schema's voor toepassingsgrenzen

Traditionele gegevensmagazijnen gebruiken vaak afzonderlijke databases om toepassingsgrenzen te creëren op basis van werkbelasting, domein of beveiliging. 

Een traditioneel SQL Server-gegevensmagazijn kan bijvoorbeeld een faseringsdatabase, een gegevensmagazijndatabase en enkele databases voor gegevensopslag bevatten. In deze topologie werkt elke database als een werkbelasting en beveiligingsgrens in de architectuur.

SQL-pool voert daarentegen de volledige gegevensmagazijnworkload uit binnen één database. Cross database joins zijn niet toegestaan. SQL-groep verwacht dat alle tabellen die door het magazijn worden gebruikt, in één database worden opgeslagen.

> [!NOTE]
> SQL-pool biedt geen ondersteuning voor cross database query's van welke aard dan ook. Daarom moeten de implementaties van gegevensmagazijnen die dit patroon gebruiken, worden herzien.
> 
> 

## <a name="recommendations"></a>Aanbevelingen
Wat volgt zijn aanbevelingen voor het consolideren van workloads, beveiliging, domein en functionele grenzen met behulp van door de gebruiker gedefinieerde schema's:

- Gebruik één SQL-pooldatabase om uw volledige gegevensmagazijnwerkuitvoeren ingevoeren.
- Consolideer uw bestaande gegevensmagazijnomgeving om één SQL-pooldatabase te gebruiken.
- Maak gebruik van **door de gebruiker gedefinieerde schema's** om de eerder geïmplementeerde grens met behulp van databases te bieden.

Als door de gebruiker gedefinieerde schema's niet eerder zijn gebruikt, hebt u een schone lei. Gebruik de oude databasenaam als basis voor uw door gebruikers gedefinieerde schema's in de SQL-groepdatabase.

Als er al schema's zijn gebruikt, hebt u een aantal opties:

- Verwijder de oude schemanamen en begin opnieuw.
- Bewaar de oude schemanamen door de verouderde schemanaam in afwachting van de tabelnaam.
- Bewaar de oudere schemanamen door weergaven over de tabel te implementeren in een extra schema om de oude schemastructuur opnieuw te maken.

> [!NOTE]
> Op de eerste inspectie optie 3 lijkt misschien wel de meest aantrekkelijke optie. Echter, de duivel is in het detail. Weergaven worden alleen gelezen in SQL-pool. Alle gegevens of tabelwijzigingen moeten worden uitgevoerd tegen de basistabel. Optie 3 introduceert ook een laag weergaven in uw systeem. Misschien wilt u hier nog wat extra aandacht aan geven als u al weergaven in uw architectuur gebruikt.
> 
> 

### <a name="examples"></a>Voorbeelden:
Implementeer door de gebruiker gedefinieerde schema's op basis van databasenamen:

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Houd oudere schemanamen door ze vooraf in behandeling te nemen bij de tabelnaam. Schema's gebruiken voor de werkbelastinggrens:

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Houd oudere schemanamen bij met weergaven:

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Elke wijziging in de schemastrategie moet worden herzien van het beveiligingsmodel voor de database. In veel gevallen u het beveiligingsmodel vereenvoudigen door machtigingen toe te wijsop schemaniveau. Als er meer gedetailleerde machtigingen nodig zijn, u databaserollen gebruiken.
> 
> 

## <a name="next-steps"></a>Volgende stappen
Zie voor meer ontwikkelingstips [het ontwikkelingsoverzicht.](sql-data-warehouse-overview-develop.md)

