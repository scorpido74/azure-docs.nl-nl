---
title: Resources voor het ontwikkelen van Synapse SQL-functies
description: Ontwikkel concepten, ontwerp beslissingen, aanbevelingen en coderings technieken voor Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: f4c61b43855e4a845956a937d9d9cfe738d6f47a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90032942"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Ontwerp beslissingen en coderings technieken voor Synapse SQL-functies in azure Synapse Analytics
In dit artikel vindt u een lijst met resources voor SQL-groep en de SQL-functie op aanvraag (preview) van Synapse SQL. De aanbevolen artikelen worden opgesplitst in twee secties: belang rijke beslissingen met betrekking tot belangrijkste ontwerpen en ontwikkelings-en coderings technieken.

Het doel van deze artikelen is om u te helpen bij het ontwikkelen van de optimale technische benadering voor de Synapse SQL-onderdelen in Synapse Analytics.

## <a name="key-design-decisions"></a>Voornaamste ontwerp beslissingen
De onderstaande artikelen markeren concepten en ontwerp beslissingen voor Synapse SQL Development:

| Artikel | SQL-pool | SQL on-demand |
| ------- | -------- | ------------- |
| [Verbindingen](connect-overview.md)                    | Ja | Ja |
| [Resource-klassen en gelijktijdigheid](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Ja    | Nee |
| [Transacties](develop-transactions.md)              | Ja | Nee |
| [Door de gebruiker gedefinieerde schema's](develop-user-defined-schemas.md) | Ja | Ja |
| [Tabeldistributie](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Ja | Nee |
| [Tabelindexen](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Ja | Nee |
| [Tabelpartities](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Ja | Nee |
| [Statistieken](develop-tables-statistics.md)            | Ja | Ja |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Ja | Nee |
| [Externe tabellen](develop-tables-external-tables.md) | Ja | Ja |
| [CETAS](develop-tables-cetas.md)                     | Ja | Ja |


## <a name="recommendations"></a>Aanbevelingen

Hieronder vindt u essentiële artikelen die de nadruk leggen op specifieke coderings technieken, tips en aanbevelingen voor ontwikkeling:

| Artikel | SQL-pool | SQL on-demand |
| ------- | -------- | ------------- |
| [Opgeslagen procedures](develop-stored-procedures.md)  | Ja                | Nee                      |
| [Labels](develop-label.md)                           | Ja                | Nee                      |
| [Weergaven](develop-views.md)                             | Ja                | Ja                     |
| [Tijdelijke tabellen](develop-tables-temporary.md)       | Ja                | Ja                     |
| [Dynamic SQL](develop-dynamic-sql.md)                 | Ja                | Ja                     |
| [Lussen](develop-loops.md)                         | Ja                | Ja                     |
| [Groeperen op opties](develop-group-by-options.md)       | Ja                | Nee                      |
| [Variabele toewijzing](develop-variable-assignment.md) | Ja                | Ja                     |

## <a name="next-steps"></a>Volgende stappen
Zie [SQL-Groep T-SQL-instructies](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie.

