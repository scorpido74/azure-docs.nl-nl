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
ms.openlocfilehash: f007cf0d46d6cbee39a950b9784bbc9bde702ff5
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "69657760"
---
| Limiet | Description |
|:---|:---|
| Querytaal | Azure Monitor gebruikt dezelfde [Kusto-query taal](/azure/kusto/query/) als Azure Data Explorer. Zie [Azure monitor taal verschillen in de logboek query](../articles/azure-monitor/log-query/data-explorer-difference.md) voor KQL-taal elementen die niet worden ondersteund in azure monitor. |
| Azure-regio's | Logboek query's kunnen overmatige overhead ondervinden wanneer gegevens Log Analytics werk ruimten in meerdere Azure-regio's. Zie [query limieten](../articles/azure-monitor/log-query/scope.md#query-limits) voor meer informatie. |
| Query's voor meerdere resources | Het maximum aantal Application Insights resources en Log Analytics werk ruimten in één query beperkt tot 100.<br>Query op meerdere resources wordt niet ondersteund in de ontwerp functie voor weer gaven.<br>Er wordt een query voor meerdere resources in logboek waarschuwingen ondersteund in de nieuwe scheduledQueryRules-API.<br>Zie [query's beperken tot meerdere resources](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) voor meer informatie. |