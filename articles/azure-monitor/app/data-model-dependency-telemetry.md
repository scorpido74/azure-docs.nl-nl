---
title: Afhankelijkheidsmodel voor Azure Monitor-toepassingsinzichten
description: Toepassingsinsights-gegevensmodel voor telemetrie van afhankelijkheid
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: ba0d848904d1ba885dc53e2941953d8dfb4864cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671916"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Afhankelijkheidtelemetrie: gegevensmodel Application Insights

Dependency Telemetry (in [Application Insights)](../../azure-monitor/app/app-insights-overview.md)vertegenwoordigt een interactie van de bewaakte component met een externe component zoals SQL of een HTTP-eindpunt.

## <a name="name"></a>Name

Naam van de opdracht die met deze afhankelijkheidsoproep is gestart. Lage kardinaliteitswaarde. Voorbeelden hiervan zijn opgeslagen procedurenaam en URL-padsjabloon.

## <a name="id"></a>Id

Id van een afhankelijkheidsoproepinstantie. Wordt gebruikt voor correlatie met het item request telemetrie dat overeenkomt met deze afhankelijkheidsaanroep. Zie [correlatiepagina voor](../../azure-monitor/app/correlation.md) meer informatie.

## <a name="data"></a>Gegevens

Opdracht geïnitieerd door deze afhankelijkheidsoproep. Voorbeelden zijn SQL-instructie en HTTP-URL met alle queryparameters.

## <a name="type"></a>Type

Typenaam afhankelijkheid. Lage kardinaliteitswaarde voor logische groepering van afhankelijkheden en interpretatie van andere velden zoals commandName en resultCode. Voorbeelden zijn SQL, Azure-tabel en HTTP.

## <a name="target"></a>Doel

Doelsite van een afhankelijkheidsoproep. Voorbeelden zijn servernaam, hostadres. Zie [correlatiepagina voor](../../azure-monitor/app/correlation.md) meer informatie.

## <a name="duration"></a>Duur

Aanvraagduur in `DD.HH:MM:SS.MMMMMM`notatie: . Het moet `1000` minder dan dagen zijn.

## <a name="result-code"></a>Resultaatcode

Resultaatcode van een afhankelijkheidsoproep. Voorbeelden zijn SQL-foutcode en HTTP-statuscode.

## <a name="success"></a>Geslaagd

Indicatie van een geslaagde of mislukte oproep.

## <a name="custom-properties"></a>Aangepaste eigenschappen

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Aangepaste metingen

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Volgende stappen

- Afhankelijkheidstracking instellen voor [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Afhankelijkheidstracking instellen voor [Java](../../azure-monitor/app/java-agent.md).
- [Aangepaste afhankelijkheidstelemetrie schrijven](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Zie [gegevensmodel](data-model.md) voor toepassingsinzichten en gegevensmodel.
- Bekijk [platforms](../../azure-monitor/app/platforms.md) die worden ondersteund door Application Insights.
