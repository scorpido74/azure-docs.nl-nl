---
title: Door de gebruiker gedefinieerde schema's gebruiken
description: Tips voor het gebruik van door de gebruiker gedefinieerde T-SQL-schema's in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 697bffa36e9b208c1a027654df81fb356ddfc8ed
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685816"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Door de gebruiker gedefinieerde schema's gebruiken in SQL Data Warehouse
Tips voor het gebruik van door de gebruiker gedefinieerde T-SQL-schema's in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.

## <a name="schemas-for-application-boundaries"></a>Schema's voor toepassings grenzen

Traditionele data warehouses gebruiken vaak afzonderlijke data bases om toepassings grenzen te maken op basis van de werk belasting, het domein of de beveiliging. Een traditioneel SQL Server Data Warehouse kan bijvoorbeeld een faserings database, een Data Warehouse-data base en enkele data-datamart-data bases bevatten. In deze topologie fungeert elke Data Base als een werk belasting en beveiligings grens in de architectuur.

SQL Data Warehouse voert daarentegen de volledige werk belasting van het data warehouse in één Data Base uit. Cross-data base-samen voegingen zijn niet toegestaan. Daarom verwacht SQL Data Warehouse dat alle tabellen die door het magazijn worden gebruikt, worden opgeslagen in de ene data base.

> [!NOTE]
> SQL Data Warehouse biedt geen ondersteuning voor query's voor meerdere data bases van welke aard dan ook. Data Warehouse-implementaties die gebruikmaken van dit patroon moeten daarom worden gereviseerd.
> 
> 

## <a name="recommendations"></a>Aanbevelingen
Dit zijn aanbevelingen voor het samen voegen van werk belastingen, beveiliging, domein en functionele grenzen door gebruik te maken van door de gebruiker gedefinieerde schema's

1. Een SQL Data Warehouse-Data Base gebruiken om uw hele Data Warehouse-werk belasting uit te voeren
2. Consolideer uw bestaande data warehouse-omgeving om één SQL Data Warehouse data base te gebruiken
3. Gebruik door de **gebruiker gedefinieerde schema's** om de grens te leveren die eerder is geïmplementeerd met behulp van data bases.

Als door de gebruiker gedefinieerde schema's niet eerder zijn gebruikt, hebt u een schone pastel. Gebruik gewoon de oude database naam als basis voor uw door de gebruiker gedefinieerde schema's in de SQL Data Warehouse-data base.

Als er al schema's zijn gebruikt, hebt u een aantal opties:

1. Verwijder de oude schema namen en begin vers
2. Behoud de oude schema namen door de oude schema naam vooraf in behandeling te laten nemen aan de naam van de tabel
3. Behoud de oude schema namen door weer gaven te implementeren in de tabel in een extra schema om de oude schema structuur opnieuw te maken.

> [!NOTE]
> Op de eerste inspectie optie 3 lijkt de meest aantrekkelijke optie. De zon bevindt zich echter in het detail. Weer gaven zijn alleen-lezen in SQL Data Warehouse. Eventuele gegevens of tabel wijzigingen moeten worden uitgevoerd voor de basis tabel. Met optie 3 wordt ook een laag van weer gaven in uw systeem geïntroduceerd. U kunt hiervan een extra idee geven als u al gebruikmaakt van weer gaven in uw architectuur.
> 
> 

### <a name="examples"></a>Voorbeelden:
Door de gebruiker gedefinieerde schema's implementeren op basis van database namen

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

Verouderde schema namen behouden door deze vooraf in behandeling te laten nemen aan de naam van de tabel. Schema's gebruiken voor de grens van de werk belasting.

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

Verouderde schema namen behouden met behulp van weer gaven

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
> Voor elke wijziging in de schema strategie moet het beveiligings model voor de Data Base worden gecontroleerd. In veel gevallen kunt u het beveiligings model vereenvoudigen door machtigingen toe te wijzen op schema niveau. Als u meer gedetailleerde machtigingen nodig hebt, kunt u database rollen gebruiken.
> 
> 

## <a name="next-steps"></a>Volgende stappen
Zie [ontwikkelings overzicht](sql-data-warehouse-overview-develop.md)voor meer tips voor ontwikkel aars.

