---
title: .NET-traceer logboeken verkennen met ILogger-Azure-toepassing Insights
description: Voor beelden van het gebruik van de Azure-toepassing Insights ILogger-provider met ASP.NET Core-en console toepassingen.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 2c97c79229c6f136c154169253f2299b7756a105
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276008"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider voor .NET core ILogger-logboeken

ASP.NET Core ondersteunt een logboek registratie-API die werkt met verschillende soorten ingebouwde en registratie providers van derden. Logboek registratie wordt uitgevoerd door het aanroepen van het **logboek ()** of een variant op *ILogger* -exemplaren. In dit artikel wordt gedemonstreerd hoe u *ApplicationInsightsLoggerProvider* kunt gebruiken voor het vastleggen van ILogger-Logboeken in console-en ASP.net core toepassingen. In dit artikel wordt ook beschreven hoe ApplicationInsightsLoggerProvider integreert met andere Application Insights telemetrie.
Zie [logboek registratie in ASP.net core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)voor meer informatie.

## <a name="aspnet-core-applications"></a>ASP.NET Core toepassingen

ApplicationInsightsLoggerProvider is standaard ingeschakeld in [micro soft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.1 (en hoger) wanneer u een regel matige Application Insights bewaking inschakelt via een van de standaard methoden:

- Door het aanroepen van de **UseApplicationInsights** -extensie methode op IWebHostBuilder
- Door het aanroepen van de **AddApplicationInsightsTelemetry** -extensie methode op IServiceCollection

ILogger logboeken die ApplicationInsightsLoggerProvider Capture ondervinden, zijn onderhevig aan dezelfde configuratie als andere telemetrie die worden verzameld. Ze hebben dezelfde set TelemetryInitializers en TelemetryProcessors, gebruiken dezelfde TelemetryChannel en worden gecorreleerd en gesampled op dezelfde manier als andere telemetrie. Als u versie 2.7.1 of hoger gebruikt, hoeft u geen actie te ondernemen voor het vastleggen van ILogger-Logboeken.

Alleen *waarschuwingen* of hogere ILogger-Logboeken (uit alle [Categorieën](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-3.1#log-category)) worden standaard naar Application Insights verzonden. Maar u kunt [filters toep assen om dit gedrag te wijzigen](#control-logging-level). Er zijn aanvullende stappen vereist voor het vastleggen van ILogger-logboeken van **Program.cs** of **Startup.cs**. (Zie [ILogger-logboeken vastleggen vanuit startup.cs en Program.cs in ASP.net core-toepassingen](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Als u een eerdere versie van micro soft. ApplicationInsights. AspNet-SDK gebruikt of als u alleen ApplicationInsightsLoggerProvider zonder andere Application Insights bewaking wilt gebruiken, gebruikt u de volgende procedure:

1. Het NuGet-pakket installeren:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Wijzig **Program.cs** zoals hier wordt weer gegeven:

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

De code in stap 2 configureert `ApplicationInsightsLoggerProvider`. De volgende code toont een voor beeld-controller klasse, die `ILogger` gebruikt voor het verzenden van Logboeken. De logboeken worden vastgelegd door Application Insights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>ILogger-logboeken vastleggen vanuit Startup.cs en Program.cs in ASP.NET Core-Apps

> [!NOTE]
> In ASP.NET Core 3,0 en hoger is het niet meer mogelijk om `ILogger` in te voeren in Startup.cs en Program.cs. Zie https://github.com/aspnet/Announcements/issues/353 voor meer informatie.

Met de nieuwe ApplicationInsightsLoggerProvider kunt u logboeken van vroegtijdig vastleggen in de pijp lijn voor het opstarten van de toepassing. Hoewel ApplicationInsightsLoggerProvider automatisch wordt ingeschakeld in Application Insights (te beginnen met versie 2.7.1), is er geen instrumentatie sleutel ingesteld tot later in de pijp lijn. Dit betekent dat alleen logboeken van de **controller**/overige klassen worden vastgelegd. Als u elk logboek wilt vastleggen dat begint met **Program.cs** en **Startup.cs** zelf, moet u expliciet een instrumentatie sleutel voor ApplicationInsightsLoggerProvider inschakelen. *TelemetryConfiguration* is ook niet volledig ingesteld wanneer u zich aanmeldt vanuit **Program.cs** of **Startup.cs** . Deze logboeken hebben dus een minimale configuratie waarbij InMemoryChannel, geen steek proeven worden gebruikt en geen standaard telemetrie-initialisatie functies of-processors.

In de volgende voor beelden ziet u deze mogelijkheid met **Program.cs** en **Startup.cs**.

#### <a name="example-programcs"></a>Voor beeld Program.cs

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

#### <a name="example-startupcs"></a>Voor beeld Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migreren vanaf de oude ApplicationInsightsLoggerProvider

Micro soft. ApplicationInsights. AspNet SDK-versies voordat 2.7.1 een logboek registratie provider ondersteunt die nu verouderd is. Deze provider is ingeschakeld via de uitbreidings methode **AddApplicationInsights ()** van ILoggerFactory. We raden u aan om te migreren naar de nieuwe provider. Dit omvat twee stappen:

1. Verwijder de aanroep *ILoggerFactory. AddApplicationInsights ()* uit de methode **Startup. configure ()** om dubbele logboek registratie te voor komen.
2. Filter regels in code opnieuw Toep assen, omdat deze niet worden geëerbiedigd door de nieuwe provider. Overloads van *ILoggerFactory. AddApplicationInsights ()* hebben mini maal loglevel-of filter functies nodig. Met de nieuwe provider wordt filteren onderdeel van het Framework voor logboek registratie. Het wordt niet gedaan door de Application Insights provider. Filters die worden verschaft via *ILoggerFactory. AddApplicationInsights ()* , moeten dus worden verwijderd. En er moeten filter regels worden gegeven door de instructies voor het vastleggen van het [registratie niveau](#control-logging-level) . Als u *appSettings. json* gebruikt voor het filteren van logboek registratie, blijft het werken met de nieuwe provider, omdat beide dezelfde provider alias gebruiken, *ApplicationInsights*.

U kunt nog steeds de oude provider gebruiken. (Deze wordt alleen verwijderd in een primaire versie wijziging op 3. *xx*.) maar we raden u aan om de volgende redenen te migreren naar de nieuwe provider:

- De vorige provider heeft geen ondersteuning voor [logboek bereik](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). In de nieuwe provider worden eigenschappen van bereik automatisch toegevoegd als aangepaste eigenschappen aan de verzamelde telemetrie.
- Logboeken kunnen nu veel eerder in de opstart pijplijn van de toepassing worden vastgelegd. Logboeken van het **programma** en de **opstart** klassen kunnen nu worden vastgelegd.
- Met de nieuwe provider wordt filteren uitgevoerd op het niveau van het Framework zelf. U kunt Logboeken filteren op de Application Insights provider op dezelfde manier als voor andere providers, waaronder ingebouwde providers zoals console, debug, enzovoort. U kunt ook dezelfde filters toep assen op meerdere providers.
- In ASP.NET Core (2,0 en hoger), de aanbevolen manier om [logboek registratie providers in te scha kelen](https://github.com/aspnet/Announcements/issues/255) , is door gebruik te maken van uitbreidings methoden op ILoggingBuilder in **Program.cs** zelf.

> [!Note]
> De nieuwe provider is beschikbaar voor toepassingen die zijn gericht op netstandard 2.0 of hoger. Als uw toepassing oudere .NET Core-versies, zoals .NET Core 1,1, streeft naar de .NET Framework, kunt u de oude provider blijven gebruiken.

## <a name="console-application"></a>Console toepassing

> [!NOTE]
> Er is een nieuwe Application Insights SDK met de naam [micro soft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) die kan worden gebruikt om Application Insights (ILogger en andere Application Insights telemetrie) in te scha kelen voor elke console toepassing. U wordt aangeraden dit pakket en de bijbehorende instructies [hier](../../azure-monitor/app/worker-service.md)te gebruiken.

De volgende code toont een voor beeld van een console toepassing die is geconfigureerd om ILogger traceringen naar Application Insights te verzenden.

Geïnstalleerde pakketten:

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

In dit voor beeld wordt het zelfstandige pakket `Microsoft.Extensions.Logging.ApplicationInsights`gebruikt. Deze configuratie maakt standaard gebruik van de TelemetryConfiguration ' Bworden minimum ' voor het verzenden van gegevens naar Application Insights. De minimale waarde voor InMemoryChannel is het kanaal dat wordt gebruikt. Er zijn geen sampling-en geen standaard TelemetryInitializers. Dit gedrag kan worden overschreven voor een console toepassing, zoals in het volgende voor beeld wordt getoond.

Installeer dit aanvullende pakket:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

In de volgende sectie ziet u hoe u de standaard TelemetryConfiguration overschrijft met behulp van de **Services.\<methode TelemetryConfiguration > () configureren** . In dit voor beeld worden `ServerTelemetryChannel` en steek proeven ingesteld. Er wordt een aangepaste ITelemetryInitializer toegevoegd aan de TelemetryConfiguration.

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

## <a name="control-logging-level"></a>Logboek registratie niveau bepalen

De ASP.NET Core *ILogger* -infra structuur heeft een ingebouwd mechanisme voor het Toep assen van [logboek filtering](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). Hiermee kunt u de logboeken beheren die worden verzonden naar elke geregistreerde provider, met inbegrip van de Application Insights provider. Het filteren kan worden uitgevoerd in de configuratie (meestal met behulp van een bestand *appSettings. json* ) of in code. Deze faciliteit wordt in het Framework zelf verschaft. Het is niet specifiek voor de Application Insights provider.

In de volgende voor beelden worden filter regels toegepast op ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Filter regels maken in configuratie met appSettings. json

Voor ApplicationInsightsLoggerProvider is de alias van de provider `ApplicationInsights`. In het volgende gedeelte van het bestand *appSettings. json* worden logboeken geconfigureerd voor *waarschuwing* en hoger dan van alle categorieën en *fouten* en bovenstaande categorieën die beginnen met micro soft om naar `ApplicationInsightsLoggerProvider`te worden verzonden.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>Filter regels maken in code

Met het volgende code fragment worden logboeken geconfigureerd voor *waarschuwing* en hoger in alle categorieën en voor *fouten* en bovenstaande van categorieën die beginnen met micro soft om naar `ApplicationInsightsLoggerProvider`te worden verzonden. Deze configuratie is hetzelfde als in de vorige sectie van *appSettings. json*.

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

De [SDK van micro soft. ApplicationInsights. AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) bevat een ingebouwde ApplicationInsightsLoggerProvider (Microsoft. ApplicationInsights. AspNetCore. Logging. ApplicationInsightsLoggerProvider), die is ingeschakeld via **ILoggerFactory** -extensie methoden. Deze provider is gemarkeerd als verouderd van versie 2.7.1. Deze wordt volledig verwijderd in de volgende primaire versie wijziging. Het [micro soft. ApplicationInsights. AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) -pakket zelf is niet verouderd. Het is vereist om de bewaking van aanvragen, afhankelijkheden, enzovoort in te scha kelen.

De voorgestelde alternatieve versie is het nieuwe zelfstandige pakket [micro soft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), dat een verbeterde ApplicationInsightsLoggerProvider (micro soft. Extensions. Logging. ApplicationInsights. ApplicationInsightsLoggerProvider) en uitbreidings methoden bevat voor ILoggerBuilder om het in te scha kelen.

De SDK-versie 2.7.1 van [micro soft. ApplicationInsights. AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) maakt afhankelijk van het nieuwe pakket en zorgt ervoor dat ILogger automatisch worden vastgelegd.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Waarom worden sommige ILogger-logboeken twee keer weer gegeven in Application Insights?

Duplicatie kan optreden als u de oudere (nu verouderde) versie van ApplicationInsightsLoggerProvider hebt ingeschakeld door het aanroepen van `AddApplicationInsights` op `ILoggerFactory`. Controleer of uw **configuratie** methode het volgende heeft en verwijder deze:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Als u dubbele logboek registratie ondervindt wanneer u fouten opspoort in Visual Studio, stelt u `EnableDebugLogger` in op *False* in de code waarmee Application Insights wordt ingeschakeld. Deze duplicatie en correctie zijn alleen relevant wanneer u fout opsporing voor de toepassing uitvoert.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Ik heb een update uitgevoerd naar [micro soft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.1 en logboeken van ILogger worden automatisch vastgelegd. Hoe kan ik deze functie volledig uitschakelen?

Zie de sectie [registratie niveau beheren](../../azure-monitor/app/ilogger.md#control-logging-level) voor meer informatie over het filteren van Logboeken in het algemeen. Als u ApplicationInsightsLoggerProvider wilt uitschakelen, gebruikt u `LogLevel.None`:

**In code:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**In configuratie:**

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

Application Insights worden vastgelegd en ILogger-logboeken worden verzonden met behulp van dezelfde TelemetryConfiguration die worden gebruikt voor elke andere telemetrie. Maar er is een uitzonde ring. TelemetryConfiguration is standaard niet volledig ingesteld wanneer u zich aanmeldt vanuit **Program.cs** of **Startup.cs**. Logboeken van deze locaties hebben niet de standaard configuratie, zodat ze niet alle TelemetryInitializers en TelemetryProcessors uitvoeren.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Ik gebruik het zelfstandige pakket micro soft. Extensions. Logging. ApplicationInsights en ik wil een extra aangepaste telemetrie hand matig vastleggen. Hoe moet ik dit doen?

Wanneer u het zelfstandige pakket gebruikt, wordt `TelemetryClient` niet geïnjecteerd naar de DI-container. Daarom moet u een nieuw exemplaar van `TelemetryClient` maken en dezelfde configuratie gebruiken als de traceer provider, zoals in de volgende code wordt weer gegeven. Dit zorgt ervoor dat dezelfde configuratie wordt gebruikt voor alle aangepaste telemetrie en telemetrie van ILogger.

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
> Als u het pakket micro soft. ApplicationInsights. AspNetCore gebruikt om Application Insights in te scha kelen, wijzigt u deze code zodat `TelemetryClient` direct in de constructor wordt opgehaald. Zie [deze veelgestelde vragen](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions)voor een voor beeld.


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Wat Application Insights type telemetrie is gemaakt op basis van ILogger-logboeken? Of waar kan ik ILogger-logboeken zien in Application Insights?

ApplicationInsightsLoggerProvider legt ILogger-logboeken vast en maakt daar TraceTelemetry van. Als er een uitzonderings object wordt door gegeven aan de methode **log ()** op ILogger, wordt *ExceptionTelemetry* gemaakt in plaats van TraceTelemetry. Deze telemetrie-items kunnen op dezelfde plaatsen worden gevonden als andere TraceTelemetry of ExceptionTelemetry voor Application Insights, waaronder het portal, het analyse programma of het lokale fout opsporingsprogramma van Visual Studio.

Als u liever altijd TraceTelemetry wilt verzenden, gebruikt u dit fragment: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Ik heb de SDK niet geïnstalleerd en ik gebruik de Azure Web Apps-extensie om Application Insights in te scha kelen voor mijn ASP.NET Core toepassingen. De nieuwe provider Hoe kan ik gebruiken? 

De uitbrei ding van de Application Insights in azure Web Apps maakt gebruik van de nieuwe provider. U kunt de filter regels in het bestand *appSettings. json* voor uw toepassing wijzigen.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Ik gebruik het zelfstandige pakket micro soft. Extensions. Logging. ApplicationInsights om Application Insights provider in te scha kelen door opbouw functie aan te roepen **. AddApplicationInsights ("iKey")** . Is er een optie om een instrumentatie sleutel op te halen uit de configuratie?


Wijzig Program.cs en appSettings. json als volgt:

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

   Relevante sectie van `appsettings.json`:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Deze code is alleen vereist wanneer u een zelfstandige logboek registratie provider gebruikt. De instrumentatie sleutel wordt voor regel matige Application Insights bewaking automatisch geladen vanuit het configuratiepad *ApplicationInsights: Instrumentationkey*. AppSettings. json moet er als volgt uitzien:

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

* [Aanmelden ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [.NET-traceer Logboeken in Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
