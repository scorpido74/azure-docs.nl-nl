---
title: Azure Application Insights Data Model - Telemetrie traceren
description: Application Insights-gegevensmodel voor tracetelemetrie
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 31958b26cdb8a7897cf0051af6600014c07949fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671950"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Trace telemetrie: datamodel Application Insights

Trace telemetrie (in `printf` [Application Insights)](../../azure-monitor/app/app-insights-overview.md)vertegenwoordigt stijltraceringsinstructies die door tekst zijn doorzocht. `Log4Net`, `NLog`en andere op tekst gebaseerde logboekbestandsvermeldingen worden vertaald naar exemplaren van dit type. Het spoor heeft geen metingen als een uitbreidbaarheid.

## <a name="message"></a>Bericht

Trace bericht.

Maximale lengte: 32768 tekens

## <a name="severity-level"></a>Ernstniveau

Trace ernst niveau. Waarde kan `Verbose` `Information`worden `Warning` `Error`, `Critical`, , , .

## <a name="custom-properties"></a>Aangepaste eigenschappen

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Volgende stappen

- [Verken .NET trace logs in Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Ontdek Java trace logs in Application Insights.](../../azure-monitor/app/java-trace-logs.md)
- Zie [gegevensmodel](data-model.md) voor toepassingsinzichten en gegevensmodel.
- [Aangepaste tracetelemetrie schrijven](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Bekijk [platforms](../../azure-monitor/app/platforms.md) die worden ondersteund door Application Insights.
