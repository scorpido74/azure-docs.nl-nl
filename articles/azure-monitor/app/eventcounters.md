---
title: Gebeurtenis tellers in Application Insights | Microsoft Docs
description: Bewaak systeem-en aangepaste .NET/.NET core-EventCounters in Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: e1037766587f58a30c20f614726e1241c16e5a16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187092"
---
# <a name="eventcounters-introduction"></a>EventCounters-Inleiding

`EventCounter`is .NET/.NET core-mechanisme voor het publiceren en gebruiken van tellers of statistieken. [Dit](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) document bevat een overzicht van `EventCounters` en voor beelden voor het publiceren en gebruiken van deze. EventCounters worden ondersteund in alle OS-platformen-Windows, Linux en macOS. Het kan worden beschouwd als een platform dat gelijkwaardig is aan de [Performance Counters](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) die alleen wordt ondersteund in Windows-systemen.

Hoewel gebruikers aangepaste `EventCounters` kunnen publiceren om aan hun behoeften te voldoen, publiceert de .net Core 3,0-runtime standaard een set van deze prestatie meter items. Het document doorloopt de stappen die nodig zijn voor het verzamelen `EventCounters` en weer geven (door het systeem gedefinieerde of door de gebruiker gedefinieerde definitie) in azure-toepassing Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>EventCounters verzamelen met behulp van Application Insights

Application Insights ondersteunt het `EventCounters` verzamelen met `EventCounterCollectionModule`de, die deel uitmaakt van het zojuist gepubliceerde Nuget-pakket [micro soft. ApplicationInsights. EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule`wordt automatisch ingeschakeld wanneer [AspNetCore](asp-net-core.md) of [WorkerService](worker-service.md)wordt gebruikt. `EventCounterCollectionModule`verzamelt tellers met een niet-Configureer bare verzamelings frequentie van 60 seconden. Er zijn geen speciale machtigingen vereist voor het verzamelen van EventCounters.

## <a name="default-counters-collected"></a>Verzamelde standaard items

Voor apps die worden uitgevoerd in .NET Core 3,0 worden de volgende prestatie meter items automatisch verzameld door de SDK. De naam van de prestatie meter items heeft de indeling ' Category | Teller ".

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
> Tellers van de categorie micro soft. AspNetCore. hosting worden alleen toegevoegd in ASP.NET Core toepassingen.

## <a name="customizing-counters-to-be-collected"></a>Te verzamelen items aanpassen

In het volgende voor beeld ziet u hoe u tellers kunt toevoegen/verwijderen. Deze aanpassing wordt uitgevoerd in de `ConfigureServices` methode van uw toepassing nadat Application Insights telemetrie-verzameling is ingeschakeld met `AddApplicationInsightsTelemetry()` ofwel `AddApplicationInsightsWorkerService()`of. Hieronder volgt een voorbeeld code van een ASP.NET Core-toepassing. Raadpleeg [Dit](worker-service.md#configuring-or-removing-default-telemetrymodules) document voor een ander type toepassingen.

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

## <a name="event-counters-in-metric-explorer"></a>Gebeurtenis tellers in metrische Explorer

Als u metrische gegevens van Event Counter wilt weer geven in de [metrische Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), selecteert u Application Insights resource en kiest u metrische gegevens op basis van een logboek als metrische naam ruimte. Vervolgens worden event Counter-metrische gegevens weer gegeven onder aangepaste categorie.

> [!div class="mx-imgBorder"]
> ![Gebeurtenis tellers die zijn gerapporteerd in Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Gebeurtenis tellers in Analytics

U kunt ook rapporten over gebeurtenis tellers in [Analytics](../../azure-monitor/app/analytics.md)zoeken en weer geven in de tabel **customMetrics** .

Voer bijvoorbeeld de volgende query uit om te zien welke tellers worden verzameld en beschikbaar zijn voor het uitvoeren van query's:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Gebeurtenis tellers die zijn gerapporteerd in Application Insights](./media/event-counters/analytics-event-counters.png)

Voer de volgende query uit om een grafiek van een specifieke teller `ThreadPool Completed Work Item Count`te verkrijgen (bijvoorbeeld:) in de recente periode.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Chatten van een enkele teller in Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Net als bij andere telemetrie heeft **customMetrics** ook `cloud_RoleInstance` een kolom die de identiteit aangeeft van het exemplaar van de hostserver waarop uw app wordt uitgevoerd. De bovenstaande query toont de item waarde per exemplaar en kan worden gebruikt om de prestaties van verschillende server instanties te vergelijken.

## <a name="alerts"></a>Waarschuwingen
Net als bij andere metrische gegevens kunt u [een waarschuwing instellen](../../azure-monitor/app/alerts.md) om u te waarschuwen als een gebeurtenis teller buiten een limiet valt die u opgeeft. Open het deel venster waarschuwingen en klik op waarschuwing toevoegen.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Kan ik EventCounters in Live Metrics zien?

Met Live metrische gegevens wordt EventCounters vanaf vandaag niet weer gegeven. Gebruik metrische Explorer of Analytics om de telemetrie weer te geven.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>Op welke platformen kan ik de standaard lijst met .NET Core 3,0-tellers zien?

Event Counter vereist geen speciale machtigingen en wordt ondersteund in alle platformen .NET Core 3,0 wordt ondersteund. Dit omvat:

* **Besturings systeem**: Windows, Linux of macOS.
* **Hosting methode**: in verwerking of out-of-process.
* **Implementatie methode**: Framework-afhankelijk of zelfstandig.
* **Webserver**: IIS (Internet Information Server) of Kestrel.
* **Hosting platform**: de web apps functie van Azure app service, Azure VM, docker, Azure Kubernetes service (AKS), enzovoort.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Ik heb Application Insights ingeschakeld vanuit de Azure web app-Portal. Maar ik zie EventCounters niet.?

 Deze functie wordt nog niet ondersteund door de [Application Insights extensie](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) voor ASP.net core. Dit document wordt bijgewerkt wanneer deze functie wordt ondersteund.

## <a name="next-steps"></a><a name="next"></a>Volgende stappen

* [Afhankelijkheden bijhouden](../../azure-monitor/app/asp-net-dependencies.md)
