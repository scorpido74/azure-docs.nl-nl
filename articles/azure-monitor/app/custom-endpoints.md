---
title: Azure Application Insights overschrijven standaard SDK-eindpunten
description: Wijzig standaard AZURE Monitor Application Insights SDK-eindpunten voor regio's zoals Azure Government.
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: b4ab05c7ee815b385ffb2d1ff9e621063d744dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298321"
---
# <a name="application-insights-overriding-default-endpoints"></a>Application Insights overschrijven standaardeindpunten

Als u gegevens van Application Insights naar bepaalde regio's wilt verzenden, moet u de standaardeindpuntadressen overschrijven. Elke SDK vereist iets andere wijzigingen, die allemaal worden beschreven in dit artikel. Deze wijzigingen vereisen het aanpassen van de voorbeeldcode `QuickPulse_Endpoint_Address` `TelemetryChannel_Endpoint_Address`en `Profile_Query_Endpoint_address` het vervangen van de tijdelijke aanduidingswaarden voor , en door de werkelijke eindpuntadressen voor uw specifieke regio. Het einde van dit artikel bevat koppelingen naar de eindpuntadressen voor regio's waar deze configuratie vereist is.

## <a name="sdk-code-changes"></a>Wijzigingen in SDK-code

### <a name="net-with-applicationinsightsconfig"></a>.NET met applicationinsights.config

> [!NOTE]
> Het bestand applicationinsights.config wordt automatisch overschreven wanneer een SDK-upgrade wordt uitgevoerd. Zorg er na het uitvoeren van een SDK-upgrade voor dat u de regiospecifieke eindpuntwaarden opnieuw invoert.

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

Wijzig het bestand appsettings.json in uw project als volgt om het hoofdeindpunt aan te passen:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

De waarden voor Live Metrics en het Eindpunt van profielquery's kunnen alleen via code worden ingesteld. Als u de standaardwaarden voor alle eindpuntwaarden via code `ConfigureServices` wilt `Startup.cs` overschrijven, voert u de volgende wijzigingen aan in de methode van het bestand:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

### <a name="azure-functions-v2x"></a>Azure-functies v2.x

Installeer de volgende pakketten in uw functieproject:

- Microsoft.ApplicationInsights versie 2.10.0
- Microsoft.ApplicationInsights.PerfCounterCollector versie 2.10.0
- Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel versie 2.10.0

Voeg vervolgens de opstartcode voor uw functietoepassing toe (of wijzigt):

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

Wijzig het bestand applicationinsights.xml om het standaardeindpuntadres te wijzigen.

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

Wijzig `application.properties` het bestand en voeg toe:

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

De eindpunten kunnen ook worden geconfigureerd door middel van omgevingsvariabelen:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

### <a name="javascript"></a>JavaScript

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

### <a name="python"></a>Python

Raadpleeg de [opencensus-python repo](https://github.com/census-instrumentation/opencensus-python/blob/af284a92b80bcbaf5db53e7e0813f96691b4c696/contrib/opencensus-ext-azure/opencensus/ext/azure/common/__init__.py) voor advies over het wijzigen van het innameeindpunt voor de opencensus-python SDK.

## <a name="regions-that-require-endpoint-modification"></a>Regio's waarvoor eindpuntwijziging vereist is

Momenteel zijn [azure-overheid](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights) en [Azure China](https://docs.microsoft.com/azure/china/resources-developer-guide)de enige regio's die eindpuntwijzigingen vereisen.

|Regio |  Eindpuntnaam | Waarde |
|-----------------|:------------|:-------------|
| Azure China | Telemetriekanaal | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure China | QuickPulse (live statistieken) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure China | Profielquery |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | Telemetriekanaal |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse (live statistieken) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | Profielquery |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

Als u momenteel de [Application Insights REST API](https://dev.applicationinsights.io/
) gebruikt die normaal gesproken via 'api.applicationinsights.io' wordt geopend, moet u een eindpunt gebruiken dat lokaal is voor uw regio:

|Regio |  Eindpuntnaam | Waarde |
|-----------------|:------------|:-------------|
| Azure China | REST API | `api.applicationinsights.azure.cn` |
| Azure Government | REST API | `api.applicationinsights.us`|

> [!NOTE]
> Codeless agent/extensiegebaseerde bewaking voor Azure App Services **wordt momenteel niet ondersteund** in deze regio's. Zodra deze functionaliteit beschikbaar komt, wordt dit artikel bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de gedetailleerde richtlijnen voor [Azure monitoring en beheer](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)voor meer informatie over de aangepaste wijzigingen voor Azure Government.
- Raadpleeg het [Azure China Playbook](https://docs.microsoft.com/azure/china/)voor meer informatie over Azure China.
