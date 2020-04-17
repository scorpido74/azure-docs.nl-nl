---
title: ApplicationInsights.config referentie - Azure | Microsoft Documenten
description: Modules voor gegevensverzameling in- of uitschakelen en prestatiemeteritems en andere parameters toevoegen.
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: olegan
ms.openlocfilehash: 3375c24739da8663aa6a40deeb53e02e65d1f9bf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537556"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>De Application Insights-SDK configureren met ApplicationInsights.config of ApplicationInsights.xml
De Application Insights .NET SDK bestaat uit een aantal NuGet pakketten. Het [kernpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights) biedt de API voor het verzenden van telemetrie naar de Application Insights. [Aanvullende pakketten](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) bieden telemetriemodules en *initialisators* voor het automatisch volgen van telemetrie vanuit uw toepassing en de context ervan. *modules* Door het configuratiebestand aan te passen, u telemetriemodules en initialisators in- of uitschakelen en parameters instellen voor sommige van deze bestanden.

Het configuratiebestand `ApplicationInsights.config` heeft `ApplicationInsights.xml`de naam of , afhankelijk van het type van uw toepassing. Het wordt automatisch toegevoegd aan uw project wanneer u [de meeste versies van de SDK installeert.][start] Wanneer u de geautomatiseerde ervaring gebruikt van de Visual Studio-sjabloonprojecten die Ondersteuning bieden **voor Add > Application Insights Telemetrie,** wordt het bestand ApplicationInsights.config standaard gemaakt in de hoofdmap van het project en wordt het bestand wordt gekopieerd naar de map met opslaglocatie. Het wordt ook toegevoegd aan een web-app door [Status Monitor op een IIS-server.][redfield] Het configuratiebestand wordt genegeerd als [extensie voor Azure-website](azure-web-apps.md) of [-extensie voor Azure VM en virtuele machineschaalset](azure-vm-vmss-apps.md) wordt gebruikt.

Er is geen gelijkwaardig bestand om de SDK in een webpagina te [bedienen.][client]

In dit document worden de secties beschreven die u in het configuratiebestand ziet, hoe ze de onderdelen van de SDK beheren en welke NuGet-pakketten deze componenten laden.

> [!NOTE]
> De instructies voor ApplicationInsights.config en .xml zijn niet van toepassing op de .NET Core SDK. Volg [deze](../../azure-monitor/app/asp-net-core.md) handleiding voor het configureren van .NET Core-toepassingen.

## <a name="telemetry-modules-aspnet"></a>Telemetriemodules (ASP.NET)
Elke telemetriemodule verzamelt een specifiek type gegevens en gebruikt de kern-API om de gegevens te verzenden. De modules worden geïnstalleerd door verschillende NuGet-pakketten, die ook de vereiste regels toevoegen aan het .config-bestand.

Er is een knooppunt in het configuratiebestand voor elke module. Als u een module wilt uitschakelen, verwijdert u het knooppunt of geeft u deze een opmerking.

### <a name="dependency-tracking"></a>Afhankelijkheid bijhouden
[Afhankelijkheidstracking](../../azure-monitor/app/asp-net-dependencies.md) verzamelt telemetrie over gesprekken die uw app maakt naar databases en externe services en databases. Als u wilt dat deze module in een IIS-server werkt, moet u [statusmonitor installeren.][redfield]

U ook uw eigen afhankelijkheidstrackingcode schrijven met behulp van de [TrackDependency API.](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet-pakket.

Afhankelijkheden kunnen automatisch worden verzameld zonder uw code te wijzigen met behulp van agent-gebaseerde (codeloze) bijlage. Als u het wilt gebruiken in Azure-webapps, schakelt u de [extensie Application Insights](azure-web-apps.md)in. Als u deze wilt gebruiken in Azure VM- of Azure-schaalset voor virtuele machines, schakelt u de [extensie Toepassingsbewaking in voor vm- en virtuele machineschaalset.](azure-vm-vmss-apps.md)

### <a name="performance-collector"></a>Prestatieverzamelaar
[Verzamelt systeemprestatiemeteritems](../../azure-monitor/app/performance-counters.md) zoals CPU, geheugen en netwerkbelasting van IIS-installaties. U opgeven welke tellers u wilt verzamelen, inclusief prestatiemeteritems die u zelf hebt ingesteld.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet-pakket.

### <a name="application-insights-diagnostics-telemetry"></a>Application Insights Diagnostics Telemetry
De `DiagnosticsTelemetryModule` rapporten fouten in de Application Insights instrumentatiecode zelf. Bijvoorbeeld als de code geen toegang heeft `ITelemetryInitializer` tot prestatiemeteritems of als een uitzondering wordt gemaakt. Trace telemetrie die door deze module wordt bijgehouden, wordt weergegeven in de [diagnostische zoekopdracht][diagnostic].

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>Ontwikkelaarsmodus
`DeveloperModeWithDebuggerAttachedTelemetryModule`dwingt de `TelemetryChannel` Application Insights om gegevens onmiddellijk te verzenden, één telemetrie-item tegelijk, wanneer een foutopsporing is gekoppeld aan de aanvraagprocedure. Dit vermindert de hoeveelheid tijd tussen het moment waarop uw toepassing telemetrie bijhoudt en wanneer deze wordt weergegeven op de Portal Application Insights. Het veroorzaakt aanzienlijke overhead in CPU en netwerkbandbreedte.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Toepassingsinzichten Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet-pakket

### <a name="web-request-tracking"></a>Webrequest tracking
Rapporteert de [reactietijd en de resultaatcode](../../azure-monitor/app/asp-net.md) van HTTP-aanvragen.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet-pakket

### <a name="exception-tracking"></a>Uitzonderingstracking
`ExceptionTrackingTelemetryModule`houdt onverwerkte uitzonderingen in uw web-app bij. Zie [Fouten en uitzonderingen][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet-pakket
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`- [niet-waargenomen taakuitzonderingen](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx)bijhoudt .
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`- houdt onverwerkte uitzonderingen bij voor werknemersrollen, Windows-services en consoletoepassingen.
* [Toepassingsinzichten Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet pakket.

### <a name="eventsource-tracking"></a>EventSource-tracking
`EventSourceTelemetryModule`hiermee u EventSource-gebeurtenissen configureren om als traces naar Application Insights te worden verzonden. Zie [EventSource-gebeurtenissen gebruiken](../../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events)voor informatie over het bijhouden van EventSource-gebeurtenissen.

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>ETW-gebeurtenistracking
`EtwCollectorTelemetryModule`hiermee u gebeurtenissen van ETW-providers configureren die als traces naar Application Insights worden verzonden. Zie ETW-gebeurtenissen gebruiken voor informatie over het bijhouden [van ETW-gebeurtenissen.](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events)

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Het Microsoft.ApplicationInsights-pakket biedt de [belangrijkste API](https://msdn.microsoft.com/library/mt420197.aspx) van de SDK. De andere telemetriemodules gebruiken dit en u het ook [gebruiken om uw eigen telemetrie te definiëren.](../../azure-monitor/app/api-custom-events-metrics.md)

* Geen vermelding in ApplicationInsights.config.
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet-pakket. Als je dit NuGet gewoon installeert, wordt er geen .config-bestand gegenereerd.

## <a name="telemetry-channel"></a>Telemetriekanaal
Het [telemetriekanaal](telemetry-channels.md) beheert de buffering en overdracht van telemetrie naar de Application Insights-service.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`is het standaardkanaal voor webtoepassingen. Het buffert gegevens in het geheugen en maakt gebruik van nieuwe mechanismen en lokale schijfopslag voor betrouwbaardere telemetrielevering.
* `Microsoft.ApplicationInsights.InMemoryChannel`is een lichtgewicht telemetriekanaal, dat wordt gebruikt als er geen ander kanaal is geconfigureerd. 

## <a name="telemetry-initializers-aspnet"></a>Telemetrieinitialisten (ASP.NET)
Telemetrie Initializers stellen contexteigenschappen in die samen met elk item telemetrie worden verzonden.

U [uw eigen initialisators schrijven](../../azure-monitor/app/api-filtering-sampling.md#add-properties) om contexteigenschappen in te stellen.

De standaard initialisators zijn allemaal ingesteld door de Web- of WindowsServer NuGet-pakketten:

* `AccountIdTelemetryInitializer`hiermee wordt de eigenschap AccountId ingesteld.
* `AuthenticatedUserIdTelemetryInitializer`hiermee wordt de eigenschap AuthenticatedUserId ingesteld zoals ingesteld door de JavaScript SDK.
* `AzureRoleEnvironmentTelemetryInitializer`hiermee `RoleName` worden `RoleInstance` de `Device` eigenschappen en eigenschappen van de context bijgewerkt voor alle telemetrie-items met informatie die is geëxtraheerd uit de Azure-runtime-omgeving.
* `BuildInfoConfigComponentVersionTelemetryInitializer`hiermee `Version` wordt de `Component` eigenschap van de context voor alle `BuildInfo.config` telemetrie-items bijgewerkt met de waarde die is opgehaald uit het bestand dat door MS Build is geproduceerd.
* `ClientIpHeaderTelemetryInitializer`hiermee `Ip` wordt `Location` de eigenschap van de context `X-Forwarded-For` van alle telemetrie-items bijgewerkt op basis van de HTTP-header van de aanvraag.
* `DeviceTelemetryInitializer`hiermee worden de `Device` volgende eigenschappen van de context voor alle telemetrie-items bijgewerkt.
  * `Type`is ingesteld op "PC"
  * `Id`is ingesteld op de domeinnaam van de computer waar de webtoepassing wordt uitgevoerd.
  * `OemName`is ingesteld op de waarde `Win32_ComputerSystem.Manufacturer` die met WMI uit het veld wordt gehaald.
  * `Model`is ingesteld op de waarde `Win32_ComputerSystem.Model` die met WMI uit het veld wordt gehaald.
  * `NetworkType`is ingesteld op de waarde `NetworkInterface`die wordt gewonnen uit de .
  * `Language`is ingesteld op de `CurrentCulture`naam van de .
* `DomainNameRoleInstanceTelemetryInitializer`hiermee `RoleInstance` wordt de `Device` eigenschap van de context bijgewerkt voor alle telemetrie-items met de domeinnaam van de computer waar de webtoepassing wordt uitgevoerd.
* `OperationNameTelemetryInitializer`hiermee `Name` wordt de `RequestTelemetry` eigenschap `Name` van `Operation` de eigenschap en de eigenschap van de context van alle telemetrie-items bijgewerkt op basis van de HTTP-methode, evenals namen van ASP.NET MVC-controller en actie die wordt aangeroepen om de aanvraag te verwerken.
* `OperationIdTelemetryInitializer`of `OperationCorrelationTelemetryInitializer` werkt `Operation.Id` de contexteigenschap van alle telemetrie-items bij `RequestTelemetry.Id`die worden bijgehouden tijdens het afhandelen van een aanvraag met de automatisch gegenereerde .
* `SessionTelemetryInitializer`hiermee `Id` wordt de `Session` eigenschap van de context bijgewerkt voor `ai_session` alle telemetrie-items met waarde die wordt geëxtraheerd uit de cookie die wordt gegenereerd door de ApplicationInsights JavaScript-instrumentatiecode die wordt uitgevoerd in de browser van de gebruiker.
* `SyntheticTelemetryInitializer`of `SyntheticUserAgentTelemetryInitializer` werkt `User` `Session`de `Operation` eigenschappen van alle telemetrie-items bij die worden bijgehouden bij het verwerken van een verzoek van een synthetische bron, zoals een beschikbaarheidstest of zoekmachinebot. [Metrics Explorer](../../azure-monitor/platform/metrics-charts.md) geeft standaard geen synthetische telemetrie weer.

    De `<Filters>` set die de eigenschappen van de aanvragen identificeert.
* `UserTelemetryInitializer`hiermee `Id` worden `AcquisitionDate` de `User` contexten en eigenschappen van de `ai_user` context bijgewerkt met waarden die worden geëxtraheerd uit de cookie die wordt gegenereerd door de JavaScript-instrumentatiecode van Application Insights die in de browser van de gebruiker wordt uitgevoerd.
* `WebTestTelemetryInitializer`hiermee worden de gebruikers-id, sessie-id en de eigenschappen van de synthetische bron ingesteld voor HTTP-aanvragen die afkomstig zijn van [beschikbaarheidstests.](../../azure-monitor/app/monitor-web-app-availability.md)
  De `<Filters>` set die de eigenschappen van de aanvragen identificeert.

Voor .NET-toepassingen die in Service `Microsoft.ApplicationInsights.ServiceFabric` Fabric worden uitgevoerd, u het NuGet-pakket opnemen. Dit pakket `FabricTelemetryInitializer`bevat een , die Service Fabric eigenschappen toevoegt aan telemetrie items. Zie voor meer informatie de [GitHub-pagina](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) over de eigenschappen die zijn toegevoegd aan dit NuGet-pakket.

## <a name="telemetry-processors-aspnet"></a>Telemetrieprocessors (ASP.NET)
Telemetrieprocessors kunnen elk telemetrieitem filteren en wijzigen vlak voordat het van de SDK naar de portal wordt verzonden.

U [uw eigen telemetrieprocessors schrijven.](../../azure-monitor/app/api-filtering-sampling.md#filtering)

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Adaptieve sampling telemetrieprocessor (van 2.0.0-beta3)
Deze optie is standaard ingeschakeld. Als uw app veel telemetrie verzendt, verwijdert deze processor een deel ervan.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

De parameter biedt het doel dat het algoritme probeert te bereiken. Elk exemplaar van de SDK werkt onafhankelijk van elkaar, dus als uw server een cluster van meerdere machines is, wordt het werkelijke volume telemetrie dienovereenkomstig vermenigvuldigd.

[Meer informatie over sampling](../../azure-monitor/app/sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Telemetrieprocessor met vaste snelheid (van 2.0.0-beta1)
Er is ook een standaard [sampling Telemetry Processor](../../azure-monitor/app/api-filtering-sampling.md) (vanaf 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```

## <a name="instrumentationkey"></a>Instrumentatiesleutel
Dit bepaalt de application insights-bron waarin uw gegevens worden weergegeven. Meestal maakt u een aparte bron, met een aparte sleutel, voor elk van uw toepassingen.

Als u de sleutel dynamisch wilt instellen - bijvoorbeeld als u resultaten van uw toepassing naar verschillende bronnen wilt verzenden - u de sleutel uit het configuratiebestand weglaten en in plaats daarvan in code instellen.

De sleutel instellen voor alle exemplaren van TelemetryClient, inclusief standaard telemetriemodules. Doe dit in een initialisatiemethode, zoals global.aspx.cs in een ASP.NET-service:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights;

    protected void Application_Start()
    {
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
        var telemetryClient = new TelemetryClient(configuration);
   
```

Als u alleen een specifieke set gebeurtenissen naar een andere resource wilt verzenden, u de sleutel instellen voor een specifieke TelemetryClient:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Als u een nieuwe sleutel wilt krijgen, [maakt u een nieuwe bron in de portal Application Insights.][new]



## <a name="applicationid-provider"></a>ApplicationId Provider

_Beschikbaar vanaf v2.6.0_

Het doel van deze provider is om een toepassings-id op te zoeken op basis van een instrumentatiesleutel. De toepassings-id is opgenomen in RequestTelemetry en DependencyTelemetry en wordt gebruikt om correlatie in de portal te bepalen.

Dit is beschikbaar `TelemetryConfiguration.ApplicationIdProvider` door het instellen van code of in config.

### <a name="interface-iapplicationidprovider"></a>Interface: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Wij bieden twee implementaties in de `ApplicationInsightsApplicationIdProvider` [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) sdk: en `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Dit is een wrapper rond onze Profile API. Het zal het beperken van verzoeken en cache resultaten.

Deze provider wordt aan uw config-bestand toegevoegd wanneer u [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) of [Microsoft.ApplicationInsights.Web installeert](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Deze klasse heeft `ProfileQueryEndpoint`een optionele eigenschap.
Standaard is dit `https://dc.services.visualstudio.com/api/profiles/{0}/appId`ingesteld op .
Als u een proxy voor deze configuratie moet configureren, raden we u aan{0}het basisadres te proxyn en "/api/profiles/ /appId" op te nemen. Houd er{0}rekening mee dat ' ' wordt vervangen bij runtime per aanvraag met de instrumentatiesleutel.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Voorbeeldconfiguratie via ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Voorbeeldconfiguratie via code:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>WoordenboekApplicationIdProvider

Dit is een statische provider, die zal vertrouwen op uw geconfigureerde Instrumentatiesleutel / Application ID-paren.

Deze klasse heeft `Defined`een eigenschap , een woordenboek<tekenreeks, tekenreeks> van Instrumentatiesleutel tot toepassings-id-paren.

Deze klasse heeft `Next` een optionele eigenschap die kan worden gebruikt om een andere provider te configureren om te gebruiken wanneer een instrumentatiesleutel wordt aangevraagd die niet in uw configuratie bestaat.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Voorbeeldconfiguratie via ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.DictionaryApplicationIdProvider, Microsoft.ApplicationInsights">
        <Defined>
            <Type key="InstrumentationKey_1" value="ApplicationId_1"/>
            <Type key="InstrumentationKey_2" value="ApplicationId_2"/>
        </Defined>
        <Next Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Voorbeeldconfiguratie via code:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>Volgende stappen
[Meer informatie over de API][api].

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: ../../azure-monitor/app/asp-net-exceptions.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[new]: ../../azure-monitor/app/create-new-resource.md 
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
