---
title: Application Insights voor Worker-service-apps (niet-HTTP-apps)
description: Niet-HTTP-apps van .NET core/. NET bewaken met Azure Monitor Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/16/2019
ms.openlocfilehash: bea30ade6d9f6eb77d18c671b824b138ba94fddb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406192"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Application Insights voor Worker-service toepassingen (niet-HTTP-toepassingen)

Application Insights brengt een nieuwe SDK uit, met de naam `Microsoft.ApplicationInsights.WorkerService`, die het meest geschikt is voor niet-HTTP-workloads zoals Messa ging, achtergrond taken, console toepassingen, enzovoort. Deze typen toepassingen hebben niet het principe van een binnenkomende HTTP-aanvraag, zoals een traditionele ASP.NET/ASP.NET core-webtoepassing, en daarom wordt het gebruik van Application Insights-pakketten voor [ASP.net](asp-net.md) of [ASP.net core](asp-net-core.md) toepassingen niet ondersteund.

De nieuwe SDK voert geen telemetrie-verzameling op zichzelf uit. In plaats daarvan wordt een andere bekende Application Insights automatische verzamelaars zoals [DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) enz. Deze SDK bevat uitbreidings methoden op `IServiceCollection` om telemetrie-verzameling in te scha kelen en te configureren.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

De [Application INSIGHTS SDK for Worker-service](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) is het meest geschikt voor niet-HTTP-toepassingen, ongeacht waar of hoe ze worden uitgevoerd. Als uw toepassing wordt uitgevoerd en netwerk verbinding heeft met Azure, kan telemetrie worden verzameld. Application Insights bewaking wordt ondersteund overal wat .NET core ondersteunt. Dit pakket kan worden gebruikt in de zojuist geïntroduceerde [.net core 3,0-werk service](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances), [achtergrond taken in ASP.net Core 2.1/2.2](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2), console-apps (.net core/.NET Framework), enzovoort.

## <a name="prerequisites"></a>Vereisten

Een geldige Application Insights instrumentatie sleutel. Deze sleutel is vereist voor het verzenden van telemetrie naar Application Insights. Als u een nieuwe Application Insights resource moet maken om een instrumentatie sleutel op te halen, raadpleegt u [een Application Insights resource maken](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="using-application-insights-sdk-for-worker-services"></a>Application Insights SDK voor Worker-Services gebruiken

1. Installeer het pakket [micro soft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) op de toepassing.
   Het volgende code fragment toont de wijzigingen die moeten worden toegevoegd aan het `.csproj` bestand van uw project.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.12.0" />
    </ItemGroup>
```

1. Roep `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` extensie methode aan op `IServiceCollection`, waardoor de instrumentatie sleutel wordt verstrekt. Deze methode moet worden aangeroepen aan het begin van de toepassing. De exacte locatie is afhankelijk van het type toepassing.

1. Haal een `ILogger` exemplaar of `TelemetryClient` exemplaar op uit de container Injection (DI) door het aanroepen van `serviceProvider.GetRequiredService<TelemetryClient>();` of het gebruik van constructor-injectie. Met deze stap wordt het instellen van `TelemetryConfiguration` en modules voor automatische verzameling geactiveerd.

Specifieke instructies voor elk type toepassing worden beschreven in de volgende secties.

## <a name="net-core-30-worker-service-application"></a>.NET Core 3,0 Worker-service toepassing

[Hier](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) wordt een volledig voor beeld gedeeld

1. Down load en Installeer [.net Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Maak een nieuw werk service project met behulp van de nieuwe project sjabloon of de opdracht regel van Visual Studio `dotnet new worker`
3. Installeer het pakket [micro soft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) op de toepassing.

4. Voeg `services.AddApplicationInsightsTelemetryWorkerService();` toe aan de methode `CreateHostBuilder()` in uw `Program.cs`-klasse, zoals in dit voor beeld:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Wijzig uw `Worker.cs` volgens onderstaande voor beeld.

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

6. Stel de instrumentatie sleutel in.

    Hoewel u de instrumentatie sleutel kunt opgeven als een argument voor `AddApplicationInsightsTelemetryWorkerService`, raden we u aan om de instrumentatie sleutel in te stellen in de configuratie. In het volgende code voorbeeld ziet u hoe u een instrumentatie sleutel opgeeft in `appsettings.json`. Zorg ervoor dat `appsettings.json` tijdens het publiceren naar de hoofdmap van de toepassing is gekopieerd.

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

U kunt ook de instrumentatie sleutel opgeven in een van de volgende omgevings variabelen.
`APPINSIGHTS_INSTRUMENTATIONKEY` of `ApplicationInsights:InstrumentationKey`

Bijvoorbeeld: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
OF `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

Normaal gesp roken bevat `APPINSIGHTS_INSTRUMENTATIONKEY` de instrumentatie sleutel voor toepassingen die op Web Apps als webjobs worden geïmplementeerd.

> [!NOTE]
> Een instrumentatie sleutel die is opgegeven in code wint via de omgevings variabele `APPINSIGHTS_INSTRUMENTATIONKEY`, die WINS over andere opties overneemt.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>Achtergrond taken ASP.NET Core met gehoste services

In [Dit](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio) document wordt beschreven hoe u achtergrond taken maakt in ASP.net Core 2.1/2.2-toepassing.

[Hier](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) wordt een volledig voor beeld gedeeld

1. Installeer het pakket micro soft. ApplicationInsights. WorkerService (https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) voor de toepassing.
2. Voeg `services.AddApplicationInsightsTelemetryWorkerService();` toe aan de methode `ConfigureServices()`, zoals in dit voor beeld:

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

Hier volgt de code voor `TimedHostedService` waarbij de logica van de achtergrond taak zich bevindt.

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

3. Stel de instrumentatie sleutel in.
   Gebruik hetzelfde `appsettings.json` in het bovenstaande voor beeld van de .NET Core 3,0-werk service.

## <a name="net-corenet-framework-console-application"></a>.NET core/. NET Framework-console toepassing

Zoals vermeld in het begin van dit artikel, kan het nieuwe pakket worden gebruikt voor het inschakelen van Application Insights Telemetry van zelfs een gewone console toepassing. Dit pakket is gericht [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)en kan daarom worden gebruikt voor console-apps in .net Core 2,0 of hoger, en .NET Framework 4.7.2 of hoger.

[Hier](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) wordt een volledig voor beeld gedeeld

1. Installeer het pakket micro soft. ApplicationInsights. WorkerService (https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) voor de toepassing.

2. Wijzig Program.cs zoals hieronder wordt beschreven.

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

Deze console toepassing gebruikt ook dezelfde standaard `TelemetryConfiguration`en kan op dezelfde manier worden aangepast als de voor beelden in een eerdere sectie.

## <a name="run-your-application"></a>Uw toepassing uitvoeren

Voer de toepassing uit. Met de voorbeeld werk rollen van het bovenstaande voor beeld wordt een HTTP-oproep elke seconde verzonden naar bing.com, en worden er ook enkele logboeken met ILogger. Deze regels worden genest in `StartOperation` aanroep van `TelemetryClient`, die wordt gebruikt voor het maken van een bewerking (in dit voor beeld `RequestTelemetry` de naam ' Operation '). Application Insights verzamelt deze ILogger-Logboeken (standaard) en afhankelijkheden, en ze worden gecorreleerd aan de `RequestTelemetry` met een relatie tussen bovenliggende en onderliggende items. De correlatie werkt ook met cross proces/netwerk grens. Als de aanroep bijvoorbeeld is gedaan aan een ander bewaakt onderdeel, wordt deze ook aan dit bovenliggende item gecorreleerd.

Deze aangepaste bewerking van `RequestTelemetry` kan worden beschouwd als het equivalent van een inkomende webaanvraag in een typische webtoepassing. Hoewel het niet nodig is om een bewerking te gebruiken, past deze het beste bij het [Application Insights correlatie gegevens model](https://docs.microsoft.com/azure/azure-monitor/app/correlation) , waarbij `RequestTelemetry` fungeert als de bovenliggende bewerking en elke telemetrie die in de worker-iteratie wordt gegenereerd, wordt beschouwd als logisch deel uitmaakt van dezelfde bewerking. Deze aanpak zorgt er ook voor dat alle telemetrie die is gegenereerd (automatisch en hand matig), hetzelfde `operation_id`heeft. Als steek proef is gebaseerd op `operation_id`, houdt het steekproef algoritme alle telemetrie van één iteratie over of verwijdert u deze.

Hieronder ziet u de volledige telemetrie die automatisch is verzameld door Application Insights.

### <a name="live-metrics"></a>Live Metrics

[Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) kunnen worden gebruikt om snel te controleren of Application Insights bewaking op de juiste wijze is geconfigureerd. Het kan enkele minuten duren voordat telemetrie in de portal en de analyse wordt weer gegeven. met Live metrische gegevens wordt het CPU-gebruik van het actieve proces in bijna realtime weer gegeven. Het kan ook andere telemetrie weer geven, zoals aanvragen, afhankelijkheden, traceringen, enzovoort.

### <a name="ilogger-logs"></a>ILogger-logboeken

Logboeken die worden verzonden via `ILogger` Ernst `Warning` of hoger worden automatisch vastgelegd. Volg de [ILogger-documenten](ilogger.md#control-logging-level) om aan te passen welke logboek niveaus worden vastgelegd door Application Insights.

### <a name="dependencies"></a>Afhankelijkheden

De afhankelijkheids verzameling is standaard ingeschakeld. In [Dit](asp-net-dependencies.md#automatically-tracked-dependencies) artikel worden de afhankelijkheden beschreven die automatisch worden verzameld en bevatten de stappen voor het hand matig bijhouden.

### <a name="eventcounter"></a>Event Counter

`EventCounterCollectionModule` is standaard ingeschakeld en er wordt een standaardset tellers van .NET Core 3,0-apps verzameld. De [Event Counter](eventcounters.md) zelf studie bevat de standaardset met verzamelde items. Het bevat ook instructies voor het aanpassen van de lijst.

### <a name="manually-tracking-additional-telemetry"></a>Hand matig extra telemetrie bijhouden

Hoewel de SDK automatisch telemetrie verzamelt zoals hierboven is uitgelegd, moet de gebruiker in de meeste gevallen extra telemetrie naar Application Insights service verzenden. De aanbevolen manier om extra telemetrie bij te houden, is door een exemplaar van `TelemetryClient` te verkrijgen bij afhankelijkheids injectie en vervolgens een van de ondersteunde `TrackXXX()` [API](api-custom-events-metrics.md) -methoden erop aan te roepen. Een andere typische use-case is het [bijhouden van bewerkingen op maat](custom-operations-tracking.md). Deze benadering wordt uitgelegd in de bovenstaande voor beelden van werk nemers.

## <a name="configure-the-application-insights-sdk"></a>De Application Insights SDK configureren

De standaard `TelemetryConfiguration` die wordt gebruikt door de worker-SDK is vergelijkbaar met de automatische configuratie die wordt gebruikt in een ASP.NET of ASP.NET Core toepassing, min de TelemetryInitializers die wordt gebruikt om de telemetrie van `HttpContext`te verrijken.

U kunt de Application Insights SDK for Worker-service aanpassen om de standaard configuratie te wijzigen. Gebruikers van de Application Insights ASP.NET Core SDK kunnen vertrouwd zijn met het wijzigen van de configuratie met behulp van ASP.NET Core ingebouwde [afhankelijkheids injectie](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). De WorkerService SDK is ook gebaseerd op soort gelijke principes. Maak bijna alle configuratie wijzigingen in de sectie `ConfigureServices()` door de juiste methoden op `IServiceCollection`aan te roepen, zoals hieronder wordt beschreven.

> [!NOTE]
> Wanneer u deze SDK gebruikt, wordt het wijzigen van de configuratie door `TelemetryConfiguration.Active` niet ondersteund en worden wijzigingen niet weer gegeven.

### <a name="using-applicationinsightsserviceoptions"></a>ApplicationInsightsServiceOptions gebruiken

U kunt enkele algemene instellingen wijzigen door `ApplicationInsightsServiceOptions` door te geven aan `AddApplicationInsightsTelemetryWorkerService`, zoals in dit voor beeld:

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

Houd er rekening mee dat `ApplicationInsightsServiceOptions` in deze SDK zich in de naam ruimte bevindt `Microsoft.ApplicationInsights.WorkerService` in plaats van `Microsoft.ApplicationInsights.AspNetCore.Extensions` in de ASP.NET Core SDK.

Veelgebruikte instellingen in `ApplicationInsightsServiceOptions`

|Instelling | Beschrijving | Standaard
|---------------|-------|-------
|EnableQuickPulseMetricStream | Functie LiveMetrics in-of uitschakelen | waar
|EnableAdaptiveSampling | Adaptieve steek proeven in-/uitschakelen | waar
|EnableHeartbeat | De functie heartbeats inschakelen/uitschakelen, die periodiek (standaard 15-minuten) een aangepaste metriek met de naam ' HeartBeatState ' verzendt met informatie over de runtime, zoals .NET-versie, informatie over de Azure-omgeving, indien van toepassing, enzovoort. | waar
|AddAutoCollectedMetricExtractor | Schakel AutoCollectedMetrics extractor in/uit. Dit is een TelemetryProcessor die vooraf geaggregeerde metrische gegevens over aanvragen/afhankelijkheden verzendt voordat steek proeven worden uitgevoerd. | waar

Zie de [Configureer bare instellingen in `ApplicationInsightsServiceOptions`](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) voor de meest recente lijst.

### <a name="sampling"></a>Sampling

De Application Insights SDK voor Worker-service ondersteunt zowel vaste als adaptieve steek proeven. Adaptieve steek proeven zijn standaard ingeschakeld. Het configureren van sampling voor Worker-service gebeurt op dezelfde manier als voor [ASP.net core toepassingen](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers toevoegen

Gebruik de [initialisatie functies voor telemetrie](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) wanneer u eigenschappen wilt definiëren die worden verzonden met alle telemetrie.

Nieuwe `TelemetryInitializer` toevoegen aan de `DependencyInjection`-container en SDK worden ze automatisch toegevoegd aan de `TelemetryConfiguration`.

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers verwijderen

De initialisatie functies voor telemetrie zijn standaard aanwezig. Als u alle of specifieke telemetrie-initialisatie functies wilt verwijderen, gebruikt u de volgende voorbeeld code *nadat* u `AddApplicationInsightsTelemetryWorkerService()`aanroept.

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

### <a name="adding-telemetry-processors"></a>Telemetrie-processors toevoegen

U kunt aangepaste telemetrie-processors toevoegen aan `TelemetryConfiguration` met behulp van de uitbreidings methode `AddApplicationInsightsTelemetryProcessor` op `IServiceCollection`. U kunt telemetrie-processors gebruiken in [Geavanceerde filter scenario's](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer) voor meer directe controle over wat er is opgenomen of uitgesloten van de telemetrie die u naar de Application Insights-service stuurt. Gebruik het volgende voor beeld.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Standaard TelemetryModules configureren of verwijderen

Application Insights maakt gebruik van telemetrie-modules voor het automatisch verzamelen van telemetrie over specifieke workloads zonder hand matige tracering.

De volgende automatische verzamelings modules zijn standaard ingeschakeld. Deze modules zijn verantwoordelijk voor het automatisch verzamelen van telemetrie. U kunt ze uitschakelen of configureren om het standaard gedrag te wijzigen.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Als u een standaard `TelemetryModule`wilt configureren, gebruikt u de extensie methode `ConfigureTelemetryModule<T>` op `IServiceCollection`, zoals in het volgende voor beeld wordt weer gegeven.

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

### <a name="configuring-telemetry-channel"></a>Telemetrie-kanaal configureren

Het standaard kanaal is `ServerTelemetryChannel`. U kunt deze overschrijven zoals in het volgende voor beeld wordt weer gegeven.

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

Als u telemetrie voorwaardelijk en dynamisch wilt uitschakelen, kunt u `TelemetryConfiguration`-exemplaar met ASP.NET Core-injectie container voor afhankelijkheid in de code omzetten en `DisableTelemetry` vlag hierop instellen.

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

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Hoe kan ik telemetrie traceren die niet automatisch wordt verzameld?

Haal een exemplaar van `TelemetryClient` op met behulp van constructor-injectie en roep de vereiste `TrackXXX()`-methode aan. Het maken van nieuwe `TelemetryClient`-exemplaren wordt niet aanbevolen. Een singleton-exemplaar van `TelemetryClient` is al geregistreerd in de `DependencyInjection` container, die `TelemetryConfiguration` deelt met de rest van de telemetrie. Het maken van een nieuw `TelemetryClient`-exemplaar wordt alleen aanbevolen als er een configuratie nodig is die gescheiden is van de rest van de telemetrie.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Kan ik Visual Studio IDE gebruiken om Application Insights te doen voor een werk nemer service-project?

Visual Studio IDE-onboarding wordt momenteel alleen ondersteund voor ASP.NET/ASP.NET-kern toepassingen. Dit document wordt bijgewerkt wanneer Visual Studio ondersteuning biedt voor de onboarding van Worker-service toepassingen.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Kan ik Application Insights bewaking inschakelen met behulp van hulpprogram ma's zoals Status Monitor?

Nee. [Status monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) en [status monitor v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) ondersteunen momenteel alleen ASP.net 4. x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Als ik mijn toepassing in Linux uitvoer, worden alle functies ondersteund?

Ja. Functie ondersteuning voor deze SDK is hetzelfde in alle platforms, met de volgende uitzonde ringen:

* Prestatie meter items worden alleen ondersteund in Windows, met uitzonde ring van CPU/geheugen van proces die wordt weer gegeven in Live Metrics.
* Hoewel `ServerTelemetryChannel` standaard is ingeschakeld en de toepassing wordt uitgevoerd in Linux of MacOS, maakt het kanaal niet automatisch een lokale opslagmap om telemetrie tijdelijk te blijven als er netwerk problemen zijn. Vanwege deze beperking gaat telemetrie verloren als er tijdelijke problemen zijn met het netwerk of de server. U kunt dit probleem omzeilen door een lokale map voor het kanaal te configureren:

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

[.Net core-console toepassing](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Gebruik dit voor beeld als u een console toepassing gebruikt die is geschreven in .NET core (2,0 of hoger) of .NET Framework (4.7.2 of hoger)

[ASP .net core-achtergrond taken met HostedServices](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Gebruik dit voor beeld als u zich in Asp.Net Core 2.1/2.2 bevindt en u [hier](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2) achtergrond taken als per officiële richt lijn maakt

[.Net Core 3,0-werk service](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Gebruik dit voor beeld als u een .NET Core 3,0 Worker-service toepassing hebt als per [officiële richt lijn](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template)

## <a name="open-source-sdk"></a>Open-Source-SDK

[Lees en bijdragen aan de code](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="next-steps"></a>Volgende stappen

* [Gebruik de API](../../azure-monitor/app/api-custom-events-metrics.md) om uw eigen gebeurtenissen en metrische gegevens te verzenden voor een gedetailleerde weer gave van de prestaties en het gebruik van uw app.
* [Houd extra afhankelijkheden bij die niet automatisch worden bijgehouden](../../azure-monitor/app/auto-collect-dependencies.md).
* [Telemetrie van het automatisch verzamelen van gegevens verrijken of filteren](../../azure-monitor/app/api-filtering-sampling.md).
* [Afhankelijkheids injectie in ASP.net core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).
