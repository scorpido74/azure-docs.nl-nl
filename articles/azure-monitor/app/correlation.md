---
title: Telemetriecorrelatie Azure Application Insights | Microsoft Documenten
description: Correlatie met telemetrie van Application Insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: c68b83726371d346019d18d0b066173f93196e6d
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982052"
---
# <a name="telemetry-correlation-in-application-insights"></a>Telemetriecorrelatie in Application Insights

In de wereld van microservices, elke logische operatie vereist werk worden gedaan in verschillende onderdelen van de dienst. U elk van deze componenten afzonderlijk controleren met Behulp van [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights ondersteunt gedistribueerde telemetriecorrelatie, die u gebruikt om te detecteren welke component verantwoordelijk is voor storingen of prestatiedegradatie.

In dit artikel wordt uitgelegd in het gegevensmodel dat Door Application Insights wordt gebruikt om telemetrie te correleren die door meerdere componenten wordt verzonden. Het omvat context-propagatietechnieken en protocollen. Het heeft ook betrekking op de implementatie van correlatietactieken op verschillende talen en platforms.

## <a name="data-model-for-telemetry-correlation"></a>Gegevensmodel voor telemetriecorrelatie

Application Insights definieert een [gegevensmodel](../../azure-monitor/app/data-model.md) voor gedistribueerde telemetriecorrelatie. Als u telemetrie wilt koppelen aan een logische `operation_Id`bewerking, heeft elk telemetrieitem een contextveld met de naam . Deze id wordt gedeeld door elk telemetrieitem in het gedistribueerde trace. Dus zelfs als u telemetrie verliest van één laag, u telemetrie die door andere componenten wordt gerapporteerd, nog steeds koppelen.

Een gedistribueerde logische bewerking bestaat meestal uit een set kleinere bewerkingen die door een van de componenten worden verwerkt. Deze bewerkingen worden gedefinieerd door [request telemetrie](../../azure-monitor/app/data-model-request-telemetry.md). Elk verzoek telemetrie `id` item heeft zijn eigen dat identificeert het uniek en wereldwijd. En alle telemetrie-items (zoals sporen en uitzonderingen) die `operation_parentId` aan de aanvraag `id`zijn gekoppeld, moeten de waarde van de aanvraag instellen.

Elke uitgaande bewerking, zoals een HTTP-aanroep naar een andere component, wordt vertegenwoordigd door [afhankelijkheidstelemetrie](../../azure-monitor/app/data-model-dependency-telemetry.md). Afhankelijkheid telemetrie definieert `id` ook zijn eigen die wereldwijd uniek is. Verzoek telemetrie, geïnitieerd door deze afhankelijkheidsoproep, gebruikt dit `id` als zijn `operation_parentId`.

U een weergave van de gedistribueerde `operation_parentId`logische `request.id` `dependency.id`bewerking maken met behulp van `operation_Id`, en met. Deze velden definiëren ook de causaliteitsvolgorde van telemetrieoproepen.

In een microservices-omgeving kunnen sporen van componenten naar verschillende opslagartikelen gaan. Elk onderdeel kan zijn eigen instrumentatiesleutel hebben in Application Insights. Als u telemetrie wilt krijgen voor de logische bewerking, stelt Application Insights gegevens op van elk opslagitem. Wanneer het aantal opslagitems groot is, hebt u een hint nodig over waar u nu moet uitzien. Het gegevensmodel Application Insights definieert twee `request.source` `dependency.target`velden om dit probleem op te lossen: en . Het eerste veld identificeert de component die de afhankelijkheidsaanvraag heeft gestart. Het tweede veld identificeert welke component de reactie van de afhankelijkheidsaanroep heeft geretourneerd.

## <a name="example"></a>Voorbeeld

Laten we eens naar een voorbeeld kijken. Een toepassing genaamd Stock Prices toont de huidige marktprijs van een aandeel met behulp van een externe API genaamd Stock. De toepassing Aandelenkoersen heeft een pagina met de naam `GET /Home/Stock`Stock-pagina die de webbrowser van de client opent met behulp van . De toepassing query's de Stock `GET /api/stock/value`API met behulp van de HTTP-aanroep .

U de resulterende telemetrie analyseren door een query uit te voeren:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Houd er in de resultaten rekening mee `operation_Id`dat alle telemetrie-items de hoofdmap delen. Wanneer een Ajax-oproep vanaf de pagina wordt`qJSXU`gedaan, wordt een nieuwe unieke ID ( ) toegewezen `operation_ParentId`aan de telemetrie van afhankelijkheid en wordt de ID van de pageView gebruikt als . De serveraanvraag gebruikt vervolgens `operation_ParentId`de Ajax-id als .

| itemType   | name                      | Id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| Paginaweergave   | Voorraadpagina                |              | STYz STYz               | STYz STYz         |
| Afhankelijkheid | GET /Home/Stock           | qJSXU qJSXU        | STYz STYz               | STYz STYz         |
| Verzoek    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU qJSXU              | STYz STYz         |
| Afhankelijkheid | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz STYz         |

Wanneer de `GET /api/stock/value` oproep wordt gedaan naar een externe service, moet u de `dependency.target` identiteit van die server kennen, zodat u het veld op de juiste manier instellen. Wanneer de externe service de `target` bewaking niet ondersteunt, wordt ingesteld op `stock-prices-api.com`de hostnaam van de service (bijvoorbeeld). Maar als de service zich identificeert door een `target` vooraf gedefinieerde HTTP-header terug te sturen, bevat deze de service-identiteit waarmee Application Insights een gedistribueerd e-overs te bouwen door telemetrie van die service op te vragen.

## <a name="correlation-headers"></a>Correlatiekoppen

Application Insights is de overgang naar [W3C Trace-Context](https://w3c.github.io/trace-context/), die definieert:

- `traceparent`: Draagt de wereldwijd unieke operationele ID en unieke id van de oproep.
- `tracestate`: Draagt systeemspecifieke traceringscontext.

De nieuwste versie van de Application Insights SDK ondersteunt het Trace-Context-protocol, maar mogelijk moet u zich hiervoor aanmelden. (Achterwaartse compatibiliteit met het vorige correlatieprotocol dat wordt ondersteund door de Application Insights SDK, blijft behouden.)

De [correlatie HTTP-protocol, ook wel Request-Id](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), wordt afgeschaft. Dit protocol definieert twee kopteksten:

- `Request-Id`: Draagt de wereldwijd unieke ID van het gesprek.
- `Correlation-Context`: Draagt de naam-waarde paren collectie van de gedistribueerde trace eigenschappen.

Application Insights definieert ook de [extensie](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) voor het http-protocol met correlatie. Het `Request-Context` maakt gebruik van naam-waarde paren om de verzameling van eigenschappen die worden gebruikt door de onmiddellijke beller of beller te verspreiden. De Application Insights SDK gebruikt `dependency.target` deze `request.source` header om de velden in te stellen.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>W3C-ondersteuning voor gedistribueerde tracering inschakelen voor klassieke ASP.NET-apps
 
  > [!NOTE]
  >  Beginnend `Microsoft.ApplicationInsights.Web` `Microsoft.ApplicationInsights.DependencyCollector`met en , is geen configuratie nodig.

W3C Trace-Context-ondersteuning wordt achterwaarts geïmplementeerd. Correlatie zal naar verwachting werken met toepassingen die zijn uitgevoerd met eerdere versies van de SDK (zonder W3C-ondersteuning).

Als u het verouderde `Request-Id` protocol wilt blijven gebruiken, u Trace-Context uitschakelen met behulp van deze configuratie:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Als u een oudere versie van de SDK uitvoert, raden we u aan deze bij te werken of de volgende configuratie toe te passen om Trace-Context in te schakelen.
Deze functie is `Microsoft.ApplicationInsights.Web` beschikbaar `Microsoft.ApplicationInsights.DependencyCollector` in de pakketten en pakketten, te beginnen met versie 2.8.0-beta1.
Het is standaard uitgeschakeld. Breng deze wijzigingen aan `ApplicationInsights.config`in :

- Onder `RequestTrackingTelemetryModule`, `EnableW3CHeadersExtraction` voeg het element `true`toe en stel de waarde ervan in op .
- Onder `DependencyTrackingTelemetryModule`, `EnableW3CHeadersInjection` voeg het element `true`toe en stel de waarde ervan in op .
- Voeg `W3COperationCorrelationTelemetryInitializer` `TelemetryInitializers`onder . Het zal lijken op dit voorbeeld:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>W3C-ondersteuning voor gedistribueerde tracering inschakelen voor ASP.NET Core-apps

 > [!NOTE]
  > Vanaf `Microsoft.ApplicationInsights.AspNetCore` versie 2.8.0 is geen configuratie nodig.
 
W3C Trace-Context-ondersteuning wordt achterwaarts geïmplementeerd. Correlatie zal naar verwachting werken met toepassingen die zijn uitgevoerd met eerdere versies van de SDK (zonder W3C-ondersteuning).

Als u het verouderde `Request-Id` protocol wilt blijven gebruiken, u Trace-Context uitschakelen met behulp van deze configuratie:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Als u een oudere versie van de SDK uitvoert, raden we u aan deze bij te werken of de volgende configuratie toe te passen om Trace-Context in te schakelen.

Deze functie `Microsoft.ApplicationInsights.AspNetCore` bevindt zich in versie 2.5.0-beta1 en in `Microsoft.ApplicationInsights.DependencyCollector` versie 2.8.0-beta1.
Het is standaard uitgeschakeld. Als u dit `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` `true`wilt inschakelen, stelt u in op :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>W3C distributed tracing-ondersteuning inschakelen voor Java-apps

- **Binnenkomende configuratie**

  - Voeg voor Java EE-apps `<TelemetryModules>` het volgende toe aan de tag in ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - Voeg voor apps voor Spring Boot de volgende eigenschappen toe:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Uitgaande configuratie**

  Voeg het volgende toe aan AI-Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > Achterwaartse compatibiliteitsmodus is standaard ingeschakeld `enableW3CBackCompat` en de parameter is optioneel. Gebruik het alleen wanneer u achterwaartse compatibiliteit wilt uitschakelen.
  >
  > Idealiter zou u dit uitschakelen wanneer al uw services zijn bijgewerkt naar nieuwere versies van SDK's die het W3C-protocol ondersteunen. Wij raden u ten zeerste aan om zo snel mogelijk naar deze nieuwere SDK's te gaan.

> [!IMPORTANT]
> Zorg ervoor dat de inkomende en uitgaande configuraties precies hetzelfde zijn.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Ondersteuning voor W3C-gedistribueerde tracering inschakelen voor webapps

Deze functie `Microsoft.ApplicationInsights.JavaScript`is in . Het is standaard uitgeschakeld. Gebruik `distributedTracingMode` config om het in te schakelen. AI_AND_W3C wordt geleverd voor achterwaartse compatibiliteit met alle verouderde services die worden uitgevoerd door Application Insights.

- **npm-instelling (negeren als u de installatie Snippet gebruikt)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Snippet setup (negeren als je npm-setup gebruikt)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...other configuration options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing en Application Insights

De [opentracing-gegevensmodelspecificatie](https://opentracing.io/) en Application Insights-gegevensmodellen worden op de volgende manier in kaart brengen:

| Application Insights                   | OpenTracing                                        |
|------------------------------------    |-------------------------------------------------    |
| `Request`, `PageView`                  | `Span`Met`span.kind = server`                    |
| `Dependency`                           | `Span`Met`span.kind = client`                    |
| `Id`van `Request` en`Dependency`     | `SpanId`                                            |
| `Operation_Id`                         | `TraceId`                                           |
| `Operation_ParentId`                   | `Reference`van `ChildOf` het type (de bovenliggende reeks)     |

Zie [Telemetriegegevensmodel Application Insights](../../azure-monitor/app/data-model.md)voor meer informatie .

Zie de [OpenTracing-specificatie](https://github.com/opentracing/specification/blob/master/specification.md) en [semantische conventies](https://github.com/opentracing/specification/blob/master/semantic_conventions.md)voor definities van OpenTracing-concepten.

## <a name="telemetry-correlation-in-opencensus-python"></a>Telemetriecorrelatie in OpenCensus Python

OpenCensus Python `OpenTracing` volgt de specificaties van het gegevensmodel die eerder zijn beschreven. Het ondersteunt ook [W3C Trace-Context](https://w3c.github.io/trace-context/) zonder enige configuratie.

### <a name="incoming-request-correlation"></a>Correlatie met binnenkomende aanvragen

OpenCensus Python correleert W3C Trace-Context headers van binnenkomende aanvragen naar de overspanningen die worden gegenereerd uit de aanvragen zelf. OpenCensus zal dit automatisch doen met integraties voor deze populaire webapplicatieframeworks: Flask, Django en Pyramid. U hoeft alleen maar de W3C Trace-Context headers te vullen met de [juiste indeling](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) en ze te verzenden met het verzoek. Hier is een voorbeeld flask applicatie die dit aantoont:

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Deze code voert een voorbeeld flask applicatie op `8080`uw lokale machine, luisteren naar poort . Als u de traceringscontext wilt correleren, stuurt u een verzoek naar het eindpunt. In dit voorbeeld `curl` u een opdracht gebruiken:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Door te kijken naar de [kopindeling Trace-Context,](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)u de volgende informatie afleiden:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Als u de aanvraagvermelding bekijkt die naar Azure Monitor is verzonden, u velden zien die zijn gevuld met de gegevens van de traceheader. U deze gegevens vinden onder Logboeken (Analytics) in de Azure Monitor Application Insights-bron.

![Telemetrie aanvragen in logboeken (Analytics)](./media/opencensus-python/0011-correlation.png)

Het `id` veld is `<trace-id>.<span-id>`in de `trace-id` indeling, waar de wordt genomen uit `span-id` de trace header die is doorgegeven in de aanvraag en de is een gegenereerde 8-byte array voor deze serie.

Het `operation_ParentId` veld is `<trace-id>.<parent-id>`in de `trace-id` indeling, `parent-id` waar zowel de en de zijn genomen uit de trace header die is doorgegeven in de aanvraag.

### <a name="log-correlation"></a>Logboekcorrelatie

Met OpenCensus Python u logboeken correleren door een trace-id, een span-id en een samplingvlag toe te voegen om records te registreren. U voegt deze kenmerken toe door de [integratie van OpenCensus-logboekregistratie te](https://pypi.org/project/opencensus-ext-logging/)installeren. De volgende kenmerken worden toegevoegd `LogRecord` aan `traceId` `spanId`Python-objecten: , en `traceSampled`. Houd er rekening mee dat dit alleen van kracht wordt voor loggers die na de integratie zijn gemaakt.

Hier is een voorbeeld toepassing die dit aantoont:

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
Wanneer deze code wordt uitgevoerd, worden de volgende afdrukken afgedrukt in de console:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Merk op dat `spanId` er een cadeau is voor het logboekbericht dat binnen de spanwijdte valt. Dit is `spanId` hetzelfde dat behoort `hello`tot de spanwijdte met de naam .

U de loggegevens `AzureLogHandler`exporteren met behulp van. Raadpleeg [dit artikel](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs) voor meer informatie.

## <a name="telemetry-correlation-in-net"></a>Telemetriecorrelatie in .NET

In de loop van de tijd heeft .NET verschillende manieren gedefinieerd om telemetrie- en diagnostische logboeken te correleren:

- `System.Diagnostics.CorrelationManager`maakt het bijhouden van [LogicalOperationStack en ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx)mogelijk.
- `System.Diagnostics.Tracing.EventSource`en Event Tracing for Windows (ETW) definiëren de [setCurrentThreadActivityId-methode.](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx)
- `ILogger`gebruikt [logboekscopen](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes).
- Windows Communication Foundation (WCF) en HTTP wire up "huidige" context propagatie.

Maar die methoden maakten geen automatische ondersteuning voor gedistribueerde tracering mogelijk. `DiagnosticSource`ondersteunt automatische cross-machine correlatie. .NET-bibliotheken `DiagnosticSource` ondersteunen en maken automatische cross-machine propagatie van de correlatiecontext via het transport mogelijk, zoals HTTP.

In [de gebruikershandleiding voor activiteiten](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) wordt `DiagnosticSource` de basis beginselen van het bijhouden van activiteiten uitgelegd.

ASP.NET Core 2.0 ondersteunt het extraheren van HTTP-headers en het starten van nieuwe activiteiten.

`System.Net.Http.HttpClient`, te beginnen met versie 4.1.0, ondersteunt automatische injectie van http-headers met correlatie en het bijhouden van HTTP-oproepen als activiteiten.

Er is een nieuwe HTTP-module, [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), voor klassieke ASP.NET. Deze module implementeert telemetrie correlatie met behulp van `DiagnosticSource`. Het start een activiteit op basis van inkomende aanvraagkoppen. Het correleert ook telemetrie van de verschillende fasen van de verwerking van aanvragen, zelfs wanneer elke fase van Internet Information Services (IIS) verwerking draait op een andere beheerde thread.

De Application Insights SDK, te beginnen met versie 2.4.0-beta1, gebruikt `DiagnosticSource` en `Activity` verzamelt telemetrie en koppelt deze aan de huidige activiteit.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java"></a>Telemetriecorrelatie in Java

[Application Insights Java-agent](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) en [Java SDK-versie](../../azure-monitor/app/java-get-started.md) 2.0.0 of hoger ondersteunt automatische correlatie van telemetrie. Het wordt automatisch `operation_id` ingevuld voor alle telemetrie (zoals traces, uitzonderingen en aangepaste gebeurtenissen) die worden uitgegeven binnen het bereik van een aanvraag. Het verspreidt ook de correlatieheaders (eerder beschreven) voor service-to-service-aanroepen via HTTP, als de [Java SDK-agent](../../azure-monitor/app/java-agent.md) is geconfigureerd.

> [!NOTE]
> Application Insights Java-agent verzamelt automatisch aanvragen en afhankelijkheden voor JMS, Kafka, Netty/Webflux en meer. Voor Java SDK worden alleen oproepen via Apache HttpClient ondersteund voor de correlatiefunctie. Automatische contextpropagatie over messaging-technologieën (zoals Kafka, RabbitMQ en Azure Service Bus) wordt niet ondersteund in de SDK. 

<a name="java-role-name"></a>
## <a name="role-name"></a>Rolnaam

U de manier aanpassen waarop componentnamen worden weergegeven in de [toepassingskaart](../../azure-monitor/app/app-map.md). Om dit te doen, kunt `cloud_RoleName` u handmatig instellen door een van de volgende acties uit te voeren:

- Met Application Insights Java SDK 2.5.0 en `cloud_RoleName` hoger `<RoleName>` u het toevoegen aan uw ApplicationInsights.xml-bestand opgeven:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Als u Spring Boot gebruikt met de Appen startvoor de lente van toepassing, hoeft u alleen uw aangepaste naam voor de toepassing in het bestand application.properties in te stellen:

  `spring.application.name=<name-of-app>`

  De Spring Boot Starter `cloudRoleName` wijst automatisch de waarde `spring.application.name` toe die u voor de accommodatie invoert.

## <a name="next-steps"></a>Volgende stappen

- Aangepaste [telemetrie schrijven](../../azure-monitor/app/api-custom-events-metrics.md).
- Zie [Aangepaste bewerkingen bijhouden voor](custom-operations-tracking.md)geavanceerde correlatiescenario's in ASP.NET Kern- en ASP.NET.
- Meer informatie over [het instellen van cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) voor andere SDK's.
- Aan boord van alle onderdelen van uw microservice op Application Insights. Bekijk de [ondersteunde platforms.](../../azure-monitor/app/platforms.md)
- Zie het [gegevensmodel](../../azure-monitor/app/data-model.md) voor toepassingsinzichten.
- Meer informatie over het [uitbreiden en filteren van telemetrie](../../azure-monitor/app/api-filtering-sampling.md).
- Bekijk de [referentie van Application Insights config](configuration-with-applicationinsights-config.md).
