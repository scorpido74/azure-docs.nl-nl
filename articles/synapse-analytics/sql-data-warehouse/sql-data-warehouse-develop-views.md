---
title: T-SQL-weergaven gebruiken
description: Tips voor het gebruik van T-SQL-weergaven in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.
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
ms.openlocfilehash: 9eccc225c5473291b7535339ff64a036730894ae
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351524"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Weergaven in Azure SQL Data Warehouse
Weergaven kunnen op verschillende manieren worden gebruikt om de kwaliteit van uw oplossing te verbeteren. 

Azure SQL Data Warehouse ondersteunt standaard- en gematerialiseerde weergaven. Beide zijn virtuele tabellen gemaakt met SELECT-expressies en gepresenteerd aan query's als logische tabellen. Weergaven bevatten de complexiteit van algemene gegevensberekening en voegen een abstractielaag toe aan berekeningswijzigingen, zodat query's niet hoeven te worden herschreven.

## <a name="standard-view"></a>Standaardweergave
Een standaardweergave berekent de gegevens telkens wanneer de weergave wordt gebruikt. Er zijn geen gegevens opgeslagen op schijf. Mensen gebruiken standaardweergaven meestal als een hulpmiddel waarmee de logische objecten en query's in een database kunnen worden georganiseerd. Als u een standaardweergave wilt gebruiken, moet een query er rechtstreeks naar verwijzen. Zie de documentatie [WEERGAVE MAKEN](/sql/t-sql/statements/create-view-transact-sql) voor meer informatie.

Weergaven in SQL Data Warehouse worden alleen als metagegevens opgeslagen. De volgende opties zijn dan ook niet beschikbaar:
* Er is geen schemabindingsoptie
* Basistabellen kunnen niet worden bijgewerkt via de weergave
* Weergaven kunnen niet worden gemaakt via tijdelijke tabellen
* Er is geen ondersteuning voor de EXPAND / NOEXPAND hints
* Er zijn geen geïndexeerde weergaven in SQL Data Warehouse

Standaardweergaven kunnen worden gebruikt om prestatiegeoptimaliseerde joins tussen tabellen af te dwingen. Een weergave kan bijvoorbeeld een redundante distributiesleutel bevatten als onderdeel van de verbindingscriteria om gegevensverplaatsing te minimaliseren. Een ander voordeel van een weergave zou kunnen zijn om een specifieke query of join hint te forceren. Het gebruik van weergaven op deze manier garandeert dat joins altijd optimaal worden uitgevoerd en voorkomen dat gebruikers de juiste constructie voor hun joins hoeven te onthouden.

## <a name="materialized-view"></a>Gerealiseerde weergave
Een gematerialiseerde weergave berekent, slaat en onderhoudt de gegevens ervan in Azure SQL Data Warehouse, net als een tabel. Er is geen herberekening nodig elke keer wanneer een gematerialiseerde weergave wordt gebruikt. Terwijl de gegevens in basistabellen worden geladen, vernieuwt Azure SQL Data Warehouse synchroon de gematerialiseerde weergaven.  De queryoptimizer gebruikt automatisch geïmplementeerde gematerialiseerde weergaven om de queryprestaties te verbeteren, zelfs als er geen naar de weergaven wordt verwezen in de query.  Query's die het meest profiteren van gematerialiseerde weergaven zijn complexe query's (meestal query's met joins en aggregaties) op grote tabellen die een klein resultaatset produceren.  

Zie [GEMATERIALISEERDE WEERGAVE MAKEN ALS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)voor meer informatie over de gematerialiseerde weergavesyntaxis en andere vereisten .  

Controleer [Prestatieafstemming met gematerialiseerde weergaven voor](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)richtlijnen voor queryafstemming. 

## <a name="example"></a>Voorbeeld
Een gemeenschappelijk toepassingspatroon is het opnieuw maken van tabellen met CREATE TABLE AS SELECT (CTAS), gevolgd door een object dat de naam van het patroon hernoemt terwijl gegevens worden geladen.  In het volgende voorbeeld worden nieuwe datumrecords toegevoegd aan een datumdimensie. Houd er rekening mee hoe een nieuwe tabel, DimDate_New, eerst wordt gemaakt en vervolgens wordt hernoemd om de oorspronkelijke versie van de tabel te vervangen.

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
Deze aanpak kan er echter toe leiden dat tabellen worden weergegeven en verdwijnen uit de weergave van een gebruiker en dat er geen foutberichten in de tabel bestaan. Weergaven kunnen worden gebruikt om gebruikers een consistente presentatielaag te bieden terwijl de onderliggende objecten een andere naam krijgen. Door toegang te bieden tot gegevens via weergaven, hebben gebruikers geen zichtbaarheid nodig voor de onderliggende tabellen. Deze laag biedt een consistente gebruikerservaring en zorgt ervoor dat de ontwerpers van het datawarehouse het gegevensmodel kunnen ontwikkelen. Als u de onderliggende tabellen ontwikkelen, kunnen ontwerpers CTAS gebruiken om de prestaties tijdens het gegevenslaadproces te maximaliseren.   

## <a name="next-steps"></a>Volgende stappen
Zie [Overzicht van SQL Data Warehouse voor ontwikkelaars](sql-data-warehouse-overview-develop.md) voor meer tips voor ontwikkelaars.


