---
title: Azure-toepassing inzichten voor ASP.NET Core toepassingen | Microsoft Docs
description: Bewaak ASP.NET Core webtoepassingen voor Beschik baarheid, prestaties en gebruik.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 9c7c2e22d2befb503a388df1fa8a42c3d6eb07c5
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652778"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights voor ASP.NET Core toepassingen

In dit artikel wordt beschreven hoe u Application Insights inschakelt voor een [ASP.net core](https://docs.microsoft.com/aspnet/core) toepassing. Wanneer u de instructies in dit artikel hebt voltooid, verzamelt Application Insights aanvragen, afhankelijkheden, uitzonde ringen, prestatie meter items, heartbeats en logboeken van uw ASP.NET Core toepassing.

Het voor beeld dat hier wordt gebruikt, is een [MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) - `netcoreapp3.0`toepassing die gericht is. U kunt deze instructies Toep assen op alle ASP.NET Core-toepassingen.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

Met de [Application INSIGHTS SDK voor ASP.net core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) kunt u uw toepassingen bewaken, ongeacht waar of hoe ze worden uitgevoerd. Als uw toepassing wordt uitgevoerd en netwerk verbinding heeft met Azure, kan telemetrie worden verzameld. Application Insights bewaking wordt ondersteund overal wat .NET core ondersteunt. Ondersteunings dekkingen:
* **Besturings systeem**: Windows, Linux of Mac.
* **Hosting methode**: in verwerking of out-of-process.
* **Implementatie methode**: Framework-afhankelijk of zelfstandig.
* **Webserver**: IIS (Internet Information Server) of Kestrel.
* **Hosting platform**: de web apps functie van Azure app service, Azure VM, docker, Azure Kubernetes service (AKS), enzovoort.
* **.Net core runtime-versie**: 1. xx, 2. xx of 3. xx
* **IDE**: Visual Studio, VS code of opdracht regel.

> [!NOTE]
> ASP.NET Core 3. X vereist [Application Insights 2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) of hoger.

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
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
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

    * `APPINSIGHTS_INSTRUMENTATIONKEY`wordt doorgaans gebruikt in [Azure web apps](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps?tabs=net), maar kan ook worden gebruikt op alle plaatsen waar deze SDK wordt ondersteund. (Als u de bewaking van web-apps met code uitvoert, is deze indeling vereist als u geen verbindings reeksen gebruikt.)

    In plaats van het instellen van instrumentatie sleutels kunt u nu ook [verbindings reeksen](https://docs.microsoft.com/azure/azure-monitor/app/sdk-connection-string?tabs=net)gebruiken.

    > [!NOTE]
    > Een instrumentatie sleutel die is opgegeven in code WINS via de `APPINSIGHTS_INSTRUMENTATIONKEY`omgevings variabele, die WINS over andere opties overneemt.

## <a name="run-your-application"></a>Uw toepassing uitvoeren

Voer de toepassing uit en maak er aanvragen aan. Telemetrie moet nu stromen naar Application Insights. De Application Insights SDK verzamelt automatisch inkomende webaanvragen voor uw toepassing, samen met de volgende telemetrie.

### <a name="live-metrics"></a>Live Metrics

[Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) kunnen worden gebruikt om snel te controleren of Application Insights bewaking op de juiste wijze is geconfigureerd. Het kan enkele minuten duren voordat telemetrie in de portal en de analyse wordt weer gegeven. met Live metrische gegevens wordt het CPU-gebruik van het actieve proces in bijna realtime weer gegeven. Het kan ook andere telemetrie weer geven, zoals aanvragen, afhankelijkheden, traceringen, enzovoort.

### <a name="ilogger-logs"></a>ILogger-logboeken

Logboeken die worden `ILogger` verzonden via Ernst `Warning` of meer, worden automatisch vastgelegd. Volg de [ILogger-documenten](ilogger.md#control-logging-level) om aan te passen welke logboek niveaus worden vastgelegd door Application Insights.

### <a name="dependencies"></a>Afhankelijkheden

De afhankelijkheids verzameling is standaard ingeschakeld. In [Dit](asp-net-dependencies.md#automatically-tracked-dependencies) artikel worden de afhankelijkheden beschreven die automatisch worden verzameld en bevatten de stappen voor het hand matig bijhouden.

### <a name="performance-counters"></a>Prestatiemeteritems

De ondersteuning voor [prestatie meter items](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) in ASP.net Core is beperkt:

* Met SDK-versies 2.4.1 en hoger worden prestatie meter items verzameld als de toepassing wordt uitgevoerd in azure Web Apps (Windows).
* Met SDK-versies 2.7.1 en hoger worden prestatie meter items verzameld als de toepassing wordt uitgevoerd `NETSTANDARD2.0` in Windows en doelen of hoger.
* Voor toepassingen die zijn gericht op de .NET Framework, worden alle versies van de SDK-prestatie meter items ondersteund.
* SDK-versies 2.8.0 en hoger ondersteunen CPU/geheugen teller in Linux. Er wordt geen ander item ondersteund in Linux. De aanbevolen manier om systeem tellers op te halen in Linux (en andere niet-Windows-omgevingen) is met behulp van [EventCounters](#eventcounter)

### <a name="eventcounter"></a>Event Counter

`EventCounterCollectionModule`is standaard ingeschakeld en er wordt een standaardset tellers van .NET Core 3. X-apps verzameld. De [Event Counter](eventcounters.md) zelf studie bevat de standaardset met verzamelde items. Het bevat ook instructies voor het aanpassen van de lijst.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Telemetrie aan de client zijde inschakelen voor webtoepassingen

De voor gaande stappen zijn voldoende om u te helpen bij het verzamelen van telemetrie aan de server zijde. Als uw toepassing onderdelen aan client zijde heeft, voert u de volgende stappen uit om te beginnen met het verzamelen van [telemetrie](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)van het gebruik.

1. Voeg `_ViewImports.cshtml`in een injectie toe:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. Voeg `_Layout.cshtml` `HtmlHelper` aan het einde van de `<head>` sectie in, maar vóór elk ander script. Als u een aangepaste Java script-telemetrie wilt rapporteren vanaf de pagina, moet u deze na dit fragment injecteren:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```
    
U kunt de `FullScript` de `ScriptBody` -oplossing ook gebruiken vanaf SDK v 2.14. Gebruik deze instelling als u de `<script>` tag moet beheren om een beveiligings beleid voor inhoud in te stellen:

```cshtml
 <script> // apply custom changes to this script tag.
     @Html.Raw(JavaScriptSnippet.ScriptBody)
 </script>
```

De `.cshtml` bestands namen waarnaar eerder wordt verwezen, zijn afkomstig uit een standaard sjabloon voor MVC-toepassingen. Als u bewaking aan client zijde wilt inschakelen voor uw toepassing, moet het Java script-fragment uiteindelijk worden weer gegeven in `<head>` de sectie van elke pagina van de toepassing die u wilt bewaken. U kunt dit doel voor deze toepassings sjabloon bereiken door het Java script-fragment `_Layout.cshtml`toe te voegen aan. 

Als uw project niet bevat `_Layout.cshtml`, kunt u nog steeds [bewaking aan client zijde](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring)toevoegen. U kunt dit doen door het Java script-fragment toe te voegen aan een `<head>` gelijkwaardig bestand dat de van alle pagina's in uw app beheert. Of u kunt het fragment toevoegen aan meerdere pagina's, maar deze oplossing is lastig te onderhouden. het wordt over het algemeen niet aanbevolen.

## <a name="configure-the-application-insights-sdk"></a>De Application Insights SDK configureren

U kunt de Application Insights SDK voor ASP.NET Core aanpassen om de standaard configuratie te wijzigen. Gebruikers van de Application Insights ASP.NET SDK kunnen bekend zijn met het wijzigen van de `ApplicationInsights.config` configuratie door te `TelemetryConfiguration.Active`gebruiken of door te wijzigen. U wijzigt de configuratie anders voor ASP.NET Core. Voeg de ASP.NET Core SDK aan de toepassing toe en configureer deze met behulp van ASP.NET Core ingebouwde [afhankelijkheids injectie](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Maak bijna alle configuratie wijzigingen in de `ConfigureServices()` methode van uw `Startup.cs` klasse, tenzij u iets anders omgaat. De volgende secties bieden meer informatie.

> [!NOTE]
> In ASP.NET Core toepassingen wordt het wijzigen van de `TelemetryConfiguration.Active` configuratie door wijzigen niet ondersteund.

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

|Instelling | Beschrijving | Standaard
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | Inschakelen/uitschakelen`PerformanceCounterCollectionModule` | waar
|EnableRequestTrackingTelemetryModule   | Inschakelen/uitschakelen`RequestTrackingTelemetryModule` | waar
|EnableEventCounterCollectionModule   | Inschakelen/uitschakelen`EventCounterCollectionModule` | waar
|EnableDependencyTrackingTelemetryModule   | Inschakelen/uitschakelen`DependencyTrackingTelemetryModule` | waar
|EnableAppServicesHeartbeatTelemetryModule  |  Inschakelen/uitschakelen`AppServicesHeartbeatTelemetryModule` | waar
|EnableAzureInstanceMetadataTelemetryModule   |  Inschakelen/uitschakelen`AzureInstanceMetadataTelemetryModule` | waar
|EnableQuickPulseMetricStream | Functie LiveMetrics in-of uitschakelen | waar
|EnableAdaptiveSampling | Adaptieve steek proeven in-/uitschakelen | waar
|EnableHeartbeat | De functie heartbeats inschakelen/uitschakelen, die periodiek (standaard 15-minuten) een aangepaste metriek met de naam ' HeartbeatState ' verzendt met informatie over de runtime, zoals .NET-versie, informatie over de Azure-omgeving, indien van toepassing, enzovoort. | waar
|AddAutoCollectedMetricExtractor | Schakel AutoCollectedMetrics extractor in/uit. Dit is een TelemetryProcessor die vooraf geaggregeerde metrische gegevens over aanvragen/afhankelijkheden verzendt voordat steek proeven worden uitgevoerd. | waar
|RequestCollectionOptions.TrackExceptions | Rapportage van niet-verwerkte uitzonderings tracering door de verzamelings module voor aanvragen in-of uitschakelen. | False in netstandard 2.0 (omdat uitzonde ringen worden bijgehouden met ApplicationInsightsLoggerProvider), anders waar.

Zie de [Configureer bare instellingen in `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) voor de meest recente lijst.

### <a name="sampling"></a>Steekproeven

De Application Insights SDK voor ASP.NET Core ondersteunt zowel vaste als adaptieve steek proeven. Adaptieve steek proeven zijn standaard ingeschakeld. 

Zie [adaptieve steek proeven voor ASP.net core toepassingen configureren](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)voor meer informatie.

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers toevoegen

Gebruik de [initialisatie functies voor telemetrie](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) wanneer u algemene eigenschappen wilt definiëren die worden verzonden met alle telemetrie.

Voeg nieuwe `TelemetryInitializer` toe aan de `DependencyInjection` container, zoals in de volgende code wordt weer gegeven. De SDK neemt automatisch een `TelemetryInitializer` selectie op die wordt toegevoegd aan de `DependencyInjection` container.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers verwijderen

De initialisatie functies voor telemetrie zijn standaard aanwezig. Als u alle of specifieke telemetrie-initialisatie functies wilt verwijderen, gebruikt *after* u de volgende `AddApplicationInsightsTelemetry()`voorbeeld code nadat u hebt gebeld.

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

U kunt aangepaste telemetrie-processors `TelemetryConfiguration` toevoegen aan met behulp `AddApplicationInsightsTelemetryProcessor` van `IServiceCollection`de uitbreidings methode op. U kunt telemetrie-processors gebruiken in [Geavanceerde filter scenario's](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer). Gebruik het volgende voor beeld.

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
* `AppServicesHeartbeatTelemetryModule`-Hiermee worden kern maten (die worden verzonden als aangepaste metrische gegevens) verzameld over Azure App Service omgeving waarin de toepassing wordt gehost.
* `AzureInstanceMetadataTelemetryModule`-Hiermee worden kern maten (die worden verzonden als aangepaste metrische gegevens) verzameld over de Azure VM-omgeving waarin de toepassing wordt gehost.
* `EventCounterCollectionModule`-Verzamelt [EventCounters.](eventcounters.md) Deze module is een nieuwe functie die beschikbaar is in SDK-versie 2.8.0 en hoger.

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

Vanaf 2.12.2- [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) versie bevat een eenvoudige optie om een van de standaard modules uit te scha kelen.

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

Met de bovenstaande optie wordt niet voor komen dat modules voor automatische verzameling telemetrie verzamelen. Alleen het verzenden van telemetrie naar Application Insights wordt uitgeschakeld met de bovenstaande methode. Als een bepaalde module voor automatische verzameling niet gewenst is, kunt u [de telemetrie-module het beste verwijderen](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="does-application-insights-support-aspnet-core-3x"></a>Ondersteunt Application Insights ASP.NET Core 3. X?

Ja. Werk bij naar [Application INSIGHTS SDK voor ASP.net core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versie 2.8.0 of hoger. Oudere versies van de SDK bieden geen ondersteuning voor ASP.NET Core 3. X.

Als u [hier](#enable-application-insights-server-side-telemetry-visual-studio)op basis van Visual Studio-instructies gebruikt, werkt u ook bij naar de nieuwste versie van visual studio 2019 (16.3.0) voor onboarding. Eerdere versies van Visual Studio bieden geen ondersteuning voor automatische onboarding voor ASP.NET Core 3. X-apps.

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

Hoewel de uitbreidings methode `UseApplicationInsights()` nog steeds wordt ondersteund, is deze verouderd gemarkeerd in Application Insights SDK-versie 2.8.0. Deze wordt verwijderd uit de volgende primaire versie van de SDK. De aanbevolen manier om Application Insights telemetrie in te scha `AddApplicationInsightsTelemetry()` kelen, is door gebruik te maken van overbelasting voor het beheren van een bepaalde configuratie. Daarnaast is in ASP.NET Core 3. X- `services.AddApplicationInsightsTelemetry()` apps de enige manier om Application Insights in te scha kelen.

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

* De SDK verzamelt [gebeurtenis tellers](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters) voor Linux omdat [prestatie meter items](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters) alleen worden ondersteund in Windows. De meeste meet waarden zijn hetzelfde.
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

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>Wordt deze SDK ondersteund voor de nieuwe .NET Core 3. X Worker-service sjabloon toepassingen?

Deze SDK vereist `HttpContext`en werkt daarom niet in niet-HTTP-toepassingen, waaronder de .net Core 3. X Worker-service toepassingen. Raadpleeg [Dit](worker-service.md) document voor het inschakelen van Application Insights in dergelijke toepassingen, met behulp van de zojuist gepubliceerde micro soft. ApplicationInsights. WorkerService SDK.

## <a name="open-source-sdk"></a>Open-Source-SDK

[Lees en bijdragen aan de code](https://github.com/microsoft/ApplicationInsights-dotnet#recent-updates).

## <a name="next-steps"></a>Volgende stappen

* [Verken de gebruikers stromen](../../azure-monitor/app/usage-flows.md) om te begrijpen hoe gebruikers door uw app navigeren.
* [Configureer een momentopname verzameling](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) om de status van de bron code en variabelen weer te geven op het moment dat een uitzonde ring wordt gegenereerd.
* [Gebruik de API](../../azure-monitor/app/api-custom-events-metrics.md) om uw eigen gebeurtenissen en metrische gegevens te verzenden voor een gedetailleerde weer gave van de prestaties en het gebruik van uw app.
* Gebruik [beschikbaarheids tests](../../azure-monitor/app/monitor-web-app-availability.md) om uw app voortdurend van over de hele wereld te controleren.
* [Afhankelijkheids injectie in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
