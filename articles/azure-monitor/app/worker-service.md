---
title: Toepassingsinzichten voor Worker Service-apps (niet-HTTP-apps)
description: Monitoring .NET Core/.NET Framework non-HTTP apps with Azure Monitor Application Insights.
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 34a64ffa67b1a43a77391e0d50ddf1bfad0f73ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501153"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Toepassingsinzichten voor Worker Service-toepassingen (niet-HTTP-toepassingen)

Application Insights is het vrijgeven `Microsoft.ApplicationInsights.WorkerService`van een nieuwe SDK, genaamd , die het meest geschikt is voor niet-HTTP-workloads zoals messaging, achtergrondtaken, console toepassingen etc. Dit soort toepassingen hebben niet het begrip van een inkomende HTTP-aanvraag zoals een traditionele ASP.NET/ASP.NET Core Web Application, en dus met behulp van Application Insights pakketten voor [ASP.NET](asp-net.md) of [ASP.NET Core-toepassingen](asp-net-core.md) wordt niet ondersteund.

De nieuwe SDK doet geen telemetrieverzameling op zichzelf. In plaats daarvan brengt het andere bekende Application Insights autocollectoren zoals [DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) etc. Deze SDK stelt extensiemethoden `IServiceCollection` bloot om telemetrieverzameling in te schakelen en te configureren.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

De [Application Insights SDK for Worker Service](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) is het meest geschikt voor niet-HTTP-toepassingen, ongeacht waar of hoe ze worden uitgevoerd. Als uw toepassing wordt uitgevoerd en netwerkconnectiviteit met Azure heeft, kan telemetrie worden verzameld. Application Insights monitoring wordt overal ondersteund .NET Core wordt ondersteund. Dit pakket kan worden gebruikt in de onlangs geïntroduceerde [.NET Core 3.0 Worker Service,](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances) [achtergrondtaken in Asp.Net Core 2.1/2.2](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2), Console-apps (.NET Core/ .NET Framework), enz.

## <a name="prerequisites"></a>Vereisten

Een geldige Application Insights-instrumentatiesleutel. Deze sleutel is vereist om telemetrie naar Application Insights te verzenden. Zie [Een toepassingsinzichtenbron maken](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)als u een nieuwe Application Insights-bron wilt maken om een instrumentatiesleutel te krijgen.

## <a name="using-application-insights-sdk-for-worker-services"></a>Application Insights SDK voor werknemersservices gebruiken

1. Installeer het [Microsoft.ApplicationInsights.WorkerService-pakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) op de toepassing.
   In het volgende fragment worden de wijzigingen weergegeven `.csproj` die aan het bestand van uw project moeten worden toegevoegd.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. Call `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` extension `IServiceCollection`methode op, het verstrekken van de instrumentatie sleutel. Deze methode moet worden aangeroepen aan het begin van de toepassing. De exacte locatie is afhankelijk van het type toepassing.

1. Haal `ILogger` een `TelemetryClient` instantie of instantie uit de DI-container (Dependency Injection) op door constructorinjection aan te roepen `serviceProvider.GetRequiredService<TelemetryClient>();` of te gebruiken. Deze stap zal leiden `TelemetryConfiguration` tot het instellen van en automatische verzameling modules.

Specifieke instructies voor elk type toepassing worden beschreven in de volgende secties.

## <a name="net-core-30-worker-service-application"></a>.NET Core 3.0 worker service-toepassing

Volledig voorbeeld wordt [hier](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) gedeeld

1. .NET [Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0) downloaden en installeren
2. Een nieuw Worker Service-project maken met visual studio nieuwe projectsjabloon of opdrachtregel`dotnet new worker`
3. Installeer het [Microsoft.ApplicationInsights.WorkerService-pakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) op de toepassing.

4. Toevoegen `services.AddApplicationInsightsTelemetryWorkerService();` aan `CreateHostBuilder()` de `Program.cs` methode in uw klasse, zoals in dit voorbeeld:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Wijzig `Worker.cs` uw per onderstaand voorbeeld.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. Stel de instrumentatiesleutel in.

    Hoewel u de instrumentatiesleutel als `AddApplicationInsightsTelemetryWorkerService`argument opgeven, raden we u aan de instrumentatiesleutel in configuratie op te geven. In het volgende codevoorbeeld ziet u `appsettings.json`hoe u een instrumentatiesleutel opgeeft in . Zorg `appsettings.json` ervoor dat deze tijdens het publiceren naar de hoofdmap van de toepassing wordt gekopieerd.

```json
    {
        "ApplicationInsights":
            {
            "InstrumentationKey": "putinstrumentationkeyhere"
            },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

U ook de instrumentatiesleutel opgeven in een van de volgende omgevingsvariabelen.
`APPINSIGHTS_INSTRUMENTATIONKEY` of `ApplicationInsights:InstrumentationKey`

Bijvoorbeeld: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
Of`SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

Hiermee geeft `APPINSIGHTS_INSTRUMENTATIONKEY` u doorgaans de instrumentatiesleutel op voor toepassingen die zijn geïmplementeerd in Web Apps als webtaken.

> [!NOTE]
> Een instrumentatiesleutel die in code `APPINSIGHTS_INSTRUMENTATIONKEY`is opgegeven, wint de omgevingsvariabele , die andere opties overwint.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET Kernachtergrondtaken met gehoste services

[In](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio) dit document wordt beschreven hoe u achtergrondentaken maakt in ASP.NET Core 2.1/2.2-toepassing.

Volledig voorbeeld wordt [hier](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) gedeeld

1. Installeer het Microsoft.ApplicationInsights.WorkerService(pakkethttps://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) aan de toepassing.
2. Toevoegen `services.AddApplicationInsightsTelemetryWorkerService();` aan `ConfigureServices()` de methode, zoals in dit voorbeeld:

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

Hieronder volgt de `TimedHostedService` code voor waar de achtergrondtaaklogica zich bevindt.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = await httpClient.GetAsync("https://bing.com");
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. Stel de instrumentatiesleutel in.
   Gebruik hetzelfde `appsettings.json` in het bovenstaande voorbeeld .NET Core 3.0 Worker Service.

## <a name="net-corenet-framework-console-application"></a>.NET Core/.NET Framework Console-toepassing

Zoals vermeld in het begin van dit artikel, kan het nieuwe pakket worden gebruikt om Application Insights Telemetrie mogelijk te maken van zelfs een gewone console-applicatie. Dit pakket [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)is bedoeld en kan dus worden gebruikt voor console-apps in .NET Core 2.0 of hoger en .NET Framework 4.7.2 of hoger.

Volledig voorbeeld wordt [hier](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) gedeeld

1. Installeer het Microsoft.ApplicationInsights.WorkerService(pakkethttps://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) aan de toepassing.

2. Wijzig Program.cs als hieronder voorbeeld.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key and any changes to default logging level must be specified here.
                services.AddLogging(loggingBuilder => loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Category", LogLevel.Information));
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

Deze consoletoepassing gebruikt ook `TelemetryConfiguration`dezelfde standaard en kan op dezelfde manier worden aangepast als de voorbeelden in eerdere sectie.

## <a name="run-your-application"></a>Uw toepassing uitvoeren

Voer uw aanvraag uit. Het voorbeeld werknemers van alle bovenstaande maakt een http-oproep elke seconde om bing.com, en zendt ook weinig logs met behulp van ILogger. Deze lijnen worden `StartOperation` verpakt `TelemetryClient`in de aanroep van , die `RequestTelemetry` wordt gebruikt om een bewerking te maken (in dit voorbeeld met de naam 'bewerking'). Application Insights verzamelt deze ILogger-logboeken (waarschuwing of hierboven standaard) en afhankelijkheden, en ze worden gecorreleerd aan de `RequestTelemetry` relatie met ouder-kind. De correlatie werkt ook cross proces / netwerk grens. Als de aanroep bijvoorbeeld naar een andere bewaakte component is gedaan, wordt deze ook gecorreleerd aan deze ouder.

Deze aangepaste `RequestTelemetry` bewerking van kan worden gezien als het equivalent van een inkomende webaanvraag in een typische webtoepassing. Hoewel het niet nodig is om een bewerking te gebruiken, past `RequestTelemetry` deze het beste bij het [correlatiegegevensmodel van Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/correlation) - met optreden als de bovenliggende bewerking en elke telemetrie die wordt gegenereerd binnen de werknemeriteratie die wordt behandeld als logisch erbij dezelfde bewerking. Deze aanpak zorgt er ook voor dat alle gegenereerde `operation_id`telemetrie (automatisch en handmatig) dezelfde hebben. Aangezien de bemonstering `operation_id`is gebaseerd op , houdt het bemonsteringsalgoritme of laat alle telemetrie van één enkele iteratie vallen.

Hieronder vindt u de volledige telemetrie die automatisch wordt verzameld door Application Insights.

### <a name="live-metrics"></a>Live Metrics

[Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) kunnen worden gebruikt om snel te controleren of de monitor van Application Insights correct is geconfigureerd. Hoewel het een paar minuten kan duren voordat telemetrie in de portal en analytics verschijnt, tonen Live Metrics het CPU-gebruik van het lopende proces in bijna realtime. Het kan ook andere telemetrie zoals Verzoeken, Afhankelijkheden, Sporen enz.

### <a name="ilogger-logs"></a>ILogger-logboeken

Logs uitgezonden `ILogger` via van `Warning` ernst of groter worden automatisch vastgelegd. Volg [ILogger-documenten](ilogger.md#control-logging-level) om aan te passen welke logniveaus worden vastgelegd door Application Insights.

### <a name="dependencies"></a>Afhankelijkheden

Afhankelijkheidsverzameling is standaard ingeschakeld. [In](asp-net-dependencies.md#automatically-tracked-dependencies) dit artikel worden de afhankelijkheden uitgelegd die automatisch worden verzameld en bevat het ook stappen om handmatigbijhouden uit te voeren.

### <a name="eventcounter"></a>EventCounter (EventCounter)

`EventCounterCollectionModule`is standaard ingeschakeld en het verzamelt een standaardset tellers van .NET Core 3.0-apps. De [zelfstudie EventCounter](eventcounters.md) bevat de standaardset met verzamelde tellers. Het heeft ook instructies over het aanpassen van de lijst.

### <a name="manually-tracking-additional-telemetry"></a>Handmatig bijhouden van extra telemetrie

Terwijl de SDK automatisch telemetrie verzamelt zoals hierboven is uitgelegd, moet de gebruiker in de meeste gevallen extra telemetrie naar de Application Insights-service verzenden. De aanbevolen manier om extra telemetrie bij `TelemetryClient` te houden is door het verkrijgen van `TrackXXX()` een instantie van van Dependency Injection, en vervolgens het aanroepen van een van de ondersteunde [API-methoden](api-custom-events-metrics.md) op. Een andere typische use case is [custom tracking van bewerkingen.](custom-operations-tracking.md) Deze aanpak wordt aangetoond in de bovenstaande voorbeelden van werknemers.

## <a name="configure-the-application-insights-sdk"></a>De SDK voor toepassingsinzichten configureren

De `TelemetryConfiguration` standaardinstelling die wordt gebruikt door de sdk van de werknemersservice is vergelijkbaar met de automatische configuratie die `HttpContext`wordt gebruikt in een ASP.NET of ASP.NET Core-toepassing, verminderd met de telemetrieinitialisators die worden gebruikt om telemetrie van .

U de Application Insights SDK for Worker Service aanpassen om de standaardconfiguratie te wijzigen. Gebruikers van de Application Insights ASP.NET Core SDK zijn mogelijk bekend met het wijzigen van de configuratie door gebruik te maken van ASP.NET Core ingebouwde [afhankelijkheidsinjectie.](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) De WorkerService SDK is ook gebaseerd op vergelijkbare principes. Breng bijna alle configuratiewijzigingen in de `ConfigureServices()` sectie `IServiceCollection`door het aanroepen van de juiste methoden op, zoals hieronder beschreven.

> [!NOTE]
> Tijdens het gebruik van deze `TelemetryConfiguration.Active` SDK wordt het wijzigen van de configuratie door te wijzigen niet ondersteund en worden wijzigingen niet weergegeven.

### <a name="using-applicationinsightsserviceoptions"></a>ApplicationInsightsServiceOptions gebruiken

U een paar veelvoorkomende instellingen wijzigen door door te geven `ApplicationInsightsServiceOptions` aan, `AddApplicationInsightsTelemetryWorkerService`zoals in dit voorbeeld:

```csharp
    using Microsoft.ApplicationInsights.WorkerService;

    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
    }
```

Merk `ApplicationInsightsServiceOptions` op dat in deze `Microsoft.ApplicationInsights.WorkerService` SDK is `Microsoft.ApplicationInsights.AspNetCore.Extensions` in de naamruimte in tegenstelling tot in de ASP.NET Core SDK.

Veelgebruikte instellingen in`ApplicationInsightsServiceOptions`

|Instelling | Beschrijving | Standaard
|---------------|-------|-------
|InschakelenQuickPulsemetricStream | Functie LiveMetrics inschakelen/uitschakelen | waar
|InschakelenAdaptiveSampling | Adaptieve bemonstering inschakelen/uitschakelen | waar
|Heartbeat inschakelen | Heartbeats-functie inschakelen/uitschakelen, die periodiek (standaard 15 min) een aangepaste statistiek met de naam 'HeartBeatState' verzendt met informatie over de runtime zoals .NET-versie, Azure-omgevingsinformatie, indien van toepassing, enz. | waar
|AddAutoCollectedMetricExtractor | AutoCollectedMetrics-extractor in--/uitschakelen, een telemetrieprocessor die vooraf geaggregeerde statistieken over aanvragen/afhankelijkheden verzendt voordat de bemonstering plaatsvindt. | waar

Zie de [configureerbare `ApplicationInsightsServiceOptions` instellingen in](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) voor de meest up-to-date lijst.

### <a name="sampling"></a>Steekproeven

De Application Insights SDK for Worker Service ondersteunt zowel vaste als adaptieve sampling. Adaptieve bemonstering is standaard ingeschakeld. Het configureren van sampling voor Worker Service gebeurt op dezelfde manier als voor [ASP.NET Core Applications.](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications)

### <a name="adding-telemetryinitializers"></a>Telemetrieinitialisatizers toevoegen

Gebruik [telemetrieinitialisators](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) wanneer u eigenschappen wilt definiëren die met alle telemetrie worden verzonden.

Voeg alle `TelemetryInitializer` nieuwe `DependencyInjection` toe aan de container en `TelemetryConfiguration`SDK voegt ze automatisch toe aan de .

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>Telemetrieinitialisators verwijderen

Telemetrieinitialisators zijn standaard aanwezig. Als u alle of specifieke telemetrieinitialisators `AddApplicationInsightsTelemetryWorkerService()`wilt verwijderen, gebruikt u de volgende voorbeeldcode *nadat* u hebt gebeld.

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>Telemetrieprocessors toevoegen

U aangepaste telemetrieprocessors toevoegen aan `TelemetryConfiguration` de extensiemethode `AddApplicationInsightsTelemetryProcessor` op `IServiceCollection`. U gebruikt telemetrieprocessors in [geavanceerde filterscenario's](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer) om meer directe controle te krijgen over wat er is opgenomen of uitgesloten van de telemetrie die u naar de Application Insights-service verzendt. Gebruik het volgende voorbeeld.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Standaardtelemetriemodules configureren of verwijderen

Application Insights gebruikt telemetriemodules om automatisch telemetrie over specifieke workloads te verzamelen zonder handmatige tracking.

De volgende modules voor automatische verzameling zijn standaard ingeschakeld. Deze modules zijn verantwoordelijk voor het automatisch verzamelen van telemetrie. U ze uitschakelen of configureren om hun standaardgedrag te wijzigen.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Als u `TelemetryModule`een standaardinstelling `ConfigureTelemetryModule<T>` wilt `IServiceCollection`configureren, gebruikt u de extensiemethode op , zoals in het volgende voorbeeld wordt weergegeven.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>Telemetriekanaal configureren

Het standaardkanaal `ServerTelemetryChannel`is . U het overschrijven zoals in het volgende voorbeeld wordt weergegeven.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>Telemetrie dynamisch uitschakelen

Als u telemetrie voorwaardelijk en dynamisch wilt `TelemetryConfiguration` uitschakelen, u instantie oplossen met ASP.NET `DisableTelemetry` Core-injectiecontainer voor afhankelijkheid staken overal in uw code en de vlag erop instellen.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Hoe kan ik telemetrie bijhouden die niet automatisch wordt verzameld?

Krijg een `TelemetryClient` voorbeeld van met behulp van `TrackXXX()` constructor injectie, en bel de vereiste methode op. We raden u af `TelemetryClient` om nieuwe exemplaren te maken. Een singleton `TelemetryClient` exemplaar van is `DependencyInjection` al geregistreerd `TelemetryConfiguration` in de container, die deelt met de rest van de telemetrie. Het maken `TelemetryClient` van een nieuwe instantie wordt alleen aanbevolen als er een configuratie nodig is die los staat van de rest van de telemetrie.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Kan ik Visual Studio IDE gebruiken om Application Insights aan te boord van een Worker Service-project?

Visual Studio IDE onboarding wordt momenteel alleen ondersteund voor ASP.NET/ASP.NET Core Applications. Dit document wordt bijgewerkt wanneer Visual Studio ondersteuning biedt voor onboarding Worker-servicetoepassingen.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Kan ik Application Insights-monitoring inschakelen met behulp van tools zoals Statusmonitor?

Nee. [Statusmonitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) en [Status monitor v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) ondersteunen momenteel ASP.NET 4.x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Als ik mijn applicatie in Linux, zijn alle functies ondersteund?

Ja. Functieondersteuning voor deze SDK is hetzelfde op alle platforms, met de volgende uitzonderingen:

* Prestatiemeteritems worden alleen ondersteund in Windows, met uitzondering van proces-CPU/Geheugen in Live Metrics.
* Hoewel `ServerTelemetryChannel` deze standaard is ingeschakeld, maakt het kanaal niet automatisch een lokale opslagmap als er netwerkproblemen zijn als de toepassing wordt uitgevoerd in Linux of MacOS. Door deze beperking gaat telemetrie verloren wanneer er tijdelijke netwerk- of serverproblemen zijn. Als u dit probleem wilt oplossen, configureert u een lokale map voor het kanaal:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>Voorbeeldtoepassingen

[.NET Core Console-toepassing](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Gebruik dit voorbeeld als u een consoletoepassing gebruikt die is geschreven in .NET Core (2.0 of hoger) of .NET Framework (4.7.2 of hoger)

[ASP .NET Core achtergrondtaken met HostedServices](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Gebruik dit voorbeeld als u zich in Asp.Net Core 2.1/2.2 bevindt en hier achtergrondtaken maakt volgens de officiële [richtlijnen](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)

[.NET Core 3.0 Worker Service](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Gebruik dit voorbeeld als u een .NET Core 3.0 Worker Service-toepassing hebt volgens de officiële richtlijnen [hier](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template)

## <a name="open-source-sdk"></a>Open-source SDK

[Lees en draag bij aan de code](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="next-steps"></a>Volgende stappen

* [Gebruik de API](../../azure-monitor/app/api-custom-events-metrics.md) om uw eigen gebeurtenissen en statistieken te verzenden voor een gedetailleerd overzicht van de prestaties en het gebruik van uw app.
* [Extra afhankelijkheden bijhouden die niet automatisch worden bijgehouden.](../../azure-monitor/app/auto-collect-dependencies.md)
* [Automatisch verzamelde telemetrie verrijken of filteren](../../azure-monitor/app/api-filtering-sampling.md).
* [Afhankelijkheidsinjectie in ASP.NET Kern](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).
