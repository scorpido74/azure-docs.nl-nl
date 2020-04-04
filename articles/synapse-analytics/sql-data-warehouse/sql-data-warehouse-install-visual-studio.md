---
title: Visual Studio 2019 installeren
description: Visual Studio en SQL Server Development Tools (SSDT) installeren voor Synapse SQL
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b8e37a15f473e5777fe5412b785a0916bd78f095
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655904"
---
# <a name="getting-started-with-visual-studio-2019"></a>Aan de slag met Visual Studio 2019

Visual Studio **2019** SQL Server Data Tools (SSDT) is één tool waarmee u het volgende doen:

- Toepassingen verbinden, bevragen en ontwikkelen
- Maak gebruik van een objectverkenner om alle objecten in uw gegevensmodel visueel te verkennen, inclusief tabellen, weergaven, opgeslagen procedures en dergelijke.
- DDL-scripts (T-SQL-gegevensdefinitietaal) voor uw objecten genereren
- Ontwikkel uw datawarehouse met behulp van een state-based aanpak met SSDT Database Projects
- Uw databaseproject integreren met bronbeheersystemen zoals Git met Azure Repos
- Continue integratie- en implementatiepijplijnen instellen met automatiseringsservers zoals Azure DevOps

## <a name="install-visual-studio-2019"></a>Visual Studio 2019 installeren

Zie [Visual Studio 2019 downloaden][] om Visual Studio **16.3 en hoger**te downloaden en te installeren. Selecteer tijdens de installatie de werkbelasting voor gegevensopslag en -verwerking. Standalone SSDT-installatie is niet langer nodig in Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Niet-ondersteunde functies in SSDT

Er zijn momenten waarop functiereleases voor Synapse SQL mogelijk geen ondersteuning voor SSDT bevatten. De volgende functies worden momenteel niet ondersteund:

- [Gematerialiseerde weergaven](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Georderde clusterindexen](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#examples--and-)
- [COPY-instructie](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Workloadmanagement](sql-data-warehouse-workload-management.md) - werkbelastinggroepen en classificaties
- [Beveiliging op rijniveau](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
  - Stuur hier een supportticket in of stem [hier](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) om de functie te ondersteunen.
- [Dynamische gegevensmaskering](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
  - Stuur hier een supportticket in of stem [hier](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) om de functie te ondersteunen.
- [Tabellen met beperkingen](sql-data-warehouse-table-constraints.md#table-constraints) worden niet ondersteund. Stel voor deze tabelobjecten de buildactie in op 'Geen'.

## <a name="next-steps"></a>Volgende stappen

Nu u de nieuwste versie van SSDT hebt, bent u klaar om verbinding te [maken met](sql-data-warehouse-query-visual-studio.md) uw SQL-groep.

<!--Other-->

[Visual Studio 2019 downloaden]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
