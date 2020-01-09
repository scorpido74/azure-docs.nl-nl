---
title: Azure-toepassing Insights-gegevens model-telemetrie traceren
description: Application Insights gegevens model voor het traceren van telemetrie
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 6e188039a86f4c655df3098be1d769668dcf3571
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75407143"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetrie traceren: Application Insights gegevens model

Telemetrie traceren (in [Application Insights](../../azure-monitor/app/app-insights-overview.md)) vertegenwoordigt `printf` Style trace-instructies die met tekst worden doorzocht. `Log4Net`, `NLog`en andere vermeldingen op basis van tekst logboek bestanden worden omgezet in exemplaren van dit type. De tracering heeft geen metingen als een uitbreid baarheid.

## <a name="message"></a>Bericht

Traceer bericht.

Maximale lengte: 32768 tekens

## <a name="severity-level"></a>Ernstniveau

Niveau van de ernst van de tracering. De waarde kan `Verbose`, `Information`, `Warning`, `Error``Critical`zijn.

## <a name="custom-properties"></a>Aangepaste eigenschappen

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Volgende stappen

- [Verken .net-traceer Logboeken in Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Verken Java-traceer Logboeken in Application Insights](../../azure-monitor/app/java-trace-logs.md).
- Zie [gegevens model](data-model.md) voor Application Insights typen en gegevens model.
- [Telemetrie van aangepaste tracering schrijven](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Bekijk de [platforms](../../azure-monitor/app/platforms.md) die door Application Insights worden ondersteund.
