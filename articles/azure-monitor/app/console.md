---
title: Azure-toepassingsinzichten voor consoletoepassingen | Microsoft Documenten
description: Monitor webapplicaties op beschikbaarheid, prestaties en gebruik.
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: lmolkova
ms.openlocfilehash: baaea0f8055eeff0314fcf5fde00729ea8091d12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655426"
---
# <a name="application-insights-for-net-console-applications"></a>Toepassingsinzichten voor .NET-consoletoepassingen

[Met Application Insights](../../azure-monitor/app/app-insights-overview.md) u uw webtoepassing controleren op beschikbaarheid, prestaties en gebruik.

U hebt een abonnement met [Microsoft Azure](https://azure.com)nodig. Meld u aan met een Microsoft-account, dat u mogelijk hebt voor Windows, Xbox Live of andere Microsoft-cloudservices. Uw team heeft mogelijk een organisatieabonnement op Azure: vraag de eigenaar om u eraan toe te voegen met uw Microsoft-account.

> [!NOTE]
> Er is een nieuwe Application Insights SDK genaamd [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) die kan worden gebruikt om Application Insights in te schakelen voor elke consoletoepassingen. Het wordt aanbevolen om dit pakket en de bijbehorende instructies vanaf [hier](../../azure-monitor/app/worker-service.md)te gebruiken. Dit pakket [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)richt zich op , en kan dus worden gebruikt in .NET Core 2.0 of hoger, en .NET Framework 4.7.2 of hoger.

## <a name="getting-started"></a>Aan de slag

* Maak in de [Azure Portal](https://portal.azure.com)[een Application Insights-resource](../../azure-monitor/app/create-new-resource.md). Kies **Algemeen**voor toepassingstype .
* Kopieer de instrumentatiesleutel. Vind de sleutel in de vervolgkeuzelijst **Essentials** van de nieuwe bron die u hebt gemaakt.
* Installeer het nieuwste [Microsoft.ApplicationInsights-pakket.](https://www.nuget.org/packages/Microsoft.ApplicationInsights)
* Stel de instrumentatiesleutel in uw code in voordat u telemetrie bijhoudt (of APPINSIGHTS_INSTRUMENTATIONKEY omgevingsvariabele instelt). Daarna moet u telemetrie handmatig kunnen volgen en bekijken op de Azure-portal

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> Telemetrie wordt niet direct verzonden. Telemetrie-items worden gebatched en verzonden door de ApplicationInsights SDK. In Console-apps, die direct `Track()` na het aanroepen van `Flush()` methoden `Sleep` worden afgesloten, kan telemetrie alleen worden verzonden en wordt deze uitgevoerd voordat de app wordt afgesloten, zoals later in dit artikel in [het volledige voorbeeld](#full-example) wordt weergegeven.


* Installeer de nieuwste versie van [het Microsoft.ApplicationInsights.DependencyCollector-pakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) - het houdt automatisch HTTP, SQL of andere externe afhankelijkheidsoproepen bij.

U Toepassingsinzichten initialiseren en configureren `ApplicationInsights.config` vanuit de code of het gebruik van het bestand. Zorg ervoor dat initialisatie zo vroeg mogelijk plaatsvindt. 

> [!NOTE]
> Instructies die verwijzen naar **ApplicationInsights.config** zijn alleen van toepassing op apps die zich richten op het .NET Framework en zijn niet van toepassing op .NET Core-toepassingen.

### <a name="using-config-file"></a>Config-bestand gebruiken

Application Insights SDK zoekt `ApplicationInsights.config` standaard naar bestand `TelemetryConfiguration` in de werkmap wanneer deze wordt gemaakt

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

U ook het pad naar het config-bestand opgeven.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Zie [referentie van configuratiebestanden voor](configuration-with-applicationinsights-config.md)meer informatie .

U een volledig voorbeeld van het config-bestand krijgen door de nieuwste versie van [het Microsoft.ApplicationInsights.WindowsServer-pakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) te installeren. Hier is de **minimale** configuratie voor afhankelijkheidsverzameling die gelijk is aan het codevoorbeeld.

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

### <a name="configuring-telemetry-collection-from-code"></a>Telemetrieverzameling configureren uit code
> [!NOTE]
> Het lezen van config-bestand wordt niet ondersteund op .NET Core. U overwegen [application Insights SDK te](../../azure-monitor/app/asp-net-core.md) gebruiken voor ASP.NET Core

* Tijdens het opstarten van `DependencyTrackingTelemetryModule` toepassingen maken en configureren instantie - het moet singleton en moet worden bewaard voor de levensduur van de toepassing.

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

* Algemene telemetrieinitialisators toevoegen

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Als u configuratie `TelemetryConfiguration()` hebt gemaakt met een gewone constructor, moet u ook correlatieondersteuning inschakelen. **Het is niet nodig** als u `TelemetryConfiguration.CreateDefault()` configuratie `TelemetryConfiguration.Active`leest uit bestand, gebruikt of .

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* U ook de Collector-module van de Prestatiemeter installeren en initialiseren zoals [hier](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/) beschreven


#### <a name="full-example"></a>Volledig voorbeeld

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
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

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
* [Controleer afhankelijkheden](../../azure-monitor/app/asp-net-dependencies.md) om te zien of REST, SQL of andere externe bronnen u vertragen.
* [Gebruik de API](../../azure-monitor/app/api-custom-events-metrics.md) om uw eigen gebeurtenissen en statistieken te verzenden voor een meer gedetailleerde weergave van de prestaties en het gebruik van uw app.
