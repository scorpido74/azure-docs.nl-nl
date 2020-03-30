---
title: Event tellers in Application Insights | Microsoft Documenten
description: Monitor systeem en aangepaste .NET/.NET Core EventCounters in Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 2094c012e86131073fc66be4f2ac2fb2e81ef4c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663586"
---
# <a name="eventcounters-introduction"></a>Introductie eventcounters

`EventCounter`is .NET/.NET Core-mechanisme voor het publiceren en consumeren van tellers of statistieken. [Dit](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) document geeft `EventCounters` een overzicht van en voorbeelden over hoe ze te publiceren en te consumeren. EventCounters worden ondersteund in alle OS-platforms - Windows, Linux en macOS. Het kan worden gezien als een cross-platform equivalent voor de [PerformanceCounters](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) die alleen wordt ondersteund in Windows-systemen.

Hoewel gebruikers elke `EventCounters` aangepaste kunnen publiceren om aan hun behoeften te voldoen, publiceert de .NET Core 3.0 runtime standaard een set van deze tellers. Het document doorloopt de stappen die `EventCounters` nodig zijn om te verzamelen en weer te geven (systeem gedefinieerd of door de gebruiker gedefinieerd) in Azure Application Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>Toepassingsinzichten gebruiken om eventcounters te verzamelen

Application Insights `EventCounters` ondersteunt `EventCounterCollectionModule`het verzamelen met zijn , die deel uitmaakt van de onlangs vrijgegeven nuget pakket [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule`wordt automatisch ingeschakeld wanneer u [AspNetCore](asp-net-core.md) of [WorkerService gebruikt.](worker-service.md) `EventCounterCollectionModule`verzamelt tellers met een niet-configureerbare verzamelfrequentie van 60 seconden. Er zijn geen speciale machtigingen vereist om EventCounters te verzamelen.

## <a name="default-counters-collected"></a>Standaardtellers verzameld

Voor apps die in .NET Core 3.0 worden uitgevoerd, worden de volgende tellers automatisch verzameld door de SDK. De naam van de tellers is van het formulier "Categorie| Counter".

|Categorie | Prestatiemeteritem|
|---------------|-------|
|`System.Runtime` | `cpu-usage` |
|`System.Runtime` | `working-set` |
|`System.Runtime` | `gc-heap-size` |
|`System.Runtime` | `gen-0-gc-count` |
|`System.Runtime` | `gen-1-gc-count` |
|`System.Runtime` | `gen-2-gc-count` |
|`System.Runtime` | `time-in-gc` |
|`System.Runtime` | `gen-0-size` |
|`System.Runtime` | `gen-1-size` |
|`System.Runtime` | `gen-2-size` |
|`System.Runtime` | `loh-size` |
|`System.Runtime` | `alloc-rate` |
|`System.Runtime` | `assembly-count` |
|`System.Runtime` | `exception-count` |
|`System.Runtime` | `threadpool-thread-count` |
|`System.Runtime` | `monitor-lock-contention-count` |
|`System.Runtime` | `threadpool-queue-length` |
|`System.Runtime` | `threadpool-completed-items-count` |
|`System.Runtime` | `active-timer-count` |
|`Microsoft.AspNetCore.Hosting` | `requests-per-second` |
|`Microsoft.AspNetCore.Hosting` | `total-requests` |
|`Microsoft.AspNetCore.Hosting` | `current-requests` |
|`Microsoft.AspNetCore.Hosting` | `failed-requests` |

> [!NOTE]
> Tellers van categorie Microsoft.AspNetCore.Hosting worden alleen toegevoegd in ASP.NET Core Applications.

## <a name="customizing-counters-to-be-collected"></a>Tellers aanpassen die moeten worden verzameld

In het volgende voorbeeld ziet u hoe u tellers toevoegt/verwijdert. Deze aanpassing zou worden `ConfigureServices` gedaan in de methode van uw toepassing `AddApplicationInsightsTelemetry()` na `AddApplicationInsightsWorkerService()`Application Insights telemetrie verzameling is ingeschakeld met behulp van een of . Hieronder volgt een voorbeeldcode van een ASP.NET Core-toepassing. Raadpleeg [dit](worker-service.md#configuring-or-removing-default-telemetrymodules) document voor andere soorten toepassingen.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows several customizations done to EventCounterCollectionModule.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

        // The following code removes EventCounterCollectionModule to disable the module completely.
        var eventCounterModule = services.FirstOrDefault<ServiceDescriptor>
                    (t => t.ImplementationType == typeof(EventCounterCollectionModule));
        if (eventCounterModule != null)
        {
            services.Remove(eventCounterModule);
        }
    }
```

## <a name="event-counters-in-metric-explorer"></a>Gebeurtenistellers in Metric Explorer

Als u EventCounter-statistieken wilt weergeven in [Metric Explorer,](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)selecteert u de bron Application Insights en kiest u op logboeken gebaseerde metrische gegevens als metrische naamruimte. Vervolgens worden EventCounter-statistieken weergegeven onder Aangepaste categorie.

> [!div class="mx-imgBorder"]
> ![Gebeurtenistellers gerapporteerd in Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Gebeurtenistellers in Analytics

U ook rapporten van gebeurtenisgegevensrapporten zoeken en weergeven in [Analytics](../../azure-monitor/app/analytics.md)in de tabel **customMetrics.**

Voer bijvoorbeeld de volgende query uit om te zien welke tellers worden verzameld en beschikbaar zijn voor query's:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Gebeurtenistellers gerapporteerd in Application Insights](./media/event-counters/analytics-event-counters.png)

Voer de volgende query uit om `ThreadPool Completed Work Item Count`een grafiek van een specifieke teller (bijvoorbeeld: ) in de afgelopen periode uit te voeren.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Chat van één teller in Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Net als andere telemetrie heeft `cloud_RoleInstance` **customMetrics** ook een kolom die de identiteit aangeeft van het hostserverexemplaar waarop uw app wordt uitgevoerd. De bovenstaande query toont de tegenwaarde per instantie en kan worden gebruikt om de prestaties van verschillende serverexemplaren te vergelijken.

## <a name="alerts"></a>Waarschuwingen
Net als andere statistieken u [een waarschuwing instellen](../../azure-monitor/app/alerts.md) om u te waarschuwen als een gebeurtenisteller buiten een door u opgegeven limiet valt. Open het deelvenster Waarschuwingen en klik op Waarschuwing toevoegen.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Kan ik EventCounters zien in Live Metrics?

Live Metrics worden vanaf vandaag niet meer weergegeven in EventCounters. Gebruik Metric Explorer of Analytics om de telemetrie te bekijken.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>Welke platforms kan ik de standaardlijst van .NET Core 3.0-tellers zien?

EventCounter vereist geen speciale machtigingen en wordt ondersteund op alle platforms .NET Core 3.0 wordt ondersteund. Dit omvat:

* **Besturingssysteem**: Windows, Linux of macOS.
* **Hostingmethode**: In proces of uit proces.
* **Implementatiemethode**: Framework afhankelijk of op zichzelf staand.
* **Webserver**: IIS (Internet Information Server) of Torenvalk.
* **Hostingplatform**: de Web Apps-functie van Azure App Service, Azure VM, Docker, Azure Kubernetes Service (AKS), enzovoort.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Ik heb Application Insights ingeschakeld vanuit Azure Web App Portal. Maar ik kan eventcounters niet zien.

 [Application Insights-extensie](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) voor ASP.NET Core ondersteunt deze functie nog niet. Dit document wordt bijgewerkt wanneer deze functie wordt ondersteund.

## <a name="next-steps"></a><a name="next"></a>Volgende stappen

* [Afhankelijkheidstracking](../../azure-monitor/app/asp-net-dependencies.md)
