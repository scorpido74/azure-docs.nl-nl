---
title: Door de gebruiker gedefinieerde schema's binnen Synapse SQL
description: In de onderstaande secties vindt u verschillende tips voor het gebruik van door De Gebruiker van T-SQL gedefinieerde schema's om oplossingen te ontwikkelen met de Synapse SQL-mogelijkheid van Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: ac4753da1405fe6b8cd209bb4899192e9f317aa1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428704"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Door de gebruiker gedefinieerde schema's binnen Synapse SQL

In de onderstaande secties vindt u verschillende tips voor het gebruik van Door De Gebruiker van T-SQL gedefinieerde schema's om oplossingen binnen Synapse SQL te ontwikkelen.

## <a name="schemas-for-application-boundaries"></a>Schema's voor toepassingsgrenzen

Traditionele analysearchitectuur maakt vaak gebruik van afzonderlijke databases om toepassingsgrenzen te creëren op basis van werkbelasting, domein of beveiliging. Een traditionele SQL Server-analyse-infrastructuur kan bijvoorbeeld een faseringsdatabase, een analysedatabase en gegevensmart-databases bevatten. In deze topologie werkt elke database als een werkbelasting en beveiligingsgrens in de architectuur.

In plaats daarvan voert Synapse SQL de volledige analytics-workload uit binnen één database. Cross database joins zijn niet toegestaan. Synapse SQL verwacht dat alle tabellen die door het magazijn worden gebruikt, in één database worden opgeslagen.

> [!NOTE]
> SQL-groepen bieden geen ondersteuning voor cross database query's van welke aard dan ook. Daarom moeten analyse-implementaties die dit patroon gebruiken, worden herzien. SQL on-demand (preview) ondersteunt cross database queries.

## <a name="user-defined-schema-recommendations"></a>Door de gebruiker gedefinieerde schemaaanbevelingen

Hieronder zijn aanbevelingen voor het consolideren van workloads, beveiliging, domein en functionele grenzen met behulp van door de gebruiker gedefinieerde schema's:

- Gebruik één database om uw volledige analytics-workload uit te voeren.
- Consolideer uw bestaande analyseomgeving om één database te gebruiken.
- Maak gebruik van **door de gebruiker gedefinieerde schema's** om de eerder geïmplementeerde grens met behulp van databases te bieden.

Als door de gebruiker gedefinieerde schema's niet eerder zijn gebruikt, hebt u een schone lei. Gebruik de oude databasenaam als basis voor uw door de gebruiker gedefinieerde schema's in de Synapse SQL-database.

Als er al schema's zijn gebruikt, hebt u een aantal opties:

- De oude schemanamen verwijderen en opnieuw beginnen
- De namen van het verouderde schema behouden door de oude schemanaam vooraf in behandeling te nemen bij de tabelnaam
- Bewaar de oudere schemanamen door weergaven over de tabel te implementeren in een extra schema, waarmee de oude schemastructuur opnieuw wordt gemaakt.

> [!NOTE]
> Bij de eerste inspectie lijkt optie 3 misschien wel de meest aansprekende keuze. Weergaven worden alleen gelezen in Synapse SQL. Alle gegevens of tabelwijzigingen moeten worden uitgevoerd tegen de basistabel. Optie 3 introduceert ook een laag weergaven in uw systeem. Misschien wilt u hier nog wat extra aandacht aan geven als u al weergaven in uw architectuur gebruikt.
> 
> 

### <a name="examples"></a>Voorbeelden

Implementeer door de gebruiker gedefinieerde schema's op basis van databasenamen.

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Houd de namen van het oude schema door ze vooraf in behandeling te nemen bij de tabelnaam. Gebruik schema's voor de werkbelastinggrens.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Bewaar de oude schemanamen met weergaven.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
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
> Elke wijziging in de schemastrategie vereist een herziening van het beveiligingsmodel voor de database. In veel gevallen u het beveiligingsmodel vereenvoudigen door machtigingen toe te wijsop schemaniveau.

Als er meer gedetailleerde machtigingen nodig zijn, u databaserollen gebruiken. Zie het artikel [Databaserollen en gebruikers beheren](../../analysis-services/analysis-services-database-users.md) voor meer informatie over databaserollen.

## <a name="next-steps"></a>Volgende stappen

Zie [Synapse SQL-ontwikkelingsoverzicht](develop-overview.md)voor meer ontwikkelingstips.
