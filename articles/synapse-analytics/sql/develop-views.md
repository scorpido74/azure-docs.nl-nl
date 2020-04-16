---
title: T-SQL-weergaven met Synapse SQL
description: Tips voor het gebruik van T-SQL-weergaven en het ontwikkelen van oplossingen met Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3a073c9539f2fb996ae59ef513525c217170f2e7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428652"
---
# <a name="t-sql-views-using-synapse-sql"></a>T-SQL-weergaven met Synapse SQL
In dit artikel vindt u tips voor het gebruik van T-SQL-weergaven en het ontwikkelen van oplossingen met Synapse SQL. 

## <a name="why-use-views"></a>Waarom weergaven gebruiken?

Weergaven kunnen op verschillende manieren worden gebruikt om de kwaliteit van uw oplossing te verbeteren.  In dit artikel worden enkele voorbeelden belicht van hoe u uw oplossing verrijken met weergaven en worden de beperkingen beschreven die in overweging moeten worden genomen.

### <a name="sql-pool---create-view"></a>SQL-groep - weergave maken

> [!NOTE]
> **SQL-groep:** Syntaxis voor WEERGAVE MAKEN wordt niet besproken in dit artikel. Zie de documentatie [WEERGAVE MAKEN](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor meer informatie.

### <a name="sql-on-demand-preview---create-view"></a>SQL on-demand (preview) - weergave maken

> [!NOTE]
> **SQL on-demand:** Syntaxis voor CREATE VIEW wordt niet besproken in dit artikel. Zie de documentatie [WEERGAVE MAKEN](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor meer informatie.

## <a name="architectural-abstraction"></a>Architecturale abstractie

Een gemeenschappelijk toepassingspatroon is het opnieuw maken van tabellen met [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS), waarna een objectdenaampatroon wordt gewijzigd tijdens het laden van gegevens.

In het volgende voorbeeld worden nieuwe datumrecords toegevoegd aan een datumdimensie. Houd er rekening mee hoe een nieuwe tabel, DimDate_New, eerst wordt gemaakt en vervolgens wordt hernoemd om de oorspronkelijke versie van de tabel te vervangen.

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
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Houd er rekening mee dat deze aanpak ertoe kan leiden dat tabellen worden weergegeven en verdwijnen uit de weergave van een gebruiker en dat er 'tabel bestaat niet' foutberichten worden gevraagd. Weergaven kunnen worden gebruikt om gebruikers een consistente presentatielaag te bieden terwijl de onderliggende objecten een andere naam krijgen.

Door toegang te bieden tot gegevens via weergaven, hebben gebruikers geen zichtbaarheid nodig voor de onderliggende tabellen. Naast een consistente gebruikerservaring zorgt deze laag ervoor dat analyseontwerpers het gegevensmodel kunnen ontwikkelen. De mogelijkheid om de onderliggende tabellen te ontwikkelen betekent dat ontwerpers CTAS kunnen gebruiken om de prestaties te maximaliseren tijdens het gegevenslaadproces.

## <a name="performance-optimization"></a>Optimalisatie van prestaties

Weergaven kunnen ook worden gebruikt om prestatiegeoptimaliseerde joins tussen tabellen af te dwingen. Een weergave kan bijvoorbeeld een redundante distributiesleutel bevatten als onderdeel van de verbindingscriteria om gegevensverplaatsing te minimaliseren.

Het forceren van een specifieke query of joinhint is een ander voordeel van het gebruik van T-SQL-weergaven. Als zodanig zorgt de weergavecapaciteit ervoor dat joins altijd optimaal worden uitgevoerd. U vermijdt de noodzaak voor gebruikers om de juiste constructie voor hun joins te onthouden.

## <a name="limitations"></a>Beperkingen

Weergaven in Synapse SQL worden alleen opgeslagen als metagegevens. De volgende opties zijn dan ook niet beschikbaar:

* Er is geen schemabindingsoptie
* Basistabellen kunnen niet worden bijgewerkt via de weergave
* Weergaven kunnen niet worden gemaakt via tijdelijke tabellen
* Er is geen ondersteuning voor de EXPAND / NOEXPAND hints
* Er zijn geen geïndexeerde weergaven in Synapse SQL

## <a name="next-steps"></a>Volgende stappen

Zie [Synapse SQL-ontwikkelingsoverzicht](develop-overview.md)voor meer ontwikkelingstips.



