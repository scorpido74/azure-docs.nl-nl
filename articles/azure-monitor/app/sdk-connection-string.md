---
title: Verbindings reeksen in Azure-toepassing inzichten | Microsoft Docs
description: Verbindings reeksen gebruiken.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.reviewer: mbullwin
ms.openlocfilehash: d9906e43bad296cc850942c01c83c6bfee2651fb
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/27/2020
ms.locfileid: "85482119"
---
# <a name="connection-strings"></a>Verbindingsreeksen

## <a name="overview"></a>Overzicht

Met verbindings reeksen kunnen gebruikers van toepassings inzicht beschikken over een enkele configuratie-instelling, waardoor meerdere proxy-instellingen niet meer nodig zijn. Zeer nuttig voor intranet webservers, soevereine of hybride Cloud omgevingen die in gegevens willen verzenden naar de bewakings service.

De sleutel-waardeparen bieden een eenvoudige manier om gebruikers een combi natie van achtervoegsel voor voor voegsel te definiëren voor elke Application Insights (AI) service/product.

> [!IMPORTANT]
> Het is niet raadzaam om zowel de verbindings reeks als de instrumentatie sleutel in te stellen. In het geval dat een gebruiker beide heeft ingesteld, is de laatste ingesteld, afhankelijk van de waarde van de vorige. 


## <a name="scenario-overview"></a>Overzicht van scenario's 

Klanten scenario's waarin we dit hebben gevisualiseerd, hebben de meeste impact:

- Firewall-uitzonde ringen of proxy omleidingen 

    In gevallen waarin de bewaking voor intranet webserver is vereist, hebben onze oudere oplossingen klanten de mogelijkheid verleend om afzonderlijke service-eind punten toe te voegen aan uw configuratie. Klik [hier](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#can-i-monitor-an-intranet-web-server) voor meer informatie. 
    Verbindings reeksen bieden een beter alternatief door deze moeite te beperken tot één instelling. Een eenvoudig voor voegsel, achtervoegsel wijziging maakt automatische populatie en omleiding van alle eind punten naar de juiste services mogelijk. 

- Soevereine of hybride Cloud omgevingen

    Gebruikers kunnen gegevens verzenden naar een gedefinieerde [Azure Government regio](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights).
    Met verbindings reeksen kunt u eindpunt instellingen definiëren voor uw intranet servers of Hybrid Cloud-instellingen. 

## <a name="getting-started"></a>Aan de slag

### <a name="finding-my-connection-string"></a>Mijn connection string vinden?

Uw connection string wordt weer gegeven op de Blade overzicht van uw Application Insights resource.

![connection string op de Blade overzicht](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>Schema

#### <a name="max-length"></a>Max. lengte

De verbinding heeft een maximale ondersteunde lengte van 4096 tekens.

#### <a name="key-value-pairs"></a>Sleutel-waardeparen

De verbindings reeks bestaat uit een lijst met instellingen die worden weer gegeven als sleutel-waardeparen, gescheiden door punt komma's:`key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Syntax

- `InstrumentationKey`(bijvoorbeeld: 00000000-0000-0000-0000-000000000000)  Het connection string is een **verplicht** veld.
- `Authorization`(bijvoorbeeld: Ikey) (Deze instelling is optioneel, omdat we alleen iKey-autorisatie ondersteunen.)
- `EndpointSuffix`(bijvoorbeeld: applicationinsights.azure.cn) Als u het eind punt achtervoegsel instelt, wordt de SDK geïnstrueerd waarmee de Azure-Cloud verbinding maakt. De SDK stelt de rest van het eind punt samen voor afzonderlijke services.
- Expliciete eind punten.
  Elke service kan expliciet worden overschreven in de connection string.
   - `IngestionEndpoint`(bijvoorbeeld: `https://dc.applicationinsights.azure.com` )
   - `LiveEndpoint`(bijvoorbeeld: `https://live.applicationinsights.azure.com` )
   - `ProfilerEndpoint`(bijvoorbeeld: `https://profiler.applicationinsights.azure.com` )
   - `SnapshotEndpoint`(bijvoorbeeld: `https://snapshot.applicationinsights.azure.com` )

#### <a name="endpoint-schema"></a>Eindpunt schema

`<prefix>.<suffix>`
- Voor voegsel: definieert een service. 
- Achtervoegsel: Hiermee wordt de algemene domein naam gedefinieerd.

##### <a name="valid-suffixes"></a>Geldige achtervoegsels

Hier volgt een lijst met geldige achtervoegsels 
- applicationinsights.azure.cn
- applicationinsights.us


Zie ook:https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Geldige voor voegsels

- [Telemetrie-opname](./app-insights-overview.md):`dc`
- [Live metrische gegevens](./live-stream.md):`live`
- [Profiler](./profiler-overview.md):`profiler`
- [Moment opname](./snapshot-debugger.md):`snapshot`



## <a name="connection-string-examples"></a>Voor beelden van verbindings reeksen


### <a name="minimal-valid-connection-string"></a>Minimale geldige connection string

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

In dit voor beeld is alleen de instrumentatie sleutel ingesteld.

- Het autorisatie schema wordt standaard ingesteld op ' iKey ' 
- Instrumentatie sleutel: 00000000-0000-0000-0000-000000000000
- De regionale service-Uri's zijn gebaseerd op de [standaard instellingen](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6) van de SDK en er wordt verbinding gemaakt met de open bare wereld wijde Azure:
   - Opname`https://dc.services.visualstudio.com/`
   - Live metrische gegevens:`https://rt.services.visualstudio.com/`
   - Profiler`https://agent.azureserviceprofiler.net/`
   - Foutopsporingsversie`https://agent.azureserviceprofiler.net/`



### <a name="connection-string-with-endpoint-suffix"></a>Verbindings reeks met eindpunt achtervoegsel

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

In dit voor beeld geeft deze connection string het eindpunt achtervoegsel op en de SDK maakt service-eind punten.

- Het autorisatie schema wordt standaard ingesteld op ' iKey ' 
- Instrumentatie sleutel: 00000000-0000-0000-0000-000000000000
- De regionale service-Uri's zijn gebaseerd op het gegeven eind punt achtervoegsel: 
   - Opname`https://dc.ai.contoso.com`
   - Live metrische gegevens:`https://live.ai.contoso.com`
   - Profiler`https://profiler.ai.contoso.com`
   - Foutopsporingsversie`https://snapshot.ai.contoso.com`  



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Verbindings reeks met expliciete onderdrukkingen van eind punten 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

In dit voor beeld geeft deze connection string expliciete onderdrukkingen voor elke service op. De SDK gebruikt de exacte eind punten die zijn meegeleverd zonder aanpassing.

- Het autorisatie schema wordt standaard ingesteld op ' iKey ' 
- Instrumentatie sleutel: 00000000-0000-0000-0000-000000000000
- De regionale service-Uri's zijn gebaseerd op de expliciete onderdrukkings waarden: 
   - Opname`https://custom.com:111/`
   - Live metrische gegevens:`https://custom.com:222/`
   - Profiler`https://custom.com:333/`
   - Foutopsporingsversie`https://custom.com:444/`  


## <a name="how-to-set-a-connection-string"></a>Een connection string instellen

Verbindings reeksen worden ondersteund in de volgende SDK-versies:
- .NET en .NET core v 2.12.0
- Java v 2.5.1 en Java 3,0
- Java script v-2.3.0
- NodeJS v 1.5.0
- Python v-1.0.0

Een connection string kan worden ingesteld met behulp van code, omgevings variabele of configuratie bestand.



### <a name="environment-variable"></a>Omgevingsvariabele

- Verbindings reeks:`APPLICATIONINSIGHTS_CONNECTION_STRING`

# <a name="netnetcore"></a>[.NET/. NetCore](#tab/net)

TelemetryConfiguration. Connections Tring:https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

.NET expliciet ingesteld:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

.NET-configuratie bestand:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

NetCore expliciet ingesteld:
```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions { ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;" };
    services.AddApplicationInsightsTelemetry(options: options);
}
```

NetCore-config.jsop: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)


Java (v 2.5. x) expliciet ingesteld:
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

Belang rijk: Java script biedt geen ondersteuning voor het gebruik van omgevings variabelen.

Het fragment gebruiken:

```javascript
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```


Hand matige installatie:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

Gebruikers wordt aangeraden de omgevings variabele in te stellen.

De connection string expliciet instellen:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>Volgende stappen

Gebruik tijdens runtime:

* [Met IIS gehoste apps in Azure-VM en virtuele-machineschaalset van Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [IIS-server](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Web Apps](../../azure-monitor/app/azure-web-apps.md)

Gebruik tijdens het ontwikkelen:

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)
