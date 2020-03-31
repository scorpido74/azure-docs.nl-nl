---
title: Door de gebruiker gedefinieerde schema's gebruiken
description: Tips voor het gebruik van Door T-SQL door de gebruiker gedefinieerde schema's in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.
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
ms.openlocfilehash: a9ed4f01aae6ace1af6c1652fe3c5ecfe14dc6bf
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351543"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Door de gebruiker gedefinieerde schema's gebruiken in SQL Data Warehouse
Tips voor het gebruik van Door T-SQL door de gebruiker gedefinieerde schema's in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.

## <a name="schemas-for-application-boundaries"></a>Schema's voor toepassingsgrenzen

Traditionele gegevensmagazijnen gebruiken vaak afzonderlijke databases om toepassingsgrenzen te creëren op basis van werkbelasting, domein of beveiliging. Een traditioneel SQL Server-gegevensmagazijn kan bijvoorbeeld een faseringsdatabase, een gegevensmagazijndatabase en enkele databases voor gegevensopslag bevatten. In deze topologie fungeert elke database als een werkbelasting en beveiligingsgrens in de architectuur.

SQL Data Warehouse voert daarentegen de volledige datawarehouseworkload uit binnen één database. Cross database joins zijn niet toegestaan. Sql Data Warehouse verwacht daarom dat alle tabellen die door het magazijn worden gebruikt, in één database worden opgeslagen.

> [!NOTE]
> SQL Data Warehouse ondersteunt geen cross database query's van welke aard dan ook. Daarom moeten de implementaties van gegevensmagazijnen die dit patroon gebruiken, worden herzien.
> 
> 

## <a name="recommendations"></a>Aanbevelingen
Dit zijn aanbevelingen voor het consolideren van workloads, beveiliging, domein en functionele grenzen met behulp van door de gebruiker gedefinieerde schema's

1. Gebruik één SQL Data Warehouse-database om uw volledige gegevensmagazijnwerkuitvoeren uit te voeren
2. Uw bestaande datawarehouse-omgeving consolideren om één SQL Data Warehouse-database te gebruiken
3. Maak gebruik van **door de gebruiker gedefinieerde schema's** om de eerder geïmplementeerde grens met behulp van databases te bieden.

Als door de gebruiker gedefinieerde schema's niet eerder zijn gebruikt, hebt u een schone lei. Gebruik de oude databasenaam als basis voor uw door de gebruiker gedefinieerde schema's in de SQL Data Warehouse-database.

Als er al schema's zijn gebruikt, hebt u een aantal opties:

1. De oude schemanamen verwijderen en opnieuw beginnen
2. De oude schemanamen behouden door de verouderde schemanaam in afwachting van de tabelnaam
3. Bewaar de oudere schemanamen door weergaven over de tabel te implementeren in een extra schema om de oude schemastructuur opnieuw te maken.

> [!NOTE]
> Op de eerste inspectie optie 3 lijkt misschien wel de meest aantrekkelijke optie. Echter, de duivel is in het detail. Weergaven worden alleen gelezen in SQL Data Warehouse. Alle gegevens of tabelwijzigingen moeten worden uitgevoerd tegen de basistabel. Optie 3 introduceert ook een laag weergaven in uw systeem. Misschien wilt u hier nog wat extra aandacht aan geven als u al weergaven in uw architectuur gebruikt.
> 
> 

### <a name="examples"></a>Voorbeelden:
Door de gebruiker gedefinieerde schema's implementeren op basis van databasenamen

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

Behouden legacy schema namen door pre-pending hen aan de tabel naam. Gebruik schema's voor de werkbelastinggrens.

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

Oudere schemanamen behouden met weergaven

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

