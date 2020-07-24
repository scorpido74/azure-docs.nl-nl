---
title: Resources voor het ontwikkelen van Synapse SQL-functies
description: Ontwikkel concepten, ontwerp beslissingen, aanbevelingen en coderings technieken voor Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 85b0137f8d89def2f38ffe82199950c9158888d0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87070044"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Ontwerp beslissingen en coderings technieken voor Synapse SQL-functies in azure Synapse Analytics
In dit artikel vindt u een lijst met resources voor SQL-groep en de SQL-functie op aanvraag (preview) van Synapse SQL. De aanbevolen artikelen worden opgesplitst in twee secties: belang rijke beslissingen met betrekking tot belangrijkste ontwerpen en ontwikkelings-en coderings technieken.

Het doel van deze artikelen is om u te helpen bij het ontwikkelen van de optimale technische benadering voor de Synapse SQL-onderdelen in Synapse Analytics.

## <a name="key-design-decisions"></a>Voornaamste ontwerp beslissingen
De onderstaande artikelen markeren concepten en ontwerp beslissingen voor Synapse SQL Development:

| Artikel | SQL-pool | SQL on-demand |
| ------- | -------- | ------------- |
| [Verbindingen](connect-overview.md)                    | Yes | Ja |
| [Resource-klassen en gelijktijdigheid](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Yes    | Nee |
| [Transacties](develop-transactions.md)              | Yes | Nee |
| [Door de gebruiker gedefinieerde schema's](develop-user-defined-schemas.md) | Yes | Ja |
| [Tabeldistributie](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Yes | Nee |
| [Tabelindexen](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Yes | Nee |
| [Tabelpartities](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Yes | Nee |
| [Statistieken](develop-tables-statistics.md)            | Yes | Ja |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Yes | Nee |
| [Externe tabellen](develop-tables-external-tables.md) | Yes | Ja |
| [CETAS](develop-tables-cetas.md)                     | Yes | Ja |


## <a name="recommendations"></a>Aanbevelingen

Hieronder vindt u essentiële artikelen die de nadruk leggen op specifieke coderings technieken, tips en aanbevelingen voor ontwikkeling:

| Artikel | SQL-pool | SQL on-demand |
| ------- | -------- | ------------- |
| [Opgeslagen procedures](develop-stored-procedures.md)  | Yes                | Nee                      |
| [Labels](develop-label.md)                           | Yes                | Nee                      |
| [Weergaven](develop-views.md)                             | Yes                | Ja                     |
| [Tijdelijke tabellen](develop-tables-temporary.md)       | Yes                | Ja                     |
| [Dynamic SQL](develop-dynamic-sql.md)                 | Yes                | Ja                     |
| [Lussen](develop-loops.md)                         | Yes                | Ja                     |
| [Groeperen op opties](develop-group-by-options.md)       | Yes                | Nee                      |
| [Variabele toewijzing](develop-variable-assignment.md) | Yes                | Ja                     |

## <a name="next-steps"></a>Volgende stappen
Zie [SQL-Groep T-SQL-instructies](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie.

