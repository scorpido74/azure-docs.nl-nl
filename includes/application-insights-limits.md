---
title: bestand opnemen
description: bestand opnemen
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 14e2bd4af2616e9dd33fe8267de132ab6c0f1cfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "69982613"
---
Er zijn enkele beperkingen op het aantal statistieken en gebeurtenissen per toepassing, dat wil zeggen per instrumentatiesleutel. De limieten zijn afhankelijk van de [prijscategorie](https://azure.microsoft.com/pricing/details/application-insights/) die u kiest.

| Resource | Standaardlimiet | Opmerking
| --- | --- | --- |
| Totale hoeveelheid gegevens per dag | 100 GB | U kunt gegevens beperken door een maximum in te stellen. Als u meer gegevens nodig hebt, u de limiet in de portal verhogen, tot 1.000 GB. Voor capaciteiten van meer dan 1.000 AIDataCap@microsoft.comGB stuurt u e-mail naar .
| Beperking | 32.000 evenementen per seconde | De limiet wordt gemeten in een minuut.
| Bewaartijd van gegevens | [30 - 730 dagen](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period)  | Deze resource is voor [Search](../articles/azure-monitor/app/diagnostic-search.md), [Analytics](../articles/azure-monitor/app/analytics.md) en [Metrics Explorer](../articles/azure-monitor/app/metrics-explorer.md).
| Bewaartijd van gedetailleerde resultaten van [beschikbaarheidstests met meerdere stappen](../articles/azure-monitor/app/availability-multistep.md) | 90 dagen | Deze resource biedt gedetailleerde resultaten van elke stap.
| Maximale grootte van telemetrieitem | 64 kB |
| Maximale telemetrie-items per batch | 64 K |
| Naamlengte voor de eigenschappen en meetgegevens | 150 | Zie [typeschema's](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Lengte van de tekenreeks eigenschapswaarde | 8.192  | Zie [typeschema's](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Lengte van berichten voor tracering en uitzonderingen | 32,768  | Zie [typeschema's](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Aantal [beschikbaarheidstests](../articles/azure-monitor/app/monitor-web-app-availability.md) per app | 100 |
| [Profielbewaardoende profilergegevens](../articles/azure-monitor/app/profiler.md) | 5 dagen |
| [Profiler-gegevens](../articles/azure-monitor/app/profiler.md) die per dag worden verzonden | 10 GB |

Zie [Over prijzen en quota voor Application Insights](../articles/azure-monitor/app/pricing.md) voor meer informatie.