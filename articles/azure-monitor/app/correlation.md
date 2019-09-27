---
title: Correlatie van de telemetrie van Azure-toepassing Insights | Microsoft Docs
description: Application Insights telemetrie-correlatie
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: fe52fe51b347b232e03bad943906413b90c853c0
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338178"
---
# <a name="telemetry-correlation-in-application-insights"></a>Intermetrie-correlatie in Application Insights

In de wereld van micro Services moet voor elke logische bewerking werk worden uitgevoerd in verschillende onderdelen van de service. Elk van deze onderdelen kan afzonderlijk worden bewaakt door [Azure-toepassing Insights](../../azure-monitor/app/app-insights-overview.md). Het onderdeel van de web-app communiceert met het onderdeel van de verificatie provider om gebruikers referenties te valideren en met het API-onderdeel om gegevens op te halen voor visualisatie. Het API-onderdeel kan gegevens uit andere services opvragen en cache provider onderdelen gebruiken om het facturerings onderdeel op de hoogte te stellen van deze aanroep. Application Insights ondersteunt gedistribueerde telemetrie-correlatie, die u gebruikt om te detecteren welk onderdeel verantwoordelijk is voor storingen of verminderde prestaties.

In dit artikel wordt het gegevens model uitgelegd dat door Application Insights wordt gebruikt voor het correleren van telemetrie die door meerdere onderdelen worden verzonden. Hierin worden technieken en protocollen voor context doorgifte beschreven. Het behandelt ook de implementatie van correlatie concepten op verschillende talen en platformen.

## <a name="data-model-for-telemetry-correlation"></a>Gegevens model voor de correlatie van de telemetrie

Application Insights definieert een [gegevens model](../../azure-monitor/app/data-model.md) voor gedistribueerde telemetrie-correlatie. Om telemetrie te koppelen aan de logische bewerking, heeft elk telemetrie-item een context veld met de naam `operation_Id`. Deze id wordt gedeeld door elk telemetrie-item in de gedistribueerde tracering. Dus zelfs als de telemetrie van één laag is verbroken, kunt u nog steeds telemetrie koppelen die door andere onderdelen zijn gerapporteerd.

Een gedistribueerde logische bewerking bestaat meestal uit een set kleinere bewerkingen, die aanvragen verwerken door een van de onderdelen. Deze bewerkingen worden gedefinieerd op basis van [aanvraag-telemetrie](../../azure-monitor/app/data-model-request-telemetry.md). Elke telemetrie van de aanvraag heeft een eigen `id` waarmee het uniek en wereld wijd wordt geïdentificeerd. En alle telemetrie-items (zoals traceringen en uitzonde ringen) die aan deze aanvraag zijn gekoppeld, moeten de `operation_parentId` instellen op de waarde van de aanvraag `id`.

Elke uitgaande bewerking, zoals een HTTP-aanroep naar een ander onderdeel, wordt weer gegeven door middel van [afhankelijkheids-telemetrie](../../azure-monitor/app/data-model-dependency-telemetry.md). Met de telemetrie van een afhankelijkheid wordt ook een eigen `id` gedefinieerd dat wereld wijd uniek is. Telemetrie aanvragen, geïnitieerd door deze afhankelijkheids aanroep, gebruikt deze `id` als `operation_parentId`.

U kunt een weer gave van de gedistribueerde logische bewerking maken met behulp van `operation_Id`, `operation_parentId` en `request.id` met `dependency.id`. Deze velden definiëren ook de causality volgorde van telemetrie-aanroepen.

In een micro Services-omgeving kunnen traceringen van onderdelen naar verschillende opslag items gaan. Elk onderdeel kan een eigen instrumentatie sleutel hebben in Application Insights. Als u telemetrie wilt ophalen voor de logische bewerking, vraagt de Application Insights UX gegevens van elk opslag item op. Wanneer het aantal opslag items enorm is, hebt u een hint nodig over waar u de volgende moet zien. Het Application Insights gegevens model definieert twee velden om dit probleem op te lossen: `request.source` en `dependency.target`. Het eerste veld identificeert het onderdeel dat de afhankelijkheids aanvraag heeft gestart, en de tweede identificeert welk onderdeel het antwoord van de afhankelijkheids aanroep retourneert.

## <a name="example"></a>Voorbeeld

We gaan nu een voor beeld van een toepassing met de naam aandelen prijzen bekijken, waarin de huidige markt prijs van een aandeel wordt weer gegeven met behulp van een externe API met de naam `Stock`. De voorraad prijzen toepassing heeft een pagina met de naam `Stock page` dat de webbrowser van de client wordt geopend met behulp van `GET /Home/Stock`. De toepassing voert een query uit op de `Stock`-API met behulp van een HTTP-aanroep `GET /api/stock/value`.

U kunt de resulterende telemetrie analyseren door een query uit te voeren:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Houd er rekening mee dat alle telemetrie-items de root `operation_Id` delen. Wanneer er een Ajax-aanroep van de pagina wordt gemaakt, wordt er een nieuwe unieke ID (@no__t 0) toegewezen aan de telemetrie van de afhankelijkheid en wordt de ID van de pagina weergave gebruikt als `operation_ParentId`. De server aanvraag gebruikt vervolgens de Ajax-ID als `operation_ParentId`.

| itemType   | name                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Voorraad pagina                |              | STYz               | STYz         |
| afhankelijkheid | /Home/Stock ophalen           | qJSXU        | STYz               | STYz         |
| request    | Home/Stock ophalen            | KqKwlrSt9PA= | qJSXU              | STYz         |
| afhankelijkheid | /API/Stock/value ophalen      | bBrf2L7mm2g = | KqKwlrSt9PA=       | STYz         |

Wanneer de aanroep `GET /api/stock/value` wordt gedaan bij een externe service, wilt u weten wat de identiteit van de server is, zodat u het veld `dependency.target` op de juiste wijze kunt instellen. Wanneer de externe service bewaking niet ondersteunt, wordt `target` ingesteld op de hostnaam van de service (bijvoorbeeld `stock-prices-api.com`). Als de service echter zichzelf identificeert door een vooraf gedefinieerde HTTP-header te retour neren, bevat `target` de service-identiteit waarmee Application Insights een gedistribueerde tracering kunt bouwen door de telemetrie van die service op te vragen.

## <a name="correlation-headers"></a>Correlatie headers

Er wordt overgeschakeld naar de [W3C-traceer context, die het](https://w3c.github.io/trace-context/) volgende definieert:

- `traceparent`: Hiermee wordt de wereld wijde unieke bewerkings-ID en de unieke id van de aanroep uitgevoerd.
- `tracestate`: Voert een tracering systeem-specifieke context uit.

De nieuwste versies van Application Insights Sdk's ondersteunen tracering-context protocol, maar u moet er mogelijk voor kiezen (het zorgt voor achterwaartse compatibiliteit met het oude correlatie protocol dat door ApplicationInsights Sdk's wordt ondersteund).

De [aanvraag-id van het correlatie HTTP-protocol ook wel](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) is op het afschaffing-pad. Dit protocol definieert twee headers:

- `Request-Id`: Hiermee wordt de wereld wijde unieke ID van de aanroep uitgevoerd.
- `Correlation-Context`: Hiermee wordt de verzameling naam/waarde-paren van de gedistribueerde tracerings eigenschappen.

Application Insights definieert ook de [uitbrei ding](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) voor het correlatie-http-protocol. Hierbij worden `Request-Context`-naam/waarde-paren gebruikt voor het door geven van de verzameling eigenschappen die door de directe aanroeper of de aanroepee worden gebruikt. De Application Insights SDK gebruikt deze koptekst om velden `dependency.target` en `request.source` in te stellen.

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
Deze functie is beschikbaar in `Microsoft.ApplicationInsights.Web`-en `Microsoft.ApplicationInsights.DependencyCollector`-pakketten die beginnen met versie 2.8.0-beta1.
Het is standaard uitgeschakeld. Wijzig `ApplicationInsights.config` om dit in te scha kelen:

- Voeg onder `RequestTrackingTelemetryModule` het element `EnableW3CHeadersExtraction` toe met de waarde ingesteld op `true`.
- Voeg onder `DependencyTrackingTelemetryModule` het element `EnableW3CHeadersInjection` toe met de waarde ingesteld op `true`.
- Voeg `W3COperationCorrelationTelemetryInitializer` toe onder de `TelemetryInitializers` vergelijkbaar met 

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers> 
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Ondersteuning voor gedistribueerde W3C-tracering voor ASP.NET Core-Apps inschakelen

 > [!NOTE]
  > Er is geen configuratie vereist, te beginnen met `Microsoft.ApplicationInsights.AspNetCore`-versie 2.8.0.
 
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

  - Voor Java EE-apps voegt u het volgende toe aan de tag `<TelemetryModules>` in ApplicationInsights. XML:

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

Deze functie bevindt zich in `Microsoft.ApplicationInsights.JavaScript`. Het is standaard uitgeschakeld. Gebruik `distributedTracingMode` config om het in te scha kelen. AI_AND_W3C wordt verzorgd voor back-compatibiliteit met verouderde services van Application Insights instrument:

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

## <a name="telemetry-correlation-in-net"></a>Telemetrie-correlatie in .NET

In de loop van de tijd zijn er verschillende manieren gedefinieerd voor het correleren van telemetrie-en Diagnostische logboeken:

- `System.Diagnostics.CorrelationManager` staat het bijhouden van [LogicalOperationStack en ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx)toe. 
- met `System.Diagnostics.Tracing.EventSource` en Event Tracing for Windows (ETW) wordt de methode [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) gedefinieerd.
- `ILogger` maakt gebruik van [logboek bereik](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- Windows Communication Foundation (WCF) en HTTP-updoorgifte van de huidige context.

Deze methoden hebben echter geen automatische ondersteuning voor gedistribueerde tracering ingeschakeld. `DiagnosticSource` is een manier om automatische correlatie tussen machines te ondersteunen. .NET-bibliotheken ondersteunen ' DiagnosticSource ' en kunnen automatische doorgifte van de correlatie tussen computers via het Trans Port, zoals HTTP, toestaan.

In de [hand leiding voor activiteiten](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) in `DiagnosticSource` worden de basis beginselen van het bijhouden van activiteiten uitgelegd.

ASP.NET Core 2,0 ondersteunt het ophalen van HTTP-headers en het starten van een nieuwe activiteit.

`System.Net.Http.HttpClient`, te beginnen met versie 4.1.0, ondersteunt automatische injectie van de correlatie-HTTP-headers en het bijhouden van de HTTP-aanroep als een activiteit.

Er is een nieuwe HTTP-module, [micro soft. AspNet. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), voor klassieke ASP.net. Met deze module wordt de telemetrie-correlatie geïmplementeerd met behulp van `DiagnosticSource`. Er wordt een activiteit gestart op basis van binnenkomende aanvraag headers. Ook wordt de telemetrie gecorreleerd vanuit de verschillende stadia van aanvraag verwerking, zelfs voor gevallen waarin elke fase van de verwerking van Internet Information Services (IIS) wordt uitgevoerd op een andere beheerde thread.

De Application Insights SDK, te beginnen met versie 2.4.0-beta1, gebruikt `DiagnosticSource` en `Activity` om telemetrie te verzamelen en te koppelen aan de huidige activiteit.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Telemetrie-correlatie in de Java SDK

De [Application INSIGHTS SDK voor Java](../../azure-monitor/app/java-get-started.md) ondersteunt automatische correlatie van telemetrie, te beginnen met versie 2.0.0. @No__t-0 wordt automatisch gevuld voor alle telemetrie (zoals traceringen, uitzonde ringen en aangepaste gebeurtenissen) die binnen het bereik van een aanvraag worden uitgegeven. Het zorgt er ook voor dat de correlatie headers worden door gegeven (eerder beschreven) voor service-naar-service-aanroepen via HTTP, als de [Java SDK-agent](../../azure-monitor/app/java-agent.md) is geconfigureerd.

> [!NOTE]
> Alleen aanroepen via Apache httpclient maakt worden ondersteund voor de correlatie functie. Als u veer RestTemplate of Feign gebruikt, kunnen beide worden gebruikt met Apache httpclient maakt onder de schermen.

Momenteel wordt de automatische context doorgifte voor berichten technologieën (zoals Kafka, RabbitMQ of Azure Service Bus) niet ondersteund. Het is echter mogelijk om dergelijke scenario's hand matig te coderen met behulp van de Api's `trackDependency` en `trackRequest`. In deze Api's vertegenwoordigt een telemetrie van een afhankelijkheid een bericht in de wachtrij van een producent. de aanvraag vertegenwoordigt een bericht dat door een gebruiker wordt verwerkt. In dit geval moeten zowel `operation_id` als `operation_parentId` worden door gegeven aan de eigenschappen van het bericht.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>Telemetrie-correlatie in asynchrone Java-toepassing

Volg dit uitgebreide artikel als u [de](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) telemetrie in een asynchrone Spring boot-toepassing wilt correleren. Het bevat richt lijnen voor het [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) van de lente en [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html). 


<a name="java-role-name"></a>
## <a name="role-name"></a>Rolnaam

Soms wilt u de manier waarop onderdeel namen worden weer gegeven in de [toepassings toewijzing](../../azure-monitor/app/app-map.md)aanpassen. Hiervoor kunt u de `cloud_RoleName` hand matig instellen door een van de volgende handelingen uit te voeren:

- Als u achtereen met de Application Insights Spring boot Starter gebruikt, hoeft u alleen de aangepaste naam voor de toepassing in het bestand Application. Properties in te stellen.

  `spring.application.name=<name-of-app>`

  De Spring boot starter wijst automatisch `cloudRoleName` toe aan de waarde die u invoert voor de eigenschap `spring.application.name`.

- Als u de `WebRequestTrackingFilter` gebruikt, wordt de naam van de toepassing automatisch ingesteld met de `WebAppNameContextInitializer`. Voeg het volgende toe aan het configuratie bestand (ApplicationInsights. XML):

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
