---
title: Azure-toepassing inzichten voor ASP.NET Core toepassingen | Microsoft Docs
description: Bewaak ASP.NET Core webtoepassingen voor Beschik baarheid, prestaties en gebruik.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mbullwin
ms.openlocfilehash: 9e14a9f3f2f27112a591f14e9a93580f66aadef7
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169550"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights voor ASP.NET Core toepassingen

In dit artikel wordt beschreven hoe u Application Insights inschakelt voor een [ASP.net core](https://docs.microsoft.com/aspnet/core) toepassing. Wanneer u de instructies in dit artikel hebt voltooid, verzamelt Application Insights aanvragen, afhankelijkheden, uitzonde ringen, prestatie meter items, heartbeats en logboeken van uw ASP.NET Core toepassing.

Het voor beeld dat hier wordt gebruikt, is een [MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) - `netcoreapp2.2`toepassing die gericht is. U kunt deze instructies Toep assen op alle ASP.NET Core-toepassingen.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

Met de [Application INSIGHTS SDK voor ASP.net core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) kunt u uw toepassingen bewaken, ongeacht waar of hoe ze worden uitgevoerd. Als uw toepassing wordt uitgevoerd en netwerk verbinding heeft met Azure, kan telemetrie worden verzameld. Application Insights bewaking wordt ondersteund overal wat .NET core ondersteunt. Ondersteunings dekkingen:
* **Besturingssysteem**: Windows, Linux of Mac.
* **Hosting methode**: In behandeling of out-of-process.
* **Implementatie methode**: Framework-afhankelijk of zelfstandig.
* **Webserver**: IIS (Internet Information Server) of Kestrel.
* **Hosting platform**: De functie Web Apps van Azure App Service, Azure VM, docker, Azure Kubernetes service (AKS), enzovoort.
* **IDE**: Visual Studio, VS code of opdracht regel.

> [!NOTE]
> Als u ASP.NET Core 3,0-Preview gebruikt, samen met Application Insights, moet u de [2.8.0-beta3-](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0-beta3) versie of hoger gebruiken. Dit is de enige versie die bekend is bij het werken met ASP.NET Core 3,0. Daarnaast wordt onboarding op basis van Visual Studio nog niet ondersteund voor ASP.NET Core 3,0-apps.

## <a name="prerequisites"></a>Vereisten

- Een functionerende ASP.NET Core-toepassing. Als u een ASP.NET Core-toepassing moet maken, volgt u deze [ASP.net core zelf studie](https://docs.microsoft.com/aspnet/core/getting-started/).
- Een geldige Application Insights instrumentatie sleutel. Deze sleutel is vereist voor het verzenden van telemetrie naar Application Insights. Als u een nieuwe Application Insights resource moet maken om een instrumentatie sleutel op te halen, raadpleegt u [een Application Insights resource maken](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Telemetrie van Application Insights server inschakelen (Visual Studio)

1. Open uw project in Visual Studio.

    > [!TIP]
    > Als u wilt, kunt u broncode beheer instellen voor uw project, zodat u alle wijzigingen kunt bijhouden die Application Insights maakt. Als u broncode beheer wilt inschakelen, selecteert u **bestand** > **toevoegen aan bron beheer**.

2. Selecteer **project** > **toevoegen Application Insights Telemetry**.

3. Selecteer **aan de slag**. De tekst van deze selectie kan variëren, afhankelijk van uw versie van Visual Studio. In een aantal eerdere versies wordt in plaats daarvan een knop **Start Free** gebruikt.

4. Selecteer uw abonnement. Selecteer vervolgens **resource** > **register**.

5. Nadat u Application Insights aan uw project hebt toegevoegd, controleert u of u de laatste stabiele versie van de SDK gebruikt. Ga naar **project** > **Manage NuGet packages** > **micro soft. ApplicationInsights. AspNetCore**. Als dat het geval is, kiest u **bijwerken**.

     ![Scherm opname van het selecteren van het Application Insights pakket dat moet worden bijgewerkt](./media/asp-net-core/update-nuget-package.png)

6. Als u de optionele tip hebt gevolgd en uw project aan broncode beheer hebt toegevoegd, gaat u naar**team Explorer** > -**wijzigingen** **weer geven** > . Selecteer vervolgens elk bestand om een diff-weer gave te zien van de wijzigingen die zijn aangebracht door Application Insights telemetrie.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Telemetrie van Application Insights server inschakelen (geen Visual Studio)

1. Installeer het [Application INSIGHTS SDK NuGet-pakket voor ASP.net core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). U wordt aangeraden altijd de nieuwste stabiele versie te gebruiken. Zoek volledige release opmerkingen voor de SDK op de [open-source github opslag plaats](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    In het volgende code voorbeeld ziet u de wijzigingen die moeten worden toegevoegd aan `.csproj` het bestand van het project.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. Voeg `services.AddApplicationInsightsTelemetry();` toe aan `ConfigureServices()` de methode in `Startup` uw klasse, zoals in dit voor beeld:

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. Stel de instrumentatie sleutel in.

    Hoewel u de instrumentatie sleutel als een argument kunt opgeven, `AddApplicationInsightsTelemetry`is het raadzaam om de instrumentatie sleutel in te stellen in de configuratie. In het volgende code voorbeeld ziet u hoe u een instrumentatie sleutel `appsettings.json`opgeeft in. Zorg ervoor `appsettings.json` dat tijdens het publiceren naar de hoofdmap van de toepassing is gekopieerd.

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    U kunt ook de instrumentatie sleutel opgeven in een van de volgende omgevings variabelen:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Bijvoorbeeld:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    `APPINSIGHTS_INSTRUMENTATIONKEY` Geeft doorgaans de instrumentatie sleutel voor toepassingen die zijn geïmplementeerd op Web apps.

    > [!NOTE]
    > Een instrumentatie sleutel die is opgegeven in code WINS via de `APPINSIGHTS_INSTRUMENTATIONKEY`omgevings variabele, die WINS over andere opties overneemt.

## <a name="run-your-application"></a>Uw toepassing uitvoeren

Voer de toepassing uit en maak er aanvragen aan. Telemetrie moet nu stromen naar Application Insights. De Application Insights SDK verzamelt automatisch de volgende telemetrie.

|Aanvragen/afhankelijkheden |Details|
|---------------|-------|
|Aanvragen | Inkomende webaanvragen voor uw toepassing. |
|HTTP of HTTPS | Aanroepen van `HttpClient`. |
|SQL | Aanroepen van `SqlClient`. |
|[Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) | Aanroepen van de Azure Storage-client. |
|[Event hubs-client-SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Versie 1.1.0 en hoger. |
|[ServiceBus-client-SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Versie 3.0.0 en hoger. |
|Azure Cosmos DB | Alleen automatisch bijgehouden als HTTP/HTTPS wordt gebruikt. De TCP-modus wordt niet vastgelegd Application Insights. |

### <a name="performance-counters"></a>Prestatiemeteritems

De ondersteuning voor [prestatie meter items](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) in ASP.net Core is beperkt:

* Met SDK-versies 2.4.1 en hoger worden prestatie meter items verzameld als de toepassing wordt uitgevoerd in azure Web Apps (Windows).
* Met SDK-versies 2.7.1 en hoger worden prestatie meter items verzameld als de toepassing wordt uitgevoerd `NETSTANDARD2.0` in Windows en doelen of hoger.
* Voor toepassingen die zijn gericht op de .NET Framework, worden alle versies van de SDK-prestatie meter items ondersteund.
* SDK-versies 2.8.0-beta3 en hoger ondersteunen CPU/geheugen-teller in Linux. Er wordt geen ander item ondersteund in Linux. De aanbevolen manier om systeem tellers op te halen in Linux (en andere niet-Windows-omgevingen) is met behulp van [EventCounters](#eventcounter)

### <a name="eventcounter"></a>Event Counter

[Event Counter](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md)is een platformoverschrijdende methode voor het publiceren en gebruiken van tellers in .NET/.net core. Hoewel deze functie voorheen bestond, waren er geen ingebouwde providers die deze items hebben gepubliceerd. Vanaf .NET Core 3,0 worden verschillende tellers gepubliceerd uit het vak, zoals CLR-tellers, ASP.NET Core meters enzovoort.

SDK-versies 2.8.0-beta3 en hoger ondersteunt het verzamelen van EventCounters. De SDK verzamelt standaard de volgende prestatie meter items en u kunt deze tellers opvragen in Metrics Explorer of door analyse query's te gebruiken in de tabel Performance Counter. De naam van de prestatie meter items heeft de indeling ' Category | Teller ".

|Categorie | Teller|
|---------------|-------|
|System. runtime | CPU-gebruik |
|System. runtime | Working-set |
|System. runtime | GC-heap-grootte |
|System. runtime | gen-0-GC-aantal |
|System. runtime | gen-1-GC-aantal |
|System. runtime | gen-2-GC-aantal |
|System. runtime | tijd-in-GC |
|System. runtime | gen-0-grootte |
|System. runtime | gen-1-grootte |
|System. runtime | gen-2-grootte |
|System. runtime | Loh-grootte |
|System. runtime | toewijzing-rate |
|System. runtime | assembly-aantal |
|System. runtime | uitzonde ring-aantal |
|System. runtime | thread pool-aantal threads |
|System. runtime | monitor-vergren delen-aantal conflicten |
|System. runtime | thread pool-lengte van wachtrij |
|System. runtime | thread pool-voltooid-items-aantal |
|System. runtime | actief-timer-aantal |
|Micro soft. AspNetCore. hosting | aanvragen per seconde |
|Micro soft. AspNetCore. hosting | Totaal-aanvragen |
|Micro soft. AspNetCore. hosting | huidige-aanvragen |
|Micro soft. AspNetCore. hosting | mislukte aanvragen |

### <a name="ilogger-logs"></a>ILogger-logboeken

[ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) -logboeken met `Warning` ernst of meer worden automatisch vastgelegd in SDK-versies 2.7.0-beta3 en hoger.

### <a name="live-metrics"></a>Live Metrics

Het kan enkele minuten duren voordat telemetrie in de portal wordt weer gegeven. Als u snel wilt controleren of alles werkt, kunt u het beste [Live metrische gegevens](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) gebruiken wanneer u aanvragen voor de actieve toepassing maakt.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Telemetrie aan de client zijde inschakelen voor webtoepassingen

De voor gaande stappen zijn voldoende om u te helpen bij het verzamelen van telemetrie aan de server zijde. Als uw toepassing onderdelen aan client zijde heeft, voert u de volgende stappen uit om te beginnen met het verzamelen van telemetrie van het [gebruik](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview).

1. Voeg `_ViewImports.cshtml`in een injectie toe:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. `<head>` Voeg `_Layout.cshtml` aan`HtmlHelper` het einde van de sectie in, maar vóór elk ander script. Als u een aangepaste Java script-telemetrie wilt rapporteren vanaf de pagina, moet u deze na dit fragment injecteren:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

De `.cshtml` bestands namen waarnaar eerder wordt verwezen, zijn afkomstig uit een standaard sjabloon voor MVC-toepassingen. Als u bewaking aan client zijde wilt inschakelen voor uw toepassing, moet het Java script-fragment uiteindelijk worden weer gegeven in `<head>` de sectie van elke pagina van de toepassing die u wilt bewaken. U kunt dit doel voor deze toepassings sjabloon bereiken door het Java script-fragment `_Layout.cshtml`toe te voegen aan. 

Als uw project niet bevat `_Layout.cshtml`, kunt u nog steeds [bewaking aan client zijde](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring)toevoegen. U kunt dit doen door het Java script-fragment toe te voegen aan een `<head>` gelijkwaardig bestand dat de van alle pagina's in uw app beheert. Of u kunt het fragment toevoegen aan meerdere pagina's, maar deze oplossing is lastig te onderhouden. het wordt over het algemeen niet aanbevolen.

## <a name="configure-the-application-insights-sdk"></a>De Application Insights SDK configureren

U kunt de Application Insights SDK voor ASP.NET Core aanpassen om de standaard configuratie te wijzigen. Gebruikers van de Application Insights ASP.NET SDK kunnen bekend zijn met het wijzigen van de `ApplicationInsights.config` configuratie door te `TelemetryConfiguration.Active`gebruiken of door te wijzigen. U wijzigt de configuratie anders voor ASP.NET Core. Voeg de ASP.NET Core SDK aan de toepassing toe en configureer deze met behulp van ASP.NET Core ingebouwde [afhankelijkheids injectie](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Maak bijna alle configuratie wijzigingen in de `ConfigureServices()` methode van uw `Startup.cs` klasse, tenzij u iets anders omgaat. De volgende secties bieden meer informatie.

> [!NOTE]
> In ASP.net core toepassingen wordt het wijzigen van de `TelemetryConfiguration.Active` configuratie door wijzigen niet ondersteund.

### <a name="using-applicationinsightsserviceoptions"></a>ApplicationInsightsServiceOptions gebruiken

U kunt enkele algemene instellingen wijzigen door door te `ApplicationInsightsServiceOptions` geven `AddApplicationInsightsTelemetry`aan, zoals in dit voor beeld:

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    }
```

Volledige lijst met instellingen in`ApplicationInsightsServiceOptions`

|Instelling | Description | Standaard
|---------------|-------|-------
|EnableQuickPulseMetricStream | Functie LiveMetrics in-of uitschakelen | true
|EnableAdaptiveSampling | Adaptieve steek proeven in-/uitschakelen | true
|EnableHeartbeat | De functie heartbeats inschakelen/uitschakelen, die periodiek (standaard 15 minuten) een aangepaste metriek met de naam ' HeartBeatState ' verzendt met informatie over de runtime, zoals .NET-versie, informatie over de Azure-omgeving, indien van toepassing, enzovoort. | true
|AddAutoCollectedMetricExtractor | Schakel AutoCollectedMetrics extractor in/uit. Dit is een TelemetryProcessor die vooraf geaggregeerde metrische gegevens over aanvragen/afhankelijkheden verzendt voordat steek proeven worden uitgevoerd. | true
|RequestCollectionOptions.TrackExceptions | Rapportage van niet-verwerkte uitzonderings tracering door de verzamelings module voor aanvragen in-of uitschakelen. | False in netstandard 2.0 (omdat uitzonde ringen worden bijgehouden met ApplicationInsightsLoggerProvider), anders waar.

Zie de [Configureer bare instellingen in `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) voor de meest recente lijst.

### <a name="sampling"></a>Steekproef nemen

De Application Insights SDK voor ASP.NET Core ondersteunt zowel vaste als adaptieve steek proeven. Adaptieve steek proeven zijn standaard ingeschakeld. 

Zie [adaptieve steek proeven voor ASP.net core toepassingen configureren](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)voor meer informatie.

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers toevoegen

Gebruik de [initialisatie functies](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) voor telemetrie wanneer u algemene eigenschappen wilt definiëren die worden verzonden met alle telemetrie.

Voeg nieuwe `TelemetryInitializer` toe aan de `DependencyInjection` container, zoals in de volgende code wordt weer gegeven. De SDK neemt automatisch een `TelemetryInitializer` selectie op die wordt toegevoegd aan de `DependencyInjection` container.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers verwijderen

De initialisatie functies voor telemetrie zijn standaard aanwezig. Als u alle of specifieke telemetrie-initialisatie functies wilt verwijderen, gebruikt u de volgende `AddApplicationInsightsTelemetry()`voorbeeld code nadat u hebt gebeld.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

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

U kunt aangepaste telemetrie-processors `TelemetryConfiguration` toevoegen aan met behulp `AddApplicationInsightsTelemetryProcessor` van `IServiceCollection`de uitbreidings methode op. U kunt telemetrie-processors gebruiken in [Geavanceerde filter scenario's](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) voor meer directe controle over wat er is opgenomen of uitgesloten van de telemetrie die u naar de Application Insights-service stuurt. Gebruik het volgende voor beeld.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Standaard TelemetryModules configureren of verwijderen

Application Insights maakt gebruik van telemetrie-modules om automatisch nuttige telemetrie te verzamelen over specifieke workloads zonder hand matige tracking door de gebruiker.

De volgende automatische verzamelings modules zijn standaard ingeschakeld. Deze modules zijn verantwoordelijk voor het automatisch verzamelen van telemetrie. U kunt ze uitschakelen of configureren om het standaard gedrag te wijzigen.

* `RequestTrackingTelemetryModule`-Verzamelt RequestTelemetry van inkomende webaanvragen.
* `DependencyTrackingTelemetryModule`-Verzamelt DependencyTelemetry van uitgaande HTTP-aanroepen en SQL-aanroepen.
* `PerformanceCollectorModule`-Verzamelt Windows Performance Counters.
* `QuickPulseTelemetryModule`-Verzamelt telemetrie voor weer gave in de portal voor Live Metrics.
* `AppServicesHeartbeatTelemetryModule`-Hiermee worden kern maten verzameld (die als aangepaste metrische gegevens worden verzonden), over Azure App Service omgeving waarin de toepassing wordt gehost.
* `AzureInstanceMetadataTelemetryModule`-Hiermee worden kern maten (die worden verzonden als aangepaste metrische gegevens) verzameld, over de Azure VM-omgeving waar de toepassing wordt gehost.
* `EventCounterCollectionModule`-Verzamelt [EventCounters.](#eventcounter). Deze module is een nieuwe functie die beschikbaar is in SDK-versie 2.8.0-beta3 en hoger.

Als u een standaard `TelemetryModule`instelling wilt configureren, gebruikt `ConfigureTelemetryModule<T>` u `IServiceCollection`de uitbreidings methode op, zoals wordt weer gegeven in het volgende voor beeld.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures DependencyTrackingTelemetryModule.
        // Similarly, any other default modules can be configured.
        services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
                        {
                            module.EnableW3CHeadersInjection = true;
                        });

        // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
                            (module, o) =>
                            {
                                module.Counters.Clear();
                                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
                            }
                        );

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-a-telemetry-channel"></a>Een telemetrie-kanaal configureren

Het standaard kanaal is `ServerTelemetryChannel`. U kunt deze overschrijven zoals in het volgende voor beeld wordt weer gegeven.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>Telemetrie dynamisch uitschakelen

Als u telemetrie voorwaardelijk en dynamisch wilt uitschakelen, kunt u het exemplaar `TelemetryConfiguration` met ASP.net core afhankelijkheids container voor injecties overal in uw code omzetten en `DisableTelemetry` er een vlag op instellen.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

Houd er rekening mee dat met de bovenstaande niet wordt voor komen dat modules voor automatische verzameling telemetrie verzamelen. Alleen het verzenden van telemetrie naar Application Insights wordt uitgeschakeld met de bovenstaande methode. Als een bepaalde module voor automatische verzameling niet gewenst is, is het de beste om [de telemetrie-module te verwijderen](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Hoe kan ik telemetrie traceren die niet automatisch wordt verzameld?

Haal een exemplaar op `TelemetryClient` van met behulp van constructor-injectie en roep de `TrackXXX()` vereiste methode aan. Het is niet raadzaam om `TelemetryClient` nieuwe instanties te maken in een ASP.net core-toepassing. Een singleton-exemplaar `TelemetryClient` van is al geregistreerd in `DependencyInjection` de container, die `TelemetryConfiguration` met de rest van de telemetrie deelt. Het maken van `TelemetryClient` een nieuw exemplaar wordt alleen aanbevolen als er een configuratie nodig is die gescheiden is van de rest van de telemetrie. 

In het volgende voor beeld ziet u hoe u extra telemetrie van een controller kunt bijhouden.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

Voor meer informatie over aangepaste gegevens rapportage in Application Insights raadpleegt u Application Insights data API-naslag informatie voor [aangepaste metrieken](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Sommige Visual Studio-sjablonen hebben de uitbreidings methode UseApplicationInsights () gebruikt op IWebHostBuilder om Application Insights in te scha kelen. Is dit gebruik nog geldig?

Ja, het inschakelen van Application Insights met deze methode is geldig. Deze techniek wordt gebruikt in Visual Studio-onboarding en in de Web Apps-extensies. We raden u echter aan `services.AddApplicationInsightsTelemetry()` gebruik te maken van Overloads voor het beheren van een bepaalde configuratie. Beide methoden doen hetzelfde als intern, dus als u geen aangepaste configuratie hoeft toe te passen, kunt u een beide-methode aanroepen.

`IWebHostBuilder`wordt vervangen door `IHostBuilder` in ASP.net Core 3,0 en om Verwar ring te voor komen, wordt Application Insights versie 2.8.0-beta3 de methode UseApplicationInsights () gemarkeerd als verouderd en wordt deze verwijderd uit de volgende primaire versie.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Ik implementeer mijn ASP.NET Core-toepassing naar Web Apps. Moet ik de uitbrei ding van de Application Insights nog steeds inschakelen vanuit Web Apps?

Als de SDK tijdens het bouwen is geïnstalleerd, zoals wordt weer gegeven in dit artikel, hoeft u de [uitbrei ding Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) niet in te scha kelen vanuit de app Service Portal. Zelfs als de uitbrei ding is geïnstalleerd, wordt deze weer uitgeschakeld wanneer wordt gedetecteerd dat de SDK al aan de toepassing is toegevoegd. Als u Application Insights van de uitbrei ding inschakelt, hoeft u de SDK niet te installeren en bij te werken. Maar als u Application Insights inschakelt door de volg instructies in dit artikel, hebt u meer flexibiliteit omdat:

   * Application Insights telemetrie blijft werken in:
       * Alle besturings systemen, waaronder Windows, Linux en Mac.
       * Alle publicatie modi, inclusief zelfstandig of Framework afhankelijk.
       * Alle doel raamwerken, met inbegrip van de volledige .NET Framework.
       * Alle hosting opties, waaronder Web Apps, Vm's, Linux, containers, Azure Kubernetes-service en niet-Azure-hosting.
       * Alle .NET Core-versies inclusief Preview-versies.
   * U kunt telemetrie lokaal zien wanneer u fouten opspoort vanuit Visual Studio.
   * U kunt aanvullende aangepaste telemetrie bijhouden met behulp van de `TrackXXX()` API.
   * U hebt volledige controle over de configuratie.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Kan ik Application Insights bewaking inschakelen met behulp van hulpprogram ma's zoals Status Monitor?

Nee. [Status monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) en [status monitor v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) ondersteunen momenteel alleen ASP.net 4. x.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Is Application Insights automatisch ingeschakeld voor mijn ASP.NET Core 2,0-toepassing?

De `Microsoft.AspNetCore.All` 2,0 metapackage bevatte de Application Insights SDK (versie 2.1.0). Als u de toepassing uitvoert onder Visual Studio Debugger, maakt Visual Studio Application Insights en wordt telemetrie lokaal in de IDE zelf weer gegeven. Er is geen telemetrie naar de Application Insights-service verzonden, tenzij er een instrumentatie sleutel is opgegeven. U wordt aangeraden de instructies in dit artikel te volgen om Application Insights in te scha kelen, zelfs voor 2,0-apps.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Als ik mijn toepassing in Linux uitvoer, worden alle functies ondersteund?

Ja. De functie ondersteuning voor de SDK is op alle platforms hetzelfde, met de volgende uitzonde ringen:

* Prestatie meter items worden alleen ondersteund in Windows.
* Hoewel `ServerTelemetryChannel` de toepassing standaard is ingeschakeld in Linux of MacOS, maakt het kanaal niet automatisch een lokale opslagmap om telemetrie tijdelijk te blijven als er netwerk problemen zijn. Vanwege deze beperking gaat telemetrie verloren als er tijdelijke problemen zijn met het netwerk of de server. U kunt dit probleem omzeilen door een lokale map voor het kanaal te configureren:

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
        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="open-source-sdk"></a>Open-Source-SDK

[Lees en bijdragen aan de code](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Video

- Bekijk deze externe stapsgewijze video om [Application Insights te configureren met .net core en Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) .
- Bekijk deze externe stap-voor-stap-video om [Application Insights te configureren met de .net core-en Visual Studio code](https://youtu.be/ygGt84GDync) .

## <a name="next-steps"></a>Volgende stappen

* [Verken de gebruikers stromen](../../azure-monitor/app/usage-flows.md) om te begrijpen hoe gebruikers door uw app navigeren.
* [Configureer een momentopname verzameling](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) om de status van de bron code en variabelen weer te geven op het moment dat een uitzonde ring wordt gegenereerd.
* [Gebruik de API](../../azure-monitor/app/api-custom-events-metrics.md) om uw eigen gebeurtenissen en metrische gegevens te verzenden voor een gedetailleerde weer gave van de prestaties en het gebruik van uw app.
* Gebruik [beschikbaarheids tests](../../azure-monitor/app/monitor-web-app-availability.md) om uw app voortdurend van over de hele wereld te controleren.
* [Afhankelijkheids injectie in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
