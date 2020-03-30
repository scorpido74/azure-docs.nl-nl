---
title: Azure Monitor-logboekquery's | Microsoft Documenten
description: Verwijzingen naar bronnen om te leren hoe u logboekquery's schrijven in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2019
ms.openlocfilehash: fc943ac3cf82d22a58d0ba3390ad8d9bbee6a4c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669383"
---
# <a name="azure-monitor-log-queries"></a>Azure Monitor-logboekquery's
Azure Monitor-logboeken zijn gebouwd op Azure Data Explorer en Azure Monitor-logboekquery's gebruiken een versie van dezelfde Kusto-querytaal. De [kusto-querytaaldocumentatie](/azure/kusto/query) bevat alle details voor de taal en moet uw primaire bron zijn voor het schrijven van Azure Monitor-logboekquery's. Op deze pagina vindt u koppelingen naar andere bronnen om te leren hoe u query's schrijven en over verschillen met de Azure Monitor-implementatie van de taal.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Aan de slag

- [Aan de slag met Azure Monitor Log Analytics](get-started-portal.md) is een les voor het schrijven van query's en het werken met resultaten in de Azure-portal.
- [Aan de slag met Azure Monitor-logboekquery's](get-started-queries.md) is een les voor het schrijven van query's met Azure Monitor-logboekgegevens.

## <a name="concepts"></a>Concepten
- [Loggegevens analyseren in Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) geeft een kort overzicht van logboekquery's en beschrijft hoe azure monitor-logboekgegevens zijn gestructureerd.
- [Met het weergeven en analyseren van logboekgegevens in Azure Monitor](../../azure-monitor/log-query/portals.md) worden de portalen uitgelegd waar u logboekquery's maakt en uitvoert.

## <a name="reference"></a>Naslaginformatie

- [Querytaalverwijzing](/azure/kusto/query) is de volledige taalverwijzing voor de Kusto-querytaal.
- [Taalverschillen in azure monitor-logboekquery's](data-explorer-difference.md) beschrijven verschillen tussen versies van de Kusto-querytaal.
- [Standaardeigenschappen in Azure Monitor-logboekrecords](../../azure-monitor/platform/log-standard-properties.md) beschrijven eigenschappen die standaard zijn voor alle Azure Monitor-logboekgegevens.
- [Met query's](../../azure-monitor/log-query/cross-workspace-query.md) voor logboeken met meerdere resources in Azure Monitor wordt beschreven hoe u logboekquery's schrijft met gegevens uit meerdere Log Analytics-werkruimten en Application Insights-toepassingen.


## <a name="examples"></a>Voorbeelden

- [Voorbeelden van azure monitor-logboekquery's](examples.md) bevatten voorbeeldquery's met azure monitor-logboekgegevens.



## <a name="lessons"></a>Lessen

- [Met tekenreeksen werken in Azure Monitor-logboekquery's](string-operations.md) wordt beschreven hoe u met tekenreeksgegevens werken.
- [In het werken met datumtijdwaarden in Azure Monitor-logboekquery's](datetime-operations.md) wordt beschreven hoe u met datum- en tijdgegevens werken. 
- [Aggregaties in Azure Monitor-logboekquery's](aggregations.md) en [geavanceerde aggregaties in Azure Monitor-logboekquery's](advanced-aggregations.md) beschrijven hoe u gegevens samenvoegen en samenvatten.
- [Joins in Azure Monitor-logboekquery's](joins.md) beschrijven hoe u gegevens uit meerdere tabellen aansluiten.
- [In het werken met JSON- en gegevensstructuren in Azure Monitor-logboekquery's](json-data-structures.md) wordt beschreven hoe u json-gegevens ontleden.
- [Het schrijven van geavanceerde logboekquery's in Azure Monitor](advanced-query-writing.md) beschrijft strategieën voor het maken van complexe query's en het hergebruik van code.
- [Bij het maken van grafieken en diagrammen uit azure monitor-logboekquery's](charts.md) wordt beschreven hoe u gegevens uit een logboekquery visualiseert.

## <a name="cheatsheets"></a>Cheatsheets

-  [SQL naar Azure Monitor-logboekquery](sql-cheatsheet.md) helpt gebruikers die al bekend zijn met SQL.
-  [Splunk to Azure Monitor log query](splunk-cheatsheet.md) helpt gebruikers die al bekend zijn met Splunk.
 
## <a name="next-steps"></a>Volgende stappen

- Toegang tot de volledige [referentiedocumentatie voor de Kusto-querytaal](/azure/kusto/query/).
