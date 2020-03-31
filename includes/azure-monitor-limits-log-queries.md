---
title: bestand opnemen
description: bestand opnemen
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f2092753ab054a639e208aab4a6b7317c1bd5edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74796179"
---
| Limiet | Beschrijving |
|:---|:---|
| Querytaal | Azure Monitor gebruikt dezelfde [Kusto-querytaal](/azure/kusto/query/) als Azure Data Explorer. Zie [taalverschillen in azure monitor-logboekquery's](../articles/azure-monitor/log-query/data-explorer-difference.md) voor KQL-taalelementen die niet worden ondersteund in Azure Monitor. |
| Azure-regio's | Logquery's kunnen overmatige overhead ervaren wanneer gegevens log analytics-werkruimten in meerdere Azure-regio's omvatten. Zie [Querylimieten](../articles/azure-monitor/log-query/scope.md#query-limits) voor meer informatie. |
| Bronquery's voor kruisbronnen | Maximaal aantal Resources voor Application Insights en Log Analytics-werkruimten in één query die beperkt is tot 100.<br>Query met kruisresources wordt niet ondersteund in View Designer.<br>Logboekwaarschuwingen voor cross-resourcequery's worden ondersteund in de nieuwe api voor geplandeQueryregels.<br>Zie Querylimieten voor [kruisresources](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) voor meer informatie. |
| Querybeperking | Een gebruiker is beperkt tot 200 query's per 30 seconden op een willekeurig aantal werkruimten. Deze limiet is van toepassing op programmatische query's of op query's die zijn gestart met visualisatieonderdelen zoals Azure-dashboards en de overzichtspagina van logboekanalysewerkruimte. |
