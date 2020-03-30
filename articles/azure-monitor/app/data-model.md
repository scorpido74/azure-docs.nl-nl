---
title: Telemetriegegevensmodel Azure Application Insights | Microsoft Documenten
description: Overzicht van het gegevensmodel van Application Insights
services: application-insights
documentationcenter: .net
manager: carmonm
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/14/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 94013a12e1cf48a8007fce2547c200d82a657b71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671831"
---
# <a name="application-insights-telemetry-data-model"></a>Telemetriegegevensmodel Application Insights

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) stuurt telemetrie van uw webtoepassing naar de Azure-portal, zodat u de prestaties en het gebruik van uw toepassing analyseren. Het telemetriemodel is gestandaardiseerd zodat het mogelijk is om platform- en taalonafhankelijke monitoring te creëren. 

Gegevens die worden verzameld door Application Insights-modellen dit typische toepassingsuitvoeringspatroon:

![Application Insights-toepassingsmodel](./media/data-model/application-insights-data-model.png)

De volgende typen telemetrie worden gebruikt om de uitvoering van uw app te controleren. De volgende drie typen worden meestal automatisch verzameld door de Application Insights SDK vanuit het webtoepassingsframework:

* [**Verzoek**](data-model-request-telemetry.md) - Gegenereerd om een aanvraag te registreren die door uw app is ontvangen. De Web SDK Application Insights genereert bijvoorbeeld automatisch een telemetrieitem Verzoek voor elk HTTP-verzoek dat uw web-app ontvangt. 

    Een **bewerking** is de threads van uitvoering die een aanvraag verwerkt. U ook [code schrijven](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) om andere typen bewerkingen te controleren, zoals een 'wake up' in een webtaak of -functie die periodiek gegevens verwerkt.  Elke bewerking heeft een ID. Deze ID die kan worden gebruikt [om](../../azure-monitor/app/correlation.md) alle telemetrie die wordt gegenereerd terwijl uw app het verzoek verwerkt, te groeperen. Elke bewerking slaagt of mislukt en heeft een tijdsduur.
* [**Uitzondering**](data-model-exception-telemetry.md) : het is meestal een uitzondering waardoor een bewerking mislukt.
* [**Afhankelijkheid**](data-model-dependency-telemetry.md) - Vertegenwoordigt een oproep van uw app naar een externe service of opslag, zoals een REST API of SQL. In ASP.NET worden afhankelijkheidsoproepen naar `System.Data`SQL gedefinieerd door . Oproepen naar HTTP-eindpunten `System.Net`worden gedefinieerd door . 

Application Insights biedt drie extra gegevenstypen voor aangepaste telemetrie:

* [Trace](data-model-trace-telemetry.md) - direct gebruikt, of via een adapter om diagnostische logging te implementeren `Log4Net` `System.Diagnostics`met behulp van een instrumentatiekader dat u bekend is, zoals of .
* [Gebeurtenis](data-model-event-telemetry.md) - meestal gebruikt om gebruikersinteractie met uw service vast te leggen, om gebruikspatronen te analyseren.
* [Metric](data-model-metric-telemetry.md) - gebruikt om periodieke scalaire metingen te rapporteren.

Elk telemetrie-item kan de [contextinformatie](data-model-context.md) definiëren, zoals de toepassingsversie of de gebruikerssessie-id. Context is een set sterk getypte velden die bepaalde scenario's deblokkeren. Wanneer de toepassingsversie goed is geïnitialiseerd, kan Application Insights nieuwe patronen in toepassingsgedrag detecteren die zijn gecorreleerd met herplaatsing. Sessie-id kan worden gebruikt om de storing of een probleemimpact op gebruikers te berekenen. Het berekenen van het aantal waarden voor sessie-id's voor bepaalde mislukte afhankelijkheid, fouttracering of kritieke uitzondering geeft een goed inzicht in de impact.

Het telemetriemodel van Application Insights definieert een manier om telemetrie te [correleren met](../../azure-monitor/app/correlation.md) de werking waarvan het deel uitmaakt. Een aanvraag kan bijvoorbeeld een SQL Database-aanroepen en opgenomen diagnostische gegevens maken. U de correlatiecontext instellen voor die telemetrie-items die deze koppelen aan de telemetrie van het verzoek.

## <a name="schema-improvements"></a>Schemaverbeteringen

Application Insights data model is een eenvoudige en eenvoudige maar krachtige manier om uw toepassing telemetrie model. We streven ernaar om het model eenvoudig en slank te houden om essentiële scenario's te ondersteunen en het schema uit te breiden voor geavanceerd gebruik.

Als u gegevensmodel- of schemaproblemen en suggesties wilt melden, gebruikt u de GitHub [ApplicationInsights-Home-opslagplaats.](https://github.com/Microsoft/ApplicationInsights-Home/issues)

## <a name="next-steps"></a>Volgende stappen

- [Aangepaste telemetrie schrijven](../../azure-monitor/app/api-custom-events-metrics.md)
- Meer informatie over het [uitbreiden en filteren van telemetrie](../../azure-monitor/app/api-filtering-sampling.md).
- Gebruik [sampling](../../azure-monitor/app/sampling.md) om de hoeveelheid telemetrie op basis van het gegevensmodel te minimaliseren.
- Bekijk [platforms](../../azure-monitor/app/platforms.md) die worden ondersteund door Application Insights.
