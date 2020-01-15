---
title: Bemonsterde telemetrie in Azure-toepassing Insights | Microsoft Docs
description: Hoe u het telemetrie-volume onder controle kunt houden.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/14/2019
ms.reviewer: vitalyg
ms.openlocfilehash: d07bb9b69f022da98b3d46e3e36f4c4de2b4d006
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945638"
---
# <a name="sampling-in-application-insights"></a>Steekproeven in Application Insights

Steek proeven zijn een functie in [Azure-toepassing Insights](../../azure-monitor/app/app-insights-overview.md). Het is de aanbevolen manier om het telemetrische verkeer en de opslag te verminderen, terwijl u een statistische, juiste analyse van toepassings gegevens behoudt. Het filter selecteert items die gerelateerd zijn, zodat u kunt navigeren tussen items wanneer u diagnostische tests uitvoert.
Wanneer metrische aantallen worden weer gegeven in de portal, worden ze opnieuw genormaliseerd om rekening te houden met de steek proeven. Zo minimaliseert u het effect op de statistieken.

Door steek proeven worden de kosten voor verkeer en gegevens verminderd, en kunt u de beperking voor komen.

## <a name="in-brief"></a>In het kort:

* Bij de steek proef blijven 1 in *n* records en wordt de rest verwijderd. Een voor beeld: het kan één in vijf gebeurtenissen behouden, een sampling frequentie van 20%. 
* Adaptieve steek proeven zijn standaard ingeschakeld in de meest recente versie van ASP.NET en ASP.NET Core Sdk's (Software Development Kits).
* U kunt ook hand matig steek proeven instellen. Dit kan in de portal worden geconfigureerd op de *pagina gebruik en geschatte kosten*in de ASP.NET SDK in het bestand ApplicationInsights. config, in de ASP.net core SDK via code of in de Java-SDK in het bestand ApplicationInsights. XML.
* Als u aangepaste gebeurtenissen registreert en er zeker van wilt zijn dat een set gebeurtenissen wordt behouden of verwijderd, moeten de gebeurtenissen dezelfde OperationId-waarde hebben.
* De sampling-deler *n* wordt gerapporteerd in elke record in de eigenschap `itemCount`, die in de zoek opdracht wordt weer gegeven onder de beschrijvende naam ' aanvraag aantal ' of ' aantal gebeurtenissen '. `itemCount==1`wanneer de steek proeven niet in werking zijn.
* Als u analyse query's schrijft, moet u [rekening houden met steek proeven](../../azure-monitor/log-query/aggregations.md). In plaats van records eenvoudigweg te tellen, moet u in het bijzonder `summarize sum(itemCount)`gebruiken.

## <a name="types-of-sampling"></a>Typen steek proeven

Er zijn drie alternatieve bemonsterings methoden:

* Met **adaptieve steek proeven** wordt het volume van de telemetrie van de SDK in uw ASP.NET/ASP.net core-app automatisch aangepast. Dit is de standaard steekproef van ASP.NET Web SDK v 2.0.0-beta3 en micro soft. ApplicationInsights. AspNetCore SDK v 2.2.0-beta1 en hoger.  Adaptieve steek proeven zijn momenteel alleen beschikbaar voor telemetrie op de ASP.NET-Server.

* Bij **steek proeven met een vaste frequentie** wordt het volume van de telemetrie gereduceerd van uw ASP.net-of ASP.net core-of Java-Server en van de browsers van uw gebruikers. U stelt het aantal in. De steek proeven van de client en de server worden gesynchroniseerd, zodat u kunt navigeren tussen gerelateerde pagina weergaven en-aanvragen.

* **Steek proeven voor opname** Werkt in de Azure Portal. Er wordt een deel van de telemetrie verwijderd uit uw app, met een sampling frequentie die u hebt ingesteld. Het vermindert niet het telemetrie verkeer dat vanuit uw app wordt verzonden, maar helpt u binnen uw maandelijkse quotum te houden. Het belangrijkste voor deel van opname sampling is dat u de sampling frequentie kunt instellen zonder uw app opnieuw te implementeren. De steek proef van opname werkt op dezelfde manier voor alle servers en clients.

Als er een adaptieve of vaste beoordeling wordt uitgevoerd, worden steek proeven voor opname uitgeschakeld.


## <a name="adaptive-sampling-in-your-aspnetaspnet-core-web-applications"></a>Adaptieve steek proeven in uw ASP.NET/ASP.NET core-webtoepassingen

Adaptieve steek proeven zijn beschikbaar voor de Application Insights SDK voor ASP.NET v 2.0.0-beta3 en hoger, micro soft. ApplicationInsights. AspNetCore SDK v 2.2.0-beta1 en hoger, en is standaard ingeschakeld.

Adaptieve steek proeven zijn van invloed op het volume van de telemetrie die vanuit uw webserver-app wordt verzonden naar het Application Insights service-eind punt. Het volume wordt automatisch aangepast om binnen een opgegeven maximale hoeveelheid verkeer te blijven en wordt beheerd via de instelling `MaxTelemetryItemsPerSecond`. Als de toepassing een geringe hoeveelheid telemetrie produceert, zoals bij het opsporen van fouten of wegens een laag gebruik, worden items niet verwijderd door de sampling processor zolang het volume onder `MaxTelemetryItemsPerSecond`is. Naarmate het volume van de telemetrie toeneemt, wordt de sampling frequentie aangepast om het doel volume te bereiken.

Om het doel volume te bereiken, wordt een deel van de gegenereerde telemetrie genegeerd. Maar net als andere typen steek proeven behouden de algoritme gerelateerde telemetrie-items. Wanneer u bijvoorbeeld de telemetrie in de zoek actie inspecteert, kunt u de aanvraag voor een bepaalde uitzonde ring vinden.

Metrische aantallen, zoals de frequentie van aanvragen en uitzonderings snelheden, worden aangepast om de sampling frequentie te compenseren, zodat ze ongeveer de juiste waarden in metrische Explorer weer geven.

## <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Adaptieve steek proeven configureren voor ASP.NET-toepassingen

[Meer informatie](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) over het configureren van adaptieve steek proeven voor ASP.net core toepassingen. 

In [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)kunt u verschillende para meters aanpassen in het knoop punt `AdaptiveSamplingTelemetryProcessor`. De weer gegeven afbeeldingen zijn de standaard waarden:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Het doel frequentie waarmee het adaptieve algoritme **op elke server host**gericht is. Als uw web-app op veel hosts wordt uitgevoerd, verlaagt u deze waarde zodat deze binnen de doel frequentie van het verkeer op de Application Insights Portal blijft.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Het interval waarmee de huidige frequentie van de telemetrie opnieuw wordt geëvalueerd. De evaluatie wordt uitgevoerd als een zwevend gemiddelde. Mogelijk wilt u dit interval verkorten als uw telemetrie op plotselinge bursts is.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Wanneer het percentage van de waarde wordt gewijzigd, hoe snel nadat we het sampling percentage opnieuw hebben toegestaan om minder gegevens vast te leggen.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Wanneer het percentage van de waarde wordt gewijzigd, hoe snel nadat we het steekproef percentage opnieuw hebben toegestaan om meer gegevens vast te leggen.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Wat de minimum waarde is die we mogen instellen, is afhankelijk van het bemonsterings percentage.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Wat de maximum waarde is die we mogen instellen, is afhankelijk van het bemonsterings percentage.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Bij het berekenen van het zwevend gemiddelde wordt het gewicht toegewezen aan de meest recente waarde. Gebruik een waarde die gelijk is aan of kleiner is dan 1. Kleinere waarden zorgen ervoor dat het algoritme minder opnieuw wordt geactiveerd tot onverwachte wijzigingen.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    De waarde die wordt toegewezen wanneer de app zojuist is gestart. Verminder de waarde niet tijdens het opsporen van fouten.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Een door punt komma's gescheiden lijst met typen waarvan u geen steek proef wilt maken. De herkende typen zijn: afhankelijkheid, gebeurtenis, uitzonde ring, pagina weergave, aanvraag, tracering. Alle exemplaren van de opgegeven typen worden verzonden. voor de typen die niet zijn opgegeven, worden steek proeven gegeven.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Een door punt komma's gescheiden lijst met typen waarvan u een steek proef wilt maken. De herkende typen zijn: afhankelijkheid, gebeurtenis, uitzonde ring, pagina weergave, aanvraag, tracering. De opgegeven typen worden gesampled. alle exemplaren van de andere typen worden altijd verzonden.


**Als u** adaptieve steek proeven wilt uitschakelen, verwijdert u de knoop adaptivesamplingtelemetryprocessor-knoop punt (en) uit applicationinsights-config.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternatief: adaptieve steek proeven in code configureren

In plaats van de sampling para meter in het. config-bestand in te stellen, kunt u deze waarden programmatisch instellen.

1. Verwijder alle `AdaptiveSamplingTelemetryProcessor` knoop punt (en) uit het. config-bestand.
2. Gebruik het volgende code fragment voor het configureren van adaptieve steek proeven.

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Meer informatie over telemetrie-processors](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

U kunt ook de sampling frequentie voor elk type telemetrie aanpassen, of u kunt zelfs bepaalde typen uitsluiten van steek proeven. 

*C#*

```csharp
    // The following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

## <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Adaptieve steek proeven configureren voor ASP.NET Core toepassingen.

Er zijn geen `ApplicationInsights.Config` voor ASP.NET Core toepassingen, dus elke configuratie wordt uitgevoerd via code.
Adaptieve steek proeven zijn standaard ingeschakeld voor alle ASP.NET Core-toepassingen. U kunt het steekproef gedrag uitschakelen of aanpassen.

### <a name="turning-off-adaptive-sampling"></a>Adaptieve steek proeven uitschakelen

De standaard steekproef functie kan worden uitgeschakeld tijdens het toevoegen van Application Insights-service in de methode ```ConfigureServices```met behulp van ```ApplicationInsightsServiceOptions``` binnen het `Startup.cs`-bestand:

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

Met deze code wordt de bemonsterings functie uitgeschakeld. Volg de onderstaande stappen om steek proeven toe te voegen met meer aanpassings opties.

### <a name="configure-sampling-settings"></a>Sampling-instellingen configureren

Gebruik de extensie methoden van ```TelemetryProcessorChainBuilder```, zoals hieronder wordt weer gegeven om het steekproef gedrag aan te passen.

> [!IMPORTANT]
> Als u deze methode gebruikt om steek proeven te configureren, moet u ervoor zorgen dat u aiOptions. EnableAdaptiveSampling = False gebruikt. instellingen met AddApplicationInsightsTelemetry ().

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.TelemetryProcessorChainBuilder;
    // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
    // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

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

**Als u de bovenstaande methode gebruikt om steek proeven te configureren, moet u ```aiOptions.EnableAdaptiveSampling = false;``` instellingen gebruiken met AddApplicationInsightsTelemetry ().**

## <a name="fixed-rate-sampling-for-aspnet-aspnet-core-java-websites-and-python-applications"></a>Steek proeven met een vaste frequentie voor ASP.NET, ASP.NET Core, Java-websites en python-toepassingen

Met een steek proef van een vast bedrag wordt het verkeer dat wordt verzonden vanaf uw webserver en webbrowsers verminderd. In tegens telling tot adaptieve steek proeven wordt de telemetrie verminderd met een vast bedrag dat door u wordt bepaald. Ook worden de client-en server sampling gesynchroniseerd, zodat verwante items worden bewaard. Als u bijvoorbeeld een pagina weergave in de zoek opdracht bekijkt, kunt u de bijbehorende aanvraag vinden.

Net als andere technieken voor steek proeven blijven ook verwante items behouden. Voor elke HTTP-aanvraag gebeurtenis worden de aanvraag en de bijbehorende gebeurtenissen verwijderd of gezamenlijk verzonden.

In Metrics Explorer worden tarieven zoals het aantal aanvragen en uitzonde ringen vermenigvuldigd met een factor om de sampling frequentie te compenseren, zodat ze ongeveer correct zijn.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Bemonsterde vaste frequentie in ASP.NET configureren

1. **Adaptieve steek proeven uitschakelen**: in [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)kunt u het knoop punt `AdaptiveSamplingTelemetryProcessor` verwijderen of opmerkingen toevoegen.

    ```xml

    <TelemetryProcessors>

    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    ```

2. **Schakel de bemonsterings module met vaste frequentie in.** Voeg dit fragment toe aan [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```
   ### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Alternatief: steek proeven met een vaste frequentie inschakelen in uw server code
    
    In plaats van de sampling para meter in het. config-bestand in te stellen, kunt u deze waarden programmatisch instellen. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```
([Meer informatie over telemetrie-processors](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-in-aspnet-core"></a>Bemonsterde vaste frequentie in ASP.NET Core configureren

1. **Adaptieve bemonstering uitschakelen**: wijzigingen kunnen worden aangebracht in de methode ```ConfigureServices```, met behulp van ```ApplicationInsightsServiceOptions```:

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

2. **Schakel de bemonsterings module met vaste frequentie in.** U kunt wijzigingen aanbrengen in de methode ```Configure```, zoals in het onderstaande fragment wordt weer gegeven:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.TelemetryProcessorChainBuilder;
        // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
        // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }

    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>Bemonsterde vaste frequentie in JAVA configureren ###

1. Down load en configureer uw webtoepassing met de nieuwste [Application Insights Java-SDK](../../azure-monitor/app/java-get-started.md)

2. **Schakel de bemonsterings module met vaste frequentie** in door het volgende fragment toe te voegen aan het bestand ApplicationInsights. XML.

    ```XML
        <TelemetryProcessors>
            <BuiltInProcessors>
                <Processor type = "FixedRateSamplingTelemetryProcessor">
                    <!-- Set a percentage close to 100/N where N is an integer. -->
                    <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                    <Add name = "SamplingPercentage" value = "50" />
                </Processor>
            </BuiltInProcessors>
        </TelemetryProcessors>
    ```

3. U kunt specifieke soorten telemetrie opnemen of uitsluiten van steek proeven met behulp van de volgende tags in de processor code ' FixedRateSamplingTelemetryProcessor '
    ```XML
        <ExcludedTypes>
            <ExcludedType>Request</ExcludedType>
        </ExcludedTypes>

        <IncludedTypes>
            <IncludedType>Exception</IncludedType>
        </IncludedTypes>
    ```

De typen telemetrie die kunnen worden opgenomen in of uitgesloten van steek proeven zijn: afhankelijkheid, gebeurtenis, uitzonde ring, pagina weergave, aanvraag en tracering.

> [!NOTE]
> Kies voor het steekproef percentage een percentage dat dicht bij 100/N ligt waarbij N een geheel getal is.  De huidige steek proef biedt geen ondersteuning voor andere waarden.
> 
> 

<a name="other-web-pages"></a>

### <a name="configuring-fixed-rate-sampling-in-opencensus-python"></a>Bemonsterde vaste frequentie configureren in opentellingen python ###

1. Instrumenteer uw toepassing met de meest recente [Opentellings Azure monitor exporteurs](../../azure-monitor/app/opencensus-python.md).

> [!NOTE]
> Steek proeven met een vaste frequentie zijn alleen beschikbaar via de trace-export functie. Dit betekent dat binnenkomende en uitgaande aanvragen de enige typen telemetrie zijn waar steek proeven kunnen worden geconfigureerd.
> 
> 

2. U kunt een `sampler` opgeven als onderdeel van uw `Tracer`-configuratie. Als er geen expliciete Voorbeeldset wordt gegeven, wordt de ProbabilitySampler standaard gebruikt. De ProbabilitySampler gebruikt standaard een frequentie van 1/10000, wat betekent dat één van elke 10000 aanvragen wordt verzonden naar Application Insights. Zie hieronder als u zelf een samplefrequentie wilt opgeven.

3. Wanneer u een sampler opgeeft, moet u ervoor zorgen dat uw `Tracer` een sampler opgeeft met een samplefrequentie tussen 0,0 en 1,0. Een sampling frequentie van 1,0 vertegenwoordigt 100%, wat betekent dat al uw aanvragen worden verzonden als telemetrie naar Application Insights.

    ```python
    tracer = Tracer(
        exporter=AzureExporter(
            instrumentation_key='00000000-0000-0000-0000-000000000000',
        ),
        sampler=ProbabilitySampler(1.0),
    )
    ```

## <a name="ingestion-sampling"></a>Steek proeven voor opname

Deze vorm van steek proeven werkt op het punt waar de telemetrie van uw webserver, browsers en apparaten het Application Insights service-eind punt bereikt. Hoewel het telemetrie verkeer dat vanuit uw app wordt verzonden, niet wordt verminderd, wordt de hoeveelheid verwerkte en behouden (en in rekening gebracht voor) door Application Insights verminderd.

Gebruik dit type steek proef als uw app vaak het maandelijkse quotum overschrijdt en u niet de mogelijkheid hebt om een van de op SDK gebaseerde typen steek proeven te gebruiken. 

Stel de sampling frequentie in op de pagina gebruik en geschatte kosten:

![Klik op de Blade toepassings overzicht op instellingen, quotum, voor beelden, selecteer een sampling frequentie en klik op bijwerken.](./media/sampling/data-sampling.png)

Net als bij andere soorten steek proeven blijven de verwante telemetrie-items behouden. Wanneer u bijvoorbeeld de telemetrie in de zoek actie inspecteert, kunt u de aanvraag voor een bepaalde uitzonde ring vinden. Metrische aantallen, zoals de frequentie van aanvragen en uitzonderings snelheden, worden correct bewaard.

Gegevens punten die worden verwijderd door middel van steek proeven zijn niet beschikbaar in een Application Insights functie, zoals [continue export](../../azure-monitor/app/export-telemetry.md).

De steek proeven voor opname worden niet uitgevoerd terwijl de op SDK gebaseerde adaptieve of vaste sampling bewerking is. Adaptieve steek proeven zijn standaard ingeschakeld wanneer ASP.NET/ASP.NET core SDK is ingeschakeld in Visual Studio of is ingeschakeld in azure web app-extensies of door gebruik te maken van Status Monitor, en de steek proeven voor opname zijn uitgeschakeld. Als de sampling frequentie bij de SDK kleiner is dan 100% (d.w.z. Er worden items van de steek proef verzameld) dan wordt de sampling frequentie voor opname die u hebt ingesteld, genegeerd.

> [!WARNING]
> De waarde die wordt weer gegeven op de tegel duidt op de waarde die u hebt ingesteld voor steek proeven van opname. Het bevat niet de werkelijke sampling frequentie als de SDK-steek proeven worden uitgevoerd.
>
>
## <a name="sampling-for-web-pages-with-javascript"></a>Steek proeven voor webpagina's met Java script
U kunt webpagina's configureren voor steek proeven met een vaste frequentie vanaf elke server. 

Wanneer u [de webpagina's voor Application Insights configureert](../../azure-monitor/app/javascript.md), wijzigt u het Java script-fragment dat u van de Application Insights Portal ontvangt. (In ASP.NET-apps gaat het fragment meestal in _Layout. cshtml.)  Voeg een regel toe, zoals `samplingPercentage: 10,` vóór de instrumentatie sleutel:

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 

    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

Kies voor het steekproef percentage een percentage dat dicht bij 100/N ligt waarbij N een geheel getal is.  De huidige steek proef biedt geen ondersteuning voor andere waarden.

Als u ook een steek proef op basis van een vaste snelheid op de server inschakelt, worden de clients en de server gesynchroniseerd zodat u kunt navigeren tussen gerelateerde pagina weergaven en-aanvragen in zoeken.

## <a name="when-to-use-sampling"></a>Wanneer gebruikt u steek proeven?

Adaptieve steek proeven worden automatisch ingeschakeld in de nieuwste Sdk's voor .NET en .NET core. Ongeacht welke versie van de SDK u gebruikt, kunt u opname sampling inschakelen om Application Insights de verzamelde gegevens te bemonsteren.

Standaard is geen steek proef ingeschakeld in Java SDK. Op dit moment wordt alleen de sampling van vaste kosten ondersteund. Adaptieve steek proeven worden niet ondersteund in Java SDK.

Over het algemeen hebt u voor de meeste kleine en middel grote toepassingen geen steek proeven nodig. De meest nuttige diagnostische informatie en de meest nauw keurige statistieken worden verkregen door het verzamelen van gegevens voor al uw gebruikers activiteiten. 

De belangrijkste voor delen van steek proeven zijn:

* De gegevens punten van Application Insights-service worden beperkt (' gashendel ') wanneer uw app een zeer hoog aantal telemetrie verstuurt in een korte periode. 
* Om binnen het [quotum](../../azure-monitor/app/pricing.md) van gegevens punten voor uw prijs categorie te blijven. 
* Het netwerk verkeer van de verzameling telemetrie verminderen. 

### <a name="which-type-of-sampling-should-i-use"></a>Welk type steek proef moet ik gebruiken?

**Opname sampling gebruiken als:**

* U gaat vaak uw maandelijkse quotum van telemetrie door lopen.
* U gebruikt een versie van de SDK die geen steek proeven ondersteunt, bijvoorbeeld ASP.NET-versies die ouder zijn dan 2.
* U krijgt te veel telemetrie van de webbrowsers van uw gebruikers.

**Gebruik een bemonsterde vaste frequentie als:**

* U gebruikt de Application Insights SDK voor ASP.NET Web Services versie 2.0.0 of hoger of Java SDK v 2.0.1 of hoger, en
* U wilt gesynchroniseerde steek proeven tussen de client en de server, zodat, wanneer u gebeurtenissen in de [Zoek opdracht](../../azure-monitor/app/diagnostic-search.md)onderzoekt, u kunt navigeren tussen gerelateerde gebeurtenissen op de client en de server, zoals pagina weergaven en HTTP-aanvragen.
* U bent zeker van het juiste sampling-percentage voor uw app. Het moet hoog genoeg zijn om nauw keurige metrische gegevens te krijgen, maar minder dan het tarief dat het quotum voor prijzen en de beperkings limieten overschrijdt. 

**Adaptieve steek proeven gebruiken:**

Als de voor waarden voor het gebruik van de andere vormen van steek proeven niet van toepassing zijn, raden we aan om adaptieve steek proeven te nemen. Deze instelling is standaard ingeschakeld in de ASP.NET/ASP.NET core-server-SDK. Het vermindert het verkeer niet totdat een bepaald minimum aantal is bereikt, daarom worden sites met weinig gebruik niet beïnvloed.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Hoe kan ik weet u of er steek proeven worden uitgevoerd?

Als u de werkelijke sampling frequentie wilt detecteren, ongeacht waar deze is toegepast, gebruikt u een [analyse query](../../azure-monitor/app/analytics.md) zoals deze:

```
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Als RetainedPercentage voor elk type kleiner is dan 100, wordt er een steek proef van dat item weer genomen.

**Application Insights geen voor beeld van een sessie, metrische gegevens en prestatie meters is een type telemetrie in de hierboven beschreven voorbeeld technieken. Deze typen worden altijd uitgesloten van steek proeven, omdat de nauw keurigheid zeer onwenselijk is voor deze typen telemetrie**

## <a name="how-does-sampling-work"></a>Hoe werkt steek proeven?

Bemonsterings functie met een vast aantal van de SDK in ASP.NET-versies van 2.0.0 en Java SDK-versie 2.0.1 en hoger. Adaptieve steek proeven zijn een functie van SDK in ASP.NET-versies van 2.0.0. Steek proeven voor opname zijn een functie van de Application Insights-service en kunnen worden uitgevoerd als de SDK geen steek proeven uitvoert.

Het bemonsterings algoritme bepaalt welke telemetriegegevens moeten worden verwijderd en welke items moeten worden bewaard (of het nu in de SDK of in de Application Insights-service). De beoordelings beslissing is gebaseerd op verschillende regels die erop gericht zijn alle gerelateerde gegevens punten intact te houden, waarbij een diagnostische ervaring wordt bijgehouden in Application Insights die actiebaar en betrouwbaar is, zelfs met een gereduceerde gegevensset. Als de app bijvoorbeeld een mislukte aanvraag verzendt, worden er extra telemetriegegevens verzonden (zoals uitzonde ringen en traceringen die van deze aanvraag zijn geregistreerd), maar wordt deze aanvraag en andere telemetrie niet gesplitst door steek proeven. Ze worden allemaal bij elkaar bewaard of neergezet. Als u de details van de aanvraag in Application Insights bekijkt, kunt u de aanvraag altijd samen met de bijbehorende telemetrie-items weer geven. 

De beoordelings beslissing is gebaseerd op de bewerkings-ID van de aanvraag, wat betekent dat alle telemetrie-items die deel uitmaken van een bepaalde bewerking, worden behouden of verwijderd. Voor de telemetrie-items waarvoor geen bewerkings-ID is ingesteld (bijvoorbeeld telemetrie-items die zijn gerapporteerd van asynchrone threads zonder HTTP-context), wordt een percentage van de telemetriegegevens van elk type vastgelegd. Vóór de 2.5.0-beta2 van .NET SDK en 2.2.0-beta3 van ASP.NET Core SDK werd de steekproef beslissing gebaseerd op de hash van de gebruikers-ID voor toepassingen die ' gebruiker ' definiëren (dat wil zeggen de meest voorkomende webtoepassingen). Voor de typen toepassingen die geen gebruikers hebben gedefinieerd (zoals webservices), is de bemonsterings beslissing gebaseerd op de bewerkings-ID van de aanvraag.

Wanneer u telemetrie naar u presenteert, past de Application Insights-service de metrische gegevens aan op basis van het steekproef percentage dat op het moment van de verzameling is gebruikt, om de ontbrekende data punten te compenseren. Daarom zien de gebruikers bij het bekijken van de telemetrie in Application Insights statistische juiste benaderingen die zeer dicht bij de reële getallen vallen.

De nauw keurigheid van de benadering is grotendeels afhankelijk van het geconfigureerde sampling percentage. De nauw keurigheid neemt ook toe voor toepassingen die een grote hoeveelheid algemeen vergelijk bare aanvragen van veel gebruikers verwerken. Daarentegen, voor toepassingen die niet met een aanzienlijke belasting werken, is er geen steek proef nodig omdat deze toepassingen doorgaans al hun telemetrie kunnen verzenden terwijl ze binnen het quotum blijven, zonder dat er gegevens verloren gaan. 

> [!WARNING]
> Application Insights biedt geen voorbeeld typen voor metrische gegevens en telemetrie sessies. Het verminderen van de precisie kan zeer onwenselijk zijn voor deze typen telemetrie.
> 

### <a name="adaptive-sampling"></a>Adaptieve steek proef

Met adaptieve steek proef wordt een onderdeel toegevoegd waarmee de huidige frequentie van de overdracht van de SDK wordt gecontroleerd en wordt het steekproef percentage aangepast om te proberen binnen het maximum percentage van de doel groep te blijven. De correctie wordt met regel matige tussen pozen opnieuw berekend en is gebaseerd op een zwevend gemiddelde van de uitgaande verzend snelheid.

## <a name="sampling-and-the-javascript-sdk"></a>Bemonstering en de Java script-SDK

De SDK aan de client zijde (Java script) maakt deel uit van een vast aantal steek proeven in combi natie met de SDK aan de server zijde. Op de pagina's met een instrument worden alleen telemetrie aan de client zijde verzonden van dezelfde gebruikers waarvoor de server kant is ingesteld op ' voor beeld in '. Deze logica is ontworpen om de integriteit van gebruikers sessies op client-en server zijde te hand haven. Als gevolg hiervan kunt u vanuit een bepaald telemetrie-item in Application Insights alle andere telemetrie-items voor deze gebruiker of sessie vinden. 

*In mijn telemetrie-client en server zijde worden geen gecoördineerde voor beelden weer gegeven zoals hierboven wordt beschreven.*

* Controleer of u de bemonsterde vaste frequentie hebt ingeschakeld op de server en de client.
* Zorg ervoor dat de SDK-versie 2,0 of hoger is.
* Controleer of u hetzelfde steekproef percentage hebt ingesteld in zowel de client als de server.

### <a name="sampling-in-azure-functions"></a>Bemonstering in Azure Functions

Volg de instructies in [Dit](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling) om steek proeven te configureren voor apps die worden uitgevoerd in azure functions.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

*Wat is het standaard steekproef gedrag in ASP.NET en de SDK van ASP.NET Core?*

* Als u een van de nieuwste versies van de bovenstaande SDK gebruikt, is adaptieve steek proeven standaard ingeschakeld met vijf telemetriegegevens per seconde.
  Er zijn twee AdaptiveSamplingTelemetryProcessors toegevoegd, waarbij een gebeurtenis type in steek proeven wordt opgenomen en de andere uitsluiten gebeurtenis type van steek proeven. Deze configuratie houdt in dat de SDK probeert om telemetriegegevens te beperken tot vijf telemetriegegevens van gebeurtenis typen en vijf telemetriegegevens van alle andere typen gecombineerd, waardoor de gebeurtenissen afzonderlijk van andere typen telemetrie worden gesampled. Gebeurtenissen worden meestal gebruikt voor zakelijke telemetrie, en worden waarschijnlijk niet beïnvloed door de diagnostische telemetrie-volumes.
  
  Hieronder ziet u het standaard bestand ApplicationInsights. config dat is gegenereerd. Zoals beschreven, worden er twee afzonderlijke knoop adaptivesamplingtelemetryprocessor-knoop punten toegevoegd, een uitzonderings gebeurtenis typen en een andere, inclusief. In ASP.NET Core is precies hetzelfde standaard gedrag ingeschakeld in code. Gebruik de voor beelden in de vorige sectie van het document om dit standaard gedrag te wijzigen.

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

*Kan telemetrie meerdere malen worden bemonsterd?*

* Nee. SamplingTelemetryProcessors negeren items van bemonsterings overwegingen als er al een voor beeld van het item is. Hetzelfde geldt ook voor de opname steekproef, waardoor er geen steek proeven worden toegepast op die items die al worden bemonsterd in de SDK zelf.

*Waarom wordt niet gesampling van een eenvoudig "Collect X procent van elk type telemetrie"?*

* Hoewel deze bemonsterings benadering een hoge mate van nauw keurigheid biedt in metrische benaderingen, zou het de mogelijkheid hebben om diagnostische gegevens per gebruiker, sessie en aanvraag te correleren, wat essentieel is voor diagnose. Daarom werkt steek proeven beter met ' alle telemetriegegevens items voor X% van app-gebruikers verzamelen ' of ' alle telemetrie voor X procent van app-aanvragen verzamelen '. Voor de telemetrie-items die niet aan de aanvragen zijn gekoppeld (zoals asynchrone verwerking op de achtergrond), is de terugval het ' collect X procent van alle items voor elk type telemetrie '. 

*Kan het steekproef percentage worden gewijzigd in de loop van de tijd?*

* Ja, met adaptieve steek proef wordt het steekproef percentage geleidelijk gewijzigd, op basis van het momenteel waargenomen volume van de telemetrie.

*Hoe weet ik welk steekproef percentage het beste werkt voor mijn app als ik steek proeven met een vaste frequentie gebruik?*

* Een manier is om te beginnen met adaptieve steek proeven, erachter te komen wat de rente is (Zie de bovenstaande vraag) en vervolgens over te scha kelen naar een vast aantal steek proeven met dat aantal. 
  
    Anders moet u raden. Analyseer het huidige gebruik van de telemetrie in Application Insights, Bekijk eventuele beperkingen die zich voordoen en schat het volume van de verzamelde telemetrie. Deze drie invoer waarden, samen met de geselecteerde prijs categorie, stellen aan hoeveel u het volume van de verzamelde telemetrie wellicht wilt reduceren. Een toename van het aantal gebruikers of een andere verschuiving in het volume van de telemetrie kan de schatting echter ongeldig maken.

*Wat gebeurt er als ik het sampling percentage te laag Configureer?*

* Buitensporig weinig steekproef percentage (over-agressieve steek proeven) vermindert de nauw keurigheid van de benaderingen, wanneer Application Insights probeert de visualisatie van de gegevens voor de reductie van het gegevens volume te compenseren. Het is ook mogelijk dat de diagnostische ervaring een negatieve invloed heeft op het aantal niet-regel matig mislukte of trage aanvragen.

*Wat gebeurt er als ik het sampling percentage te hoog Configureer?*

* Het configureren van een te hoge steekproef percentage (niet agressief genoeg) resulteert in een ontoereikende vermindering van het volume van de verzamelde telemetrie. U kunt nog steeds telemetrie-gegevens verlies ondervinden met betrekking tot beperking en de kosten voor het gebruik van Application Insights zijn mogelijk hoger dan gepland wegens overschrijding kosten.

*Op welke platformen kan ik steek proeven gebruiken?*

* Steek proeven voor opname kunnen automatisch worden uitgevoerd voor telemetrie boven een bepaald volume, als de SDK geen steek proeven uitvoert. Deze configuratie werkt bijvoorbeeld als u een oudere versie van de ASP.NET SDK of een eerdere versie van de Java SDK (1.0.10 of eerder) gebruikt.
* Als u ASP.NET SDK-versies 2.0.0 en hoger of ASP.NET CORE SDK-versie 2.2.0 en hoger gebruikt (gehost in azure of op uw eigen server), krijgt u standaard adaptieve steek proeven, maar u kunt overschakelen naar een vaste frequentie zoals hierboven wordt beschreven. Met een steek proef van een vaste frequentie synchroniseert de SDK van de browser automatisch met voor beeld van gerelateerde gebeurtenissen. 
* Als u Java SDK versie 2.0.1 of hoger gebruikt, kunt u ApplicationInsights. xml configureren om de sampling van vaste kosten in te scha kelen. Steek proeven zijn standaard uitgeschakeld. Met een steek proef van een vaste frequentie synchroniseert de SDK van de browser automatisch met voor beeld van gerelateerde gebeurtenissen.

*Er zijn bepaalde zeldzame gebeurtenissen die ik altijd wil zien. Hoe kan ik de bemonsterings module naraken?*

* De beste manier om dit te doen is door een aangepaste [TelemetryInitializer](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)te schrijven, waarmee de `SamplingPercentage` wordt ingesteld op 100 op het telemetrie-item dat u wilt behouden, zoals hieronder wordt weer gegeven. Als initialisatie functies worden gegarandeerd voordat telemetrie-processors (inclusief steek proeven) worden uitgevoerd, zorgt dit ervoor dat alle bemonsterings technieken dit item negeren van eventuele bemonsterings overwegingen.

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

## <a name="next-steps"></a>Volgende stappen

* [Filteren](../../azure-monitor/app/api-filtering-sampling.md) kan meer controle bieden over wat uw SDK verzendt.
* Lees het artikel over het ontwikkel netwerk om [telemetrie te optimaliseren met Application Insights](https://msdn.microsoft.com/magazine/mt808502.aspx).
