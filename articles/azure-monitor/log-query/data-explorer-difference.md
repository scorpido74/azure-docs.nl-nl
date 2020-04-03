---
title: Taalverschillen in azure monitor-logboekquery 's | Microsoft Documenten
description: Referentiegegevens voor Kusto-querytaal die wordt gebruikt door Azure Monitor. Bevat extra elementen die specifiek zijn voor Azure Monitor en elementen die niet worden ondersteund in azure monitor-logboekquery's.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/01/2020
ms.openlocfilehash: 265179909c8ae4a6fa630b835bc9993f042d6460
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585700"
---
# <a name="azure-monitor-log-query-language-differences"></a>Taalverschillen in Azure Monitor-logboekquery's

Hoewel [logboeken in Azure Monitor](log-query-overview.md) zijn gebouwd op Azure Data [Explorer](/azure/data-explorer) en dezelfde [Kusto-querytaal](/azure/kusto/query)worden gebruikt, heeft de versie van de taal wel enkele verschillen. In dit artikel worden elementen geïdentificeerd die verschillen tussen de versie van de taal die wordt gebruikt voor Gegevensverkenner en de versie die wordt gebruikt voor Azure Monitor-logboekquery's.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>KQL-elementen worden niet ondersteund in Azure Monitor
In de volgende secties worden elementen van de Kusto-querytaal beschreven die niet worden ondersteund door Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Instructies die niet worden ondersteund in Azure Monitor

* [Alias](/azure/kusto/query/aliasstatement)
* [Queryparameters](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Functies die niet worden ondersteund in Azure Monitor

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal.](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operators worden niet ondersteund in Azure Monitor

* [Deelnemen aan clusteroverschrijden](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Plug-ins die niet worden ondersteund in Azure Monitor

* [Python-plug-in](/azure/kusto/query/pythonplugin)
* [sql_request-plug-in](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Extra operatoren in Azure Monitor
De volgende operatoren ondersteunen specifieke Azure Monitor-functies en zijn niet beschikbaar buiten Azure Monitor.

* [app()](app-expression.md)
* [werkruimte()](workspace-expression.md)

## <a name="next-steps"></a>Volgende stappen

- Hier worden verwijzingen naar verschillende [bronnen voor het schrijven van Azure Monitor-logboekquery's](query-language.md).
- Toegang tot de volledige [referentiedocumentatie voor Kusto-querytaal](/azure/kusto/query/).
