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
ms.openlocfilehash: bb28171ceca9861fb5cc0b7be1db9ab58ef72a1b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70124108"
---
# <a name="telemetry-correlation-in-application-insights"></a>Intermetrie-correlatie in Application Insights

In de wereld van micro Services moet voor elke logische bewerking werk worden uitgevoerd in verschillende onderdelen van de service. Elk van deze onderdelen kan afzonderlijk worden bewaakt door [Azure-toepassing Insights](../../azure-monitor/app/app-insights-overview.md). Het onderdeel van de web-app communiceert met het onderdeel van de verificatie provider om gebruikers referenties te valideren en met het API-onderdeel om gegevens op te halen voor visualisatie. Het API-onderdeel kan gegevens uit andere services opvragen en cache provider onderdelen gebruiken om het facturerings onderdeel op de hoogte te stellen van deze aanroep. Application Insights ondersteunt gedistribueerde telemetrie-correlatie, die u gebruikt om te detecteren welk onderdeel verantwoordelijk is voor storingen of verminderde prestaties.

In dit artikel wordt het gegevens model uitgelegd dat door Application Insights wordt gebruikt voor het correleren van telemetrie die door meerdere onderdelen worden verzonden. Hierin worden technieken en protocollen voor context doorgifte beschreven. Het behandelt ook de implementatie van correlatie concepten op verschillende talen en platformen.

## <a name="data-model-for-telemetry-correlation"></a>Gegevens model voor de correlatie van de telemetrie

Application Insights definieert een [gegevens model](../../azure-monitor/app/data-model.md) voor gedistribueerde telemetrie-correlatie. Om telemetrie te koppelen aan de logische bewerking, heeft elk telemetrie-item `operation_Id`een context veld met de naam. Deze id wordt gedeeld door elk telemetrie-item in de gedistribueerde tracering. Dus zelfs als de telemetrie van één laag is verbroken, kunt u nog steeds telemetrie koppelen die door andere onderdelen zijn gerapporteerd.

Een gedistribueerde logische bewerking bestaat meestal uit een set kleinere bewerkingen, die aanvragen verwerken door een van de onderdelen. Deze bewerkingen worden gedefinieerd op basis van [aanvraag](../../azure-monitor/app/data-model-request-telemetry.md)-telemetrie. Elke telemetrie van de aanvraag `id` heeft een eigen, waardoor deze uniek en wereld wijd wordt geïdentificeerd. En alle telemetrie-items (zoals traceringen en uitzonde ringen) die aan deze aanvraag zijn gekoppeld `operation_parentId` , moeten de waarde voor de `id`aanvraag instellen.

Elke uitgaande bewerking, zoals een HTTP-aanroep naar een ander onderdeel, wordt weer gegeven door middel van [afhankelijkheids](../../azure-monitor/app/data-model-dependency-telemetry.md)-telemetrie. Met de telemetrie van een `id` afhankelijkheid wordt ook een eigen, unieke id gedefinieerd. Telemetrie aanvragen, geïnitieerd door deze afhankelijkheids aanroep, gebruikt `id` deze `operation_parentId`als.

U kunt een weer gave van de gedistribueerde logische bewerking `operation_Id`maken `operation_parentId`met behulp `dependency.id`van, en `request.id` met. Deze velden definiëren ook de causality volgorde van telemetrie-aanroepen.

In een micro Services-omgeving kunnen traceringen van onderdelen naar verschillende opslag items gaan. Elk onderdeel kan een eigen instrumentatie sleutel hebben in Application Insights. Als u telemetrie wilt ophalen voor de logische bewerking, vraagt de Application Insights UX gegevens van elk opslag item op. Wanneer het aantal opslag items enorm is, hebt u een hint nodig over waar u de volgende moet zien. Het Application Insights gegevens model definieert twee velden om dit probleem op te `request.source` lossen: en `dependency.target`. Het eerste veld identificeert het onderdeel dat de afhankelijkheids aanvraag heeft gestart, en de tweede identificeert welk onderdeel het antwoord van de afhankelijkheids aanroep retourneert.

## <a name="example"></a>Voorbeeld

We gaan nu een voor beeld van een toepassing met de naam aandelen prijzen bekijken, waarin de huidige markt prijs van een aandeel wordt weer gegeven `Stock`met behulp van een externe API genaamd. De voorraad prijzen toepassing bevat een pagina met `Stock page` de naam dat de webbrowser van de client `GET /Home/Stock`wordt geopend met behulp van. De toepassing voert een `Stock` query uit op de API met `GET /api/stock/value`behulp van een http-aanroep.

U kunt de resulterende telemetrie analyseren door een query uit te voeren:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Houd er rekening mee dat alle telemetrie-items de hoofdmap `operation_Id`delen. Wanneer er een Ajax-aanroep van de pagina wordt gemaakt, wordt er een`qJSXU`nieuwe unieke id () toegewezen aan de telemetrie van de afhankelijkheid en wordt `operation_ParentId`de id van de pagina weergave gebruikt als. De server aanvraag gebruikt vervolgens de Ajax-ID `operation_ParentId`als.

| itemType   | name                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Voorraad pagina                |              | STYz               | STYz         |
| afhankelijkheid | /Home/Stock ophalen           | qJSXU        | STYz               | STYz         |
| request    | Home/Stock ophalen            | KqKwlrSt9PA= | qJSXU              | STYz         |
| afhankelijkheid | /API/Stock/value ophalen      | bBrf2L7mm2g = | KqKwlrSt9PA=       | STYz         |

Wanneer de aanroep `GET /api/stock/value` naar een externe service wordt gedaan, wilt u de identiteit van die server weten zodat u het veld op de `dependency.target` juiste wijze kunt instellen. Wanneer de externe service bewaking niet ondersteunt, `target` wordt ingesteld op de hostnaam van de service ( `stock-prices-api.com`bijvoorbeeld). Als de service echter zichzelf identificeert door een vooraf gedefinieerde http-header `target` te retour neren, bevat de service-identiteit waarmee Application Insights een gedistribueerde tracering kunt bouwen door de telemetrie van die service op te vragen.

## <a name="correlation-headers"></a>Correlatie headers

We werken aan een RFC-voor stel voor het [correlatie-HTTP-protocol](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Dit voor stel definieert twee kopteksten:

- `Request-Id`: Hiermee wordt de wereld wijde unieke ID van de aanroep uitgevoerd.
- `Correlation-Context`: Hiermee wordt de verzameling naam/waarde-paren van de gedistribueerde tracerings eigenschappen.

De standaard definieert twee schema's voor `Request-Id` generatie: plat en hiërarchisch. Met het platte schema wordt een bekende `Id` sleutel gedefinieerd voor de `Correlation-Context` verzameling.

Application Insights definieert de [uitbrei ding](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) voor het correlatie-http-protocol. Het maakt `Request-Context` gebruik van naam/waarde-paren voor het door geven van de verzameling eigenschappen die wordt gebruikt door de directe aanroeper of de aanroepee. De Application Insights SDK gebruikt deze header om `dependency.target` `request.source` velden in te stellen.

### <a name="w3c-distributed-tracing"></a>Gedistribueerde W3C-tracering

Er wordt overgeschakeld naar de [W3C-indeling](https://w3c.github.io/trace-context/)voor gedistribueerde tracering. Hiermee wordt het volgende gedefinieerd:

- `traceparent`: Hiermee wordt de wereld wijde unieke bewerkings-ID en de unieke id van de aanroep uitgevoerd.
- `tracestate`: Voert een tracering systeem-specifieke context uit.

#### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Ondersteuning voor gedistribueerde W3C-tracering inschakelen voor klassieke ASP.NET-Apps

Deze functie is beschikbaar in `Microsoft.ApplicationInsights.Web` en `Microsoft.ApplicationInsights.DependencyCollector` pakketten vanaf versie 2.8.0-beta1.
Het is standaard uitgeschakeld. Als u deze wilt inschakelen `ApplicationInsights.config`, wijzigt u:

- Voeg `RequestTrackingTelemetryModule`onder het element `EnableW3CHeadersExtraction` met de waarde ingesteld op `true`toe.
- Voeg `DependencyTrackingTelemetryModule`onder het element `EnableW3CHeadersInjection` met de waarde ingesteld op `true`toe.
- Voeg `W3COperationCorrelationTelemetryInitializer` onder de `TelemetryInitializers` soort 

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers> 
```

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Ondersteuning voor gedistribueerde W3C-tracering voor ASP.NET Core-Apps inschakelen

Deze functie bevindt zich in `Microsoft.ApplicationInsights.AspNetCore` versie 2.5.0-beta1 en in `Microsoft.ApplicationInsights.DependencyCollector` versie 2.8.0-beta1.
Het is standaard uitgeschakeld. Als u deze wilt inschakelen `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` , `true`stelt u in op:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

#### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Ondersteuning voor gedistribueerde W3C-tracering inschakelen voor java-apps

- **Binnenkomende configuratie**

  - Voor Java EE-apps voegt u het volgende toe `<TelemetryModules>` aan de tag in ApplicationInsights. XML:

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
  > De modus voor achterwaartse compatibiliteit is standaard ingeschakeld en `enableW3CBackCompat` de para meter is optioneel. Gebruik deze optie alleen als u achterwaartse compatibiliteit wilt uitschakelen.
  >
  > In het ideale geval kunt u deze uitschakelen wanneer al uw services zijn bijgewerkt naar nieuwere versies van Sdk's die ondersteuning bieden voor het W3C-protocol. Wij raden u ten zeerste aan deze nieuwere Sdk's zo snel mogelijk te verplaatsen.

> [!IMPORTANT]
> Zorg ervoor dat zowel binnenkomende als uitgaande configuraties exact hetzelfde zijn.

#### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Ondersteuning voor gedistribueerde W3C-tracering inschakelen voor web-apps

Deze functie is in `Microsoft.ApplicationInsights.JavaScript`. Het is standaard uitgeschakeld. Als u deze wilt inschakelen `distributedTracingMode` , gebruikt u configuratie. AI_AND_W3C wordt verzorgd voor back-compatibiliteit met verouderde services van Application Insights instrument:

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

De [gegevens model specificatie](https://opentracing.io/) opentracering en Application Insights gegevens modellen op de volgende manier:

| Application Insights                  | Opentraceren                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span`met`span.kind = server`                  |
| `Dependency`                          | `Span`met`span.kind = client`                  |
| `Id`van `Request` en`Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference`van het `ChildOf` type (het bovenliggende bereik)   |

Zie het Application Insights telemetrie- [gegevens model](../../azure-monitor/app/data-model.md)voor meer informatie. 

Zie voor definities van opentracerings concepten de opentracerings [specificatie](https://github.com/opentracing/specification/blob/master/specification.md) en [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-net"></a>Telemetrie-correlatie in .NET

In de loop van de tijd zijn er verschillende manieren gedefinieerd voor het correleren van telemetrie-en Diagnostische logboeken:

- `System.Diagnostics.CorrelationManager`Hiermee kunt u [LogicalOperationStack en ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx)bijhouden. 
- `System.Diagnostics.Tracing.EventSource`en Event Tracing for Windows (ETW) definiëren de methode [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) .
- `ILogger`maakt gebruik van [logboek bereiken](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- Windows Communication Foundation (WCF) en HTTP-updoorgifte van de huidige context.

Deze methoden hebben echter geen automatische ondersteuning voor gedistribueerde tracering ingeschakeld. `DiagnosticSource`is een manier om automatische correlatie tussen machines te ondersteunen. .NET-bibliotheken ondersteunen ' DiagnosticSource ' en kunnen automatische doorgifte van de correlatie tussen computers via het Trans Port, zoals HTTP, toestaan.

In de [hand leiding voor activiteiten](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) in `DiagnosticSource` worden de basis beginselen van het bijhouden van activiteiten uitgelegd.

ASP.NET Core 2,0 ondersteunt het ophalen van HTTP-headers en het starten van een nieuwe activiteit.

`System.Net.HttpClient`, te beginnen met versie 4.1.0, ondersteunt automatische injectie van de correlatie-HTTP-headers en het bijhouden van de HTTP-aanroep als een activiteit.

Er is een nieuwe HTTP-module, [micro soft. AspNet. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), voor klassieke ASP.net. Met `DiagnosticSource`deze module wordt de telemetrie-correlatie geïmplementeerd met. Er wordt een activiteit gestart op basis van binnenkomende aanvraag headers. Ook wordt de telemetrie gecorreleerd vanuit de verschillende stadia van aanvraag verwerking, zelfs voor gevallen waarin elke fase van de verwerking van Internet Information Services (IIS) wordt uitgevoerd op een andere beheerde thread.

De Application Insights SDK, te beginnen met versie 2.4.0-beta1, `DiagnosticSource` gebruikt `Activity` en voor het verzamelen van telemetrie en om deze te koppelen aan de huidige activiteit.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Telemetrie-correlatie in de Java SDK

De [Application INSIGHTS SDK voor Java](../../azure-monitor/app/java-get-started.md) ondersteunt automatische correlatie van telemetrie, te beginnen met versie 2.0.0. De gegevens worden automatisch `operation_id` gevuld voor alle telemetrie (zoals traceringen, uitzonde ringen en aangepaste gebeurtenissen) die binnen het bereik van een aanvraag worden uitgegeven. Het zorgt er ook voor dat de correlatie headers worden door gegeven (eerder beschreven) voor service-naar-service-aanroepen via HTTP, als de [Java SDK-agent](../../azure-monitor/app/java-agent.md) is geconfigureerd.

> [!NOTE]
> Alleen aanroepen via Apache httpclient maakt worden ondersteund voor de correlatie functie. Als u veer RestTemplate of Feign gebruikt, kunnen beide worden gebruikt met Apache httpclient maakt onder de schermen.

Momenteel wordt de automatische context doorgifte voor berichten technologieën (zoals Kafka, RabbitMQ of Azure Service Bus) niet ondersteund. Het is echter mogelijk om dergelijke scenario's hand matig te coderen met behulp `trackRequest` van de `trackDependency` -en-api's. In deze Api's vertegenwoordigt een telemetrie van een afhankelijkheid een bericht in de wachtrij van een producent. de aanvraag vertegenwoordigt een bericht dat door een gebruiker wordt verwerkt. In dit geval moeten beide `operation_id` en `operation_parentId` worden door gegeven in de eigenschappen van het bericht.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>Telemetrie-correlatie in asynchrone Java-toepassing

Volg dit uitgebreide artikel als u [de](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) telemetrie in een asynchrone Spring boot-toepassing wilt correleren. Het bevat richt lijnen voor het [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) van de lente en [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html). 


<a name="java-role-name"></a>
## <a name="role-name"></a>Rolnaam

Soms wilt u de manier waarop onderdeel namen worden weer gegeven in de [toepassings toewijzing](../../azure-monitor/app/app-map.md)aanpassen. Hiertoe kunt u de `cloud_RoleName` op een van de volgende manieren hand matig instellen:

- Als u achtereen met de Application Insights Spring boot Starter gebruikt, hoeft u alleen de aangepaste naam voor de toepassing in het bestand Application. Properties in te stellen.

  `spring.application.name=<name-of-app>`

  De Spring boot-Starter wordt automatisch `cloudRoleName` toegewezen aan de waarde die u invoert `spring.application.name` voor de eigenschap.

- Als u de `WebRequestTrackingFilter`gebruikt `WebAppNameContextInitializer` , wordt de naam van de toepassing automatisch ingesteld. Voeg het volgende toe aan het configuratie bestand (ApplicationInsights. XML):

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

- Schrijf [aangepaste](../../azure-monitor/app/api-custom-events-metrics.md)telemetrie.
- Raadpleeg voor geavanceerde correlatie scenario's in ASP.NET Core en ASP.NET het artikel [aangepaste bewerkingen bijhouden](custom-operations-tracking.md) .
- Meer informatie over het [instellen van cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) voor andere sdk's.
- Alle onderdelen van uw micro service op Application Insights onboarden. Bekijk de [ondersteunde platforms](../../azure-monitor/app/platforms.md).
- Zie het [gegevens model](../../azure-monitor/app/data-model.md) voor Application Insights typen.
- Meer informatie over hoe u telemetrie kunt [uitbreiden en filteren](../../azure-monitor/app/api-filtering-sampling.md).
- Raadpleeg de [Application Insights-configuratie referentie](configuration-with-applicationinsights-config.md).
