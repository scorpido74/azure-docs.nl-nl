---
title: Telemetrie-gegevens model van Azure-toepassing Insights-telemetrie-uitzonde ring | Microsoft Docs
description: Application Insights gegevens model voor uitzonderings-telemetrie
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 0ba1c94ee8dc78b937d650cff32e1518a7ca5a12
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677426"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetrie van uitzonde ring: Application Insights gegevens model

In [Application Insights](../../azure-monitor/app/app-insights-overview.md)vertegenwoordigt een exemplaar van uitzonde ring een verwerkte of onverwerkte uitzonde ring die is opgetreden tijdens de uitvoering van de bewaakte toepassing.

## <a name="problem-id"></a>Probleem-id

De id van de plaats waar de uitzonde ring is opgetreden in de code. Wordt gebruikt voor de groepering van uitzonde ringen. Meestal een combi natie van het uitzonderings type en een functie van de aanroep stack.

Maximale lengte: 1024 tekens

## <a name="severity-level"></a>Ernstniveau

Niveau van de ernst van de tracering. De waarde kan `Verbose`, `Information`, `Warning`, `Error` `Critical` zijn.

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
