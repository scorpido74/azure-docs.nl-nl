---
title: Correlatie van de telemetrie van Azure-toepassing Insights | Microsoft Docs
description: Application Insights telemetrie-correlatie
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: df93405940c02affa224fba2d2e6f07ce5278b15
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755355"
---
# <a name="telemetry-correlation-in-application-insights"></a>Intermetrie-correlatie in Application Insights

In de wereld van micro Services moet voor elke logische bewerking werk worden uitgevoerd in verschillende onderdelen van de service. Elk van deze onderdelen kan afzonderlijk worden bewaakt door [Azure-toepassing Insights](../../azure-monitor/app/app-insights-overview.md). Het onderdeel van de web-app communiceert met het onderdeel van de verificatie provider om gebruikers referenties te valideren en met het API-onderdeel om gegevens op te halen voor visualisatie. Het API-onderdeel kan gegevens uit andere services opvragen en cache provider onderdelen gebruiken om het facturerings onderdeel op de hoogte te stellen van deze aanroep. Application Insights ondersteunt gedistribueerde telemetrie-correlatie, die u gebruikt om te detecteren welk onderdeel verantwoordelijk is voor storingen of verminderde prestaties.

In dit artikel wordt het gegevens model uitgelegd dat door Application Insights wordt gebruikt voor het correleren van telemetrie die door meerdere onderdelen worden verzonden. Hierin worden technieken en protocollen voor context doorgifte beschreven. Het behandelt ook de implementatie van correlatie concepten op verschillende talen en platformen.

## <a name="data-model-for-telemetry-correlation"></a>Gegevens model voor de correlatie van de telemetrie

Application Insights definieert een [gegevens model](../../azure-monitor/app/data-model.md) voor gedistribueerde telemetrie-correlatie. Om telemetrie te koppelen aan de logische bewerking, heeft elk telemetrie-item een context veld met de naam `operation_Id`. Deze id wordt gedeeld door elk telemetrie-item in de gedistribueerde tracering. Dus zelfs als de telemetrie van één laag is verbroken, kunt u nog steeds telemetrie koppelen die door andere onderdelen zijn gerapporteerd.

Een gedistribueerde logische bewerking bestaat meestal uit een set kleinere bewerkingen, die aanvragen verwerken door een van de onderdelen. Deze bewerkingen worden gedefinieerd op basis van [aanvraag-telemetrie](../../azure-monitor/app/data-model-request-telemetry.md). Elke telemetrie van een aanvraag heeft een eigen `id` dat deze uniek en wereld wijd identificeert. En alle telemetrie-items (zoals traceringen en uitzonde ringen) die aan deze aanvraag zijn gekoppeld, moeten de `operation_parentId` instellen op de waarde van de aanvraag `id`.

Elke uitgaande bewerking, zoals een HTTP-aanroep naar een ander onderdeel, wordt weer gegeven door middel van [afhankelijkheids-telemetrie](../../azure-monitor/app/data-model-dependency-telemetry.md). Met de telemetrie van een afhankelijkheid wordt ook een eigen `id` gedefinieerd die wereld wijd uniek is. Telemetrie aanvragen, geïnitieerd door deze afhankelijkheids aanroep, gebruikt deze `id` als `operation_parentId`.

U kunt een weer gave van de gedistribueerde logische bewerking maken met behulp van `operation_Id`, `operation_parentId` en `request.id` met `dependency.id`. Deze velden definiëren ook de causality volgorde van telemetrie-aanroepen.

In een micro Services-omgeving kunnen traceringen van onderdelen naar verschillende opslag items gaan. Elk onderdeel kan een eigen instrumentatie sleutel hebben in Application Insights. Als u telemetrie wilt ophalen voor de logische bewerking, vraagt de Application Insights UX gegevens van elk opslag item op. Wanneer het aantal opslag items enorm is, hebt u een hint nodig over waar u de volgende moet zien. Het Application Insights gegevens model definieert twee velden om dit probleem op te lossen: `request.source` en `dependency.target`. Het eerste veld identificeert het onderdeel dat de afhankelijkheids aanvraag heeft gestart, en de tweede identificeert welk onderdeel het antwoord van de afhankelijkheids aanroep retourneert.

## <a name="example"></a>Voorbeeld

We gaan nu een voor beeld van een toepassing met de naam aandelen prijzen bekijken, waarin de huidige markt prijs van een aandeel wordt weer gegeven met behulp van een externe API met de naam `Stock`. De voorraad prijzen toepassing bevat een pagina met de naam `Stock page` dat de client webbrowser wordt geopend met behulp van `GET /Home/Stock`. De toepassing zoekt de `Stock`-API met behulp van een HTTP-aanroep `GET /api/stock/value`.

U kunt de resulterende telemetrie analyseren door een query uit te voeren:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Houd er rekening mee dat alle telemetrie-items de hoofd `operation_Id` delen. Wanneer er een Ajax-aanroep van de pagina wordt gemaakt, wordt er een nieuwe unieke ID (`qJSXU`) toegewezen aan de telemetrie van de afhankelijkheid en wordt de ID van de pagina weergave gebruikt als `operation_ParentId`. De server aanvraag gebruikt vervolgens de Ajax-ID als `operation_ParentId`.

| Item type   | name                      | Id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| Pagina weergave   | Voorraad pagina                |              | STYz               | STYz         |
| einde | /Home/Stock ophalen           | qJSXU        | STYz               | STYz         |
| Schot    | Home/Stock ophalen            | KqKwlrSt9PA = | qJSXU              | STYz         |
| einde | /API/Stock/value ophalen      | bBrf2L7mm2g = | KqKwlrSt9PA =       | STYz         |

Wanneer de oproep `GET /api/stock/value` wordt gedaan bij een externe service, weet u zeker dat u de identiteit van die server wilt weten, zodat u het veld `dependency.target` op de juiste wijze kunt instellen. Wanneer de externe service geen bewaking ondersteunt, wordt `target` ingesteld op de hostnaam van de service (bijvoorbeeld `stock-prices-api.com`). Als de service echter zichzelf identificeert door een vooraf gedefinieerde HTTP-header te retour neren, bevat `target` de service-identiteit waarmee Application Insights een gedistribueerde tracering kunt bouwen door de telemetrie van die service op te vragen.

## <a name="correlation-headers"></a>Correlatie headers

Er wordt overgeschakeld naar de [W3C-traceer context, die het](https://w3c.github.io/trace-context/) volgende definieert:

- `traceparent`: bevat de wereld wijd unieke bewerkings-ID en de unieke id van de aanroep.
- `tracestate`: voert een tracering systeem-specifieke context uit.

De nieuwste versies van Application Insights Sdk's ondersteunen tracering-context protocol, maar u moet er mogelijk voor kiezen (het zorgt voor achterwaartse compatibiliteit met het oude correlatie protocol dat door ApplicationInsights Sdk's wordt ondersteund).

De [aanvraag-id van het correlatie HTTP-protocol ook wel](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) is op het afschaffing-pad. Dit protocol definieert twee headers:

- `Request-Id`: de wereld wijd unieke ID van de aanroep.
- `Correlation-Context`: de verzameling naam/waarde-paren van de gedistribueerde tracerings eigenschappen.

Application Insights definieert ook de [uitbrei ding](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) voor het correlatie-http-protocol. Er worden `Request-Context` naam/waarde-paren gebruikt voor het door geven van de verzameling eigenschappen die door de directe aanroeper of de aanroepee worden gebruikt. De Application Insights SDK gebruikt deze header om `dependency.target` en `request.source` velden in te stellen.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Ondersteuning voor gedistribueerde W3C-tracering inschakelen voor klassieke ASP.NET-Apps
 
  > [!NOTE]
  > Er is geen configuratie vereist, te beginnen met `Microsoft.ApplicationInsights.Web` en `Microsoft.ApplicationInsights.DependencyCollector` 

W3C-tracering: context ondersteuning wordt uitgevoerd op de neerwaarts compatibele manier en correlatie wordt verwacht te werken met toepassingen die worden gebruikt in combi natie met eerdere versies van SDK (zonder W3C-ondersteuning). 

Als u een verouderd `Request-Id`-protocol wilt blijven gebruiken, kunt u trace-context met de volgende configuratie *uitschakelen*

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Als u een oudere versie van de SDK uitvoert, raden we u aan om deze bij te werken of de volgende configuratie toe te passen om tracering context in te scha kelen.
Deze functie is beschikbaar in `Microsoft.ApplicationInsights.Web` en `Microsoft.ApplicationInsights.DependencyCollector` pakketten die beginnen met versie 2.8.0-beta1.
Het is standaard uitgeschakeld. Als u deze wilt inschakelen, wijzigt u `ApplicationInsights.config`:

- Voeg onder `RequestTrackingTelemetryModule` het element `EnableW3CHeadersExtraction` toe met de waarde ingesteld op `true`.
- Voeg onder `DependencyTrackingTelemetryModule` het element `EnableW3CHeadersInjection` toe met de waarde ingesteld op `true`.
- @No__t_0 toevoegen onder de `TelemetryInitializers` vergelijkbaar met 

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers> 
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Ondersteuning voor gedistribueerde W3C-tracering voor ASP.NET Core-Apps inschakelen

 > [!NOTE]
  > Er is geen configuratie nodig die begint met `Microsoft.ApplicationInsights.AspNetCore` versie 2.8.0.
 
W3C-tracering: context ondersteuning wordt uitgevoerd op de neerwaarts compatibele manier en correlatie wordt verwacht te werken met toepassingen die worden gebruikt in combi natie met eerdere versies van SDK (zonder W3C-ondersteuning). 

Als u een verouderd `Request-Id`-protocol wilt blijven gebruiken, kunt u trace-context met de volgende configuratie *uitschakelen*

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Als u een oudere versie van de SDK uitvoert, raden we u aan om deze bij te werken of de volgende configuratie toe te passen om tracering context in te scha kelen.

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
> Zorg ervoor dat zowel binnenkomende als uitgaande configuraties exact hetzelfde zijn.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Ondersteuning voor gedistribueerde W3C-tracering inschakelen voor web-apps

Deze functie bevindt zich in `Microsoft.ApplicationInsights.JavaScript`. Het is standaard uitgeschakeld. Gebruik `distributedTracingMode` configuratie om deze functie in te scha kelen. AI_AND_W3C wordt verzorgd voor back-compatibiliteit met verouderde services van Application Insights instrument:

- **NPM-installatie (negeren bij het instellen van de fragment instelling)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...Other Configuration Options... */
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
      /* ...Other Configuration Options... */
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

Zie het [Application Insights telemetrie-gegevens model](../../azure-monitor/app/data-model.md)voor meer informatie. 

Zie voor definities van opentracerings concepten de opentracerings [specificatie](https://github.com/opentracing/specification/blob/master/specification.md) en [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-opencensus-python"></a>Telemetrie-correlatie in opentellingen python

De opentellingen python volgt de `OpenTracing` hierboven beschreven gegevens model specificaties. Het ondersteunt ook de [W3C-tracering-context](https://w3c.github.io/trace-context/) zonder dat er een configuratie nodig is.

### <a name="incoming-request-correlation"></a>Correlatie van binnenkomende aanvragen

Met opentellingen python worden de kopteksten van de W3C-tracerings context van binnenkomende aanvragen gecorreleerd naar de reeksen die worden gegenereerd op basis van de aanvragen. Met opentellingen wordt dit automatisch gedaan met integraties voor populaire Web Application Frameworks, zoals `flask`, `django` en `pyramid`. De kopteksten van de W3C-traceer context moeten eenvoudigweg worden gevuld met de [juiste indeling](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)en met de aanvraag worden verzonden. Hieronder ziet u een voor beeld `flask` toepassing die dit demonstreert.

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

Hiermee wordt een voorbeeld toepassing `flask` op de lokale computer uitgevoerd en wordt geluisterd naar poort `8080`. Om de tracerings context te correleren, sturen we een aanvraag naar het eind punt. In dit voor beeld kunnen we een `curl`-opdracht gebruiken.
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Als u de indeling van de koptekst van de [tracerings context](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)bekijkt, wordt de volgende informatie afgeleid: `version`: `00` 
 `trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736` 
 `parent-id/span-id`: `00f067aa0ba902b7` 
 0: 1

Als we kijken naar de aanvraag vermelding die naar Azure Monitor is verzonden, kunnen we de velden zien die zijn ingevuld met de traceer header-informatie.

![Scherm afbeelding van aanvraag-telemetrie in Logboeken (analyse) met traceer kopregel velden gemarkeerd in het rood vak](./media/opencensus-python/0011-correlation.png)

Het veld `id` heeft de indeling `<trace-id>.<span-id>`, waarbij de `trace-id` wordt opgehaald uit de trace-header die in de aanvraag is door gegeven en het `span-id` een gegenereerde 8-bytes matrix is voor deze periode. 

Het veld `operation_ParentId` heeft de indeling `<trace-id>.<parent-id>`, waarbij zowel de `trace-id` als de `parent-id` worden opgehaald uit de trace-header die in de aanvraag is door gegeven.

### <a name="logs-correlation"></a>Logboeken correlatie

Met opentellingen python wordt correlatie van Logboeken toegestaan door logboek records te verrijken met traceer-ID, reeks-ID en steekproef vlag. Dit wordt gedaan door de integratie van de opentellings [logboek registratie](https://pypi.org/project/opencensus-ext-logging/)te installeren. De volgende kenmerken worden toegevoegd aan python `LogRecord`s: `traceId`, `spanId` en `traceSampled`. Houd er rekening mee dat dit alleen van kracht is voor logboeken die zijn gemaakt na de integratie.
Hieronder ziet u een voor beeld van een toepassing die dit vertoont.

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
Wanneer deze code wordt uitgevoerd, worden de volgende opties weer geven in de-console:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Houd er rekening mee dat er een spanId aanwezig is voor het logboek bericht dat zich binnen het bereik bevindt. Dit is de spanId die hoort bij de reeks met de naam `hello`.

## <a name="telemetry-correlation-in-net"></a>Telemetrie-correlatie in .NET

In de loop van de tijd zijn er verschillende manieren gedefinieerd voor het correleren van telemetrie-en Diagnostische logboeken:

- `System.Diagnostics.CorrelationManager` kunt het bijhouden van [LogicalOperationStack en ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). 
- `System.Diagnostics.Tracing.EventSource` en Event Tracing for Windows (ETW) definiëren de methode [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) .
- `ILogger` maakt gebruik van [logboek bereiken](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- Windows Communication Foundation (WCF) en HTTP-updoorgifte van de huidige context.

Deze methoden hebben echter geen automatische ondersteuning voor gedistribueerde tracering ingeschakeld. `DiagnosticSource` is een manier om automatische correlatie tussen machines te ondersteunen. .NET-bibliotheken ondersteunen ' DiagnosticSource ' en kunnen automatische doorgifte van de correlatie tussen computers via het Trans Port, zoals HTTP, toestaan.

In de [hand leiding voor activiteiten](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) in `DiagnosticSource` worden de basis beginselen van het bijhouden van activiteiten uitgelegd.

ASP.NET Core 2,0 ondersteunt het ophalen van HTTP-headers en het starten van een nieuwe activiteit.

`System.Net.Http.HttpClient`, te beginnen met versie 4.1.0, ondersteunt automatische injectie van de correlatie-HTTP-headers en het volgen van de HTTP-aanroep als een activiteit.

Er is een nieuwe HTTP-module, [micro soft. AspNet. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), voor klassieke ASP.net. Deze module implementeert telemetrie-correlatie met behulp van `DiagnosticSource`. Er wordt een activiteit gestart op basis van binnenkomende aanvraag headers. Ook wordt de telemetrie gecorreleerd vanuit de verschillende stadia van aanvraag verwerking, zelfs voor gevallen waarin elke fase van de verwerking van Internet Information Services (IIS) wordt uitgevoerd op een andere beheerde thread.

De Application Insights SDK, te beginnen met versie 2.4.0-beta1, gebruikt `DiagnosticSource` en `Activity` om telemetrie te verzamelen en te koppelen aan de huidige activiteit.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Telemetrie-correlatie in de Java SDK

De [Application INSIGHTS SDK voor Java](../../azure-monitor/app/java-get-started.md) ondersteunt automatische correlatie van telemetrie, te beginnen met versie 2.0.0. @No__t_0 wordt automatisch gevuld voor alle telemetrie (zoals traceringen, uitzonde ringen en aangepaste gebeurtenissen) die binnen het bereik van een aanvraag zijn uitgegeven. Het zorgt er ook voor dat de correlatie headers worden door gegeven (eerder beschreven) voor service-naar-service-aanroepen via HTTP, als de [Java SDK-agent](../../azure-monitor/app/java-agent.md) is geconfigureerd.

> [!NOTE]
> Alleen aanroepen via Apache httpclient maakt worden ondersteund voor de correlatie functie. Als u veer RestTemplate of Feign gebruikt, kunnen beide worden gebruikt met Apache httpclient maakt onder de schermen.

Momenteel wordt de automatische context doorgifte voor berichten technologieën (zoals Kafka, RabbitMQ of Azure Service Bus) niet ondersteund. Het is echter mogelijk om dergelijke scenario's hand matig te coderen met behulp van de `trackDependency`-en `trackRequest`-Api's. In deze Api's vertegenwoordigt een telemetrie van een afhankelijkheid een bericht in de wachtrij van een producent. de aanvraag vertegenwoordigt een bericht dat door een gebruiker wordt verwerkt. In dit geval moeten zowel `operation_id` als `operation_parentId` worden door gegeven in de eigenschappen van het bericht.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>Telemetrie-correlatie in asynchrone Java-toepassing

Volg dit uitgebreide artikel als u [de](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) telemetrie in een asynchrone Spring boot-toepassing wilt correleren. Het bevat richt lijnen voor het [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) van de lente en [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html). 


<a name="java-role-name"></a>
## <a name="role-name"></a>Rolnaam

Soms wilt u de manier waarop onderdeel namen worden weer gegeven in de [toepassings toewijzing](../../azure-monitor/app/app-map.md)aanpassen. Hiervoor kunt u de `cloud_RoleName` hand matig instellen door een van de volgende handelingen uit te voeren:

- Als u achtereen met de Application Insights Spring boot Starter gebruikt, hoeft u alleen de aangepaste naam voor de toepassing in het bestand Application. Properties in te stellen.

  `spring.application.name=<name-of-app>`

  De Spring boot starter wijst automatisch `cloudRoleName` toe aan de waarde die u invoert voor de eigenschap `spring.application.name`.

- Als u de `WebRequestTrackingFilter` gebruikt, wordt de naam van de toepassing automatisch ingesteld door de `WebAppNameContextInitializer`. Voeg het volgende toe aan het configuratie bestand (ApplicationInsights. XML):

  ```XML
  <ContextInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
  </ContextInitializers>
  ```

- Als u de context klasse Cloud gebruikt:

  ```Java
  telemetryClient.getContext().getCloud().setRole("My Component Name");
  ```

## <a name="next-steps"></a>Volgende stappen

- Schrijf [aangepaste telemetrie](../../azure-monitor/app/api-custom-events-metrics.md).
- Raadpleeg voor geavanceerde correlatie scenario's in ASP.NET Core en ASP.NET het artikel [aangepaste bewerkingen bijhouden](custom-operations-tracking.md) .
- Meer informatie over het [instellen van cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) voor andere sdk's.
- Alle onderdelen van uw micro service op Application Insights onboarden. Bekijk de [ondersteunde platforms](../../azure-monitor/app/platforms.md).
- Zie het [gegevens model](../../azure-monitor/app/data-model.md) voor Application Insights typen.
- Meer informatie over hoe u [telemetrie kunt uitbreiden en filteren](../../azure-monitor/app/api-filtering-sampling.md).
- Raadpleeg de [Application Insights-configuratie referentie](configuration-with-applicationinsights-config.md).
