---
title: Verbindingstekenreeksen in Azure Application Insights | Microsoft Documenten
description: Verbindingstekenreeksen gebruiken.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 7b049c04913d3415074f46b9d90ec34be874a2da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136703"
---
# <a name="connection-strings"></a>Verbindingsreeksen

## <a name="overview"></a>Overzicht

Verbindingstekenreeksen bieden gebruikers van Application Insight één configuratie-instelling, waardoor meerdere proxy-instellingen niet nodig zijn. Zeer nuttig voor intranet webservers, soevereine of hybride cloudomgevingen die gegevens naar de monitoringservice willen verzenden.

De key value pairs bieden gebruikers een eenvoudige manier om een voorvoegsel-achtervoegselcombinatie te definiëren voor elke AI-service/-product (Application Insights).

> [!IMPORTANT]
> We raden u aan zowel verbindingstekenreeks als instrumentatietoets in te stellen. In het geval dat een gebruiker beide instelt, heeft de laatste plaats voorrang. 


## <a name="scenario-overview"></a>Overzicht van scenario's 

Klantscenario's waarbij we dit visualiseren met de meeste impact:

- Firewalluitzonderingen of proxyomleidingen 

    In gevallen waarin monitoring voor intranet webserver vereist is, vroeg onze eerdere oplossing klanten om individuele serviceeindpunten toe te voegen aan uw configuratie. Voor meer informatie, zie [hier](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#can-i-monitor-an-intranet-web-server). 
    Verbindingstekenreeksen bieden een beter alternatief door deze inspanning te reduceren tot één instelling. Een eenvoudig voorvoegsel, achtervoegsel wijziging maakt automatische populatie en omleiding van alle eindpunten naar de juiste diensten. 

- Soevereine of hybride cloudomgevingen

    Gebruikers kunnen gegevens verzenden naar een gedefinieerd [Azure Government-gebied.](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)
    Met verbindingstekenreeksen u eindpuntinstellingen definiëren voor uw intranetservers of hybride cloudinstellingen. 

## <a name="getting-started"></a>Aan de slag

### <a name="finding-my-connection-string"></a>Vind je mijn verbindingstekenreeks?

Uw verbindingstekenreeks wordt weergegeven op het overzichtsblad van uw Application Insights-bron.

![verbindingstekenreeks op overzichtsblad](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>Schema

#### <a name="max-length"></a>Max. lengte

De verbinding heeft een maximale ondersteunde lengte van 4096 tekens.

#### <a name="key-value-pairs"></a>Sleutel-waardeparen

Verbindingstekenreeks bestaat uit een lijst met instellingen die worden weergegeven als sleutelwaardeparen gescheiden door puntkomma:`key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Syntaxis

- `InstrumentationKey`(v. 00000000-0000-0000-0000-00000000000000000)  De verbindingstekenreeks is een **vereist** veld.
- `Authorization`(bijvoorbeeld ikey) (Deze instelling is optioneel omdat we vandaag alleen ikey-autorisatie ondersteunen.)
- `EndpointSuffix`(bijvoorbeeld applicationinsights.azure.cn) Als u het eindpuntachtervoegsel instelt, wordt de SDK inopdracht gegeven met welke Azure-cloud u verbinding moet maken. De SDK zal de rest van het eindpunt assembleren voor individuele diensten.
- Expliciete eindpunten.
  Elke service kan expliciet worden overschreven in de verbindingstekenreeks.
   - `IngestionEndpoint`(ex)https://dc.applicationinsights.azure.com)
   - `LiveEndpoint`(ex)https://live.applicationinsights.azure.com)
   - `ProfilerEndpoint`(ex)https://profiler.applicationinsights.azure.com)
   - `SnapshotEndpoint`(ex)https://snapshot.applicationinsights.azure.com)

#### <a name="endpoint-schema"></a>Eindpuntschema

`<prefix>.<suffix>`
- Voorvoegsel: definieert een service. 
- Achtervoegsel: definieert de algemene domeinnaam.

##### <a name="valid-suffixes"></a>Geldige achtervoegsels

Hier is een lijst met geldige achtervoegsels 
- applicationinsights.azure.cn
- applicationinsights.us


Zie ook:https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Geldige voorvoegsels

- [Telemetrie-opname](./app-insights-overview.md):`dc`
- [Live statistieken:](./live-stream.md)`live`
- [Profiler](./profiler-overview.md): Profiler :`profiler`
- [Momentopname](./snapshot-debugger.md):`snapshot`



## <a name="connection-string-examples"></a>Voorbeelden van verbindingstekenreeksen


### <a name="minimal-valid-connection-string"></a>Minimale geldige verbindingstekenreeks

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

In dit voorbeeld is alleen de instrumentatiesleutel ingesteld.

- Autorisatieregeling wordt standaard 'ikey' 
- Instrumentatiesleutel: 00000000-0000-0000-0000-000000000000000000
- De URL's van de regionale service zijn gebaseerd op de [SDK-standaardinstellingen](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6) en maken verbinding met de openbare globale Azure:
   - Ingestion:https://dc.services.visualstudio.com/
   - Live statistieken:https://rt.services.visualstudio.com/
   - Profiler:https://agent.azureserviceprofiler.net/
   - Debugger:https://agent.azureserviceprofiler.net/  



### <a name="connection-string-with-endpoint-suffix"></a>Verbindingstekenreeks met eindpuntachtervoegsel

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

In dit voorbeeld geeft deze verbindingstekenreeks het eindpuntachtervoegsel op en de SDK worden serviceeindpunten gebouwd.

- Autorisatieregeling wordt standaard 'ikey' 
- Instrumentatiesleutel: 00000000-0000-0000-0000-000000000000000000
- De URL's van de regionale dienst zijn gebaseerd op het meegeleverde eindpuntachtervoegsel: 
   - Ingestion:https://dc.ai.contoso.com
   - Live statistieken:https://live.ai.contoso.com
   - Profiler:https://profiler.ai.contoso.com 
   - Debugger:https://snapshot.ai.contoso.com   



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Verbindingstekenreeks met expliciete eindpuntoverschrijvingen 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

In dit voorbeeld geeft deze verbindingstekenreeks expliciete overschrijvingen op voor elke service. De SDK zal gebruik maken van de exacte eindpunten die zonder wijziging.

- Autorisatieregeling wordt standaard 'ikey' 
- Instrumentatiesleutel: 00000000-0000-0000-0000-000000000000000000
- De URL's van de regionale dienst zijn gebaseerd op de expliciete overschrijfwaarden: 
   - Inname:\/https: /custom.com:111/
   - Live metrics:\/https: /custom.com:222/
   - Profiler: https:\//custom.com:333/ 
   - Foutopsporing: https:\//custom.com:444/   


## <a name="how-to-set-a-connection-string"></a>Een verbindingstekenreeks instellen

Verbindingstekenreeksen worden ondersteund in de volgende SDK-versies:
- .NET en .NET Core v2.12.0
- Java v2.5.1
- Javascript v2.3.0
- NodeJS v1.5.0
- Python v1.0.0

Een verbindingstekenreeks kan worden ingesteld op code, omgevingsvariabele of configuratiebestand.



### <a name="environment-variable"></a>Omgevingsvariabele

- Verbindingsreeks:`APPLICATIONINSIGHTS_CONNECTION_STRING`

### <a name="net-sdk-example"></a>.Net SDK voorbeeld

TelemetrieConfiguratie.ConnectionString:https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

.Net expliciet ingesteld:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

.Net Config-bestand:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```


NetCore config.json: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


### <a name="java-sdk-example"></a>Voorbeeld van Java SDK


Java expliciet ingesteld:
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

### <a name="javascript-sdk-example"></a>Javascript SDK-voorbeeld

Belangrijk: Javascript ondersteunt het gebruik van omgevingsvariabelen niet.

Met behulp van het fragment:

```javascript
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```


Handmatig instellen:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

### <a name="node-sdk-example"></a>Voorbeeld van Node SDK

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

### <a name="python-sdk-example"></a>Python SDK voorbeeld

We raden gebruikers aan de omgevingsvariabele in te stellen.

Ga als u de verbindingstekenreeks expliciet instelt:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>Volgende stappen

Gebruik tijdens runtime:

* [Azure VM- en Azure-apps voor virtuele machines](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [IIS-server](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Web Apps](../../azure-monitor/app/azure-web-apps.md)

Gebruik tijdens het ontwikkelen:

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)
