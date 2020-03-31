---
title: Telemetriebemonstering in Azure Application Insights | Microsoft Documenten
description: Hoe het volume van telemetrie onder controle te houden.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: fc9db23f7733f97ca207e834d4543fbdb1b9db5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275826"
---
# <a name="sampling-in-application-insights"></a>Steekproeven in Application Insights

Sampling is een functie in [Azure Application Insights.](../../azure-monitor/app/app-insights-overview.md) Het is de aanbevolen manier om telemetrieverkeer, gegevenskosten en opslagkosten te verlagen, met behoud van een statistisch correcte analyse van toepassingsgegevens. Met sampling u ook voorkomen dat Application Insights uw telemetrie throttling. Het bemonsteringsfilter selecteert items die gerelateerd zijn, zodat u tussen items navigeren wanneer u diagnostische onderzoeken doet.

Wanneer metrische tellingen in het portaal worden weergegeven, worden ze opnieuw genormaliseerd om rekening te houden met bemonstering. Hierdoor minimaliseert elk effect op de statistieken.

## <a name="brief-summary"></a>Korte samenvatting

* Er zijn drie verschillende soorten steekproeven: adaptieve bemonstering, bemonstering met vaste snelheid en innamebemonstering.
* Adaptieve sampling is standaard ingeschakeld in alle nieuwste versies van de Application Insights ASP.NET en ASP.NET Core Software Development Kits (SDKs). Het wordt ook gebruikt door [Azure-functies.](https://docs.microsoft.com/azure/azure-functions/functions-overview)
* Fixed-rate sampling is beschikbaar in recente versies van de Application Insights SDKs voor ASP.NET, ASP.NET Core, Java en Python.
* Innamesampling werkt op het eindpunt van de Application Insights-service. Het is alleen van toepassing wanneer er geen andere bemonstering van kracht is. Als de SDK uw telemetrie bemonstert, wordt de innamebemonstering uitgeschakeld.
* Als u aangepaste gebeurtenissen registreert en ervoor moet zorgen dat een reeks gebeurtenissen samen wordt bewaard `OperationId` of verwijderd, moeten de gebeurtenissen dezelfde waarde hebben.
* Als u Analytics-query's schrijft, moet u [rekening houden met steekproeven.](../../azure-monitor/log-query/aggregations.md) In het bijzonder, in plaats van `summarize sum(itemCount)`gewoon tellen records, moet u gebruik maken van .
* Sommige telemetrietypen, waaronder prestatiestatistieken en aangepaste statistieken, worden altijd bewaard, ongeacht of sampling is ingeschakeld of niet.

In de volgende tabel worden de beschikbare bemonsteringstypen voor elke SDK en elk type toepassing samengevat:

| Application Insights SDK | Adaptieve bemonstering ondersteund | Ondersteunde steekproeven met vaste snelheid | Innamebemonstering ondersteund |
|-|-|-|-|
| ASP.NET | [Ja (standaard aan)](#configuring-adaptive-sampling-for-aspnet-applications) | [Ja](#configuring-fixed-rate-sampling-for-aspnet-applications) | Alleen als er geen andere bemonstering van kracht is |
| ASP.NET Core | [Ja (standaard aan)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Ja](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Alleen als er geen andere bemonstering van kracht is |
| Azure Functions | [Ja (standaard aan)](#configuring-adaptive-sampling-for-azure-functions) | Nee | Alleen als er geen andere bemonstering van kracht is |
| Java | Nee | [Ja](#configuring-fixed-rate-sampling-for-java-applications) | Alleen als er geen andere bemonstering van kracht is |
| Python | Nee | [Ja](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Alleen als er geen andere bemonstering van kracht is |
| Alle anderen | Nee | Nee | [Ja](#ingestion-sampling) |

> [!NOTE]
> De informatie op het grootste deel van deze pagina is van toepassing op de huidige versies van de Application Insights SDKs. Zie [de sectie hieronder voor](#older-sdk-versions)informatie over oudere versies van de SDK's.

## <a name="types-of-sampling"></a>Soorten bemonstering

Er zijn drie verschillende bemonsteringsmethoden:

* **Adaptieve bemonstering** past automatisch het volume van telemetrie dat vanuit de SDK wordt verzonden in uw ASP.NET/ASP.NET Core-app en vanuit Azure-functies. Dit is de standaardbemonstering wanneer u de ASP.NET of ASP.NET Core SDK gebruikt. Adaptieve sampling is momenteel alleen beschikbaar voor ASP.NET telemetrie aan de serverzijde en voor Azure-functies.

* **Fixed-rate sampling** vermindert het volume van telemetrie dat wordt verzonden vanaf zowel uw ASP.NET of ASP.NET Core- of Java-server als vanuit de browsers van uw gebruikers. Jij stelt het tarief in. De client en server synchroniseren hun sampling zodat u in Search navigeren tussen gerelateerde paginaweergaven en aanvragen.

* **Innamebemonstering** vindt plaats op het eindpunt van de Application Insights-service. Het verwijdert een deel van de telemetrie die afkomstig is van uw app, met een sampling rate die u instelt. Het vermindert het telemetrieverkeer dat vanuit uw app wordt verzonden niet, maar helpt u om binnen uw maandelijkse quotum te blijven. Het belangrijkste voordeel van innamebemonstering is dat u de samplingrate instellen zonder uw app opnieuw te implementeren. Innamebemonstering werkt gelijkmatig voor alle servers en clients, maar is niet van toepassing wanneer andere soorten steekproeven in werking zijn.

> [!IMPORTANT]
> Als adaptieve of vaste rate samplingmethoden in werking zijn, wordt de innamebemonstering uitgeschakeld.

## <a name="adaptive-sampling"></a>Adaptieve bemonstering

Adaptieve bemonstering is van invloed op het volume telemetrie dat vanuit uw webserver-app naar het eindpunt van de Application Insights-service wordt verzonden.

> [!TIP]
> Adaptieve bemonstering is standaard ingeschakeld wanneer u de ASP.NET SDK of de ASP.NET Core SDK gebruikt, en is standaard ook ingeschakeld voor Azure Functions.

Het volume wordt automatisch aangepast om binnen een bepaalde maximale verkeerssnelheid te blijven en wordt gecontroleerd via de instelling `MaxTelemetryItemsPerSecond`. Als de toepassing een lage hoeveelheid telemetrie produceert, zoals bij het foutopsporing of vanwege een laag gebruik, worden `MaxTelemetryItemsPerSecond`items niet door de bemonsteringsprocessor verwijderd zolang het volume lager is . Naarmate het volume van de telemetrie toeneemt, wordt de samplingrate aangepast om het doelvolume te bereiken. De aanpassing wordt op regelmatige tijdstippen opnieuw berekend en is gebaseerd op een voortschrijdend gemiddelde van de uitgaande transmissiesnelheid.

Om het doelvolume te bereiken, wordt een deel van de gegenereerde telemetrie verwijderd. Maar net als andere soorten steekproeven behoudt het algoritme gerelateerde telemetrie-items. Wanneer u bijvoorbeeld de telemetrie in Zoeken inspecteert, u de aanvraag met betrekking tot een bepaalde uitzondering vinden.

Metrische tellingen zoals het verzoekpercentage en het uitzonderingspercentage worden aangepast om de bemonsteringsfrequentie te compenseren, zodat ze ongeveer de juiste waarden in Metric Explorer weergeven.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Adaptieve sampling configureren voor ASP.NET toepassingen

> [!NOTE]
> Deze sectie is van toepassing op ASP.NET toepassingen, niet op ASP.NET Core-toepassingen. [Meer informatie over het configureren van adaptieve sampling voor ASP.NET Core-toepassingen later in dit document.](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)

In [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)u verschillende parameters `AdaptiveSamplingTelemetryProcessor` in het knooppunt aanpassen. De weergegeven cijfers zijn de standaardwaarden:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    De doelsnelheid die het adaptieve algoritme beoogt **op elke serverhost.** Als uw web-app op veel hosts wordt uitgevoerd, verlaagt u deze waarde om binnen uw doelsnelheid van het verkeer te blijven op de Application Insights-portal.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Het interval waarmee de huidige telemetriesnelheid opnieuw wordt geëvalueerd. Evaluatie wordt uitgevoerd als een voortschrijdend gemiddelde. U dit interval verkorten als uw telemetrie onderhevig is aan plotselinge uitbarstingen.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Wanneer de waarde van het bemonsteringspercentage verandert, hoe snel mogen we het bemonsteringspercentage opnieuw verlagen om minder gegevens vast te leggen?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Wanneer de waarde van het bemonsteringspercentage verandert, hoe snel mogen we het bemonsteringspercentage opnieuw verhogen om meer gegevens vast te leggen?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Als het bemonsteringspercentage varieert, wat is de minimumwaarde die we mogen instellen?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Als het bemonsteringspercentage varieert, wat is de maximale waarde die we mogen instellen?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Bij de berekening van het voortschrijdend gemiddelde wordt het gewicht aangegeven dat moet worden toegewezen aan de meest recente waarde. Gebruik een waarde die gelijk is aan of lager is dan 1. Kleinere waarden maken het algoritme minder reactief voor plotselinge veranderingen.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    De hoeveelheid telemetrie die moet worden gesampled wanneer de app net is gestart. Verlaag deze waarde niet tijdens het foutspelen.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Een lijst met typen met een halve punt van dubbele punt die u niet wilt worden gesampled. Erkende typen `Dependency`zijn: `Event` `Exception`, `PageView` `Request`, `Trace`, , , . Alle telemetrie van de opgegeven typen wordt verzonden; de typen die niet zijn opgegeven, worden bemonsterd.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Een lijst met typen met een halve punt van dubbele punt die u wel wilt nemen. Erkende typen `Dependency`zijn: `Event` `Exception`, `PageView` `Request`, `Trace`, , , . De opgegeven typen worden bemonsterd; alle telemetrie van de andere typen worden altijd verzonden.

**Als u** adaptieve bemonstering `AdaptiveSamplingTelemetryProcessor` wilt uitschakelen, verwijdert `ApplicationInsights.config`u het knooppunt(en) uit .

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternatief: Adaptieve bemonstering in code configureren

In plaats van de `.config` parameter sampling in het bestand in te stellen, u deze waarden programmatisch instellen.

1. Verwijder alle `AdaptiveSamplingTelemetryProcessor` knooppunt(s) uit `.config` het bestand.
2. Gebruik het volgende fragment om adaptieve bemonstering te configureren:

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Meer informatie over telemetrieprocessors](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

U ook de bemonsteringsfrequentie voor elk telemetrietype afzonderlijk aanpassen of zelfs bepaalde typen uitsluiten dat deze worden bemonsterd:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Adaptieve sampling configureren voor ASP.NET Core-toepassingen

Er is `ApplicationInsights.config` geen voor ASP.NET Core-toepassingen, dus alle configuratie wordt gedaan via code.
Adaptieve sampling is standaard ingeschakeld voor alle ASP.NET Core-toepassingen. U het bemonsteringsgedrag uitschakelen of aanpassen.

#### <a name="turning-off-adaptive-sampling"></a>Adaptieve bemonstering uitschakelen

De standaardsamplingfunctie kan worden uitgeschakeld terwijl de Application `ConfigureServices`Insights-service in de methode wordt toegevoegd met behulp van `ApplicationInsightsServiceOptions` in het `Startup.cs` bestand:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);

    //...
}
```

De bovenstaande code schakelt adaptieve bemonstering uit. Volg de onderstaande stappen om steekproeven toe te voegen met meer aanpassingsopties.

#### <a name="configure-sampling-settings"></a>Bemonsteringsinstellingen configureren

Gebruik extensiemethoden `TelemetryProcessorChainBuilder` zoals hieronder weergegeven om het bemonsteringsgedrag aan te passen.

> [!IMPORTANT]
> Als u deze methode gebruikt om de bemonstering `aiOptions.EnableAdaptiveSampling` te `false` configureren, moet u ervoor zorgen dat de eigenschap wordt ingesteld bij het aanroepen `AddApplicationInsightsTelemetry()`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Adaptieve bemonstering configureren voor Azure-functies

Volg instructies vanaf [deze pagina](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling) om adaptieve bemonstering te configureren voor apps die worden uitgevoerd in Azure-functies.

## <a name="fixed-rate-sampling"></a>Bemonstering met vaste snelheid

Fixed-rate sampling vermindert het verkeer dat wordt verzonden vanaf uw webserver en webbrowsers. In tegenstelling tot adaptieve bemonstering, vermindert het telemetrie met een vaste snelheid die door u wordt bepaald. Fixed-rate sampling is beschikbaar voor ASP.NET, ASP.NET Core, Java en Python applicaties.

Net als andere bemonsteringstechnieken behoudt dit ook gerelateerde items. Het synchroniseert ook de client- en serversampling, zodat gerelateerde items worden bewaard - bijvoorbeeld wanneer u een paginaweergave in Zoeken bekijkt, u de bijbehorende serveraanvragen vinden. 

In Metrics Explorer worden tarieven zoals aanvraag- en uitzonderingstellingen vermenigvuldigd met een factor om de bemonsteringssnelheid te compenseren, zodat ze ongeveer correct zijn.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Fixed-rate sampling configureren voor ASP.NET toepassingen

1. **Adaptieve bemonstering**uitschakelen [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md): In, `AdaptiveSamplingTelemetryProcessor` verwijder of reageer op het knooppunt.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Schakel de samplingmodule met vaste snelheid in.** Voeg dit [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)fragment toe aan:
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      Als alternatief u in plaats `ApplicationInsights.config` van de parameter sampling in het bestand programmatisch de volgende waarden in te stellen:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Meer informatie over telemetrieprocessors](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Fixed-rate sampling configureren voor ASP.NET Core-toepassingen

1. **Adaptieve bemonstering uitschakelen**: Wijzigingen `ConfigureServices` kunnen worden `ApplicationInsightsServiceOptions`aangebracht in de methode met behulp van :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);

        //...
    }
    ```

2. **Schakel de samplingmodule met vaste snelheid in.** Wijzigingen kunnen worden `Configure` aangebracht in de methode zoals weergegeven in het onderstaande fragment:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Fixed-rate sampling configureren voor Java-toepassingen

Standaard is er geen bemonstering ingeschakeld in de Java SDK. Momenteel ondersteunt het alleen bemonstering met een vaste snelheid. Adaptieve sampling wordt niet ondersteund in de Java SDK.

1. Download en configureer uw webapplicatie met de nieuwste [Application Insights Java SDK.](../../azure-monitor/app/java-get-started.md)

2. **Schakel de samplingmodule met vaste snelheid** `ApplicationInsights.xml` in door het volgende fragment toe te voegen aan het bestand:

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. U specifieke soorten telemetrie opnemen of uitsluiten `Processor` van steekproeven `FixedRateSamplingTelemetryProcessor`met behulp van de volgende tags in de tag's:
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

De telemetrietypen die kunnen worden opgenomen `Dependency`of `Event` `Exception`uitgesloten `PageView` `Request`van `Trace`bemonstering zijn: , , , , en .

> [!NOTE]
> Kies voor het bemonsteringspercentage een percentage dat dicht bij 100/N ligt, waarbij N een geheel getal is.  Momenteel biedt steekproeven geen ondersteuning voor andere waarden.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Fixed-rate sampling configureren voor OpenCensus Python-toepassingen

Instrumenter uw toepassing met de nieuwste [OpenCensus Azure Monitor-exporteurs.](../../azure-monitor/app/opencensus-python.md)

> [!NOTE]
> Fixed-rate sampling is niet beschikbaar voor de metrische exporteur. Dit betekent dat aangepaste statistieken de enige soorten telemetrie zijn waarbij sampling NIET kan worden geconfigureerd. De metrische exporteur stuurt alle telemetrie die het bijhoudt.

#### <a name="fixed-rate-sampling-for-tracing"></a>Fixed-rate sampling voor tracering ####
U kunt een `sampler` opgeven als onderdeel van uw `Tracer`-configuratie. Als er geen expliciete sampler wordt verstrekt, wordt deze `ProbabilitySampler` standaard gebruikt. Het `ProbabilitySampler` zou standaard een snelheid van 1/10000 gebruiken, wat betekent dat één op de 10000 aanvragen naar Application Insights wordt verzonden. Zie hieronder als u zelf een samplefrequentie wilt opgeven.

Als u de bemonsteringsfrequentie `Tracer` wilt opgeven, controleert u of u een monsternemer opgeeft met een bemonsteringspercentage tussen 0,0 en 1,0. Een samplingrate van 1.0 vertegenwoordigt 100%, wat betekent dat al uw verzoeken als telemetrie naar Application Insights worden verzonden.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Fixed-rate sampling voor logboeken ####
U steekproeven met `AzureLogHandler` een vaste `logging_sampling_rate` snelheid configureren door het optionele argument te wijzigen. Als er geen argument wordt aangevoerd, wordt een bemonsteringspercentage van 1,0 gebruikt. Een samplingrate van 1.0 vertegenwoordigt 100%, wat betekent dat al uw verzoeken als telemetrie naar Application Insights worden verzonden.

```python
exporter = metrics_exporter.new_metrics_exporter(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Fixed-rate sampling configureren voor webpagina's met JavaScript

JavaScript-gebaseerde webpagina's kunnen worden geconfigureerd om Application Insights te gebruiken. Telemetrie wordt verzonden vanuit de clienttoepassing die wordt uitgevoerd in de browser van de gebruiker en de pagina's kunnen vanaf elke server worden gehost.

Wanneer u [uw JavaScript-gebaseerde webpagina's configureert voor Application Insights,](javascript.md)wijzigt u het JavaScript-fragment dat u krijgt van de Portal Application Insights.

> [!TIP]
> In ASP.NET apps met JavaScript inbegrepen, `_Layout.cshtml`gaat het fragment meestal in .

Een regel `samplingPercentage: 10,` invoegen zoals vóór de instrumentatietoets:

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

Kies voor het bemonsteringspercentage een percentage dat dicht bij 100/N ligt, waarbij N een geheel getal is. Momenteel biedt steekproeven geen ondersteuning voor andere waarden.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Sampling aan de server- en clientzijde coördineren

De JavaScript SDK aan clientzijde neemt deel aan fixed-rate sampling in combinatie met de sdk aan de serverzijde. De instrumented pagina's sturen alleen telemetrie aan clientzijde van dezelfde gebruiker waarvoor de sdk aan de serverzijde zijn beslissing heeft genomen om in de steekproef op te nemen. Deze logica is ontworpen om de integriteit van gebruikerssessies te behouden voor toepassingen aan de client- en serverzijde. Als gevolg hiervan u vanuit een bepaald telemetrieitem in Application Insights alle andere telemetrie-items voor deze gebruiker of sessie vinden en in Zoeken u navigeren tussen gerelateerde paginaweergaven en -verzoeken.

Als uw client- en servertelemetrie geen gecoördineerde voorbeelden weergeven:

* Controleer of u sampling hebt ingeschakeld, zowel op de server als op de client.
* Controleer of u hetzelfde bemonsteringspercentage inschakelt in zowel de client als de server.
* Zorg ervoor dat de SDK-versie 2.0 of hoger is.

## <a name="ingestion-sampling"></a>Innamebemonstering

Innamesampling werkt op het punt waar de telemetrie van uw webserver, browsers en apparaten het eindpunt van de Application Insights-service bereikt. Hoewel het telemetrieverkeer dat vanuit uw app wordt verzonden, wordt het bedrag dat door Application Insights is verwerkt en in rekening gebracht (en in rekening gebracht) niet verminderd.

Gebruik dit type sampling als uw app vaak het maandelijkse quotum overschrijdt en u niet de mogelijkheid hebt om een van de op SDK's gebaseerde typen steekproeven te gebruiken. 

Stel de bemonsteringsfrequentie in op de pagina Gebruik en geschatte kosten:

![Klik in het blad Overzicht van de toepassing op Instellingen, Quota, Voorbeelden en selecteer vervolgens een bemonsteringsfrequentie en klik op Bijwerken.](./media/sampling/data-sampling.png)

Net als andere typen steekproeven behoudt het algoritme gerelateerde telemetrie-items. Wanneer u bijvoorbeeld de telemetrie in Zoeken inspecteert, u de aanvraag met betrekking tot een bepaalde uitzondering vinden. Metrische tellingen zoals het aantal aanvragen en het uitzonderingspercentage worden correct behouden.

Gegevenspunten die door steekproeven worden verwijderd, zijn niet beschikbaar in een functie voor Toepassingsinzichten, zoals [Continue export.](../../azure-monitor/app/export-telemetry.md)

Innamebemonstering werkt niet terwijl adaptieve of vaste steekproeven in werking zijn. Adaptieve bemonstering is standaard ingeschakeld wanneer de ASP.NET SDK of de ASP.NET Core SDK wordt gebruikt, of wanneer Application Insights is ingeschakeld in [Azure App Service](azure-web-apps.md) of met statusmonitor. Wanneer telemetrie wordt ontvangen door het eindpunt van de Application Insights-service, wordt de telemetrie onderzocht en als de samplingrate minder dan 100% is (wat aangeeft dat telemetrie wordt bemonsterd) wordt de innamesamplingsnelheid die u instelt genegeerd.

> [!WARNING]
> De waarde op de portaltegel geeft de waarde aan die u instelt voor opnamebemonstering. Het geeft niet de werkelijke bemonsteringssnelheid weer als er een soort SDK-bemonstering (adaptieve of vaste steekproeven) in werking is.

## <a name="when-to-use-sampling"></a>Wanneer bemonstering te gebruiken

In het algemeen hebt u voor de meeste kleine en middelgrote toepassingen geen steekproeven nodig. De meest nuttige diagnostische informatie en meest nauwkeurige statistieken worden verkregen door het verzamelen van gegevens over al uw gebruikersactiviteiten. 

De belangrijkste voordelen van bemonstering zijn:

* Application Insights-service daalt gegevenspunten ('throttles') wanneer uw app in een kort tijdsinterval een zeer hoge snelheid telemetrie verzendt. Steekproeven verminderen de kans dat uw toepassing throttling ziet optreden.
* Als u binnen het [quotum](pricing.md) van gegevenspunten voor uw prijscategorie wilt blijven. 
* Om het netwerkverkeer uit het verzamelen van telemetrie te verminderen. 

### <a name="which-type-of-sampling-should-i-use"></a>Welk type bemonstering moet ik gebruiken?

**Gebruik de innamebemonstering als:**

* U gebruikt vaak uw maandelijkse quotum van telemetrie.
* U krijgt te veel telemetrie van de webbrowsers van uw gebruikers.
* U gebruikt een versie van de SDK die geen sampling ondersteunt, bijvoorbeeld ASP.NET versies eerder dan 2.

**Gebruik steekproeven met een vaste snelheid als:**

* U wilt gesynchroniseerde bemonstering tussen client en server, zodat u, wanneer u gebeurtenissen in [Search](../../azure-monitor/app/diagnostic-search.md)onderzoekt, navigeren tussen gerelateerde gebeurtenissen op de client en server, zoals paginaweergaven en HTTP-aanvragen.
* U bent zeker van het juiste bemonsteringspercentage voor uw app. Het moet hoog genoeg zijn om nauwkeurige statistieken te krijgen, maar onder het tarief dat uw prijsquotum en de beperkingslimieten overschrijdt.

**Gebruik adaptieve bemonstering:**

Als de voorwaarden voor het gebruik van de andere vormen van bemonstering niet van toepassing zijn, raden we adaptieve bemonstering aan. Deze instelling is standaard ingeschakeld in de ASP.NET/ASP.NET Core SDK. Het zal het verkeer niet verminderen totdat een bepaald minimumtarief is bereikt, daarom zullen sites met een laag gebruik waarschijnlijk helemaal niet worden bemonsterd.

## <a name="knowing-whether-sampling-is-in-operation"></a>Weten of de bemonstering in werking is

Als u de werkelijke bemonsteringsfrequentie wilt ontdekken, ongeacht waar deze is toegepast, gebruikt u een [Analytics-query](../../azure-monitor/app/analytics.md) zoals deze:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Als u `RetainedPercentage` ziet dat voor elk type minder dan 100 is, wordt dat type telemetrie bemonsterd.

> [!IMPORTANT]
> Application Insights neemt geen voorbeeld van sessie, statistieken (inclusief aangepaste statistieken) of telemetrietypen van prestatiemeteritems in een van de samplingtechnieken. Deze typen zijn altijd uitgesloten van bemonstering als een vermindering van de precisie kan zeer ongewenst zijn voor deze telemetrie types.

## <a name="how-sampling-works"></a>Hoe bemonstering werkt

Het samplingalgoritme bepaalt welke telemetrie-items moeten worden neergedaald en welke moeten worden behouden. Dit geldt voor de vraag of de sdk of de Application Insights-service een steekproef doet. De bemonsteringsbeslissing is gebaseerd op verschillende regels die tot doel hebben alle onderling verbonden gegevenspunten intact te houden, met behoud van een diagnostische ervaring in Application Insights die uitvoerbaar en betrouwbaar is, zelfs met een beperkte gegevensset. Als uw app bijvoorbeeld een mislukte aanvraag in een voorbeeld heeft opgenomen, blijven de extra telemetrie-items (zoals uitzondering en traces die voor dit verzoek zijn geregistreerd) behouden. Bemonstering houdt of laat ze allemaal samen. Als u de aanvraaggegevens in Application Insights bekijkt, u de aanvraag altijd zien, samen met de bijbehorende telemetrie-items.

De bemonsteringsbeslissing is gebaseerd op de bewerkings-ID van het verzoek, wat betekent dat alle telemetrie-items die tot een bepaalde bewerking behoren, worden bewaard of geschrapt. Voor de telemetrie-items die geen bewerkings-id-set hebben (zoals telemetrie-items die worden gerapporteerd vanaf asynchrone threads zonder HTTP-context), legt sampling gewoon een percentage telemetrie-items van elk type vast.

Wanneer u telemetrie weer aan u presenteert, past de Service Application Insights de statistieken aan met hetzelfde bemonsteringspercentage dat op het moment van verzameling is gebruikt om de ontbrekende gegevenspunten te compenseren. Vandaar, wanneer het bekijken van de telemetrie in De Inzichten van de toepassing, zien de gebruikers statistisch correcte benaderingen die zeer dicht bij de echte aantallen zijn.

De nauwkeurigheid van de benadering hangt grotendeels af van het geconfigureerde bemonsteringspercentage. Ook neemt de nauwkeurigheid toe voor toepassingen die een groot aantal over het algemeen vergelijkbare verzoeken van veel gebruikers verwerken. Aan de andere kant, voor toepassingen die niet werken met een aanzienlijke belasting, bemonstering is niet nodig, omdat deze toepassingen kunnen meestal al hun telemetrie te sturen tijdens een verblijf binnen het quotum, zonder dat gegevens verlies van beperking. 

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

*Wat is het standaard samplinggedrag in de ASP.NET en ASP.NET Core SDK's?*

* Als u een van de nieuwste versies van de bovenstaande SDK gebruikt, is Adaptive Sampling standaard ingeschakeld met vijf telemetrie-items per seconde.
  Er zijn `AdaptiveSamplingTelemetryProcessor` standaard twee knooppunten toegevoegd `Event` en één bevat het type `Event` in de bemonstering, terwijl het andere type uitsluit van bemonstering. Deze configuratie betekent dat de SDK zal proberen om telemetrie-items te beperken tot vijf telemetrie-items van `Event` typen en vijf telemetrie-items van alle andere typen samen, waardoor ervoor wordt gezorgd dat `Events` deze afzonderlijk van andere telemetrietypen worden bemonsterd. Gebeurtenissen worden meestal gebruikt voor zakelijke telemetrie en mogen waarschijnlijk niet worden beïnvloed door diagnostische telemetrievolumes.
  
  Het volgende toont `ApplicationInsights.config` het gegenereerde standaardbestand. In ASP.NET Core is hetzelfde standaardgedrag ingeschakeld in code. Gebruik de [voorbeelden in het eerdere gedeelte van deze pagina](#configuring-adaptive-sampling-for-aspnet-core-applications) om dit standaardgedrag te wijzigen.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*Kan telemetrie meer dan één keer worden bemonsterd?*

* Nee. SamplingTelemetryProcessors negeren items uit steekproefoverwegingen als het item al is bemonsterd. Hetzelfde geldt ook voor innamebemonstering, die geen bemonstering zal toepassen op de items die al in de SDK zelf zijn bemonsterd.

*Waarom is het niet een eenvoudige "verzamel X procent van elk telemetrietype"?*

* Hoewel deze bemonsteringsbenadering een hoog niveau van precisie in metrische benaderingen zou bieden, zou het de mogelijkheid breken om diagnostische gegevens per gebruiker, sessie en verzoek te correleren, wat essentieel is voor diagnostiek. Daarom werkt sampling beter met beleid zoals "alle telemetrie-items verzamelen voor X procent van de app-gebruikers", of "alle telemetrie verzamelen voor X procent van de app-aanvragen". Voor de telemetrie-items die niet aan de aanvragen zijn gekoppeld (zoals asynchrone verwerking op de achtergrond), moet de terugval 'X-procent van alle items voor elk telemetrietype verzamelen'. 

*Kan het bemonsteringspercentage in de loop van de tijd veranderen?*

* Ja, adaptieve bemonstering verandert geleidelijk het bemonsteringspercentage, op basis van het momenteel waargenomen volume van de telemetrie.

*Als ik fixed-rate sampling gebruik, hoe weet ik dan welk bemonsteringspercentage het beste werkt voor mijn app?*

* Een manier is om te beginnen met adaptieve bemonstering, erachter te komen welke snelheid het vestigt op (zie de bovenstaande vraag), en vervolgens overschakelen naar fixed-rate sampling met behulp van die snelheid. 
  
    Anders moet je raden. Analyseer uw huidige telemetriegebruik in Application Insights, observeer eventuele beperking en schat het volume van de verzamelde telemetrie. Deze drie ingangen, samen met de geselecteerde prijscategorie, geven aan hoeveel u het volume van de verzamelde telemetrie wilt verminderen. Een toename van het aantal gebruikers of een andere verschuiving in het volume van telemetrie kan uw schatting echter ongeldig maken.

*Wat gebeurt er als ik het bemonsteringspercentage te laag configureer?*

* Te lage bemonsteringspercentages veroorzaken overagressieve bemonstering en verminderen de nauwkeurigheid van de benaderingen wanneer Application Insights probeert de visualisatie van de gegevens te compenseren voor de vermindering van het gegevensvolume. Ook uw diagnostische ervaring kan negatief worden beïnvloed, omdat sommige van de zelden falende of langzame verzoeken kunnen worden bemonsterd.

*Wat gebeurt er als ik het bemonsteringspercentage te hoog configureer?*

* Het configureren van een te hoog bemonsteringspercentage (niet agressief genoeg) resulteert in een onvoldoende vermindering van het volume van de verzamelde telemetrie. U nog steeds telemetriegegevensverlies ervaren in verband met beperking en de kosten voor het gebruik van Application Insights kunnen hoger zijn dan u van plan was vanwege overschrijdingskosten.

*Op welke platforms kan ik sampling gebruiken?*

* Innamebemonstering kan automatisch plaatsvinden voor telemetrie boven een bepaald volume, als de SDK geen bemonstering uitvoert. Deze configuratie zou bijvoorbeeld werken als u een oudere versie van de ASP.NET SDK of Java SDK gebruikt.
* Als u de huidige ASP.NET of ASP.NET Core-SDK's gebruikt (gehost in Azure of op uw eigen server), krijgt u standaard adaptieve bemonstering, maar u overschakelen naar vaste snelheid zoals hierboven beschreven. Met fixed-rate sampling synchroniseert de browser SDK automatisch met voorbeeldgerelateerde gebeurtenissen. 
* Als u de huidige Java SDK gebruikt, u configureren `ApplicationInsights.xml` om steekproeven met een vaste snelheid in te schakelen. De bemonstering is standaard uitgeschakeld. Met fixed-rate sampling synchroniseren de browser SDK en de server automatisch met voorbeeldgerelateerde gebeurtenissen.

*Er zijn bepaalde zeldzame gebeurtenissen die ik altijd wil zien. Hoe kan ik ze voorbij de bemonsteringsmodule krijgen?*

* De beste manier om dit te bereiken is het schrijven `SamplingPercentage` van een aangepaste [TelemetryInitializer](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer), die de ingesteld op 100 op de telemetrie item dat u wilt behouden, zoals hieronder weergegeven. Aangezien initialisators gegarandeerd worden uitgevoerd vóór telemetrieprocessors (inclusief bemonstering), zorgt dit ervoor dat alle bemonsteringstechnieken dit artikel negeren uit overwegingen van bemonstering. Aangepaste telemetrieinitialisatoren zijn beschikbaar in de ASP.NET SDK, de ASP.NET Core SDK, de JavaScript SDK en de Java SDK. U bijvoorbeeld een telemetrieinitialisator configureren met de ASP.NET SDK:

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>Oudere SDK-versies

Adaptieve sampling is beschikbaar voor de Application Insights SDK voor ASP.NET v2.0.0-beta3 en hoger, Microsoft.ApplicationInsights.AspNetCore SDK v2.2.0-beta1 en hoger, en is standaard ingeschakeld.

Fixed-rate sampling is een functie van de SDK in ASP.NET versies vanaf 2.0.0 en Java SDK versie 2.0.1 en verder.

Voorafgaand aan v2.5.0-beta2 van de ASP.NET SDK en v2.2.0-beta3 van ASP.NET Core SDK, was de bemonsteringsbeslissing gebaseerd op de hash van de gebruikers-id voor toepassingen die "gebruiker" definiëren (dat wil zeggen de meest typische webapplicaties). Voor de typen toepassingen die gebruikers (zoals webservices) niet definieerden, was de bemonsteringsbeslissing gebaseerd op de bewerkings-id van de aanvraag. Recente versies van de ASP.NET en ASP.NET Core SDK's gebruiken de bedrijfs-ID voor de bemonsteringsbeslissing.

## <a name="next-steps"></a>Volgende stappen

* [Filteren](../../azure-monitor/app/api-filtering-sampling.md) kan een striktere controle geven over wat uw SDK stuurt.
* Lees het artikel Ontwikkelaarsnetwerk [Telemetrie optimaliseren met toepassingsinzichten](https://msdn.microsoft.com/magazine/mt808502.aspx).
