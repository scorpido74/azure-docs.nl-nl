---
title: T-SQL-weer gaven gebruiken in Azure SQL Data Warehouse | Microsoft Docs
description: Tips voor het gebruik van T-SQL-weer gaven in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 1fd406243f0f2f5339c4170c4ec17286fcf2ef6d
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901712"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Weer gaven in Azure SQL Data Warehouse
Weer gaven kunnen op verschillende manieren worden gebruikt om de kwaliteit van uw oplossing te verbeteren. 

Azure SQL Data Warehouse ondersteunt standaard-en gerealiseerde weer gaven. Beide zijn virtuele tabellen die zijn gemaakt met SELECT-expressies en worden weer gegeven als logische tabellen. Met weer gaven wordt de complexiteit van common data computing ingekapseld en wordt een abstractie laag aan berekenings wijzigingen toegevoegd, zodat u geen query's hoeft te schrijven.

## <a name="standard-view"></a>Standaard weergave
Een standaard weergave berekent de gegevens telkens wanneer de weer gave wordt gebruikt. Er zijn geen gegevens opgeslagen op schijf. Personen gebruiken meestal standaard weergaven als een hulp middel waarmee u de logische objecten en query's in een Data Base kunt ordenen. Als u een standaard weergave wilt gebruiken, moet er direct naar een query worden verwezen. Zie de documentatie voor het [maken van weer gaven](/sql/t-sql/statements/create-view-transact-sql) voor meer informatie.

Weer gaven in SQL Data Warehouse worden alleen als meta gegevens opgeslagen. De volgende opties zijn daarom niet beschikbaar:
* Er is geen optie voor schema bindingen
* Basis tabellen kunnen niet worden bijgewerkt via de weer gave
* Er kunnen geen weer gaven worden gemaakt over tijdelijke tabellen
* Er wordt geen ondersteuning geboden voor de instructie EXPAND/deexpand.
* Er zijn geen geïndexeerde weer gaven in SQL Data Warehouse

Standaard weergaven kunnen worden gebruikt voor het afdwingen van prestaties geoptimaliseerde samen voegingen tussen tabellen. Een weer gave kan bijvoorbeeld een redundante distributie sleutel bevatten als onderdeel van de samenvoegings criteria om de verplaatsing van gegevens te minimaliseren. Een ander voor deel van een weer gave is het afdwingen van een specifieke query of hint voor samen voegen. Als u weer gaven op deze manier gebruikt, zorgt u ervoor dat samen voegingen altijd worden uitgevoerd op een optimale manier om te voor komen dat gebruikers de juiste construct voor hun deelname kunnen onthouden.

## <a name="materialized-view"></a>Gerealiseerde weergave
Met een gerealiseerde weer gave worden de gegevens in Azure SQL Data Warehouse, zoals een tabel, bewaard en bewaard. Telkens wanneer een gerealiseerde weer gave wordt gebruikt, is er geen herberekening nodig. Wanneer de gegevens in basis tabellen worden geladen, Azure SQL Data Warehouse de gerealiseerde weer gaven synchroon vernieuwen.  De query optimalisatie maakt automatisch gebruik van geïmplementeerde gerealiseerde weer gaven om de query prestaties te verbeteren, zelfs als niet in de query wordt verwezen naar de weer gaven.  Query's die het meest geschikt zijn voor gerealiseerde weer gaven zijn complexe query's (doorgaans query's met samen voegingen en aggregaties) in grote tabellen die een kleine resultatenset genereren.  

Zie [gerealiseerde weer gave maken als selecteren](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)voor meer informatie over de syntaxis van gerealiseerde weer gaven en andere vereisten.  

Voor hulp bij het afstemmen van query's controleert u de [prestaties afstemmen met gerealiseerde weer gaven](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views). 

## <a name="example"></a>Voorbeeld
Een algemeen toepassings patroon is het opnieuw maken van tabellen met CREATE TABLE als SELECT (CTAS), gevolgd door het wijzigen van het naam patroon van een object tijdens het laden van gegevens.  In het volgende voor beeld worden nieuwe datum records toegevoegd aan een datum dimensie. U ziet hoe een nieuwe tabel, DimDate_New, wordt gemaakt en de naam ervan wordt gewijzigd om de oorspronkelijke versie van de tabel te vervangen.

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
Deze benadering kan echter leiden tot tabellen die worden weer gegeven en weer gegeven in de gebruikers weergave, evenals de fout berichten ' tabel bestaat niet '. Weer gaven kunnen worden gebruikt om gebruikers een consistente presentatielaag te bieden, terwijl de namen van de onderliggende objecten worden gewijzigd. Door toegang te bieden tot gegevens via weer gaven, hebben gebruikers geen zicht baarheid nodig voor de onderliggende tabellen. Deze laag biedt een consistente gebruikers ervaring en zorgt ervoor dat de Data Warehouse-ontwerpers het gegevens model kunnen ontwikkelen. Het is mogelijk om de onderliggende tabellen te ontwikkelen. ontwerpers kunnen CTAS gebruiken om de prestaties tijdens het proces voor het laden van gegevens te maximaliseren.   

## <a name="next-steps"></a>Volgende stappen
Zie [Overzicht van SQL Data Warehouse voor ontwikkelaars](sql-data-warehouse-overview-develop.md) voor meer tips voor ontwikkelaars.


