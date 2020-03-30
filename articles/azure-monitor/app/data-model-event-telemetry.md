---
title: Telemetriegegevensmodel azure-toepassingsinzichten - telemetrie van gebeurtenissen | Microsoft Documenten
description: Toepassingsinsights-gegevensmodel voor gebeurtenistelemetrie
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: bd8b2581f7642f6825aaf0d1b51c8e94d4333d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671877"
---
# <a name="event-telemetry-application-insights-data-model"></a>Gebeurtenistelemetrie: gegevensmodel Application Insights

U gebeurtenistelemetrie-items (in [Application Insights)](../../azure-monitor/app/app-insights-overview.md)maken om een gebeurtenis weer te geven die in uw toepassing is opgetreden. Meestal is het een gebruikersinteractie, zoals knopklik of orderafhandeling. Het kan ook een levenscyclusgebeurtenis voor toepassingen zijn, zoals initialisatie of configuratie-update. 

Semantisch kunnen gebeurtenissen al dan niet worden gecorreleerd aan aanvragen. Echter, indien correct gebruikt, gebeurtenis telemetrie is belangrijker dan verzoeken of sporen. Gebeurtenissen vertegenwoordigen zakelijke telemetrie en moeten een onderwerp zijn van afzonderlijke, minder agressieve [bemonstering.](../../azure-monitor/app/api-filtering-sampling.md)

## <a name="name"></a>Name

Naam van het evenement. Als u een goede groepering en nuttige statistieken wilt toestaan, beperkt u uw toepassing zodat deze een klein aantal afzonderlijke gebeurtenisnamen genereert. Gebruik bijvoorbeeld geen aparte naam voor elk gegenereerdexemplaar van een gebeurtenis.

Maximale lengte: 512 tekens

## <a name="custom-properties"></a>Aangepaste eigenschappen

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Aangepaste metingen

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Volgende stappen

- Zie [gegevensmodel](data-model.md) voor toepassingsinzichten en gegevensmodel.
- [Aangepaste gebeurtenistelemetrie schrijven](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)
- Bekijk [platforms](../../azure-monitor/app/platforms.md) die worden ondersteund door Application Insights.
