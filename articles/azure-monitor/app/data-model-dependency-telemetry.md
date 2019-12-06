---
title: Azure Monitor Application Insights afhankelijkheids gegevens model
description: Application Insights gegevens model voor de telemetrie van de afhankelijkheid
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 5021d3b34816159fc78590a5947ddd3a790303ee
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872635"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Telemetrie van afhankelijkheid: Application Insights gegevens model

Telemetrie van afhankelijkheid (in [Application Insights](../../azure-monitor/app/app-insights-overview.md)) vertegenwoordigt een interactie van het bewaakte onderdeel met een extern onderdeel, zoals SQL of een http-eind punt.

## <a name="name"></a>Naam

De naam van de opdracht die is gestart met deze afhankelijkheids aanroep. Waarde voor weinig kardinaliteit. Voor beelden zijn opgeslagen procedure naam en URL-pad sjabloon.

## <a name="id"></a>Id

Id van een exemplaar van een afhankelijkheids aanroep. Wordt gebruikt voor correlatie met de aanvraag voor het telemetrie-item dat overeenkomt met deze afhankelijkheids aanroep. Zie [correlatie](../../azure-monitor/app/correlation.md) pagina voor meer informatie.

## <a name="data"></a>Gegevens

Opdracht gestart door deze afhankelijkheids aanroep. Voor beelden hiervan zijn de SQL-instructie en de HTTP-URL met alle query parameters.

## <a name="type"></a>Type

Naam van type afhankelijkheid. Lage kardinaliteit waarde voor logische groepering van afhankelijkheden en interpretatie van andere velden, zoals opdrachtnaam en resultCode. Voor beelden zijn SQL, Azure Table en HTTP.

## <a name="target"></a>Kiezen

Doel site van een afhankelijkheids aanroep. Voor beelden zijn server naam, hostadres. Zie [correlatie](../../azure-monitor/app/correlation.md) pagina voor meer informatie.

## <a name="duration"></a>Duur

De duur van de aanvraag in de indeling: `DD.HH:MM:SS.MMMMMM`. Moet minder dan `1000` dagen zijn.

## <a name="result-code"></a>Resultaatcode

De resultaat code van een afhankelijkheids aanroep. Voor beelden hiervan zijn de SQL-fout code en de HTTP-status code.

## <a name="success"></a>Geslaagd

Indicatie van een geslaagde of mislukte aanroep.

## <a name="custom-properties"></a>Aangepaste eigenschappen

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Aangepaste metingen

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Volgende stappen

- Het bijhouden van afhankelijkheden instellen voor [.net](../../azure-monitor/app/asp-net-dependencies.md).
- Het bijhouden van afhankelijkheden instellen voor [Java](../../azure-monitor/app/java-agent.md).
- [Aangepaste telemetrie van afhankelijkheid schrijven](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Zie [gegevens model](data-model.md) voor Application Insights typen en gegevens model.
- Bekijk de [platforms](../../azure-monitor/app/platforms.md) die door Application Insights worden ondersteund.
