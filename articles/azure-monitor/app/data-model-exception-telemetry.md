---
title: Telemetrie-gegevens model Azure-toepassing Insights-uitzonde ring
description: Application Insights gegevens model voor uitzonderings-telemetrie
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: fa9bc4efd9549cbcb1d50439989c5dea79d9d60c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77671933"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetrie van uitzonde ring: Application Insights gegevens model

In [Application Insights](../../azure-monitor/app/app-insights-overview.md)vertegenwoordigt een exemplaar van uitzonde ring een verwerkte of onverwerkte uitzonde ring die is opgetreden tijdens de uitvoering van de bewaakte toepassing.

## <a name="problem-id"></a>Probleem-id

De id van de plaats waar de uitzonde ring is opgetreden in de code. Wordt gebruikt voor de groepering van uitzonde ringen. Meestal een combi natie van het uitzonderings type en een functie van de aanroep stack.

Maximale lengte: 1024 tekens

## <a name="severity-level"></a>Ernstniveau

Niveau van de ernst van de tracering. Waarde kan,,, `Verbose` `Information` `Warning` `Error` , `Critical` .

## <a name="exception-details"></a>Details van uitzondering

(Uitgebreid)

## <a name="custom-properties"></a>Aangepaste eigenschappen

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Aangepaste metingen

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Volgende stappen

- Zie [gegevens model](data-model.md) voor Application Insights typen en gegevens model.
- Meer informatie over het [opsporen van uitzonde ringen in uw web-apps met Application Insights](../../azure-monitor/app/asp-net-exceptions.md).
- Bekijk de [platforms](../../azure-monitor/app/platforms.md) die door Application Insights worden ondersteund.
