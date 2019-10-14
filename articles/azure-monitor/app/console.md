---
title: Azure-toepassing inzichten voor console toepassingen | Microsoft Docs
description: Bewaak webtoepassingen voor Beschik baarheid, prestaties en gebruik.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/30/2019
ms.reviewer: lmolkova
ms.author: mbullwin
ms.openlocfilehash: 53a765cd2e71b5b1eb1ac2c70506fd55aec6736e
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274140"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights voor .NET-console toepassingen

Met [Application Insights](../../azure-monitor/app/app-insights-overview.md) kunt u uw webtoepassing controleren op Beschik baarheid, prestaties en gebruik.

U hebt een abonnement met [Microsoft Azure](https://azure.com)nodig. Meld u aan met een Microsoft-account, dat u mogelijk hebt voor Windows, Xbox Live of andere micro soft-Cloud Services. Uw team heeft mogelijk een organisatie abonnement op Azure: vraag de eigenaar om u toe te voegen met behulp van uw Microsoft-account.

> [!NOTE]
> Er is een nieuwe bèta versie van Application Insights SDK met de naam [micro soft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) , die kan worden gebruikt om Application Insights in te scha kelen voor elke console toepassing. U wordt aangeraden dit pakket en de bijbehorende instructies [hier](../../azure-monitor/app/worker-service.md)te gebruiken. Dit pakket heeft [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)en kan daarom worden gebruikt in .net Core 2,0 of hoger en .NET Framework 4.7.2 of hoger.
Dit document wordt afgeschaft zodra een stabiele versie van dit nieuwe pakket wordt uitgebracht.

## <a name="getting-started"></a>Aan de slag

* Maak in de [Azure Portal](https://portal.azure.com) [een Application Insights-resource](../../azure-monitor/app/create-new-resource.md). Voor toepassings type kiest u **Algemeen**.
* Kopieer de instrumentatiesleutel. Zoek de sleutel in de vervolg keuzelijst **Essentials** van de nieuwe resource die u hebt gemaakt. 
* Installeer het meest recente pakket [micro soft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) .
* Stel de instrumentatie sleutel in uw code in voordat u een telemetrie bijhoudt (of stel de omgevings variabele APPINSIGHTS_INSTRUMENTATIONKEY in). Daarna kunt u de telemetrie hand matig bijhouden en weer geven op de Azure Portal

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

* Installeer de nieuwste versie van het pakket [micro soft. ApplicationInsights. DependencyCollector.](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) het traceert automatisch http, SQL of andere externe afhankelijkheids aanroepen.

U kunt Application Insights vanuit de code initialiseren en configureren, of `ApplicationInsights.config`-bestand gebruiken. Zorg ervoor dat de initialisatie zo snel mogelijk gebeurt. 

> [!NOTE]
> Instructies die verwijzen naar **ApplicationInsights. config** zijn alleen van toepassing op apps die zijn gericht op de .NET Framework, en zijn niet van toepassing op .net core-toepassingen.

### <a name="using-config-file"></a>Configuratie bestand gebruiken
Application Insights SDK zoekt standaard naar `ApplicationInsights.config`-bestand in de werkmap wanneer `TelemetryConfiguration` wordt gemaakt

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

U kunt ook het pad naar het configuratie bestand opgeven.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Zie voor meer informatie [configuratie bestand verwijzing](configuration-with-applicationinsights-config.md).

U kunt een volledig voor beeld van het configuratie bestand krijgen door de nieuwste versie van het pakket [micro soft. ApplicationInsights. Windowsserver](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) te installeren. Dit is de **minimale** configuratie voor afhankelijkheids verzameling die gelijk is aan het code voorbeeld.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Telemetrie verzameling configureren vanuit code
> [!NOTE]
> Het lezen van het configuratie bestand wordt niet ondersteund in .NET core. U kunt overwegen [Application INSIGHTS SDK te gebruiken voor ASP.net core](../../azure-monitor/app/asp-net-core.md)

* Bij het opstarten van de toepassing wordt `DependencyTrackingTelemetryModule`-exemplaar gemaakt en geconfigureerd. dit moet een singleton zijn en moet worden bewaard voor de levens duur van de toepassing.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Veelgebruikte telemetrie-initialisatie functies toevoegen

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Als u configuratie hebt gemaakt met de constructor zonder normale `TelemetryConfiguration()`, moet u ook ondersteuning voor correlatie inschakelen. **Het is niet nodig** als u de configuratie van het bestand hebt gelezen, wordt gebruikt `TelemetryConfiguration.CreateDefault()` of `TelemetryConfiguration.Active`.

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* U kunt ook de Collector-module voor prestatie meter items installeren en initialiseren, zoals [hier](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/) wordt beschreven


#### <a name="full-example"></a>Volledig voor beeld

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient(configuration);
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking so wait a bit
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>Volgende stappen
* [Bewaak afhankelijkheden](../../azure-monitor/app/asp-net-dependencies.md) om te zien of rest, SQL of andere externe bronnen worden vertraagd.
* [Gebruik de API](../../azure-monitor/app/api-custom-events-metrics.md) om uw eigen gebeurtenissen en metrische gegevens te verzenden voor een meer gedetailleerde weer gave van de prestaties en het gebruik van uw app.
