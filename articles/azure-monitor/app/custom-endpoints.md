---
title: Azure Monitor-Azure-toepassing Insights standaard SDK-eind punten overschrijven | Microsoft Docs
description: Wijzig de standaard Azure-toepassing Insights-eind punten voor de SDK voor regio's als Azure Government.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/26/2019
ms.openlocfilehash: e1db9782fe923f7a5759f4e001cd0db970606fed
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677481"
---
# <a name="application-insights-overriding-default-endpoints"></a>Application Insights standaard eindpunten overschrijven

Als u gegevens van Application Insights naar bepaalde regio's wilt verzenden, moet u de standaard eindpunt adressen onderdrukken. Elke SDK vereist iets verschillende wijzigingen, die allemaal in dit artikel worden beschreven. Deze wijzigingen moeten de voorbeeld code aanpassen en de tijdelijke aanduidingen voor `QuickPulse_Endpoint_Address`, `TelemetryChannel_Endpoint_Address` en `Profile_Query_Endpoint_address` vervangen door de werkelijke eindpunt adressen voor uw specifieke regio. Het einde van dit artikel bevat koppelingen naar de eindpunt adressen voor regio's waarvoor deze configuratie is vereist.

## <a name="sdk-code-changes"></a>SDK-code wijzigingen

### <a name="net-with-applicationinsightsconfig"></a>.NET met applicationinsights. config

> [!NOTE]
> Het bestand applicationinsights. config wordt automatisch overschreven wanneer een SDK-upgrade wordt uitgevoerd. Nadat u een SDK-upgrade hebt uitgevoerd, moet u de regio-specifieke eindpunt waarden opnieuw invoeren.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>Custom_QuickPulse_Endpoint_Address</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>Profile_Query_Endpoint_address</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="aspnet-core"></a>ASP.NET Core

Wijzig het bestand appSettings. json in uw project als volgt om het belangrijkste eind punt aan te passen:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

De waarden voor dynamische metrische gegevens en het eind punt voor de profiel query kunnen alleen worden ingesteld via code. Als u de standaard waarden voor alle eindpunt waarden via code wilt overschrijven, moet u de volgende wijzigingen aanbrengen in de `ConfigureServices` methode van het `Startup.cs`-bestand:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

### <a name="azure-functions-v2x"></a>Azure Functions v2. x

Installeer de volgende pakketten in uw functie project:

- Micro soft. ApplicationInsights versie 2.10.0
- Micro soft. ApplicationInsights. PerfCounterCollector versie 2.10.0
- Micro soft. ApplicationInsights. WindowsServer. TelemetryChannel-versie 2.10.0

Voeg vervolgens de opstart code voor uw functie toepassing toe (of wijzig deze):

```csharp
[assembly: WebJobsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IWebJobsBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "QuickPulse_Endpoint_Address"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });
      }
  }
}
```

### <a name="java"></a>Java

Wijzig het applicationinsights. XML-bestand om het standaard eindpunt adres te wijzigen.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Wijzig het `application.properties`-bestand en voeg het volgende toe:

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "TelemetryChannel_Endpoint_Address"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "Profile_Query_Endpoint_address"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "QuickPulse_Endpoint_Address"; //live metrics
appInsights.Configuration.start();
```

De eind punten kunnen ook worden geconfigureerd via omgevings variabelen:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

### <a name="javascript"></a>Javascript

```javascript
<script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      endpointUrl: "TelemetryChannel_Endpoint_Address"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

## <a name="regions-that-require-endpoint-modification"></a>Regio's waarvoor wijziging van het eind punt is vereist

Momenteel zijn de enige regio's waarvoor wijziging van het eind punt is vereist, [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights) en [Azure China](https://docs.microsoft.com/azure/china/resources-developer-guide).

|Regio |  Naam van eind punt | Waarde |
|-----------------|:------------|:-------------|
| Azure China | Telemetrie-kanaal | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure China | QuickPulse (Live Metrics) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure China | Profiel query |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | Telemetrie-kanaal |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse (Live Metrics) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | Profiel query |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

Als u momenteel de [Application Insights rest API](https://dev.applicationinsights.io/
) gebruikt die normaal gesp roken wordt geopend via API.applicationinsights.io, moet u een eind punt gebruiken dat lokaal is voor uw regio:

|Regio |  Naam van eind punt | Waarde |
|-----------------|:------------|:-------------|
| Azure China | REST-API | `api.applicationinsights.azure.cn` |
| Azure Government | REST-API | `api.applicationinsights.us`|

> [!NOTE]
> Bewaking op basis van agent/uitbrei ding voor Azure-app Services wordt **momenteel niet ondersteund** in deze regio's. Zodra deze functionaliteit beschikbaar wordt, wordt dit artikel bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de gedetailleerde richt lijnen voor [controle en beheer van Azure](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)voor meer informatie over de aangepaste wijzigingen voor Azure Government.
- Raadpleeg de [Azure China Playbook](https://docs.microsoft.com/azure/china/)voor meer informatie over Azure China.
