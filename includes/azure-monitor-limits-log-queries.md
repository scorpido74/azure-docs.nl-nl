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
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74796179"
---
| Limiet | Beschrijving |
|:---|:---|
| Querytaal | Azure Monitor gebruikt dezelfde [Kusto-query taal](/azure/kusto/query/) als Azure Data Explorer. Zie [Azure monitor taal verschillen in de logboek query](../articles/azure-monitor/log-query/data-explorer-difference.md) voor KQL-taal elementen die niet worden ondersteund in azure monitor. |
| Azure-regio's | Logboek query's kunnen overmatige overhead ondervinden wanneer gegevens Log Analytics werk ruimten in meerdere Azure-regio's. Zie [query limieten](../articles/azure-monitor/log-query/scope.md#query-limits) voor meer informatie. |
| Query's voor meerdere resources | Het maximum aantal Application Insights resources en Log Analytics werk ruimten in één query beperkt tot 100.<br>Query op meerdere resources wordt niet ondersteund in de ontwerp functie voor weer gaven.<br>Er wordt een query voor meerdere resources in logboek waarschuwingen ondersteund in de nieuwe scheduledQueryRules-API.<br>Zie [query's beperken tot meerdere resources](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) voor meer informatie. |
| Query beperking | Een gebruiker is beperkt tot 200 query's per 30 seconden op een wille keurig aantal werk ruimten. Deze limiet is van toepassing op programmatische query's of op query's die worden gestart door visualisatie onderdelen als Azure-Dash boards en de overzichts pagina Log Analytics werk ruimte. |
