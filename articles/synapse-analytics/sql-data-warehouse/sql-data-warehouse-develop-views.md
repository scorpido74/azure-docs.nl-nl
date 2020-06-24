---
title: T-SQL-weer gaven gebruiken
description: Tips voor het gebruik van T-SQL-weer gaven en het ontwikkelen van oplossingen in Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 26eb3a495fd1c896416265687d92da66dfc3599b
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85212289"
---
# <a name="views-in-synapse-sql-pool"></a>Weer gaven in de SQL-groep Synapse

Weer gaven kunnen op verschillende manieren worden gebruikt om de kwaliteit van uw oplossing te verbeteren.

SQL-pool ondersteunt zowel standaard als gerealiseerde weer gaven. Beide zijn virtuele tabellen die zijn gemaakt met SELECT-expressies en worden weer gegeven als logische tabellen.

Met weer gaven wordt de complexiteit van common data computing ingekapseld en wordt een abstractie laag aan berekenings wijzigingen toegevoegd, zodat u geen query's hoeft te schrijven.

## <a name="standard-view"></a>Standaard weergave

Een standaard weergave berekent de gegevens telkens wanneer de weer gave wordt gebruikt. Er zijn geen gegevens opgeslagen op schijf. Personen gebruiken meestal standaard weergaven als een hulp middel waarmee u de logische objecten en query's in een Data Base kunt ordenen.

Als u een standaard weergave wilt gebruiken, moet er direct naar een query worden verwezen. Zie de documentatie voor het [maken van weer gaven](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) voor meer informatie.

Weer gaven in de SQL-groep worden alleen opgeslagen als meta gegevens. De volgende opties zijn daarom niet beschikbaar:

* Er is geen optie voor schema bindingen
* Basis tabellen kunnen niet worden bijgewerkt in de weer gave
* Er kunnen geen weer gaven worden gemaakt over tijdelijke tabellen
* Er wordt geen ondersteuning geboden voor de instructie EXPAND/deexpand.
* Er zijn geen geïndexeerde weer gaven in de SQL-groep

Standaard weergaven kunnen worden gebruikt voor het afdwingen van prestaties geoptimaliseerde samen voegingen tussen tabellen. Een weer gave kan bijvoorbeeld een redundante distributie sleutel bevatten als onderdeel van de samenvoegings criteria om de verplaatsing van gegevens te minimaliseren.

Een ander voor deel van een weer gave is het afdwingen van een specifieke query of hint voor samen voegen. Als u weer gaven op deze manier gebruikt, zorgt u ervoor dat samen voegingen altijd worden uitgevoerd op een optimale manier om te voor komen dat gebruikers de juiste construct voor hun deelname kunnen onthouden.

## <a name="materialized-view"></a>Gerealiseerde weergave

Met een gerealiseerde weer gave worden de gegevens in de SQL-groep, net als in een tabel, opgeslagen en bewaard. Telkens wanneer een gerealiseerde weer gave wordt gebruikt, is er geen herberekening nodig.

Wanneer de gegevens in basis tabellen worden geladen, vernieuwt de SQL-pool de gerealiseerde weer gaven synchroon.  De query optimalisatie maakt automatisch gebruik van geïmplementeerde gerealiseerde weer gaven om de query prestaties te verbeteren, zelfs als niet in de query wordt verwezen naar de weer gaven.  

Query's die het meest geschikt zijn voor gerealiseerde weer gaven zijn complexe query's (doorgaans query's met samen voegingen en aggregaties) in grote tabellen die een kleine resultatenset genereren.  

Zie [gerealiseerde weer gave maken als selecteren](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)voor meer informatie over de syntaxis van gerealiseerde weer gaven en andere vereisten.  

Voor hulp bij het afstemmen van query's controleert u de [prestaties afstemmen met gerealiseerde weer gaven](performance-tuning-materialized-views.md).

## <a name="example"></a>Voorbeeld

Een algemeen toepassings patroon is het opnieuw maken van tabellen met CREATE TABLE als SELECT (CTAS), gevolgd door een patroon voor het wijzigen van de naam van een object tijdens het laden van gegevens.  

In het volgende voor beeld worden nieuwe datum records toegevoegd aan een datum dimensie. U ziet dat een nieuwe tabel, DimDate_New, eerst wordt gemaakt en de naam ervan wordt gewijzigd om de oorspronkelijke versie van de tabel te vervangen.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Deze benadering kan echter leiden tot het weer geven en weer geven van tabellen uit de weer gave van een gebruiker samen met het uitgeven van de fout berichten ' tabel bestaat niet '.

Weer gaven kunnen worden gebruikt om gebruikers een consistente presentatielaag te geven terwijl de namen van de onderliggende objecten worden gewijzigd. Door toegang te bieden tot gegevens via weer gaven, hebben gebruikers geen zicht baarheid van de onderliggende tabellen nodig.

Deze laag biedt een consistente gebruikers ervaring en zorgt ervoor dat de Data Warehouse-ontwerpers het gegevens model kunnen ontwikkelen. Het is mogelijk om de onderliggende tabellen te ontwikkelen. ontwerpers kunnen CTAS gebruiken om de prestaties tijdens het proces voor het laden van gegevens te maximaliseren.

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van SQL pool-ontwikkeling](sql-data-warehouse-overview-develop.md)voor meer tips voor ontwikkel aars.
