---
title: Application Insights API voor aangepaste gebeurtenissen en statistieken | Microsoft Documenten
description: Voeg een paar regels code in uw apparaat of bureaublad-app, webpagina of service in om het gebruik bij te houden en problemen te diagnosticeren.
ms.topic: conceptual
ms.date: 03/27/2019
ms.openlocfilehash: 4275d3ea3a340f0a4083ab929eb7f7872f3311e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295038"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>Application Insights-API voor aangepaste gebeurtenissen en metrische gegevens

Voeg een paar regels code in uw toepassing in om erachter te komen wat gebruikers ermee doen of om problemen te diagnosticeren. U telemetrie verzenden vanaf apparaat- en desktop-apps, webclients en webservers. Gebruik de Azure Application Insights-standaardtelemetrie-API om aangepaste gebeurtenissen en statistieken en uw eigen versies van standaardtelemetrie te verzenden. [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) Deze API is dezelfde API die de standaard Application Insights-gegevensverzamelaars gebruiken.

## <a name="api-summary"></a>API-overzicht

De core API is uniform voor alle platformen, met uitzondering van een paar variaties zoals `GetMetric`(.NET alleen).

| Methode | Gebruikt voor |
| --- | --- |
| [`TrackPageView`](#page-views) |Pagina's, schermen, messen of formulieren. |
| [`TrackEvent`](#trackevent) |Acties van gebruikers en andere gebeurtenissen. Wordt gebruikt om het gedrag van gebruikers bij te houden of om de prestaties te controleren. |
| [`GetMetric`](#getmetric) |Nul- en multidimensionale metrische gegevens, centraal geconfigureerde aggregatie, alleen C# |
| [`TrackMetric`](#trackmetric) |Prestatiemetingen zoals wachtrijlengtes die geen verband houden met specifieke gebeurtenissen. |
| [`TrackException`](#trackexception) |Uitzonderingen voor diagnose registreren. Traceer waar ze voorkomen in relatie tot andere gebeurtenissen en onderzoek stapelsporen. |
| [`TrackRequest`](#trackrequest) |Het registreren van de frequentie en duur van serveraanvragen voor prestatieanalyse. |
| [`TrackTrace`](#tracktrace) |Bron diagnostische logboekberichten. U ook logboeken van derden vastleggen. |
| [`TrackDependency`](#trackdependency) |Het registreren van de duur en frequentie van oproepen naar externe componenten waarvan uw app afhankelijk is. |

U [eigenschappen en statistieken koppelen](#properties) aan de meeste van deze telemetriegesprekken.

## <a name="before-you-start"></a><a name="prep"></a>Voordat u begint

Als u nog geen verwijzing hebt naar Application Insights SDK:

* Voeg de Application Insights SDK toe aan uw project:

  * [ASP.NET project](../../azure-monitor/app/asp-net.md)
  * [ASP.NET Core-project](../../azure-monitor/app/asp-net-core.md)
  * [Java-project](../../azure-monitor/app/java-get-started.md)
  * [Node.js project](../../azure-monitor/app/nodejs.md)
  * [JavaScript op elke webpagina](../../azure-monitor/app/javascript.md) 
* Neem in uw apparaat of webservercode de meeste op:

    *C#:*`using Microsoft.ApplicationInsights;`

    *Visual Basic:*`Imports Microsoft.ApplicationInsights`

    *Java:*`import com.microsoft.applicationinsights.TelemetryClient;`

    *Knooppunt.js:*`var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Een instantie TelemetrieClient

Ontvang een `TelemetryClient` exemplaar van (behalve in JavaScript op webpagina's):

Voor [ASP.NET Core-apps](asp-net-core.md#how-can-i-track-telemetry-thats-not-automatically-collected) en [Niet HTTP/Worker voor .NET/.NET Core-apps](worker-service.md#how-can-i-track-telemetry-thats-not-automatically-collected) wordt aanbevolen om een exemplaar van de injectiecontainer voor `TelemetryClient` afhankelijkheid te halen, zoals uitgelegd in hun respectievelijke documentatie.

Als u AzureFunctions v2+ of Azure WebJobs v3+ gebruikt, volgt u dit document:https://docs.microsoft.com/azure/azure-functions/functions-monitoring#version-2x-3

*C #*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```
Voor iedereen die deze methode ziet is verouderd berichten u terecht [op microsoft / ApplicationInsights-dotnet # 1152](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1152) voor meer informatie.

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

TelemetryClient is draadveilig.

Voor ASP.NET- en Java-projecten worden binnenkomende HTTP-aanvragen automatisch vastgelegd. U extra exemplaren van TelemetryClient maken voor andere modules van uw app. U bijvoorbeeld één telemetrieclient-exemplaar in uw middleware-klasse hebben om zakelijke logische gebeurtenissen te rapporteren. U eigenschappen zoals UserId en DeviceId instellen om de machine te identificeren. Deze informatie is gekoppeld aan alle gebeurtenissen die de instantie verzendt.

*C #*

```csharp
TelemetryClient.Context.User.Id = "...";
TelemetryClient.Context.Device.Id = "...";
```

*Java*

```java
telemetry.getContext().getUser().setId("...");
telemetry.getContext().getDevice().setId("...");
```

In Node.js-projecten kunt `new applicationInsights.TelemetryClient(instrumentationKey?)` u een nieuwe instantie maken, maar dit wordt alleen aanbevolen voor `defaultClient`scenario's waarvoor een geïsoleerde configuratie van het singleton vereist is.

## <a name="trackevent"></a>TrackEvent TrackEvent

In Application Insights is een *aangepaste gebeurtenis* een gegevenspunt dat u in [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) weergeven als een geaggregeerd aantal en in [Diagnostisch zoeken](../../azure-monitor/app/diagnostic-search.md) als afzonderlijke gebeurtenissen. (Het is niet gerelateerd aan MVC of andere framework "gebeurtenissen.")

Oproepen `TrackEvent` in uw code invoegen om verschillende gebeurtenissen te tellen. Hoe vaak gebruikers een bepaalde functie kiezen, hoe vaak ze bepaalde doelen bereiken, of misschien hoe vaak ze bepaalde soorten fouten maken.

Stuur bijvoorbeeld in een game-app een evenement wanneer een gebruiker het spel wint:

*Javascript*

```javascript
appInsights.trackEvent({name:"WinGame"});
```

*C #*

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

De telemetrie is `customEvents` beschikbaar in de tabel in [Application Insights Analytics.](analytics.md) Elke rij vertegenwoordigt `trackEvent(..)` een oproep naar in uw app.

Als [de steekproef](../../azure-monitor/app/sampling.md) in werking is, geeft de eigenschap itemCount een waarde weer die groter is dan 1. ItemCount==10 betekent bijvoorbeeld dat van de 10 aanroepen naar trackEvent(), het bemonsteringsproces slechts één van hen heeft verzonden. Als u een correct aantal aangepaste gebeurtenissen wilt `customEvents | summarize sum(itemCount)`krijgen, moet u daarom code gebruiken zoals .

## <a name="getmetric"></a>GetMetric (GetMetric)

### <a name="examples"></a>Voorbeelden

*C #*

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

## <a name="trackmetric"></a>TrackMetrische

> [!NOTE]
> Microsoft.ApplicationInsights.TelemetryClient.TrackMetric is niet de voorkeursmethode voor het verzenden van statistieken. Metrische gegevens moeten altijd vooraf worden samengevoegd gedurende een periode voordat ze worden verzonden. Gebruik een van de overbelastings van GetMetric (..) om een metrisch object te krijgen voor toegang tot SDK-preaggregatiemogelijkheden. Als u uw eigen pre-aggregatielogica implementeert, u de methode TrackMetric() gebruiken om de resulterende aggregaten te verzenden. Als uw toepassing vereist dat u bij elke gelegenheid een afzonderlijk telemetrieitem verzendt zonder aggregatie in de loop van de tijd, hebt u waarschijnlijk een use case voor gebeurtenistelemetrie; zie TelemetryClient.TrackEvent (Microsoft.ApplicationInsights.DataContracts.EventTelemetry).

Application Insights kan statistieken in kaart brengen die niet aan bepaalde gebeurtenissen zijn gekoppeld. U bijvoorbeeld regelmatig een wachtrijlengte controleren. Met statistieken zijn de afzonderlijke metingen van minder belang dan de variaties en trends, en dus zijn statistische grafieken nuttig.

Als u statistieken wilt verzenden naar Application `TrackMetric(..)` Insights, u de API gebruiken. Er zijn twee manieren om een statistiek te verzenden:

* Enkele waarde. Elke keer dat u een meting uitvoert in uw toepassing, stuurt u de bijbehorende waarde naar Application Insights. Stel dat u een statistiek hebt die het aantal items in een container beschrijft. Tijdens een bepaalde periode, je eerst drie items in de container en vervolgens verwijdert u twee items. Daarom zou je `TrackMetric` twee keer bellen: eerst de waarde `3` doorgeven en dan de waarde. `-2` Application Insights slaat beide waarden namens u op.

* Aggregatie. Bij het werken met statistieken is elke meting zelden van belang. In plaats daarvan is een samenvatting van wat er tijdens een bepaalde periode is gebeurd belangrijk. Een dergelijke samenvatting wordt _aggregatie_genoemd . In het bovenstaande voorbeeld is `1` de totale metrische som voor die `2`periode en is het aantal metrische waarden . Wanneer u de aggregatiebenadering gebruikt, roept u slechts één keer per periode een beroep `TrackMetric` op en verzendt u de geaggregeerde waarden. Dit is de aanbevolen aanpak, omdat het de kosten en prestatiekosten aanzienlijk kan verlagen door minder gegevenspunten naar Application Insights te verzenden, terwijl het nog steeds alle relevante informatie verzamelt.

### <a name="examples"></a>Voorbeelden

#### <a name="single-values"></a>Enkele waarden

Eén metrische waarde verzenden:

*Javascript*

 ```javascript
appInsights.trackMetric("queueLength", 42.0);
 ```

*C #*

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

### <a name="custom-metrics-in-analytics"></a>Aangepaste statistieken in Analytics

De telemetrie is `customMetrics` beschikbaar in de tabel in [Application Insights Analytics.](analytics.md) Elke rij vertegenwoordigt `trackMetric(..)` een oproep naar in uw app.

* `valueSum`- Dit is de som van de metingen. Om de gemiddelde waarde `valueCount`te krijgen, delen door .
* `valueCount`- Het aantal metingen dat in `trackMetric(..)` deze oproep is samengevoegd.

## <a name="page-views"></a>Paginaweergaven

In een apparaat- of webpagina-app wordt telemetrie voor paginaweergave standaard verzonden wanneer elk scherm of elke pagina wordt geladen. Maar u dat wijzigen om paginaweergaven op extra of verschillende tijdstippen bij te houden. In een app met tabbladen of bladen u bijvoorbeeld een pagina bijhouden wanneer de gebruiker een nieuw blad opent.

Gebruikers- en sessiegegevens worden verzonden als eigenschappen samen met paginaweergaven, zodat de gebruikers- en sessiediagrammen tot leven komen wanneer er telemetrie voor paginaweergave is.

### <a name="custom-page-views"></a>Aangepaste paginaweergaven

*Javascript*

```javascript
appInsights.trackPageView("tab1");
```

*C #*

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

Als u meerdere tabbladen binnen verschillende HTML-pagina's hebt, u de URL ook opgeven:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Weergaven van timingpagina's

Standaard worden de tijden die worden gerapporteerd als **laadtijd van de paginaweergave** gemeten vanaf het moment dat de browser het verzoek verzendt, totdat de gebeurtenis voor het laden van pagina's van de browser wordt aangeroepen.

In plaats daarvan u:

* Stel een expliciete duur in de `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);` [trackPageView-aanroep](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/API.md#trackpageview) in: .
* Gebruik de `startTrackPage` tijdsoproepen `stopTrackPage`voor de paginaweergave en .

*Javascript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

De naam die u als eerste parameter gebruikt, associeert de begin- en stop-oproepen. De huidige paginanaam wordt standaard weergegeven.

De resulterende paginabelastingsduur die wordt weergegeven in Metrics Explorer, is afgeleid van het interval tussen de begin- en stopoproepen. Het is aan jou welk interval je eigenlijk tijd.

### <a name="page-telemetry-in-analytics"></a>Paginatelemetrie in Analytics

In [Analytics](analytics.md) worden in twee tabellen gegevens uit browserbewerkingen weergegeven:

* De `pageViews` tabel bevat gegevens over de URL en de paginatitel
* De `browserTimings` tabel bevat gegevens over de prestaties van de client, zoals de tijd die nodig is om de binnenkomende gegevens te verwerken

Ga als volgt te werk om te zien hoe lang het duurt voordat de browser verschillende pagina's verwerkt:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

Om de populariteiten van verschillende browsers te ontdekken:

```kusto
pageViews
| summarize count() by client_Browser
```

Als u paginaweergaven wilt koppelen aan AJAX-oproepen, u lid worden van afhankelijkheden:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest TrackRequest TrackRequest TrackRequest

De server SDK gebruikt TrackRequest om HTTP-aanvragen te registreren.

U het ook zelf bellen als u aanvragen wilt simuleren in een context waarin u de webservicemodule niet hebt uitgevoerd.

De aanbevolen manier om aanvraagtelemetrie te verzenden is echter wanneer het verzoek fungeert als een <a href="#operation-context">bewerkingscontext.</a>

## <a name="operation-context"></a>Bedrijfscontext

U telemetrie-items samen correleren door ze te koppelen aan de bedrijfscontext. De standaard module voor het bijhouden van aanvragen doet dit voor uitzonderingen en andere gebeurtenissen die worden verzonden terwijl een HTTP-aanvraag wordt verwerkt. In [Zoeken](../../azure-monitor/app/diagnostic-search.md) en [Analytics](analytics.md)u eenvoudig alle gebeurtenissen vinden die aan het verzoek zijn gekoppeld met behulp van de bedrijfs-ID.

Zie [Telemetriecorrelatie in Application Insights](../../azure-monitor/app/correlation.md) voor meer informatie over correlatie.

Bij het handmatig bijhouden van telemetrie, de eenvoudigste manier om telemetrie correlatie te waarborgen met behulp van dit patroon:

*C #*

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

Samen met het instellen `StartOperation` van een bewerkingscontext maakt u een telemetrie-item van het type dat u opgeeft. Het stuurt het telemetrie-item wanneer u de `StopOperation`bewerking overneemt of als u expliciet belt. Als u `RequestTelemetry` het telemetrietype gebruikt, wordt de duur ingesteld op het tijdsinterval tussen start en stop.

Telemetrie-items die binnen een werkingsgebied worden gerapporteerd, worden 'kinderen' van een dergelijke bewerking. De contexten van de bewerking kunnen worden genest.

In Zoeken wordt de bewerkingscontext gebruikt om de lijst **Gerelateerde items** te maken:

![Gerelateerde objecten](./media/api-custom-events-metrics/21.png)

Zie [Aangepaste bewerkingen bijhouden met Application Insights .NET SDK](../../azure-monitor/app/custom-operations-tracking.md) voor meer informatie over het bijhouden van aangepaste bewerkingen.

### <a name="requests-in-analytics"></a>Aanvragen in Analytics

In [Application Insights Analytics](analytics.md)worden `requests` aanvragen weergegeven in de tabel.

Als [de steekproef](../../azure-monitor/app/sampling.md) in werking is, wordt in de eigenschap itemCount een waarde van meer dan 1 weergegeven. ItemCount==10 betekent bijvoorbeeld dat van de 10 oproepen naar trackRequest(), het bemonsteringsproces slechts één van hen heeft verzonden. Als u een correct aantal aanvragen en gemiddelde duur wilt krijgen die zijn gesegmenteerd op aanvraagnamen, gebruikt u code zoals:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>Spooruitzondering

Uitzonderingen verzenden naar toepassingsinzichten:

* Om ze te [tellen,](../../azure-monitor/app/metrics-explorer.md)als een indicatie van de frequentie van een probleem.
* Om individuele gebeurtenissen te [onderzoeken.](../../azure-monitor/app/diagnostic-search.md)

De rapporten bevatten de stapelsporen.

*C #*

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

*Javascript*

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

De SDK's vangen automatisch veel uitzonderingen, dus je hoeft TrackException niet altijd expliciet te bellen.

* ASP.NET: [Schrijf code om uitzonderingen te vangen.](../../azure-monitor/app/asp-net-exceptions.md)
* Java EE: [Uitzonderingen worden automatisch gevangen.](../../azure-monitor/app/java-get-started.md#exceptions-and-request-failures)
* JavaScript: Uitzonderingen worden automatisch vastgelegd. Als u de automatische verzameling wilt uitschakelen, voegt u een regel toe aan het codefragment dat u in voegt in uw webpagina's:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Uitzonderingen in Analytics

In [Application Insights Analytics](analytics.md)worden `exceptions` uitzonderingen weergegeven in de tabel.

Als [de bemonstering](../../azure-monitor/app/sampling.md) in `itemCount` werking is, geeft de eigenschap een waarde aan die groter is dan 1. ItemCount==10 betekent bijvoorbeeld dat van de 10 aanroepen om Exception te volgen(), het bemonsteringsproces slechts één van hen heeft verzonden. Als u een correct aantal uitzonderingen wilt krijgen die zijn gesegmenteerd op basis van het type uitzondering, gebruikt u code zoals:

```kusto
exceptions
| summarize sum(itemCount) by type
```

De meeste van de belangrijke stack informatie is al geëxtraheerd in afzonderlijke variabelen, maar u trekken uit elkaar de `details` structuur om meer te krijgen. Aangezien deze structuur dynamisch is, moet u het resultaat naar het type werpen dat u verwacht. Bijvoorbeeld:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Als u uitzonderingen wilt koppelen aan hun gerelateerde aanvragen, gebruikt u een join:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace TrackTrace

Gebruik TrackTrace om problemen te diagnosticeren door een "breadcrumb trail" naar Application Insights te sturen. U stukjes diagnostische gegevens verzenden en deze inspecteren in [Diagnostisch zoeken.](../../azure-monitor/app/diagnostic-search.md)

In .NET [Log-adapters](../../azure-monitor/app/asp-net-trace-logs.md) gebruiken u deze API om logboeken van derden naar de portal te verzenden.

In Java voor [standaardloggers zoals Log4J, Logback](../../azure-monitor/app/java-trace-logs.md) gebruik Application Insights Log4j of Logback Appenders om logs van derden naar de portal te sturen.

*C #*

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

*JavaScript aan client-/browserzijde*

```javascript
trackTrace(message: string, properties?: {[string]:string}, severityLevel?: SeverityLevel)
```

Log een diagnostische gebeurtenis in, zoals het invoeren of verlaten van een methode.

 Parameter | Beschrijving
---|---
`message` | Diagnostische gegevens. Kan veel langer zijn dan een naam.
`properties` | Tekenreekskaart naar tekenreeks: aanvullende gegevens die worden gebruikt om uitzonderingen in de portal te [filteren.](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/#properties) Standaard leeg.
`severityLevel` | Ondersteunde waarden: [SeverityLevel.ts](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/shared/AppInsightsCommon/src/Interfaces/Contracts/Generated/SeverityLevel.ts)

U zoeken op berichtinhoud, maar (in tegenstelling tot eigenschapswaarden) u er niet op filteren.

De groottelimiet `message` voor is veel hoger dan de limiet voor eigenschappen.
Een voordeel van TrackTrace is dat je relatief lange gegevens in het bericht plaatsen. U daar bijvoorbeeld POST-gegevens coderen.  

Daarnaast u een ernstniveau toevoegen aan uw bericht. En, net als andere telemetrie, u eigenschapswaarden toevoegen om u te helpen filteren of zoeken naar verschillende sets van sporen. Bijvoorbeeld:

*C #*

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

In [Zoeken](../../azure-monitor/app/diagnostic-search.md)u vervolgens eenvoudig alle berichten van een bepaald ernstniveau filteren die betrekking hebben op een bepaalde database.

### <a name="traces-in-analytics"></a>Traces in Analytics

In [Application Insights Analytics](analytics.md)worden oproepen naar `traces` TrackTrace weergegeven in de tabel.

Als [de steekproef](../../azure-monitor/app/sampling.md) in werking is, geeft de eigenschap itemCount een waarde weer die groter is dan 1. Bijvoorbeeld itemCount==10 betekent dat van `trackTrace()`10 aanroepen naar , het bemonsteringsproces slechts één van hen heeft verzonden. Om een correct aantal trace calls te krijgen, `traces | summarize sum(itemCount)`moet u daarom code gebruiken zoals .

## <a name="trackdependency"></a>Spoorafhankelijkheid

Gebruik de oproep TrackDependency om de responstijden en slagingspercentages van oproepen naar een extern stukje code bij te houden. De resultaten worden weergegeven in de afhankelijkheidsdiagrammen in de portal. Het onderstaande codefragment moet worden toegevoegd waar een afhankelijkheidsoproep wordt gedaan.

*C #*

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
Instant startTime = Instant.now();
try {
    success = dependency.call();
}
finally {
    Instant endTime = Instant.now();
    Duration delta = Duration.between(startTime, endTime);
    RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
    RemoteDependencyTelemetry.setTimeStamp(startTime);
    RemoteDependencyTelemetry.trackDependency(dependencyTelemetry);
}
```

*Node.js*

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

Houd er rekening mee dat de server-SDK's een [afhankelijkheidsmodule](../../azure-monitor/app/asp-net-dependencies.md) bevatten die bepaalde afhankelijkheidsoproepen automatisch detecteert en bijhoudt, bijvoorbeeld naar databases en REST-API's. U moet een agent op uw server installeren om de module te laten werken. 

In Java kunnen bepaalde afhankelijkheidsoproepen automatisch worden bijgehouden met [Java Agent.](../../azure-monitor/app/java-agent.md)

U gebruikt dit gesprek als u oproepen wilt bijhouden die de geautomatiseerde tracking niet opvangt, of als u de agent niet wilt installeren.

Als u de standaardmodule voor het bijhouden van afhankelijkheid in C#wilt `DependencyCollector.DependencyTrackingTelemetryModule`uitschakelen, bewerkt u [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) en verwijdert u de verwijzing naar . In Java, installeer geen java-agent als je niet wilt dat standaard afhankelijkheden automatisch te verzamelen.

### <a name="dependencies-in-analytics"></a>Afhankelijkheden in Analytics

In [Application Insights Analytics](analytics.md)worden trackDependency-oproepen weergegeven in de `dependencies` tabel.

Als [de steekproef](../../azure-monitor/app/sampling.md) in werking is, geeft de eigenschap itemCount een waarde weer die groter is dan 1. ItemCount==10 betekent bijvoorbeeld dat van de 10 aanroepen naar trackDependency(), het bemonsteringsproces slechts één van hen heeft verzonden. Als u een correct aantal afhankelijkheden wilt krijgen die zijn gesegmenteerd op doelcomponent, gebruikt u code zoals:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Als u afhankelijkheden wilt koppelen aan hun gerelateerde aanvragen, gebruikt u een join:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Gegevens doorspoelen

Normaal gesproken verzendt de SDK gegevens met vaste intervallen (meestal 30 seconden) of wanneer de buffer vol is (meestal 500 items). In sommige gevallen wilt u de buffer echter doorspoelen, bijvoorbeeld als u de SDK gebruikt in een toepassing die wordt afgesloten.

*C #*

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

De functie is asynchroon voor het [telemetriekanaal van](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/)de server.

Idealiter moet flush() methode worden gebruikt in de afsluitactiviteit van de toepassing.

## <a name="authenticated-users"></a>Geverifieerde gebruikers

In een web-app worden gebruikers (standaard) geïdentificeerd door cookies. Een gebruiker kan meer dan één keer worden geteld als hij of zij uw app opent vanuit een andere machine of browser of als hij cookies verwijdert.

Als gebruikers zich bij uw app aanmelden, u een nauwkeuriger aantal krijgen door de geverifieerde gebruikersnaam in de browsercode in te stellen:

*Javascript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

In een ASP.NET web MVC applicatie, bijvoorbeeld:

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

Het is niet nodig om de werkelijke aanmeldingsnaam van de gebruiker te gebruiken. Het hoeft alleen maar een ID te zijn die uniek is voor die gebruiker. Het mag geen spaties of `,;=|`een van de tekens bevatten.

De gebruikersnaam wordt ook ingesteld in een sessiecookie en naar de server verzonden. Als de serverSDK is geïnstalleerd, wordt de geverifieerde gebruikersnaam verzonden als onderdeel van de contexteigenschappen van zowel client- als servertelemetrie. U er vervolgens op filteren en zoeken.

Als uw app gebruikers groepeert in accounts, u ook een id voor het account doorgeven (met dezelfde tekenbeperkingen).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

In [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md)u een grafiek maken die **gebruikers,geverifieerde**en **gebruikersaccounts**telt.

U ook [zoeken naar](../../azure-monitor/app/diagnostic-search.md) clientgegevenspunten met specifieke gebruikersnamen en accounts.

## <a name="filtering-searching-and-segmenting-your-data-by-using-properties"></a><a name="properties"></a>Uw gegevens filteren, zoeken en segmenteren met behulp van eigenschappen

U eigenschappen en metingen aan uw gebeurtenissen koppelen (en ook aan statistieken, paginaweergaven, uitzonderingen en andere telemetriegegevens).

*Eigenschappen* zijn tekenreekswaarden die u gebruiken om uw telemetrie te filteren in de gebruiksrapporten. Als uw app bijvoorbeeld meerdere games bevat, u de naam van het spel aan elk evenement koppelen, zodat u zien welke games populairder zijn.

Er is een limiet van 8192 op de snaarlengte. (Als u grote stukken gegevens wilt verzenden, gebruikt u de berichtparameter van TrackTrace.)

*Statistieken* zijn numerieke waarden die grafisch kunnen worden weergegeven. U bijvoorbeeld zien of de scores die uw gamers behalen geleidelijk toenemen. De grafieken kunnen worden gesegmenteerd door de eigenschappen die worden verzonden met de gebeurtenis, zodat u afzonderlijke of gestapelde grafieken voor verschillende spellen krijgen.

Als u de waarden correct wilt weergeven, moeten deze groter zijn dan of gelijk zijn aan 0.

Er zijn enkele [beperkingen voor het aantal eigenschappen, eigenschapswaarden en statistieken](#limits) dat u gebruiken.

*Javascript*

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

*C #*

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
> Zorg ervoor dat u geen persoonlijk identificeerbare informatie inlogt in eigenschappen.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Alternatieve manier om eigenschappen en statistieken in te stellen

Als het handiger is, u de parameters van een gebeurtenis in een afzonderlijk object verzamelen:

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
> Gebruik niet dezelfde telemetrieiteminstantie`event` (in dit voorbeeld) om track*() meerdere keren te bellen. Dit kan ertoe leiden dat telemetrie wordt verzonden met een onjuiste configuratie.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Aangepaste metingen en eigenschappen in Analytics

In [Analytics](analytics.md)worden aangepaste statistieken `customMeasurements` en `customDimensions` eigenschappen weergegeven in de kenmerken en kenmerken van elke telemetrierecord.

Als u bijvoorbeeld een eigenschap met de naam 'spel' hebt toegevoegd aan uw aanvraagtelemetrie, telt deze query de gebeurtenissen van verschillende waarden van 'spel' en wordt het gemiddelde van de aangepaste metrische 'score' weergegeven:

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Merk op dat:

* Wanneer u een waarde uit de aangepaste Afmetingen of customMeasurements JSON haalt, `tostring` heeft `todouble`deze een dynamisch type en moet u deze dus casten of .
* Om rekening te houden met de `sum(itemCount)`mogelijkheid `count()`van [bemonstering,](../../azure-monitor/app/sampling.md)moet u gebruiken , niet .

## <a name="timing-events"></a><a name="timed"></a>Timing gebeurtenissen

Soms wilt u in kaart brengen hoe lang het duurt om een actie uit te voeren. U wilt bijvoorbeeld weten hoe lang gebruikers er nodig hebben om keuzes in een game te overwegen. Hiervoor u de meetparameter gebruiken.

*C #*

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

## <a name="default-properties-for-custom-telemetry"></a><a name="defaults"></a>Standaardeigenschappen voor aangepaste telemetrie

Als u standaardeigenschapwaarden wilt instellen voor sommige aangepaste gebeurtenissen die u schrijft, u deze instellen in een instantie TelemetryClient. Ze zijn gekoppeld aan elk telemetrie-item dat van die client wordt verzonden.

*C #*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.GlobalProperties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.GlobalProperties("Game") = currentGame.Name
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

Individuele telemetrieaangesprekken kunnen de standaardwaarden in hun eigenschapswoordenboeken overschrijven.

*Voor JavaScript-webclients*gebruikt u JavaScript-telemetrieinitialisators.

*Als u eigenschappen wilt toevoegen aan alle telemetrie,* inclusief de gegevens van standaardverzamelingsmodules, [implementeert u `ITelemetryInitializer` ](../../azure-monitor/app/api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Telemetrie samplen, filteren en verwerken

U code schrijven om uw telemetrie te verwerken voordat deze wordt verzonden vanuit de SDK. De verwerking omvat gegevens die worden verzonden vanuit de standaard telemetriemodules, zoals http-aanvraagverzameling en afhankelijkheidsverzameling.

[Eigenschappen toevoegen](../../azure-monitor/app/api-filtering-sampling.md#add-properties) aan telemetrie door het implementeren van `ITelemetryInitializer`. U bijvoorbeeld versienummers of waarden toevoegen die zijn berekend op basis van andere eigenschappen.

[Filteren](../../azure-monitor/app/api-filtering-sampling.md#filtering) kan telemetrie wijzigen of verwijderen voordat deze vanuit `ITelemetryProcessor`de SDK wordt verzonden door . U bepaalt wat er wordt verzonden of verwijderd, maar u moet rekening houden met het effect op uw statistieken. Afhankelijk van hoe u items verwijdert, u mogelijk niet meer navigeren tussen gerelateerde items.

[Sampling](../../azure-monitor/app/api-filtering-sampling.md) is een verpakte oplossing om het volume van de gegevens die vanuit uw app naar de portal worden verzonden, te verminderen. Dit gebeurt zonder dat dit gevolgen heeft voor de weergegeven statistieken. En dit gebeurt zonder dat dit gevolgen heeft voor uw vermogen om problemen te diagnosticeren door te navigeren tussen gerelateerde items zoals uitzonderingen, aanvragen en paginaweergaven.

[Meer informatie](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Telemetrie uitschakelen

Ga als het gaat om *dynamisch te stoppen en* te starten met het verzamelen en verzenden van telemetrie:

*C #*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

Als *u geselecteerde standaardverzamelingen wilt uitschakelen*- bijvoorbeeld prestatiemeteritems, HTTP-aanvragen of afhankelijkheden - de relevante regels in [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)verwijderen of erop reageren . U dit bijvoorbeeld doen als u uw eigen TrackRequest-gegevens wilt verzenden.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

Als *u geselecteerde standaardverzamelingen wilt uitschakelen*- bijvoorbeeld prestatiemeteritems, HTTP-aanvragen of afhankelijkheden - bij initialisatietijd, worden ketenconfiguratiemethoden voor uw SDK-initialisatiecode ingeschakeld:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Als u deze verzamelingen na de initialisatie wilt uitschakelen, gebruikt u het object Configuratie:`applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="developer-mode"></a><a name="debug"></a>Ontwikkelaarsmodus

Tijdens het foutopsporing is het handig om uw telemetrie via de pijplijn te laten versnellen, zodat u onmiddellijk resultaten zien. U krijgt ook aanvullende berichten die u helpen eventuele problemen met de telemetrie op te sporen. Schakel het uit in de productie, omdat het uw app kan vertragen.

*C #*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

Voor Node.js u de ontwikkelaarsmodus `setInternalLogging` inschakelen `maxBatchSize` door interne logboekregistratie via en instelling op 0 in te schakelen, waardoor uw telemetrie wordt verzonden zodra deze wordt verzameld.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="setting-the-instrumentation-key-for-selected-custom-telemetry"></a><a name="ikey"></a>De instrumentatiesleutel instellen voor geselecteerde aangepaste telemetrie

*C #*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a>Dynamische instrumentatiesleutel

Om te voorkomen dat telemetrie van ontwikkel-, test- en productieomgevingen wordt vermengd, u [afzonderlijke Application Insights-bronnen maken](../../azure-monitor/app/create-new-resource.md ) en de sleutels ervan wijzigen, afhankelijk van de omgeving.

In plaats van de instrumentatiesleutel uit het configuratiebestand te halen, u deze instellen in uw code. Stel de sleutel in een initialisatiemethode in, zoals global.aspx.cs in een ASP.NET-service:

*C #*

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

*Javascript*

```javascript
appInsights.config.instrumentationKey = myKey;
```

In webpagina's u deze instellen vanuit de status van de webserver, in plaats van het letterlijk in het script te coderen. Bijvoorbeeld op een webpagina die is gegenereerd in een ASP.NET-app:

*JavaScript in Razor*

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

## <a name="telemetrycontext"></a>TelemetrieContext

TelemetrieClient heeft een eigenschap Context, die waarden bevat die samen met alle telemetriegegevens worden verzonden. Ze worden normaal ingesteld door de standaard telemetriemodules, maar je ze ook zelf instellen. Bijvoorbeeld:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Als u een van deze waarden zelf instelt, u overwegen de relevante regel uit [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)te verwijderen, zodat uw waarden en de standaardwaarden niet in de war raken.

* **Component**: De app en de versie ervan.
* **Apparaat:** gegevens over het apparaat waarop de app wordt uitgevoerd. (In web-apps is dit de server of het clientapparaat waarvan de telemetrie wordt verzonden.)
* **InstrumentationKey:** de application insights-bron in Azure waar de telemetrie wordt weergegeven. Het wordt meestal opgepikt van ApplicationInsights.config.
* **Locatie**: De geografische locatie van het apparaat.
* **Bewerking:** In web-apps wordt de huidige HTTP-aanvraag uitgevoerd. In andere app-typen u dit instellen op het groeperen van gebeurtenissen.
  * **ID**: Een gegenereerde waarde die verschillende gebeurtenissen correleert, zodat u bij het inspecteren van een gebeurtenis in Diagnostisch zoeken gerelateerde items vinden.
  * **Naam:** Een id, meestal de URL van de HTTP-aanvraag.
  * **SyntheticSource**: Als niet null of leeg, een string die aangeeft dat de bron van de aanvraag is geïdentificeerd als een robot of webtest. Standaard is het uitgesloten van berekeningen in Metrics Explorer.
* **Eigenschappen:** eigenschappen die worden verzonden met alle telemetriegegevens. Het kan worden overschreven in individuele Track* gesprekken.
* **Sessie**: De sessie van de gebruiker. De ID is ingesteld op een gegenereerde waarde, die wordt gewijzigd wanneer de gebruiker een tijdje niet actief is geweest.
* **Gebruiker**: Gebruikersinformatie.

## <a name="limits"></a>Limieten

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Gebruik [sampling](../../azure-monitor/app/sampling.md)om te voorkomen dat u de limiet voor de gegevenssnelheid bereikt.

Zie [Gegevensbehoud en privacy](../../azure-monitor/app/data-retention-privacy.md)voor bepalen hoe lang gegevens worden bewaard.

## <a name="reference-docs"></a>Referentiedocumenten

* [ASP.NET referentie](https://docs.microsoft.com/dotnet/api/overview/azure/insights?view=azure-dotnet)
* [Java-referentie](https://docs.microsoft.com/java/api/overview/azure/appinsights?view=azure-java-stable/)
* [JavaScript-verwijzing](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)


## <a name="sdk-code"></a>SDK-code

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Windows Server-pakketten](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="questions"></a>Vragen

* *Welke uitzonderingen kunnen Track_() oproepen geven?*

    Geen. Je hoeft ze niet in try-catch clausules te verpakken. Als de SDK problemen ondervindt, worden berichten in de uitvoer van de foutopsporingsconsole en- als de berichten worden doorgegeven- in Diagnostisch zoeken.
* *Is er een REST API om gegevens uit de portal te halen?*

    Ja, de [API voor gegevenstoegang](https://dev.applicationinsights.io/). Andere manieren om gegevens te extraheren zijn [export van Analytics naar Power BI](../../azure-monitor/app/export-power-bi.md ) en continue [export.](../../azure-monitor/app/export-telemetry.md)

## <a name="next-steps"></a><a name="next"></a>Volgende stappen

* [Zoekgebeurtenissen en logboeken](../../azure-monitor/app/diagnostic-search.md)
* [Probleemoplossing](../../azure-monitor/app/troubleshoot-faq.md)
