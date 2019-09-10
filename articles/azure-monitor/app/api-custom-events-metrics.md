---
title: Application Insights-API voor aangepaste gebeurtenissen en metrische gegevens | Microsoft Docs
description: Voeg een paar regels code toe aan de apparaat-of bureau blad-app, de webpagina of de service om het gebruik en de diagnose problemen op te sporen.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: mbullwin
ms.openlocfilehash: 9bedb74f4e882ac6e4206ee7fef676c94dc2422d
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "68717455"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>Application Insights-API voor aangepaste gebeurtenissen en metrische gegevens

Voeg een paar regels code in uw toepassing toe om erachter te komen wat gebruikers mee doen, of om problemen te diagnosticeren. U kunt telemetrie verzenden vanaf apparaat-en desktop-apps, webclients en webservers. Gebruik de [Azure-toepassing Insights](../../azure-monitor/app/app-insights-overview.md) core-telemetrie-API om aangepaste gebeurtenissen en metrische gegevens en uw eigen versies van standaard-telemetrie te verzenden. Deze API is dezelfde API als de standaard Application Insights gegevens verzamelaars gebruiken.

> [!NOTE]
> `TrackMetric()`is niet langer de voorkeurs methode voor het verzenden van aangepaste metrische gegevens voor uw op .NET gebaseerde toepassingen. In [versie 2,60-bèta 3](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/CHANGELOG.md#version-260-beta3) van de Application Insights .NET SDK [`TelemetryClient.GetMetric()`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.getmetric?view=azure-dotnet) is een nieuwe methode geïntroduceerd. Vanaf de Application Insights .NET SDK- [versie 2,72](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.getmetric?view=azure-dotnet) is deze functionaliteit onderdeel van de stabiele release.

## <a name="api-summary"></a>API-overzicht

De core-API is gelijkmatig op alle platforms, van enkele variaties zoals `GetMetric`(alleen .net).

| Methode | Gebruikt voor |
| --- | --- |
| [`TrackPageView`](#page-views) |Pagina's, schermen, Blades of formulieren. |
| [`TrackEvent`](#trackevent) |Gebruikers acties en andere gebeurtenissen. Wordt gebruikt om het gebruikers gedrag bij te houden of de prestaties te bewaken. |
| [`GetMetric`](#getmetric) |Nul en multi-dimensionale metrische gegevens, centraal geconfigureerde aggregatie C# , alleen. |
| [`TrackMetric`](#trackmetric) |Prestatie metingen zoals wachtrij lengten die niet gerelateerd zijn aan specifieke gebeurtenissen. |
| [`TrackException`](#trackexception) |Logboek registratie van uitzonde ringen voor diagnose. Traceren waar deze zich voordoen ten opzichte van andere gebeurtenissen en de stack traceringen onderzoeken. |
| [`TrackRequest`](#trackrequest) |Registratie van de frequentie en de duur van server aanvragen voor prestatie analyse. |
| [`TrackTrace`](#tracktrace) |Diagnostische logboek berichten. U kunt ook logboeken van derden vastleggen. |
| [`TrackDependency`](#trackdependency) |Registratie van de duur en frequentie van aanroepen naar externe onderdelen waarvan uw app afhankelijk is. |

U kunt [Eigenschappen en metrische gegevens](#properties) aan de meeste van deze telemetrie-aanroepen koppelen.

## <a name="prep"></a>Voordat u begint

Als u nog geen verwijzing hebt over Application Insights SDK:

* Voeg de Application Insights SDK toe aan uw project:

  * [ASP.NET-project](../../azure-monitor/app/asp-net.md)
  * [ASP.NET Core project](../../azure-monitor/app/asp-net-core.md)
  * [Java-project](../../azure-monitor/app/java-get-started.md)
  * [Node. js-project](../../azure-monitor/app/nodejs.md)
  * [Java script in elke webpagina](../../azure-monitor/app/javascript.md) 
* In uw apparaat-of webservercode neemt u het volgende op:

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

    *Node. js:* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Een TelemetryClient-exemplaar ophalen

Een exemplaar van ( `TelemetryClient` behalve in Java script) ophalen van webpagina's:

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```

*Visual Basic*

```vb
Private Dim telemetry As New TelemetryClient
```

*Java*

```java
private TelemetryClient telemetry = new TelemetryClient();
``` 

*Node.js*

```javascript
var telemetry = applicationInsights.defaultClient;
```

TelemetryClient is thread-safe.

Voor ASP.NET-en Java-projecten worden binnenkomende HTTP-aanvragen automatisch vastgelegd. Mogelijk wilt u aanvullende instanties van TelemetryClient maken voor een andere module van uw app. U kunt bijvoorbeeld één TelemetryClient-exemplaar in uw middleware-klasse hebben om bedrijfs logica gebeurtenissen te rapporteren. U kunt eigenschappen zoals UserId en DeviceId instellen om de computer te identificeren. Deze informatie wordt gekoppeld aan alle gebeurtenissen die het exemplaar verzendt. 

*C#*

```csharp
TelemetryClient.Context.User.Id = "...";
TelemetryClient.Context.Device.Id = "...";
```

*Java*

```java
telemetry.getContext().getUser().setId("...");
telemetry.getContext().getDevice().setId("...");
```

In node. js-projecten kunt u gebruiken `new applicationInsights.TelemetryClient(instrumentationKey?)` om een nieuw exemplaar te maken, maar dit wordt alleen aanbevolen voor scenario's waarvoor een geïsoleerde configuratie van `defaultClient`de singleton is vereist.

## <a name="trackevent"></a>Track Event

In Application Insights is een *aangepaste gebeurtenis* een gegevens punt dat u kunt weer geven in [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) als een geaggregeerd aantal en in diagnostische [Zoek opdrachten](../../azure-monitor/app/diagnostic-search.md) als afzonderlijke exemplaren. (Deze heeft geen betrekking op de gebeurtenissen van MVC of ander Framework.)

Voeg `TrackEvent` aanroepen in uw code in om verschillende gebeurtenissen te tellen. Hoe vaak gebruikers een bepaalde functie kiezen, hoe vaak ze bepaalde doel stellingen bereiken of misschien hoe vaak ze bepaalde soorten fouten maken.

U kunt bijvoorbeeld in een game-app een gebeurtenis verzenden wanneer een gebruiker het spel wint:

*JavaScript*

```javascript
appInsights.trackEvent("WinGame");
```

*C#*

```csharp
telemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
telemetry.TrackEvent("WinGame")
```

*Java*

```java
telemetry.trackEvent("WinGame");
```

*Node.js*

```javascript
telemetry.trackEvent({name: "WinGame"});
```

### <a name="custom-events-in-analytics"></a>Aangepaste gebeurtenissen in Analytics

De telemetrie is beschikbaar in `customEvents` de tabel in [Application Insights Analytics](analytics.md). Elke rij vertegenwoordigt een aanroep naar `trackEvent(..)` in uw app.

Als er [steek proeven](../../azure-monitor/app/sampling.md) worden uitgevoerd, wordt in de eigenschap itemCount een waarde weer gegeven die groter is dan 1. Bijvoorbeeld itemCount = = 10 betekent dat er bij 10 aanroepen naar track Event (), het steekproef proces slechts een van deze toegezonden. Als u een correct aantal aangepaste gebeurtenissen wilt ontvangen, moet u dus code gebruiken, `customEvents | summarize sum(itemCount)`zoals.

## <a name="getmetric"></a>GetMetric

### <a name="examples"></a>Voorbeelden

*C#*

```csharp
namespace User.Namespace.Example01
{
    using System;
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    /// <summary>
    /// Most simple cases are one-liners.
    /// This is all possible without even importing an additional namespace.
    /// </summary>

    public class Sample01
    {
        /// <summary />
        public static void Exec()
        {
            // *** SENDING METRICS ***

            // Recall how you send custom telemetry with Application Insights in other cases, e.g. Events.
            // The following will result in an EventTelemetry object to be sent to the cloud right away.
            TelemetryClient client = new TelemetryClient();
            client.TrackEvent("SomethingInterestingHappened");

            // Metrics work very similar. However, the value is not sent right away.
            // It is aggregated with other values for the same metric, and the resulting summary (aka "aggregate" is sent automatically every minute.
            // To mark this difference, we use a pattern that is similar, but different from the established TrackXxx(..) pattern that sends telemetry right away:

            client.GetMetric("CowsSold").TrackValue(42);

            // *** MULTI-DIMENSIONAL METRICS ***

            // The above example shows a zero-dimensional metric.
            // Metrics can also be multi-dimensional.
            // In the initial version we are supporting up to 2 dimensions, and we will add support for more in the future as needed.
            // Here is an example for a one-dimensional metric:

            Metric animalsSold = client.GetMetric("AnimalsSold", "Species");

            animalsSold.TrackValue(42, "Pigs");
            animalsSold.TrackValue(24, "Horses");

            // The values for Pigs and Horses will be aggregated separately from each other and will result in two distinct aggregates.
            // You can control the maximum number of number data series per metric (and thus your resource usage and cost).
            // The default limits are no more than 1000 total data series per metric, and no more than 100 different values per dimension.
            // We discuss elsewhere how to change them.
            // We use a common .NET pattern: TryXxx(..) to make sure that the limits are observed.
            // If the limits are already reached, Metric.TrackValue(..) will return False and the value will not be tracked. Otherwise it will return True.
            // This is particularly useful if the data for a metric originates from user input, e.g. a file:

            Tuple<int, string> countAndSpecies = ReadSpeciesFromUserInput();
            int count = countAndSpecies.Item1;
            string species = countAndSpecies.Item2;

            if (!animalsSold.TrackValue(count, species))

            {
                client.TrackTrace($"Data series or dimension cap was reached for metric {animalsSold.Identifier.MetricId}.", SeverityLevel.Error);
            }

            // You can inspect a metric object to reason about its current state. For example:
            int currentNumberOfSpecies = animalsSold.GetDimensionValues(1).Count;
        }

        private static void ResetDataStructure()
        {
            // Do stuff
        }

        private static Tuple<int, string> ReadSpeciesFromUserInput()
        {
            return Tuple.Create(18, "Cows");
        }

        private static int AddItemsToDataStructure()
        {
            // Do stuff
            return 5;
        }
    }
}
```

## <a name="trackmetric"></a>TrackMetric

> [!NOTE]
> Micro soft. ApplicationInsights. TelemetryClient. TrackMetric is niet de voorkeurs methode voor het verzenden van metrische gegevens. Metrische gegevens moeten altijd vooraf worden geaggregeerd in een tijds periode voordat ze worden verzonden. Gebruik een van de GetMetric-Overloads (..) om een metrisch object op te halen voor het verkrijgen van toegang tot de vooraf aggregatie mogelijkheden van de SDK. Als u uw eigen logica voor de vooraf aggregatie implementeert, kunt u de TrackMetric ()-methode gebruiken om de resulterende aggregaties te verzenden. Als voor uw toepassing altijd een afzonderlijk telemetrie-item moet worden verzonden zonder aggregatie over een bepaalde periode, hebt u waarschijnlijk een use-case voor een gebeurtenis-telemetrie. Zie TelemetryClient. track Event (micro soft. ApplicationInsights. DataContracts. EventTelemetry).

Application Insights kan metrische gegevens van grafieken die niet zijn gekoppeld aan bepaalde gebeurtenissen. U kunt bijvoorbeeld een wachtrij lengte met regel matige tussen pozen bewaken. Met metrische gegevens zijn de afzonderlijke metingen minder belang rijker dan de variaties en trends, waardoor statistische grafieken handig zijn.

Als u metrische gegevens naar Application Insights wilt verzenden, kunt u de `TrackMetric(..)` API gebruiken. Er zijn twee manieren om een metrische waarde te verzenden:

* Enkele waarde. Telkens wanneer u een meting in uw toepassing uitvoert, verzendt u de overeenkomstige waarde naar Application Insights. Stel dat u een metriek hebt met een beschrijving van het aantal items in een container. Tijdens een bepaalde periode plaatst u eerst drie items in de container en verwijdert u vervolgens twee items. Daarom zou u twee maal `TrackMetric` aanroepen: eerst de waarde `3` en vervolgens de waarde `-2`door geven. Application Insights namens u beide waarden opslaat.

* Aggregatie. Bij het werken met metrische gegevens is elke meting altijd van belang. In plaats daarvan is een samen vatting van wat er is gebeurd tijdens een bepaalde periode belang rijk. Een dergelijke samen vatting wordt _aggregatie_genoemd. In het bovenstaande voor beeld is `1` de som van de cumulatieve metrische gegevens voor die tijds periode en het aantal meet `2`waarden. Wanneer u de aggregatie aanpak gebruikt, moet u `TrackMetric` één keer per periode aanroepen en de cumulatieve waarden verzenden. Dit is de aanbevolen benadering, omdat het de kosten en de prestaties aanzienlijk kan verminderen door minder gegevens punten naar Application Insights te verzenden, terwijl alle relevante informatie wordt verzameld.

### <a name="examples"></a>Voorbeelden

#### <a name="single-values"></a>Enkele waarden

Eén metrische waarde verzenden:

*JavaScript*

 ```javascript
appInsights.trackMetric("queueLength", 42.0);
 ```

*C#*

```csharp
var sample = new MetricTelemetry();
sample.Name = "metric name";
sample.Value = 42.3;
telemetryClient.TrackMetric(sample);
```

*Java*

```java
telemetry.trackMetric("queueLength", 42.0);
```

*Node.js*

 ```javascript
telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

### <a name="custom-metrics-in-analytics"></a>Aangepaste metrische gegevens in analyse

De telemetrie is beschikbaar in `customMetrics` de tabel in [Application Insights Analytics](analytics.md). Elke rij vertegenwoordigt een aanroep naar `trackMetric(..)` in uw app.

* `valueSum`-Dit is de som van de metingen. Delen door `valueCount`om de gemiddelde waarde op te halen.
* `valueCount`-Het aantal metingen dat is geaggregeerd in deze `trackMetric(..)` aanroep.

## <a name="page-views"></a>Paginaweergaven

In een apparaat-of webpagina-app wordt de telemetrie van de pagina weergave standaard verzonden wanneer elk scherm of elke pagina wordt geladen. Maar u kunt dit wijzigen om pagina weergaven op extra of verschillende tijdstippen bij te houden. In een app waarin tabbladen of Blades worden weer gegeven, kunt u bijvoorbeeld een pagina bijhouden wanneer de gebruiker een nieuwe blade opent.

Gebruikers-en sessie gegevens worden samen met pagina weergaven verzonden als eigenschappen, zodat de gebruikers-en sessie grafieken actief zijn wanneer er sprake is van telemetrie over de pagina weergave.

### <a name="custom-page-views"></a>Aangepaste pagina weergaven

*JavaScript*

```javascript
appInsights.trackPageView("tab1");
```

*C#*

```csharp
telemetry.TrackPageView("GameReviewPage");
```

*Visual Basic*

```vb
telemetry.TrackPageView("GameReviewPage")
```

*Java*

```java
telemetry.trackPageView("GameReviewPage");
```

Als u meerdere tabbladen in verschillende HTML-pagina's hebt, kunt u ook de URL opgeven:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Timing pagina weergaven

Standaard worden de tijden gerapporteerd als **pagina weergave laad tijd** gemeten vanaf het moment dat de browser de aanvraag verzendt, totdat de pagina load van de browser wordt aangeroepen.

In plaats daarvan kunt u het volgende doen:

* Stel een expliciete duur in de [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) - `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`aanroep in:.
* Gebruik de pagina weergave timing `startTrackPage` aanroepen `stopTrackPage`en.

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

De naam die u als de eerste para meter gebruikt, koppelt de start-en stop-aanroepen. Standaard wordt de naam van de huidige pagina gebruikt.

De resulterende laad duur van pagina's die wordt weer gegeven in Metrics Explorer zijn afgeleid van het interval tussen de begin-en stop-aanroepen. U hebt de tijd om u te laten intervallen.

### <a name="page-telemetry-in-analytics"></a>Pagina-telemetrie in Analytics

In [analyse](analytics.md) twee tabellen worden gegevens uit browser bewerkingen weer gegeven:

* De `pageViews` tabel bevat gegevens over de URL en pagina titel
* De `browserTimings` tabel bevat gegevens over client prestaties, zoals de tijd die nodig is voor het verwerken van de inkomende gegevens

Als u wilt weten hoe lang het duurt voor het verwerken van verschillende pagina's door de browser:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

De populaire opties van verschillende browsers ontdekken:

```kusto
pageViews
| summarize count() by client_Browser
```

Om pagina weergaven te koppelen aan AJAX-aanroepen, moet u lid zijn van afhankelijkheden:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest

De server-SDK gebruikt TrackRequest om HTTP-aanvragen te registreren.

U kunt het ook zelf aanroepen als u aanvragen wilt simuleren in een context waarin de webservice-module niet wordt uitgevoerd.

De aanbevolen methode voor het verzenden van telemetrie aanvragen is echter waar de aanvraag fungeert als bewerkings <a href="#operation-context">context</a>.

## <a name="operation-context"></a>Bewerkings context

U kunt telemetrie-items samen correleren door ze te koppelen aan de bewerkings context. De standaard module voor aanvraag tracering doet dit voor uitzonde ringen en andere gebeurtenissen die worden verzonden terwijl een HTTP-aanvraag wordt verwerkt. In [Search](../../azure-monitor/app/diagnostic-search.md) en [Analytics](analytics.md)kunt u eenvoudig alle gebeurtenissen vinden die zijn gekoppeld aan de aanvraag met behulp van de bewerkings-id.

Zie de [correlatie van telemetrie in Application Insights](../../azure-monitor/app/correlation.md) voor meer informatie over correlatie.

Bij het hand matig bijhouden van telemetrie, de eenvoudigste manier om een telemetrie-correlatie te garanderen met behulp van dit patroon:

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...

    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

Samen met het instellen van een bewerkings context maakt een telemetrie- `StartOperation` item van het type dat u opgeeft. Het verzendt het telemetrie-item wanneer u de bewerking ongedaan maakt, of `StopOperation`als u het expliciet aanroept. Als u gebruikt `RequestTelemetry` als het type telemetrie, wordt de duur ingesteld op het tijds interval tussen start en stoppen.

De items die binnen een bereik van de bewerking zijn gerapporteerd, worden ' kinderen ' van deze bewerking. Bewerkings contexten kunnen worden genest.

In de zoek actie wordt de context van de bewerking gebruikt voor het maken van de lijst **Verwante items** :

![Gerelateerde items](./media/api-custom-events-metrics/21.png)

Zie [aangepaste bewerkingen bijhouden met Application Insights .NET SDK](../../azure-monitor/app/custom-operations-tracking.md) voor meer informatie over het bijhouden van aangepaste bewerkingen.

### <a name="requests-in-analytics"></a>Aanvragen in Analytics

In [Application Insights Analytics](analytics.md)worden aanvragen weer gegeven in de `requests` tabel.

Als er [steek proeven](../../azure-monitor/app/sampling.md) worden uitgevoerd, wordt in de eigenschap itemCount een waarde weer gegeven die groter is dan 1. Bijvoorbeeld itemCount = = 10 betekent dat er bij 10 aanroepen naar trackRequest (), het steekproef proces slechts een van deze toegezonden. Als u het juiste aantal aanvragen en gemiddelde duur wilt ophalen op basis van aanvraag namen, gebruikt u de volgende code:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

Uitzonde ringen verzenden naar Application Insights:

* Om [ze te tellen](../../azure-monitor/app/metrics-explorer.md)als een indicatie van de frequentie van een probleem.
* Om [afzonderlijke instanties te controleren](../../azure-monitor/app/diagnostic-search.md).

De rapporten bevatten de stack traceringen.

*C#*

```csharp
try
{
    ...
}
catch (Exception ex)
{
    telemetry.TrackException(ex);
}
```

*Java*

```java
try {
    ...
} catch (Exception ex) {
    telemetry.trackException(ex);
}
```

*JavaScript*

```javascript
try
{
    ...
}
catch (ex)
{
    appInsights.trackException(ex);
}
```

*Node.js*

```javascript
try
{
    ...
}
catch (ex)
{
    telemetry.trackException({exception: ex});
}
```

De Sdk's ondervangen veel uitzonde ringen automatisch, zodat u TrackException niet altijd expliciet hoeft aan te roepen.

* ASP.NET: [Code schrijven om uitzonde ringen te ondervangen](../../azure-monitor/app/asp-net-exceptions.md).
* Java EE: [Uitzonde ringen worden automatisch afgevangen](../../azure-monitor/app/java-get-started.md#exceptions-and-request-failures).
* Ondersteunen Uitzonde ringen worden automatisch afgevangen. Als u automatische verzameling wilt uitschakelen, voegt u een regel toe aan het code fragment dat u op uw webpagina's invoegt:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Uitzonde ringen in analyse

In [Application Insights Analytics](analytics.md)worden uitzonde ringen in de `exceptions` tabel weer gegeven.

Als er [steek proeven](../../azure-monitor/app/sampling.md) worden uitgevoerd, `itemCount` wordt in de eigenschap een waarde weer gegeven die groter is dan 1. Bijvoorbeeld itemCount = = 10 betekent dat er bij 10 aanroepen naar trackException (), het steekproef proces slechts een van deze toegezonden. Als u het aantal uitzonde ringen dat is gesegmenteerd per type uitzonde ring, wilt ophalen, gebruikt u de volgende code:

```kusto
exceptions
| summarize sum(itemCount) by type
```

De meeste belang rijke stack-informatie is al in afzonderlijke variabelen geëxtraheerd, maar u kunt de `details` structuur splitsen om meer te krijgen. Omdat deze structuur dynamisch is, moet u het resultaat afstemmen op het type dat u verwacht. Bijvoorbeeld:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Als u uitzonde ringen wilt koppelen aan hun gerelateerde aanvragen, gebruikt u een koppeling:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace

Gebruik TrackTrace om problemen te diagnosticeren door het verzenden van een ' breadcrumb trail ' naar Application Insights. U kunt segmenten met diagnostische gegevens verzenden en deze in [Diagnostische Zoek opdrachten](../../azure-monitor/app/diagnostic-search.md)controleren.

In .NET- [logboek adapters](../../azure-monitor/app/asp-net-trace-logs.md) gebruiken deze API om logboeken van derden naar de portal te verzenden.

In Java voor [standaard logboeken, zoals Log4J, gebruiken Logback](../../azure-monitor/app/java-trace-logs.md) Application Insights log4j of logback-toevoeg modules om logboeken van derden naar de portal te verzenden.

*C#*

```csharp
telemetry.TrackTrace(message, SeverityLevel.Warning, properties);
```

*Java*

```java
telemetry.trackTrace(message, SeverityLevel.Warning, properties);
```

*Node.js*

```javascript
telemetry.trackTrace({
    message: message,
    severity: applicationInsights.Contracts.SeverityLevel.Warning,
    properties: properties
});
```

*Client/browser-side java script*

```javascript
trackTrace(message: string, properties?: {[string]:string}, severityLevel?: AI.SeverityLevel)
```

Een diagnostische gebeurtenis vastleggen, zoals het invoeren of verlaten van een methode.

 Parameter | Description
---|---
`message` | Diagnostische gegevens. Kan veel langer zijn dan een naam.
`properties` | Kaart van teken reeks in teken reeks: Aanvullende gegevens die worden gebruikt voor het [filteren van uitzonde ringen](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/#properties) in de portal. De standaard waarde is leeg.
`severityLevel` | Ondersteunde waarden: [SeverityLevel.ts](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/JavaScript/JavaScriptSDK.Interfaces/Contracts/Generated/SeverityLevel.ts)

U kunt zoeken op bericht inhoud, maar (in tegens telling tot eigenschaps waarden) kan er niet op worden gefilterd.

De limiet voor de `message` grootte van is veel hoger dan de limiet voor eigenschappen.
Een voor deel van TrackTrace is dat u relatief lange gegevens in het bericht kunt plaatsen. U kunt bijvoorbeeld POST-gegevens coderen.  

Daarnaast kunt u een Ernst niveau aan uw bericht toevoegen. En, net als bij andere telemetrie, kunt u eigenschaps waarden toevoegen om u te helpen bij het filteren of zoeken naar verschillende sets traceringen. Bijvoorbeeld:

*C#*

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow database response",
                SeverityLevel.Warning,
                new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```java
Map<String, Integer> properties = new HashMap<>();
properties.put("Database", db.ID);
telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);
```

In de [Zoek opdracht](../../azure-monitor/app/diagnostic-search.md)kunt u vervolgens eenvoudig alle berichten met een bepaald Ernst niveau filteren die betrekking hebben op een bepaalde data base.

### <a name="traces-in-analytics"></a>Traces in Analytics

In [Application Insights Analytics](analytics.md)worden aanroepen naar TrackTrace weer gegeven in `traces` de tabel.

Als er [steek proeven](../../azure-monitor/app/sampling.md) worden uitgevoerd, wordt in de eigenschap itemCount een waarde weer gegeven die groter is dan 1. Bijvoorbeeld itemCount = = 10 betekent dat bij 10 aanroepen naar `trackTrace()`het steekproef proces slechts één daarvan wordt verzonden. Als u het juiste aantal tracerings aanroepen wilt krijgen, moet u dus code `traces | summarize sum(itemCount)`gebruiken, zoals.

## <a name="trackdependency"></a>TrackDependency

Gebruik de aanroep TrackDependency om de reactie tijden en succes percentages van aanroepen naar een externe code te volgen. De resultaten worden weer gegeven in de afhankelijkheids grafieken in de portal.

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
catch(Exception ex) 
{
    success = false;
    telemetry.TrackException(ex);
    throw new Exception("Operation went wrong", ex);
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("DependencyType", "myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

*Java*

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
}
finally {
    long endTime = System.currentTimeMillis();
    long delta = endTime - startTime;
    RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
    telemetry.setTimeStamp(startTime);
    telemetry.trackDependency(dependencyTelemetry);
}
```

*JavaScript*

```javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({
        dependencyTypeName: "myDependency",
        name: "myCall",
        duration: elapsed,
        success: success
    });
}
```

Houd er rekening mee dat de Sdk's van de server een [afhankelijkheids module](../../azure-monitor/app/asp-net-dependencies.md) bevatten waarmee bepaalde afhankelijkheids aanroepen automatisch worden gedetecteerd en bijgehouden, bijvoorbeeld in data bases en rest-api's. U moet een agent op de server installeren om de module te laten werken. 

In Java kunnen bepaalde afhankelijkheids aanroepen automatisch worden gevolgd met behulp van [Java-Agent](../../azure-monitor/app/java-agent.md).

U gebruikt deze aanroep als u aanroepen wilt bijhouden die niet worden onderschept door automatische tracking of als u de agent niet wilt installeren.

Als u de standaard module voor het bijhouden van afhankelijkheden in C#wilt uitschakelen, bewerkt u [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) en verwijdert u de verwijzing naar. `DependencyCollector.DependencyTrackingTelemetryModule` In Java moet u de Java-Agent niet installeren als u niet automatisch standaard afhankelijkheden wilt verzamelen.

### <a name="dependencies-in-analytics"></a>Afhankelijkheden in Analytics

In [Application Insights Analytics](analytics.md)worden trackDependency-aanroepen weer gegeven `dependencies` in de tabel.

Als er [steek proeven](../../azure-monitor/app/sampling.md) worden uitgevoerd, wordt in de eigenschap itemCount een waarde weer gegeven die groter is dan 1. Bijvoorbeeld itemCount = = 10 betekent dat er bij 10 aanroepen naar trackDependency (), het steekproef proces slechts een van deze toegezonden. Als u het juiste aantal afhankelijkheden wilt ophalen dat is gesegmenteerd door het doel onderdeel, gebruikt u de volgende code:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Als u afhankelijkheden met hun gerelateerde aanvragen wilt koppelen, gebruikt u een koppeling:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Gegevens leegmaken

Normaal gesp roken verzendt de SDK gegevens met vaste intervallen (meestal 30 seconden) of wanneer de buffer vol is (meestal 500 items). In sommige gevallen is het echter mogelijk dat u de buffer wilt leegmaken, bijvoorbeeld als u de SDK gebruikt in een toepassing die wordt afgesloten.

*C#*

 ```csharp
telemetry.Flush();
// Allow some time for flushing before shutdown.
System.Threading.Thread.Sleep(5000);
```

*Java*

```java
telemetry.flush();
//Allow some time for flushing before shutting down
Thread.sleep(5000);
```

*Node.js*

```javascript
telemetry.flush();
```

De functie is asynchroon voor het [Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/)-telemetrie-kanaal.

De methode idea, flush () moet worden gebruikt voor de afsluit activiteit van de toepassing.

## <a name="authenticated-users"></a>Geverifieerde gebruikers

In een web-app worden gebruikers (standaard) geïdentificeerd door cookies. Een gebruiker kan meer dan één keer worden geteld als ze toegang hebben tot uw app vanaf een andere computer of browser, of als ze cookies verwijderen.

Als gebruikers zich aanmelden bij uw app, kunt u een nauw keuriger aantal aanvragen door de geverifieerde gebruikers-ID in de browser code in te stellen:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

In een ASP.NET Web MVC-toepassing, bijvoorbeeld:

*Razor*

```cshtml
@if (Request.IsAuthenticated)
{
    <script>
        appInsights.setAuthenticatedUserContext("@User.Identity.Name
            .Replace("\\", "\\\\")"
            .replace(/[,;=| ]+/g, "_"));
    </script>
}
```

Het is niet nodig om de werkelijke aanmeldings naam van de gebruiker te gebruiken. Het moet alleen een ID zijn die uniek is voor die gebruiker. Het mag geen spaties of een van de tekens `,;=|`bevatten.

De gebruikers-ID wordt ook ingesteld in een sessie cookie en verzonden naar de server. Als de server SDK is geïnstalleerd, wordt de geverifieerde gebruikers-ID verzonden als onderdeel van de context eigenschappen van zowel de client als de server-telemetrie. U kunt deze vervolgens filteren en ernaar zoeken.

Als uw app gebruikers in accounts groepeert, kunt u ook een id voor het account door geven (met dezelfde teken beperkingen).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

In [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md)kunt u een grafiek maken waarin **gebruikers, geverifieerde**en **gebruikers accounts**worden geteld.

U kunt ook [zoeken](../../azure-monitor/app/diagnostic-search.md) naar client gegevens punten met specifieke gebruikers namen en accounts.

## <a name="properties"></a>Gegevens filteren, zoeken en segmenteren met behulp van eigenschappen

U kunt eigenschappen en metingen aan uw gebeurtenissen koppelen (en ook met metrische gegevens, pagina weergaven, uitzonde ringen en andere telemetriegegevens).

*Eigenschappen* zijn teken reeks waarden die u kunt gebruiken voor het filteren van uw telemetrie in de gebruiks rapporten. Als uw app bijvoorbeeld verschillende spellen biedt, kunt u de naam van het spel aan elke gebeurtenis koppelen, zodat u kunt zien welke spellen populairder zijn.

Er is een limiet van 8192 voor de lengte van de teken reeks. (Als u grote gegevens segmenten wilt verzenden, gebruikt u de para meter bericht van TrackTrace.)

*Metrische gegevens* zijn numerieke waarden die grafisch kunnen worden weer gegeven. U kunt bijvoorbeeld zien of er een geleidelijke toename van de scores die uw gamers hebben. De grafieken kunnen worden gesegmenteerd door de eigenschappen die met de gebeurtenis worden verzonden, zodat u afzonderlijke of gestapelde grafieken voor verschillende spellen kunt ophalen.

Als u wilt dat metrische waarden correct worden weer gegeven, moet deze groter zijn dan of gelijk zijn aan 0.

Er zijn enkele [limieten voor het aantal eigenschappen, eigenschaps waarden en metrische gegevens](#limits) die u kunt gebruiken.

*JavaScript*

```javascript
appInsights.trackEvent
    ("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );

appInsights.trackPageView
    ("page name", "http://fabrikam.com/pageurl.html",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );
```

*C#*

```csharp
// Set up some properties and metrics:
var properties = new Dictionary <string, string>
    {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
var metrics = new Dictionary <string, double>
    {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

// Send the event:
telemetry.TrackEvent("WinGame", properties, metrics);
```

*Node.js*

```javascript
// Set up some properties and metrics:
var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

// Send the event:
telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});
```

*Visual Basic*

```vb
' Set up some properties:
Dim properties = New Dictionary (Of String, String)
properties.Add("game", currentGame.Name)
properties.Add("difficulty", currentGame.Difficulty)

Dim metrics = New Dictionary (Of String, Double)
metrics.Add("Score", currentGame.Score)
metrics.Add("Opponents", currentGame.OpponentCount)

' Send the event:
telemetry.TrackEvent("WinGame", properties, metrics)
```

*Java*

```java
Map<String, String> properties = new HashMap<String, String>();
properties.put("game", currentGame.getName());
properties.put("difficulty", currentGame.getDifficulty());

Map<String, Double> metrics = new HashMap<String, Double>();
metrics.put("Score", currentGame.getScore());
metrics.put("Opponents", currentGame.getOpponentCount());

telemetry.trackEvent("WinGame", properties, metrics);
```

> [!NOTE]
> Let erop dat u geen persoons gegevens in de eigenschappen hoeft te registreren.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Alternatieve manier om eigenschappen en metrische gegevens in te stellen

Als het handiger is, kunt u de para meters van een gebeurtenis in een afzonderlijk object verzamelen:

```csharp
var event = new EventTelemetry();

event.Name = "WinGame";
event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
event.Properties["game"] = currentGame.Name;
event.Properties["difficulty"] = currentGame.Difficulty;
event.Metrics["Score"] = currentGame.Score;
event.Metrics["Opponents"] = currentGame.Opponents.Length;

telemetry.TrackEvent(event);
```

> [!WARNING]
> Gebruik niet dezelfde instantie van het telemetrische`event` item (in dit voor beeld) om meerdere keren een tracering * () aan te roepen. Dit kan ertoe leiden dat telemetrie wordt verzonden met een onjuiste configuratie.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Aangepaste metingen en eigenschappen in Analytics

In [analyses](analytics.md)worden aangepaste metrische gegevens en eigenschappen weer gegeven in `customMeasurements` de `customDimensions` kenmerken en van elke telemetrie-record.

Als u bijvoorbeeld een eigenschap met de naam ' game ' aan uw aanvraag-telemetrie hebt toegevoegd, telt deze query het aantal exemplaren van verschillende waarden van ' game ' en wordt het gemiddelde van de aangepaste metrische waarde ' Score ' weer gegeven:

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

U ziet dat:

* Wanneer u een waarde ophaalt uit de JSON customDimensions of customMeasurements, heeft deze een dynamisch type en daarom moet u `tostring` deze `todouble`casten of.
* Gebruik`sum(itemCount)` ,`count()`om rekening te houden met de mogelijkheid van [steek proeven](../../azure-monitor/app/sampling.md).

## <a name="timed"></a>Timing gebeurtenissen

Soms wilt u een grafiek laten zien hoe lang het duurt om een actie uit te voeren. Het is bijvoorbeeld mogelijk dat u wilt weten hoe lang gebruikers gebruikmaken van keuzes in een spel. U kunt hiervoor de para meter meet gebruiken.

*C#*

```csharp
var stopwatch = System.Diagnostics.Stopwatch.StartNew();

// ... perform the timed action ...

stopwatch.Stop();

var metrics = new Dictionary <string, double>
    {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

// Set up some properties:
var properties = new Dictionary <string, string>
    {{"signalSource", currentSignalSource.Name}};

// Send the event:
telemetry.TrackEvent("SignalProcessed", properties, metrics);
```

*Java*

```java
long startTime = System.currentTimeMillis();

// Perform timed action

long endTime = System.currentTimeMillis();
Map<String, Double> metrics = new HashMap<>();
metrics.put("ProcessingTime", (double)endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="defaults"></a>Standaard eigenschappen voor aangepaste telemetrie

Als u standaard waarden voor eigenschappen wilt instellen voor een aantal aangepaste gebeurtenissen die u schrijft, kunt u deze instellen in een TelemetryClient-exemplaar. Ze zijn gekoppeld aan elk telemetrie-item dat vanaf die client wordt verzonden.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.Properties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.Properties("Game") = currentGame.Name
' Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame")
```

*Java*

```java
import com.microsoft.applicationinsights.TelemetryClient;
import com.microsoft.applicationinsights.TelemetryContext;
...


TelemetryClient gameTelemetry = new TelemetryClient();
TelemetryContext context = gameTelemetry.getContext();
context.getProperties().put("Game", currentGame.Name);

gameTelemetry.TrackEvent("WinGame");
```

*Node.js*

```javascript
var gameTelemetry = new applicationInsights.TelemetryClient();
gameTelemetry.commonProperties["Game"] = currentGame.Name;

gameTelemetry.TrackEvent({name: "WinGame"});
```

Afzonderlijke telemetrie-aanroepen kunnen de standaard waarden in de bijbehorende eigenschappen woordenlijsten onderdrukken.

Gebruik *voor Java script*-webclients java script-telemetrie-initialisatie functies.

*Om eigenschappen toe te voegen aan alle*telemetrie, met inbegrip van de gegevens uit de standaard verzamelings modules, [ `ITelemetryInitializer`implementeert ](../../azure-monitor/app/api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Telemetrie bemonsteren, filteren en verwerken

U kunt code schrijven voor het verwerken van uw telemetrie voordat deze wordt verzonden vanuit de SDK. De verwerking omvat gegevens die worden verzonden vanuit de standaard-telemetrie-modules, zoals verzameling van HTTP-aanvragen en afhankelijkheids verzamelingen.

[Eigenschappen](../../azure-monitor/app/api-filtering-sampling.md#add-properties) aan telemetrie toevoegen door `ITelemetryInitializer`de implementatie uit te voeren. U kunt bijvoorbeeld versie nummers of waarden toevoegen die worden berekend op basis van andere eigenschappen.

Door te `ITelemetryProcessor` [implementeren kunt u](../../azure-monitor/app/api-filtering-sampling.md#filtering) telemetrie wijzigen of verwijderen voordat deze vanuit de SDK wordt verzonden. U bepaalt wat er wordt verzonden of verwijderd, maar u moet rekening houden met het effect van uw metrische gegevens. Afhankelijk van hoe u items verwijdert, kan de mogelijkheid om te navigeren tussen verwante items verloren gaan.

[Steek proeven](../../azure-monitor/app/api-filtering-sampling.md) zijn een verpakte oplossing om het volume aan gegevens te beperken dat vanuit uw app naar de portal wordt verzonden. Dit gebeurt zonder dat dit van invloed is op de weer gegeven metrische gegevens. En dit heeft geen invloed op de mogelijkheid om problemen op te lossen door te navigeren tussen verwante items, zoals uitzonde ringen, aanvragen en pagina weergaven.

[Meer informatie](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Telemetrie uitschakelen

Om het verzamelen en verzenden van telemetrie *dynamisch te stoppen en te starten* :

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

*Geselecteerde standaard verzamelaars uitschakelen*: bijvoorbeeld prestatie meter items, HTTP-aanvragen of afhankelijkheden--verwijderen of opmerkingen maken uit de relevante regels in [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). U kunt dit bijvoorbeeld doen als u uw eigen TrackRequest-gegevens wilt verzenden.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

Als u *geselecteerde standaard verzamelaars wilt uitschakelen*, zoals prestatie meter items, HTTP-aanvragen of afhankelijkheden, tijdens de initialisatie tijd, koppelt u configuratie methoden aan uw SDK-initialisatie code:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Als u deze verzamelaars na de initialisatie wilt uitschakelen, gebruikt u het configuratie object:`applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="debug"></a>Ontwikkelaars modus

Tijdens de fout opsporing is het handig om uw telemetrie via de pijp lijn te versnellen, zodat u de resultaten direct kunt zien. U krijgt ook extra berichten die u helpen bij het traceren van problemen met de telemetrie. Schakel deze optie uit in productie, omdat hierdoor uw app kan vertragen.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

Voor node. js kunt u de ontwikkelaars modus inschakelen door interne logboek registratie in `setInternalLogging` te scha `maxBatchSize` kelen via en in te stellen op 0, waardoor de telemetrie wordt verzonden zodra deze wordt verzameld.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="ikey"></a>De instrumentatie sleutel voor geselecteerde aangepaste telemetrie instellen

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-ikey"></a>Dynamische instrumentatie sleutel

Om te voor komen dat telemetrie wordt gemengd vanuit ontwikkelings-, test-en productie omgevingen, kunt u [afzonderlijke Application Insights resources maken](../../azure-monitor/app/create-new-resource.md ) en de sleutels wijzigen, afhankelijk van de omgeving.

In plaats van de instrumentatie sleutel op te halen uit het configuratie bestand, kunt u deze in uw code instellen. Stel de sleutel in een initialisatie methode in, zoals global.aspx.cs in een ASP.NET-service:

*C#*

```csharp
protected void Application_Start()
{
    Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey =
        // - for example -
        WebConfigurationManager.Settings["ikey"];
    ...
}
```

*JavaScript*

```javascript
appInsights.config.instrumentationKey = myKey;
```

In webpagina's is het mogelijk om deze in te stellen op basis van de status van de webserver, in plaats van deze letterlijk te coderen in het script. Bijvoorbeeld in een webpagina die is gegenereerd in een ASP.NET-app:

*Java script in haar*

```cshtml
<script type="text/javascript">
// Standard Application Insights webpage script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
    // Generate from server property:
    @Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey;
}) // ...
```

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="telemetrycontext"></a>TelemetryContext

TelemetryClient heeft een context eigenschap, die waarden bevat die samen met alle telemetriegegevens worden verzonden. Ze worden normaal gesp roken ingesteld door de standaard-telemetrie-modules, maar u kunt ze ook zelf instellen. Bijvoorbeeld:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Als u een van deze waarden zelf instelt, kunt u overwegen om de relevante regel uit [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)te verwijderen, zodat uw waarden en de standaard waarden niet verwarrend zijn.

* **Onderdeel**: De app en de versie.
* **Apparaat**: Gegevens over het apparaat waarop de app wordt uitgevoerd. (In web apps is dit de server of het client apparaat waarvan de telemetrie is verzonden.)
* **InstrumentationKey**: De Application Insights resource in azure waarin de telemetrie wordt weer gegeven. Het wordt meestal opgehaald uit ApplicationInsights. config.
* **Locatie**: De geografische locatie van het apparaat.
* **Bewerking**: In web apps, de huidige HTTP-aanvraag. In andere app-typen kunt u dit zo instellen dat gebeurtenissen samen worden gegroepeerd.
  * **Id**: Een gegenereerde waarde die verschillende gebeurtenissen vergelijkt, zodat u gerelateerde items kunt vinden wanneer u een gebeurtenis in diagnostische Zoek opdrachten inspecteert.
  * **Naam**: Een id, meestal de URL van de HTTP-aanvraag.
  * **SyntheticSource**: Als deze niet null of leeg is, een teken reeks die aangeeft dat de bron van de aanvraag is geïdentificeerd als een robot of Webtest. De standaard instelling is uitgesloten van berekeningen in Metrics Explorer.
* **Eigenschappen**: Eigenschappen die worden verzonden met alle telemetrie-gegevens. Deze kan worden overschreven in afzonderlijke trajecten *-aanroepen.
* **Sessie**: De sessie van de gebruiker. De ID wordt ingesteld op een gegenereerde waarde, die wordt gewijzigd wanneer de gebruiker enige tijd niet actief is geweest.
* **Gebruiker**: Gebruikers informatie.

## <a name="limits"></a>Limieten

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Gebruik [steek proeven](../../azure-monitor/app/sampling.md)om te voor komen dat de gegevens frequentie limiet wordt bereikt.

Zie [gegevens retentie en privacy](../../azure-monitor/app/data-retention-privacy.md)om te bepalen hoe lang de gegevens worden bewaard.

## <a name="reference-docs"></a>Referentiedocumenten

* [ASP.NET-verwijzing](https://msdn.microsoft.com/library/dn817570.aspx)
* [Naslaginformatie over Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [Java script-referentie](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android-SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS-SDK](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>SDK-code

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Windows Server-pakketten](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [Alle platforms](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Vragen

* *Welke uitzonde ringen kunnen Track_ ()-aanroepen genereren?*

    Geen. U hoeft ze niet op te slaan in de component try-catch. Als de SDK problemen ondervindt, worden berichten in de uitvoer van de console voor fout opsporing en--als de berichten in de diagnostische zoek opdracht komen, geregistreerd.
* *Is er een REST API voor het ophalen van gegevens uit de portal?*

    Ja, de [API voor gegevens toegang](https://dev.applicationinsights.io/). Andere manieren om gegevens te extra heren, zijn onder andere [exporteren vanuit Analytics naar Power bi](../../azure-monitor/app/export-power-bi.md ) en doorlopend [exporteren](../../azure-monitor/app/export-telemetry.md).

## <a name="next"></a>Volgende stappen

* [Zoeken naar gebeurtenissen en Logboeken](../../azure-monitor/app/diagnostic-search.md)
* [Problemen oplossen](../../azure-monitor/app/troubleshoot-faq.md)
