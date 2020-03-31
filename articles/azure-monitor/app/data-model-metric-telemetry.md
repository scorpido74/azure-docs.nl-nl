---
title: Gegevensmodel voor metrische telemetrie - Azure Application Insights
description: Toepassingsinsights-gegevensmodel voor metrische telemetrie
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 3e4a1fc3de58b8e65ab9c7a288bdf3eb37e7bae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671967"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Metrische telemetrie: gegevensmodel Application Insights

Er zijn twee soorten metrische telemetrie ondersteund door [Application Insights:](../../azure-monitor/app/app-insights-overview.md)één meting en vooraf geaggregeerde metrische gegevens. Enkele meting is slechts een naam en waarde. Vooraf geaggregeerde statistiek geeft de minimum- en maximumwaarde van de statistiek aan in het aggregatie-interval en de standaarddeviatie ervan.

Vooraf geaggregeerde telemetrie gaat ervan uit dat de aggregatieperiode één minuut was.

Er zijn verschillende bekende metrische namen ondersteund door Application Insights. Deze statistieken geplaatst in de tabel performanceCounters.

Metrische vertegenwoordiger systeem- en procestellers:

| **.NET-naam**             | **Platform agnostische naam** | **REST API-naam** | **Beschrijving**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Werk in uitvoering... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | totale machine-CPU
| `\Memory\Available Bytes`                 | Werk in uitvoering... | [geheugenBeschikbaarBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Toont de hoeveelheid fysiek geheugen, in bytes, beschikbaar voor processen die op de computer worden uitgevoerd. Het wordt berekend door de hoeveelheid ruimte op de zeroed, free en stand-by geheugenlijsten op te tellen. Gratis geheugen is klaar voor gebruik; zeroed memory bestaat uit pagina's geheugen gevuld met nullen om te voorkomen dat latere processen gegevens zien die door een vorig proces worden gebruikt; stand-by geheugen is geheugen dat is verwijderd uit de werkset van een proces (het fysieke geheugen) op weg naar de schijf, maar is nog steeds beschikbaar om te worden teruggeroepen. Zie [Geheugenobject](https://msdn.microsoft.com/library/ms804008.aspx)
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Werk in uitvoering... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | CPU van het proces dat de toepassing host
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Werk in uitvoering... | [procesPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | geheugen dat wordt gebruikt door het proces dat de toepassing host
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Werk in uitvoering... | [processIOBytesPerSeconde](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | i/o-bewerkingen worden uitgevoerd door proces hosting van de toepassing
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Werk in uitvoering... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | aantal aanvragen die per aanvraag worden verwerkt 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Werk in uitvoering... | [uitzonderingenPerSeconde](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | aantal uitzonderingen dat per aanvraag wordt weggegooid
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Werk in uitvoering... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | gemiddelde uitvoeringstijd voor aanvragen
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Werk in uitvoering... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | aantal aanvragen dat wacht op de verwerking in een wachtrij

## <a name="name"></a>Name

Naam van de statistiek die u wilt zien in de Portal Application Insights en de gebruikersinterface. 

## <a name="value"></a>Waarde

Enkele waarde voor meting. Som van individuele metingen voor de aggregatie.

## <a name="count"></a>Count

Metrisch gewicht van de geaggregeerde statistiek. Moet niet worden ingesteld voor een meting.

## <a name="min"></a>Min.

Minimumwaarde van de geaggregeerde statistiek. Moet niet worden ingesteld voor een meting.

## <a name="max"></a>Max.

Maximale waarde van de geaggregeerde statistiek. Moet niet worden ingesteld voor een meting.

## <a name="standard-deviation"></a>Standaardafwijking

Standaarddeviatie van de geaggregeerde statistiek. Moet niet worden ingesteld voor een meting.

## <a name="custom-properties"></a>Aangepaste eigenschappen

Metriek met `CustomPerfCounter` de `true` aangepaste eigenschapset om aan te geven dat de statistiek het prestatiemeterteller van Windows vertegenwoordigt. Deze statistieken die in de tabel performanceCounters zijn geplaatst. Niet in customMetrics. Ook wordt de naam van deze statistiek ontleed om categorie-, teller- en instantienamen te extraheren.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van [Application Insights API voor aangepaste gebeurtenissen en statistieken](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric).
- Zie [gegevensmodel](data-model.md) voor toepassingsinzichten en gegevensmodel.
- Bekijk [platforms](../../azure-monitor/app/platforms.md) die worden ondersteund door Application Insights.
