---
title: Zoek .NET trace logs met ILogger - Azure Application Insights
description: Voorbeelden van het gebruik van de Azure Application Insights ILogger-provider met ASP.NET Core- en Console-toepassingen.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0f40c1c1a8ee7f20c769a62e9746da43face4cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276373"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider voor .NET Core ILogger logs

ASP.NET Core ondersteunt een logging-API die werkt met verschillende soorten ingebouwde en externe loggingproviders. Logging wordt gedaan door te bellen **log()** of een variant van het op *ILogger* instanties. Dit artikel laat zien hoe *je ApplicationInsightsLoggerProvider* gebruiken om ILogger-logs vast te leggen in console- en ASP.NET Core-toepassingen. In dit artikel wordt ook beschreven hoe ApplicationInsightsLoggerProvider integreert met andere Application Insights telemetrie.
Zie [Inloggen in ASP.NET Kern](https://docs.microsoft.com/aspnet/core/fundamentals/logging)voor meer informatie.

## <a name="aspnet-core-applications"></a>ASP.NET Kerntoepassingen

ApplicationInsightsLoggerProvider is standaard ingeschakeld in [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versie 2.7.1 (en hoger) wanneer u de reguliere Application Insights-monitoring inschakelt via een van de methoden:

- Door de **extensiemethode UseApplicationInsights** op IWebHostBuilder aan te roepen (nu verouderd)
- Door de **extensie AddApplicationInsightsTelemetry-extensie** aan te roepen op IServiceCollection

ILogger logs dat ApplicationInsightsLoggerProvider vangt zijn onderworpen aan dezelfde configuratie als elke andere telemetrie die is verzameld. Ze hebben dezelfde set telemetrieinitializers en telemetrieprocessors, gebruiken hetzelfde Telemetriekanaal en worden gecorreleerd en bemonsterd op dezelfde manier als andere telemetrie. Als u versie 2.7.1 of hoger gebruikt, is er geen actie vereist om ILogger-logboeken vast te leggen.

Alleen *waarschuwing* of hogere ILogger-logboeken (uit alle [categorieën)](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-3.1#log-category)worden standaard naar Application Insights verzonden. U [filters echter toepassen om dit gedrag aan te passen.](#control-logging-level) Extra stappen zijn vereist om ILogger logs vast te leggen van **Program.cs** of **Startup.cs.** (Zie [Het vastleggen van ILogger logs van Startup.cs en Program.cs in ASP.NET Core applicaties](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Als u een eerdere versie van Microsoft.ApplicationInsights.AspNet SDK gebruikt of als u Gewoon ApplicationInsightsLoggerProvider wilt gebruiken zonder enige andere Application Insights-monitoring, gebruikt u de volgende procedure:

1. Installeer het NuGet-pakket:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Wijzig **Program.cs** zoals hier wordt weergegeven:

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("ikey");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

De code in stap `ApplicationInsightsLoggerProvider`2 configureert . In de volgende code wordt een `ILogger` voorbeeldklasse Controller weergegeven, waarmee logboeken worden verzonden. De logboeken worden vastgelegd door Application Insights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>ILogger-logboeken vastleggen van Startup.cs en Program.cs in ASP.NET Core-apps

> [!NOTE]
> In ASP.NET Core 3.0 en hoger is `ILogger` het niet meer mogelijk om in Startup.cs en Program.cs te injecteren. Zie https://github.com/aspnet/Announcements/issues/353 voor meer details.

De nieuwe ApplicationInsightsLoggerProvider kan logs vastleggen vanaf het begin in de applicatie-opstartpijplijn. Hoewel ApplicationInsightsLoggerProvider automatisch is ingeschakeld in Application Insights (te beginnen met versie 2.7.1), is er pas later in de pijplijn een instrumentatiesleutel ingesteld. Dus, alleen logs van **Controller**/ andere klassen zullen worden vastgelegd. Om elk logboek vast te leggen dat begint met **Program.cs** en **Startup.cs** zelf, moet u expliciet een instrumentatiesleutel inschakelen voor ApplicationInsightsLoggerProvider. *Telemetrieconfiguratie* is ook niet volledig ingesteld wanneer u zich aanmeldt bij **Program.cs** of **Startup.cs** zelf. Dus die logs hebben een minimale configuratie die InMemoryChannel gebruikt, geen sampling en geen standaard telemetrieinitialisators of processors.

De volgende voorbeelden tonen deze mogelijkheid aan met **Program.cs** en **Startup.cs**.

#### <a name="example-programcs"></a>Voorbeeld Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Voorbeeld Startup.cs

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migreren van de oude ApplicationInsightsLoggerProvider

Microsoft.ApplicationInsights.AspNet SDK-versies vóór 2.7.1 ondersteunden een loggingprovider die nu verouderd is. Deze provider is ingeschakeld via de **AddApplicationInsights()** extensiemethode van ILoggerFactory. We raden u aan te migreren naar de nieuwe provider, die twee stappen omvat:

1. Verwijder de aanroep *ILoggerFactory.AddApplicationInsights()* uit de methode **Opstarten.Configureren()** om dubbele logboekregistratie te voorkomen.
2. Pas eventuele filterregels opnieuw toe in code, omdat deze niet worden gerespecteerd door de nieuwe provider. Overbelasting van *ILoggerFactory.AddApplicationInsights()* nam minimaal LogLevel of filterfuncties. Bij de nieuwe provider maakt filteren deel uit van het logging framework zelf. Dit wordt niet gedaan door de Application Insights-provider. Filters die worden geleverd via *ILoggerFactory.AddApplicationInsights()* overbelasting moeten dus worden verwijderd. En filterregels moeten worden verstrekt door het volgen van de instructies [voor het controlelogboekniveau.](#control-logging-level) Als u *appsettings.json* gebruikt om logboekregistratie te filteren, blijft deze werken met de nieuwe provider, omdat beide dezelfde provideralias, *ApplicationInsights*, gebruiken.

U nog steeds gebruik maken van de oude provider. (Het zal alleen worden verwijderd in een belangrijke versie te veranderen in 3. *xx*.) Maar we raden u aan om te migreren naar de nieuwe provider om de volgende redenen:

- De vorige provider mist ondersteuning voor [logboekscopen.](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes) In de nieuwe provider worden eigenschappen uit bereik automatisch toegevoegd als aangepaste eigenschappen aan de verzamelde telemetrie.
- Logboeken kunnen nu veel eerder worden vastgelegd in de opstartpijplijn van toepassingen. Logboeken van de **programma-** en **opstartklassen** kunnen nu worden vastgelegd.
- Met de nieuwe provider wordt filteren gedaan op het kaderniveau zelf. U logboeken op dezelfde manier filteren naar de Application Insights-provider als voor andere providers, waaronder ingebouwde providers zoals Console, Debug, enzovoort. U dezelfde filters ook toepassen op meerdere providers.
- In ASP.NET Core (2.0 en hoger) is de aanbevolen manier om [loggingproviders in](https://github.com/aspnet/Announcements/issues/255) te schakelen door extensiemethoden op ILoggingBuilder in **Program.cs** zelf te gebruiken.

> [!Note]
> De nieuwe provider is beschikbaar voor toepassingen die zich richten op NETSTANDARD2.0 of hoger. Als uw toepassing zich richt op oudere .NET Core-versies, zoals .NET Core 1.1 of als deze zich richt op het .NET Framework, blijft u de oude provider gebruiken.

## <a name="console-application"></a>Consoletoepassing

> [!NOTE]
> Er is een nieuwe Application Insights SDK genaamd [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) die kan worden gebruikt om Application Insights (ILogger en andere Application Insights telemetrie) in te schakelen voor elke consoletoepassingen. Het wordt aanbevolen om dit pakket en de bijbehorende instructies vanaf [hier](../../azure-monitor/app/worker-service.md)te gebruiken.

De volgende code toont een voorbeeldconsoletoepassing die is geconfigureerd om ILogger-traces naar Application Insights te verzenden.

Pakketten geïnstalleerd:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

In dit voorbeeld `Microsoft.Extensions.Logging.ApplicationInsights`wordt het standalone pakket gebruikt. Standaard maakt deze configuratie gebruik van de 'absolute minimum' Telemetrieconfiguratie voor het verzenden van gegevens naar Application Insights. Absoluut minimum betekent dat InMemoryChannel het kanaal is dat wordt gebruikt. Er is geen bemonstering en geen standaard telemetrieinitialisators. Dit gedrag kan worden overschreven voor een consoletoepassing, zoals in het volgende voorbeeld wordt weergegeven.

Installeer dit extra pakket:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

In de volgende sectie ziet u hoe u de standaard telemetrieconfiguratie overschrijft met behulp van de **services. De\<methode TelemetrieConfiguratie> configureren.** Dit voorbeeld `ServerTelemetryChannel` wordt ingesteld en gesampled. Het voegt een aangepaste ITelemetryInitializer toe aan de TelemetrieConfiguratie.

```csharp
    // Create the DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Logging-niveau beheren

De ASP.NET Core *ILogger* infra heeft een ingebouwd mechanisme om [logfiltering](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering)toe te passen. Hiermee u de logboeken beheren die naar elke geregistreerde provider worden verzonden, inclusief de Application Insights-provider. Het filteren kan in configuratie (meestal met behulp van een *appsettings.json-bestand)* of in code. Deze faciliteit wordt verzorgd door het kader zelf. Het is niet specifiek voor de Application Insights-provider.

De volgende voorbeelden zijn van toepassing filterregels op ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Filterregels maken in configuratie met appsettings.json

Voor ApplicationInsightsLoggerProvider is `ApplicationInsights`de alias van de provider . Het volgende gedeelte van *appsettings.json* instrueert logging providers over het algemeen om in te loggen op niveau *Waarschuwing* en hoger. Het overschrijft `ApplicationInsightsLoggerProvider` vervolgens de om categorieën die beginnen met "Microsoft" op niveau *Fout* en hoger te loggen.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>Filterregels maken in code

In het volgende codefragment worden logboeken geconfigureerd voor *waarschuwing* en hoger uit alle categorieën en `ApplicationInsightsLoggerProvider`voor *Fout* en hoger van categorieën die beginnen met 'Microsoft' om naar te worden verzonden. Deze configuratie is hetzelfde als in de vorige sectie in *appsettings.json*.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Wat zijn de oude en nieuwe versies van ApplicationInsightsLoggerProvider?

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) omvatte een ingebouwde ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), die werd ingeschakeld via **ILoggerFactory** extensiemethoden. Deze provider is gemarkeerd als verouderd van versie 2.7.1. Het zal volledig worden verwijderd in de volgende grote versie te veranderen. Het [Microsoft.ApplicationInsights.AspNetCore 2.6.1-pakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) zelf is niet verouderd. Het is vereist om het controleren van aanvragen, afhankelijkheden, enzovoort mogelijk te maken.

Het voorgestelde alternatief is het nieuwe standalone pakket [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), dat een verbeterde ApplicationInsightsLoggerProvider (Microsoft.Extensions.Logging.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) en extensie methoden op ILoggerBuilder voor het inschakelen ervan bevat.

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versie 2.7.1 neemt een afhankelijkheid van het nieuwe pakket en maakt iLogger automatisch vastleggen.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Waarom worden sommige ILogger-logboeken twee keer weergegeven in Application Insights?

Duplicatie kan optreden als u de oudere (nu verouderde) `AddApplicationInsights` `ILoggerFactory`versie van ApplicationInsightsLoggerProvider ingeschakeld door een beroep op . Controleer of de methode **Configureren** het volgende heeft en verwijder deze:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Als u dubbele logboekregistratie ervaart wanneer u `EnableDebugLogger` debugt vanuit Visual Studio, stelt u in op *false* in de code waarmee Application Insights als volgt wordt ingesteld. Deze duplicatie en oplossing is alleen relevant wanneer u de toepassing debugt.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Ik heb [microsoft.applicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versie 2.7.1 bijgewerkt en logboeken van ILogger worden automatisch vastgelegd. Hoe schakel ik deze functie volledig uit?

Zie de sectie [Logboekregistratieniveau beheren](../../azure-monitor/app/ilogger.md#control-logging-level) om te zien hoe logboeken in het algemeen kunnen worden gefilterd. Als u ApplicationInsightsLoggerProvider wilt `LogLevel.None`uitschakelen, gebruikt u:

**In code:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**In config:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Waarom hebben sommige ILogger-logboeken niet dezelfde eigenschappen als anderen?

Application Insights vangt en verzendt ILogger logs met behulp van dezelfde telemetrieConfiguratie die wordt gebruikt voor elke andere telemetrie. Maar er is een uitzondering. Telemetrieconfiguratie is standaard niet volledig ingesteld wanneer u zich aanmeldt bij **Program.cs** of **Startup.cs.** Logboeken van deze plaatsen hebben niet de standaardconfiguratie, dus ze worden niet alle telemetrieinitializers en telemetrieprocessors uitgevoerd.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Ik gebruik het standalone pakket Microsoft.Extensions.Logging.ApplicationInsights, en ik wil een aantal extra aangepaste telemetrie handmatig registreren. Hoe moet ik dat doen?

Wanneer u het standalone `TelemetryClient` pakket gebruikt, wordt niet geïnjecteerd in de DI-container, dus u moet een nieuw exemplaar van `TelemetryClient` maken en dezelfde configuratie gebruiken als de loggerprovider gebruikt, zoals de volgende code laat zien. Dit zorgt ervoor dat dezelfde configuratie wordt gebruikt voor alle aangepaste telemetrie en telemetrie van ILogger.

```csharp
public class MyController : ApiController
{
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Als u het Microsoft.ApplicationInsights.AspNetCore-pakket gebruikt om Application `TelemetryClient` Insights in te schakelen, wijzigt u deze code om rechtstreeks in de constructor te komen. Zie bijvoorbeeld [deze veelgestelde vragen.](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions)


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Welk Application Insights telemetrietype wordt geproduceerd uit ILogger-logboeken? Of waar kan ik iLogger logs zien in Application Insights?

ApplicationInsightsLoggerProvider legt ILogger logs vast en maakt tracetelemetry van hen. Als een uitzonderingsobject wordt doorgegeven aan de methode **Log()** op ILogger, wordt *ExceptionTelemetry* gemaakt in plaats van TraceTelemetry. Deze telemetrie-items kunnen worden gevonden op dezelfde plaatsen als elke andere TraceTelemetry of ExceptionTelemetry voor Application Insights, inclusief portal, analytics of Visual Studio local debugger.

Als u liever altijd TraceTelemetry verzendt, gebruikt u dit fragment:```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Ik heb de SDK niet geïnstalleerd en ik gebruik de Azure Web Apps-extensie om Application Insights in te schakelen voor mijn ASP.NET Core-toepassingen. Hoe gebruik ik de nieuwe provider? 

De toepassingsinzichten-extensie in Azure Web Apps maakt gebruik van de nieuwe provider. U de filterregels wijzigen in het *appsettings.json-bestand* voor uw toepassing.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Ik gebruik het standalone pakket Microsoft.Extensions.Logging.ApplicationInsights en maak Application Insights provider in staat door builder te **bellen. AddApplicationInsights("ikey")**. Is er een optie om een instrumentatiesleutel uit de configuratie te halen?


Wijzig Program.cs en appsettings.json als volgt:

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   Relevante afdeling `appsettings.json`van :

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Deze code is alleen vereist wanneer u een zelfstandige logging provider gebruikt. Voor regelmatige Application Insights-monitoring wordt de instrumentatiesleutel automatisch geladen vanuit het configuratiepad *ApplicationInsights: Instrumentationkey.* Appsettings.json moet er als volgt uitzien:

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over:

* [Inloggen ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [.NET trace logs in Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
