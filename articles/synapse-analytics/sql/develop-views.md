---
title: T-SQL-weer gaven met Synapse SQL
description: Tips voor het gebruik van T-SQL-weer gaven en het ontwikkelen van oplossingen met Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: fafa0c2e1b02cc49bfb852ed7770b0927b0e9334
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032721"
---
# <a name="t-sql-views-using-synapse-sql"></a>T-SQL-weer gaven met Synapse SQL
In dit artikel vindt u tips voor het gebruik van T-SQL-weer gaven en het ontwikkelen van oplossingen met Synapse SQL. 

## <a name="why-use-views"></a>Waarom weer gaven gebruiken

Weer gaven kunnen op verschillende manieren worden gebruikt om de kwaliteit van uw oplossing te verbeteren.  In dit artikel worden enkele voor beelden gegeven van hoe u uw oplossing kunt verrijken met weer gaven en de beperkingen kunt opnemen die moeten worden overwogen.

### <a name="sql-pool---create-view"></a>SQL-groep-weer gave maken

> [!NOTE]
> **SQL-groep**: de syntaxis voor de weer gave Create wordt niet in dit artikel besproken. Zie de documentatie voor het [maken van weer gaven](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor meer informatie.

### <a name="sql-on-demand-preview---create-view"></a>SQL on-demand (preview): weer gave maken

> [!NOTE]
> **SQL on-demand**: de syntaxis voor de weer gave Create wordt niet beschreven in dit artikel. Zie de documentatie voor het [maken van weer gaven](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor meer informatie.

## <a name="architectural-abstraction"></a>Samen vatting van architectuur

Een algemeen toepassings patroon is het opnieuw maken van tabellen met [Create Table als Select](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS), gevolgd door een patroon voor het wijzigen van de naam van een object tijdens het laden van gegevens.

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
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Houd er wel op dat deze benadering kan leiden tot tabellen die worden weer gegeven en weer gegeven in de weer gave van een gebruiker en prompts "tabel bestaat niet". Weer gaven kunnen worden gebruikt om gebruikers een consistente presentatielaag te geven terwijl de namen van de onderliggende objecten worden gewijzigd.

Door toegang te bieden tot gegevens via weer gaven, hebben gebruikers geen zicht baarheid van de onderliggende tabellen nodig. Naast een consistente gebruikers ervaring zorgt deze laag ervoor dat Analytics-ontwerpers het gegevens model kunnen ontwikkelen. De mogelijkheid om de onderliggende tabellen te ontwikkelen, betekent dat ontwerpers CTAS kunnen gebruiken om de prestaties tijdens het proces van het laden van gegevens te maximaliseren.

## <a name="performance-optimization"></a>Optimalisatie van prestaties

Weer gaven kunnen ook worden gebruikt voor het afdwingen van prestaties geoptimaliseerde samen voegingen tussen tabellen. Een weer gave kan bijvoorbeeld een redundante distributie sleutel bevatten als onderdeel van de samenvoegings criteria om de verplaatsing van gegevens te minimaliseren.

Het afdwingen van een specifieke query of samen voegen Hint is een ander voor deel van het gebruik van T-SQL-weer gaven. Als zodanig zorgt de weer gaven er voor dat samen voegingen altijd op een optimale manier worden uitgevoerd. U kunt voor komen dat gebruikers de juiste construct voor hun deelname onthouden.

## <a name="limitations"></a>Beperkingen

Weer gaven in Synapse SQL worden alleen als meta gegevens opgeslagen. De volgende opties zijn daarom niet beschikbaar:

* Er is geen optie voor schema bindingen
* Basis tabellen kunnen niet worden bijgewerkt in de weer gave
* Er kunnen geen weer gaven worden gemaakt over tijdelijke tabellen
* Er is geen ondersteuning voor de Uitvouw-en deexpand-hints
* Er zijn geen geïndexeerde weer gaven in Synapse SQL

## <a name="next-steps"></a>Volgende stappen

Zie [Synapse SQL Development Overview](develop-overview.md)(Engelstalig) voor meer tips voor ontwikkel aars.



