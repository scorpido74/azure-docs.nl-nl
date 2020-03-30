---
title: Azure Application Insights-model voor telemetriegegevens van Azure Application Insights
description: Application Insights-gegevensmodel voor uitzonderingstelemetrie
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: fa9bc4efd9549cbcb1d50439989c5dea79d9d60c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671933"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Uitzondering telemetrie: gegevensmodel Application Insights

In [Application Insights](../../azure-monitor/app/app-insights-overview.md)vertegenwoordigt een instantie van Exception een afgehandelde of onverwerkte uitzondering die is opgetreden tijdens de uitvoering van de bewaakte toepassing.

## <a name="problem-id"></a>Probleem-id

Id van waar de uitzondering in code is gegooid. Wordt gebruikt voor het groeperen van uitzonderingen. Typisch een combinatie van uitzonderingstype en een functie van de oproepstack.

Maximale lengte: 1024 tekens

## <a name="severity-level"></a>Ernstniveau

Trace ernst niveau. Waarde kan `Verbose` `Information`worden `Warning` `Error`, `Critical`, , , .

## <a name="exception-details"></a>Details van uitzondering

(Uit te breiden)

## <a name="custom-properties"></a>Aangepaste eigenschappen

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Aangepaste metingen

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Volgende stappen

- Zie [gegevensmodel](data-model.md) voor toepassingsinzichten en gegevensmodel.
- Meer informatie over het [diagnosticeren van uitzonderingen in uw webapps met Application Insights](../../azure-monitor/app/asp-net-exceptions.md).
- Bekijk [platforms](../../azure-monitor/app/platforms.md) die worden ondersteund door Application Insights.
