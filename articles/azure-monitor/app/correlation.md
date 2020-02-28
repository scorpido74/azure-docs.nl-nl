---
title: Correlatie van de telemetrie van Azure-toepassing Insights | Microsoft Docs
description: Application Insights telemetrie-correlatie
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 06897fffda490cdfcbb2a9cf6f55c7945e8afda0
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672052"
---
# <a name="telemetry-correlation-in-application-insights"></a>Intermetrie-correlatie in Application Insights

In de wereld van micro Services moet voor elke logische bewerking werk worden uitgevoerd in verschillende onderdelen van de service. U kunt elk van deze onderdelen afzonderlijk bewaken met behulp van [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights ondersteunt gedistribueerde telemetrie-correlatie, die u gebruikt om te detecteren welk onderdeel verantwoordelijk is voor storingen of verminderde prestaties.

In dit artikel wordt het gegevens model uitgelegd dat door Application Insights wordt gebruikt voor het correleren van telemetrie die door meerdere onderdelen worden verzonden. Hierin worden technieken en protocollen voor context doorgifte beschreven. Het behandelt ook de implementatie van correlatie tactieken op verschillende talen en platforms.

## <a name="data-model-for-telemetry-correlation"></a>Gegevens model voor de correlatie van de telemetrie

Application Insights definieert een [gegevens model](../../azure-monitor/app/data-model.md) voor gedistribueerde telemetrie-correlatie. Om telemetrie te koppelen aan een logische bewerking, heeft elk telemetrie-item een context veld met de naam `operation_Id`. Deze id wordt gedeeld door elk telemetrie-item in de gedistribueerde tracering. Zelfs als u telemetrie van één laag kwijtraakt, kunt u nog steeds telemetrie koppelen die door andere onderdelen worden gerapporteerd.

Een gedistribueerde logische bewerking bestaat meestal uit een set kleinere bewerkingen die aanvragen verwerken door een van de onderdelen. Deze bewerkingen worden gedefinieerd op basis van [aanvraag-telemetrie](../../azure-monitor/app/data-model-request-telemetry.md). Elk aanvraag-telemetrie-item heeft een eigen `id` waarmee het uniek en wereld wijd wordt geïdentificeerd. En alle telemetrie-items (zoals traceringen en uitzonde ringen) die zijn gekoppeld aan de aanvraag, moeten de `operation_parentId` instellen op de waarde van de aanvraag `id`.

Elke uitgaande bewerking, zoals een HTTP-aanroep naar een ander onderdeel, wordt weer gegeven door middel van [afhankelijkheids-telemetrie](../../azure-monitor/app/data-model-dependency-telemetry.md). Met de telemetrie van een afhankelijkheid worden ook de eigen `id` gedefinieerd die wereld wijd uniek zijn. Telemetrie aanvragen, geïnitieerd door deze afhankelijkheids aanroep, gebruikt deze `id` als `operation_parentId`.

U kunt een weer gave van de gedistribueerde logische bewerking maken met behulp van `operation_Id`, `operation_parentId`en `request.id` met `dependency.id`. Deze velden definiëren ook de causality volgorde van telemetrie-aanroepen.

In een micro Services-omgeving kunnen traceringen van onderdelen naar verschillende opslag items gaan. Elk onderdeel kan een eigen instrumentatie sleutel hebben in Application Insights. Als u telemetrie voor de logische bewerking wilt ophalen, Application Insights query's gegevens uit elk opslag item op. Wanneer het aantal opslag items groot is, hebt u een hint nodig over waar u de volgende moet zien. Het Application Insights gegevens model definieert twee velden om dit probleem op te lossen: `request.source` en `dependency.target`. Het eerste veld identificeert het onderdeel dat de afhankelijkheids aanvraag heeft gestart. Het tweede veld identificeert welk onderdeel het antwoord van de afhankelijkheids aanroep retourneert.

## <a name="example"></a>Voorbeeld

Laten we eens naar een voorbeeld kijken. Een toepassing met de naam aandelen prijzen toont de huidige markt prijs van een aandeel met behulp van een externe API met de naam Stock. De voorraad prijzen toepassing heeft een pagina met de naam Stock pagina die door de webbrowser van de client wordt geopend met behulp van `GET /Home/Stock`. De toepassing voert een query uit op de voorraad-API met behulp van de HTTP-aanroep `GET /api/stock/value`.

U kunt de resulterende telemetrie analyseren door een query uit te voeren:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Houd er rekening mee dat alle telemetrie-items de hoofd `operation_Id`delen. Wanneer er een Ajax-aanroep van de pagina wordt gemaakt, wordt er een nieuwe unieke ID (`qJSXU`) toegewezen aan de telemetrie van de afhankelijkheid en wordt de ID van de pagina weergave gebruikt als `operation_ParentId`. De server aanvraag gebruikt vervolgens de Ajax-ID als `operation_ParentId`.

| itemType   | naam                      | Id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Voorraad pagina                |              | STYz               | STYz         |
| einde | /Home/Stock ophalen           | qJSXU        | STYz               | STYz         |
| verzoek    | Home/Stock ophalen            | KqKwlrSt9PA= | qJSXU              | STYz         |
| einde | /API/Stock/value ophalen      | bBrf2L7mm2g = | KqKwlrSt9PA=       | STYz         |

Wanneer de oproep `GET /api/stock/value` wordt gedaan bij een externe service, moet u de identiteit van die server weten zodat u het veld `dependency.target` op de juiste wijze kunt instellen. Wanneer de externe service geen bewaking ondersteunt, wordt `target` ingesteld op de hostnaam van de service (bijvoorbeeld `stock-prices-api.com`). Maar als de service zichzelf identificeert door een vooraf gedefinieerde HTTP-header te retour neren, bevat `target` de service-identiteit waarmee Application Insights een gedistribueerde tracering kunt bouwen door de telemetrie van die service op te vragen.

## <a name="correlation-headers"></a>Correlatie headers

Application Insights wordt overgezet naar [W3C-tracering-context](https://w3c.github.io/trace-context/), die het volgende definieert:

- `traceparent`: bevat de wereld wijd unieke bewerkings-ID en de unieke id van de aanroep.
- `tracestate`: bevat een systeemspecifieke tracerings context.

De nieuwste versie van de Application Insights SDK ondersteunt het Trace-context protocol, maar u moet er mogelijk voor kiezen. (Achterwaartse compatibiliteit met het vorige correlatie protocol dat wordt ondersteund door de Application Insights SDK wordt behouden.)

Het [correlatie-HTTP-protocol, ook wel de aanvraag-id genoemd](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), wordt afgeschaft. Dit protocol definieert twee headers:

- `Request-Id`: de wereld wijd unieke ID van de aanroep.
- `Correlation-Context`: de verzameling naam/waarde-paren van de gedistribueerde tracerings eigenschappen.

Application Insights definieert ook de [uitbrei ding](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) voor het correlatie-http-protocol. Er worden `Request-Context` naam/waarde-paren gebruikt voor het door geven van de verzameling eigenschappen die door de directe aanroeper of de aanroepee worden gebruikt. De Application Insights SDK gebruikt deze header om de velden `dependency.target` en `request.source` in te stellen.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Ondersteuning voor gedistribueerde W3C-tracering inschakelen voor klassieke ASP.NET-Apps
 
  > [!NOTE]
  >  Vanaf `Microsoft.ApplicationInsights.Web` en `Microsoft.ApplicationInsights.DependencyCollector`is geen configuratie nodig.

W3C-tracering: context ondersteuning wordt op een neerwaarts compatibele manier geïmplementeerd. Correlatie wordt verwacht te werken met toepassingen die zijn gekoppeld aan eerdere versies van de SDK (zonder W3C-ondersteuning).

Als u het verouderde `Request-Id`-protocol wilt blijven gebruiken, kunt u de tracerings context uitschakelen met behulp van deze configuratie:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Als u een oudere versie van de SDK uitvoert, wordt u aangeraden deze bij te werken of de volgende configuratie toe te passen om tracering context in te scha kelen.
Deze functie is beschikbaar in de `Microsoft.ApplicationInsights.Web`-en `Microsoft.ApplicationInsights.DependencyCollector`-pakketten, te beginnen met versie 2.8.0-beta1.
Het is standaard uitgeschakeld. Als u deze wilt inschakelen, brengt u deze wijzigingen aan `ApplicationInsights.config`:

- Voeg onder `RequestTrackingTelemetryModule`het element `EnableW3CHeadersExtraction` toe en stel de waarde ervan in op `true`.
- Voeg onder `DependencyTrackingTelemetryModule`het element `EnableW3CHeadersInjection` toe en stel de waarde ervan in op `true`.
- Voeg `W3COperationCorrelationTelemetryInitializer` toe onder `TelemetryInitializers`. Dit ziet er ongeveer uit als in dit voor beeld:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Ondersteuning voor gedistribueerde W3C-tracering voor ASP.NET Core-Apps inschakelen

 > [!NOTE]
  > Vanaf `Microsoft.ApplicationInsights.AspNetCore` versie 2.8.0 is geen configuratie nodig.
 
W3C-tracering: context ondersteuning wordt op een neerwaarts compatibele manier geïmplementeerd. Correlatie wordt verwacht te werken met toepassingen die zijn gekoppeld aan eerdere versies van de SDK (zonder W3C-ondersteuning).

Als u het verouderde `Request-Id`-protocol wilt blijven gebruiken, kunt u de tracerings context uitschakelen met behulp van deze configuratie:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Als u een oudere versie van de SDK uitvoert, wordt u aangeraden deze bij te werken of de volgende configuratie toe te passen om tracering context in te scha kelen.

Deze functie bevindt zich in `Microsoft.ApplicationInsights.AspNetCore` versie 2.5.0-beta1 en in `Microsoft.ApplicationInsights.DependencyCollector` versie 2.8.0-beta1.
Het is standaard uitgeschakeld. Als u deze wilt inschakelen, stelt u `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` in op `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Ondersteuning voor gedistribueerde W3C-tracering inschakelen voor java-apps

- **Binnenkomende configuratie**

  - Voor Java EE-apps voegt u het volgende toe aan de `<TelemetryModules>`-tag in ApplicationInsights. XML:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - Voor Spring-boot-apps voegt u de volgende eigenschappen toe:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Uitgaande configuratie**

  Voeg het volgende toe aan AI-Agent. XML:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > De modus voor achterwaartse compatibiliteit is standaard ingeschakeld en de para meter `enableW3CBackCompat` is optioneel. Gebruik deze optie alleen als u achterwaartse compatibiliteit wilt uitschakelen.
  >
  > In het ideale geval kunt u deze uitschakelen wanneer al uw services zijn bijgewerkt naar nieuwere versies van Sdk's die ondersteuning bieden voor het W3C-protocol. Wij raden u ten zeerste aan deze nieuwere Sdk's zo snel mogelijk te verplaatsen.

> [!IMPORTANT]
> Zorg ervoor dat de configuraties voor binnenkomende en uitgaande verbindingen exact hetzelfde zijn.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Ondersteuning voor gedistribueerde W3C-tracering inschakelen voor web-apps

Deze functie bevindt zich in `Microsoft.ApplicationInsights.JavaScript`. Het is standaard uitgeschakeld. Gebruik `distributedTracingMode` configuratie om deze functie in te scha kelen. AI_AND_W3C is voor achterwaartse compatibiliteit met verouderde services die worden uitgevoerd door Application Insights.

- **NPM-installatie (negeren bij het instellen van de fragment instelling)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Setup van fragment (negeren bij gebruik van NPM-installatie)**

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

## <a name="opentracing-and-application-insights"></a>Opentracering en Application Insights

De [gegevens model specificatie Opentracering](https://opentracing.io/) en Application Insights gegevens modellen op de volgende manier:

| Application Insights                  | Opentraceren                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` met `span.kind = server`                  |
| `Dependency`                          | `Span` met `span.kind = client`                  |
| `Id` van `Request` en `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` van het type `ChildOf` (het bovenliggende bereik)   |

Zie [Application Insights telemetrie-gegevens model](../../azure-monitor/app/data-model.md)voor meer informatie.

Zie voor definities van openvolgende concepten de [specificatie](https://github.com/opentracing/specification/blob/master/specification.md) opentracering en [semantische conventies](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-opencensus-python"></a>Telemetrie-correlatie in opentellingen python

De opentellingen python volgt de `OpenTracing` gegevens model specificaties die eerder zijn beschreven. Het ondersteunt ook [W3C-tracering-context](https://w3c.github.io/trace-context/) zonder dat hiervoor configuratie is vereist.

### <a name="incoming-request-correlation"></a>Correlatie van binnenkomende aanvragen

Met opentellingen python worden W3C-tracering context headers van binnenkomende aanvragen afgestemd op de reeksen die worden gegenereerd op basis van de aanvragen. Met opentellingen wordt dit automatisch gedaan met integraties voor deze populaire Web Application Frameworks: kolf, Django en piramide. U hoeft alleen de header van de W3C-tracerings context in te vullen met de [juiste indeling](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) en deze te verzenden met de aanvraag. Hier volgt een voor beeld van een kolf toepassing waarin dit wordt gedemonstreerd:

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

Deze code voert een voorbeeld toepassing voor een fles op uw lokale computer uit en luistert naar poort `8080`. Als u de tracerings context wilt correleren, verzendt u een aanvraag naar het eind punt. In dit voor beeld kunt u een `curl`-opdracht gebruiken:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Als u de indeling van de header van de [tracerings context](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)bekijkt, kunt u de volgende informatie afleiden:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Als u de aanvraag vermelding bekijkt die naar Azure Monitor is verzonden, kunt u de velden weer geven die zijn ingevuld met de traceer header-informatie. U kunt deze gegevens vinden onder Logboeken (Analytics) in de Azure Monitor Application Insights resource.

![Telemetrie aanvragen in Logboeken (analyse)](./media/opencensus-python/0011-correlation.png)

Het veld `id` heeft de indeling `<trace-id>.<span-id>`, waarbij de `trace-id` wordt opgehaald uit de trace-header die in de aanvraag is door gegeven en het `span-id` een gegenereerde 8-bytes matrix is voor deze periode.

Het veld `operation_ParentId` heeft de indeling `<trace-id>.<parent-id>`, waarbij zowel de `trace-id` als de `parent-id` worden opgehaald uit de trace-header die in de aanvraag is door gegeven.

### <a name="log-correlation"></a>Logboek correlatie

Met opentellingen python kunt u Logboeken correleren door een tracerings-ID, een span-ID en een steekproef vlag toe te voegen om records te registreren. U voegt deze kenmerken toe door integratie van de [logboek registratie](https://pypi.org/project/opencensus-ext-logging/)van opentellingen te installeren. De volgende kenmerken worden toegevoegd aan python `LogRecord`-objecten: `traceId`, `spanId`en `traceSampled`. Houd er rekening mee dat dit alleen van toepassing is op Logboeken die zijn gemaakt na de integratie.

Hier volgt een voor beeld van een toepassing waarin dit wordt gedemonstreerd:

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
Wanneer deze code wordt uitgevoerd, worden de volgende afdrukken in de-console:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
U ziet dat er een `spanId` aanwezig is voor het logboek bericht dat zich binnen de reeks bevindt. Dit is hetzelfde `spanId` dat hoort bij de reeks met de naam `hello`.

U kunt de logboek gegevens exporteren met behulp van `AzureLogHandler`. Raadpleeg [dit artikel](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs) voor meer informatie.

## <a name="telemetry-correlation-in-net"></a>Telemetrie-correlatie in .NET

In de loop van de tijd heeft .NET verschillende manieren gedefinieerd voor het correleren van telemetrie-en Diagnostische logboeken:

- `System.Diagnostics.CorrelationManager` kunt het volgen van [LogicalOperationStack en ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx).
- `System.Diagnostics.Tracing.EventSource` en Event Tracing for Windows (ETW) definiëren de methode [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) .
- `ILogger` maakt gebruik van [logboek bereiken](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes).
- Windows Communication Foundation (WCF) en HTTP-updoorgifte van de huidige context.

Deze methoden hebben echter geen automatische ondersteuning voor gedistribueerde tracering ingeschakeld. `DiagnosticSource` ondersteunt automatische correlatie tussen computers. .NET-bibliotheken ondersteunen `DiagnosticSource` en toestaan dat de correlatie context automatisch wordt door gegeven via het Trans Port, zoals HTTP.

De [Gebruikers handleiding](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) voor de activiteit in `DiagnosticSource` legt de basis beginselen van het bijhouden van activiteiten uit.

ASP.NET Core 2,0 ondersteunt het ophalen van HTTP-headers en het starten van nieuwe activiteiten.

`System.Net.Http.HttpClient`, te beginnen met versie 4.1.0, ondersteunt automatische injectie van correlatie-HTTP-headers en het bijhouden van HTTP-aanroepen als activiteiten.

Er is een nieuwe HTTP-module, [micro soft. AspNet. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), voor klassieke ASP.net. Deze module implementeert telemetrie-correlatie met behulp van `DiagnosticSource`. Er wordt een activiteit gestart op basis van binnenkomende aanvraag headers. Ook wordt de telemetrie gecorreleerd vanuit de verschillende stadia van aanvraag verwerking, zelfs wanneer elke fase van de verwerking van Internet Information Services (IIS) wordt uitgevoerd op een andere beheerde thread.

De Application Insights SDK, te beginnen met versie 2.4.0-beta1, gebruikt `DiagnosticSource` en `Activity` om telemetrie te verzamelen en te koppelen aan de huidige activiteit.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Telemetrie-correlatie in de Java SDK

[Application INSIGHTS SDK voor Java](../../azure-monitor/app/java-get-started.md) versie 2.0.0 of hoger ondersteunt automatische correlatie van telemetrie. `operation_id` wordt automatisch gevuld voor alle telemetrie (zoals traceringen, uitzonde ringen en aangepaste gebeurtenissen) die binnen het bereik van een aanvraag zijn uitgegeven. Ook worden de correlatie headers (eerder beschreven) door gegeven voor service-naar-service-aanroepen via HTTP, als de [Java SDK-agent](../../azure-monitor/app/java-agent.md) is geconfigureerd.

> [!NOTE]
> Alleen aanroepen via Apache httpclient maakt worden ondersteund voor de correlatie functie. Zowel lente RestTemplate als Feign kunnen worden gebruikt met Apache httpclient maakt onder de schermen.

Op dit moment wordt de automatische context doorgifte voor berichten technologieën (zoals Kafka, RabbitMQ en Azure Service Bus) niet ondersteund. Het is mogelijk om dergelijke scenario's hand matig te coderen met behulp van de methoden `trackDependency` en `trackRequest`. In deze methoden vertegenwoordigt een telemetrie van een afhankelijkheid een bericht dat door een producent in de wachtrij wordt geplaatst. De aanvraag vertegenwoordigt een bericht dat door een gebruiker wordt verwerkt. In dit geval moeten zowel `operation_id` als `operation_parentId` worden door gegeven in de eigenschappen van het bericht.

### <a name="telemetry-correlation-in-asynchronous-java-applications"></a>Telemetrie-correlatie in asynchrone Java-toepassingen

Zie [gedistribueerde tracering in asynchrone Java-toepassingen](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications)voor meer informatie over het correleren van telemetrie in een asynchrone Spring boot-toepassing. Dit artikel bevat richt lijnen voor het [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) en [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html)van instrumentatie.


<a name="java-role-name"></a>
## <a name="role-name"></a>Rolnaam

Mogelijk wilt u de manier aanpassen waarop onderdeel namen worden weer gegeven in de [toepassings toewijzing](../../azure-monitor/app/app-map.md). Hiervoor kunt u de `cloud_RoleName` hand matig instellen door een van de volgende acties uit te voeren:

- Met Application Insights Java SDK 2.5.0 en hoger kunt u de `cloud_RoleName` opgeven door `<RoleName>` toe te voegen aan uw ApplicationInsights. XML-bestand:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Als u de nood opstartdiskette met de Application Insights Spring boot Starter gebruikt, hoeft u alleen maar uw aangepaste naam voor de toepassing in het bestand Application. Properties in te stellen:

  `spring.application.name=<name-of-app>`

  De Spring boot starter wijst automatisch `cloudRoleName` toe aan de waarde die u invoert voor de eigenschap `spring.application.name`.

## <a name="next-steps"></a>Volgende stappen

- Schrijf [aangepaste telemetrie](../../azure-monitor/app/api-custom-events-metrics.md).
- Zie [aangepaste bewerkingen bijhouden](custom-operations-tracking.md)voor geavanceerde correlatie scenario's in ASP.NET Core en ASP.net.
- Meer informatie over het [instellen van cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) voor andere sdk's.
- Alle onderdelen van uw micro service op Application Insights onboarden. Bekijk de [ondersteunde platforms](../../azure-monitor/app/platforms.md).
- Zie het [gegevens model](../../azure-monitor/app/data-model.md) voor Application Insights typen.
- Meer informatie over hoe u [telemetrie kunt uitbreiden en filteren](../../azure-monitor/app/api-filtering-sampling.md).
- Raadpleeg de [Application Insights-configuratie referentie](configuration-with-applicationinsights-config.md).
