---
title: bestand opnemen
description: bestand opnemen
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: c68e58bb18a5dba07855234af07b0a8be767bed0
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2020
ms.locfileid: "75928249"
---
Er zijn enkele limieten voor het aantal metrische gegevens en gebeurtenissen per toepassing, dat wil zeggen, per instrumentatie sleutel. De limieten zijn afhankelijk van de [prijscategorie](https://azure.microsoft.com/pricing/details/application-insights/) die u kiest.

| Bron | Standaardlimiet | Opmerking
| --- | --- | --- |
| Totale hoeveelheid gegevens per dag | 100 GB | U kunt gegevens beperken door een maximum in te stellen. Als u meer gegevens nodig hebt, kunt u de limiet in de portal verhogen tot 1.000 GB. Voor capaciteit groter dan 1.000 GB stuurt u een e-mail naar AIDataCap@microsoft.com.
| Beperking | 32.000 gebeurtenissen per seconde | De limiet wordt gemeten in een minuut.
| Gegevens bewaren | 90 dagen | Deze resource is voor [Search](../articles/azure-monitor/app/diagnostic-search.md), [Analytics](../articles/azure-monitor/app/analytics.md) en [Metrics Explorer](../articles/azure-monitor/app/metrics-explorer.md).
| Bewaartijd van gedetailleerde resultaten van [beschikbaarheidstests met meerdere stappen](../articles/azure-monitor/app/availability-multistep.md) | 90 dagen | Deze resource biedt gedetailleerde resultaten van elke stap.
| Maximale gebeurtenis grootte | 64.000.000 bytes |
| Naamlengte voor de eigenschappen en meetgegevens | 150 | Zie [schema typen](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Lengte van de tekenreeks eigenschapswaarde | 8\.192 | Zie [schema typen](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Lengte van berichten voor tracering en uitzonderingen | 32.768  | Zie [schema typen](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Aantal [beschikbaarheidstests](../articles/azure-monitor/app/monitor-web-app-availability.md) per app | 100 |
| [Profiler](../articles/azure-monitor/app/profiler.md) gegevens retentie | 5 dagen |
| Gegevens van [Profiler](../articles/azure-monitor/app/profiler.md) per dag verzonden | 10 GB |

Zie [Over prijzen en quota voor Application Insights](../articles/azure-monitor/app/pricing.md) voor meer informatie.
