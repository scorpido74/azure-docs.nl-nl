---
title: Verschillen in de query taal van Azure Monitor-logboek | Microsoft Docs
description: Naslag informatie voor de Kusto-query taal die wordt gebruikt door Azure Monitor. Bevat extra elementen die specifiek zijn voor Azure Monitor en elementen die niet worden ondersteund in Azure Monitor-logboek query's.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: b4601968a318388086a60ef98e4359ae01f652ed
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77662099"
---
# <a name="azure-monitor-log-query-language-differences"></a>Verschillen in de query taal van Azure Monitor-logboek

Hoewel [Logboeken in azure monitor](log-query-overview.md) zijn gebouwd op [Azure Data Explorer](/azure/data-explorer) en dezelfde [Kusto-query taal](/azure/kusto/query)gebruikt, heeft de versie van de taal een aantal verschillen. In dit artikel worden elementen geïdentificeerd die verschillen van de versie van de taal die wordt gebruikt voor Data Explorer en de versie die wordt gebruikt voor Azure Monitor logboek query's.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>KQL-elementen worden niet ondersteund in Azure Monitor
In de volgende secties worden elementen beschreven van de Kusto-query taal die niet door Azure Monitor worden ondersteund.

### <a name="statements-not-supported-in-azure-monitor"></a>Instructies worden niet ondersteund in Azure Monitor

* [Toe](/azure/kusto/query/aliasstatement)
* [Query parameters](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Functies die niet worden ondersteund in Azure Monitor

* [cluster ()](/azure/kusto/query/clusterfunction)
* [cursor_after ()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at ()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current (), current_cursor ()](/azure/kusto/query/cursorcurrent)
* [Data Base ()](/azure/kusto/query/databasefunction)
* [current_principal ()](/azure/kusto/query/current-principalfunction)
* [extent_id ()](/azure/kusto/query/extentidfunction)
* [extent_tags ()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Opera tors worden niet ondersteund in Azure Monitor

* [Cross-cluster-koppeling](/azure/kusto/query/joincrosscluster)
* [externaldata-operator](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-azure-monitor"></a>Invoeg toepassingen die niet worden ondersteund in Azure Monitor

* [Python-invoeg toepassing](/azure/kusto/query/pythonplugin)
* [sql_request-invoeg toepassing](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Extra Opera tors in Azure Monitor
De volgende Opera tors ondersteunen specifieke Azure Monitor functies en zijn niet beschikbaar buiten Azure Monitor.

* [app ()](app-expression.md)
* [werk ruimte ()](workspace-expression.md)

## <a name="next-steps"></a>Volgende stappen

- Verwijzingen naar verschillende [bronnen ophalen voor het schrijven van Azure monitor logboek query's](query-language.md).
- Open de volledige [referentie documentatie voor de Kusto-query taal](/azure/kusto/query/).
