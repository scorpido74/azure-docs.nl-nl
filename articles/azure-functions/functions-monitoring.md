---
title: Azure Functions controleren
description: Meer informatie over het gebruik van Azure-toepassing Insights met Azure Functions om de uitvoering van functies te bewaken.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 04/04/2019
ms.openlocfilehash: 4a182ddffd4c1ee4d2e71e7d9e6385df23e4260e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978080"
---
# <a name="monitor-azure-functions"></a>Azure Functions controleren

[Azure functions](functions-overview.md) biedt ingebouwde integratie met [Azure-toepassing inzichten](../azure-monitor/app/app-insights-overview.md) om functies te bewaken. In dit artikel wordt beschreven hoe u Azure Functions configureert om door het systeem gegenereerde logboek bestanden te verzenden naar Application Insights.

U kunt het beste Application Insights gebruiken omdat hiermee logboek-, prestatie-en fout gegevens worden verzameld. Het detecteert automatisch prestatie afwijkingen en bevat krachtige analyse hulpprogramma's waarmee u problemen kunt vaststellen en inzicht krijgt in de manier waarop uw functies worden gebruikt. Het is bedoeld om u te helpen de prestaties en bruikbaarheid continu te verbeteren. U kunt zelfs Application Insights gebruiken tijdens de ontwikkeling van een lokale functie-app-project. Zie [Wat is Application Insights?](../azure-monitor/app/app-insights-overview.md)voor meer informatie.

Omdat de vereiste Application Insights instrumentatie is ingebouwd in Azure Functions, hoeft u alleen een geldige instrumentatie sleutel te gebruiken om uw functie-app te koppelen aan een Application Insights resource.

## <a name="application-insights-pricing-and-limits"></a>Prijzen en limieten voor Application Insights

U kunt Application Insights integratie gratis uitproberen met functie-apps. Er is een dagelijkse limiet voor de hoeveelheid gegevens die gratis kan worden verwerkt. U kunt deze limiet tijdens het testen aanraken. Azure biedt Portal-en e-mail meldingen wanneer u uw dagelijkse limiet nadert. Als u deze waarschuwingen mist en de limiet bereikt, worden nieuwe logboeken niet weer gegeven in Application Insights query's. Houd rekening met de limiet om onnodige probleemoplossings tijd te voor komen. Zie [prijzen en gegevens volume beheren in Application Insights](../azure-monitor/app/pricing.md)voor meer informatie.

De volledige lijst met Application Insights functies die beschikbaar zijn voor uw functie-app, wordt beschreven in [Application Insights voor Azure functions ondersteunde functies](../azure-monitor/app/azure-functions-supported-features.md).

## <a name="enable-application-insights-integration"></a>Integratie van Application Insights inschakelen

Voor een functie-app voor het verzenden van gegevens naar Application Insights, moet de instrumentatie sleutel van een Application Insights resource bekend zijn. De sleutel moet een app-instelling met de naam **APPINSIGHTS_INSTRUMENTATIONKEY**hebben.

### <a name="new-function-app-in-the-portal"></a>Nieuwe functie-app in de portal

Wanneer u [de functie-app in de Azure Portal maakt](functions-create-first-azure-function.md), wordt Application Insights integratie standaard ingeschakeld. De Application Insights resource heeft dezelfde naam als de functie-app en wordt gemaakt in dezelfde regio of in de dichtstbijzijnde regio.

Als u wilt controleren welke Application Insights resource wordt gemaakt, selecteert u deze om het venster **Application Insights** uit te vouwen. U kunt de **nieuwe resource naam** wijzigen of een andere **locatie** in een [Azure-regio](https://azure.microsoft.com/global-infrastructure/geographies/) kiezen waar u uw gegevens wilt opslaan.

![Application Insights inschakelen tijdens het maken van een functie-app](media/functions-monitoring/enable-ai-new-function-app.png)

Wanneer u **maken**kiest, wordt er een Application Insights resource gemaakt met uw functie-app, die de `APPINSIGHTS_INSTRUMENTATIONKEY` in toepassings instellingen heeft ingesteld. Alles is klaar voor gebruik.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Toevoegen aan een bestaande functie-app 

Wanneer u een functie-app maakt met behulp van [Azure cli](functions-create-first-azure-function-azure-cli.md), [Visual Studio](functions-create-your-first-function-visual-studio.md)of [Visual Studio Code](functions-create-first-function-vs-code.md), moet u de Application Insights-resource maken. U kunt vervolgens de instrumentatie sleutel van die resource toevoegen als een toepassings instelling in uw functie-app.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Vroege versies van functies gebruiken ingebouwde bewaking. dit wordt niet meer aanbevolen. Wanneer u Application Insights integratie inschakelt voor een dergelijke functie-app, moet u ook [ingebouwde logboek registratie uitschakelen](#disable-built-in-logging).  

## <a name="view-telemetry-in-monitor-tab"></a>Telemetrie weer geven op het tabblad Monitor

Als [Application Insights integratie is ingeschakeld](#enable-application-insights-integration), kunt u telemetrie-gegevens weer geven op het tabblad **monitor** .

1. Selecteer op de pagina functie-app een functie die ten minste eenmaal is uitgevoerd nadat Application Insights is geconfigureerd. Selecteer vervolgens het tabblad **monitor** .

   ![Tabblad Monitor selecteren](media/functions-monitoring/monitor-tab.png)

1. Selecteer regel matig **vernieuwen** totdat de lijst met functie aanroepen wordt weer gegeven.

   Het kan tot vijf minuten duren voordat de lijst wordt weer gegeven terwijl de gegevens van de telemetrie-client worden gebatcheerd voor verzen ding naar de server. (De vertraging geldt niet voor de [Live Metrics stream](../azure-monitor/app/live-stream.md). Deze service maakt verbinding met de functions-host tijdens het laden van de pagina. logboeken worden dus rechtstreeks naar de pagina gestreamd.)

   ![Lijst met aanroepen](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Als u de logboeken voor een bepaalde functie aanroep wilt bekijken, selecteert u de koppeling **datum** kolom voor die aanroep.

   ![Koppeling aanroep Details](media/functions-monitoring/invocation-details-link-ai.png)

   De logboek registratie-uitvoer voor die aanroep wordt weer gegeven op een nieuwe pagina.

   ![Details van aanroep](media/functions-monitoring/invocation-details-ai.png)

U kunt zien dat op beide pagina's een **uitvoeren in Application Insights** koppeling naar de Application Insights Analytics-query die de gegevens ophaalt.

![Uitvoeren in Application Insights](media/functions-monitoring/run-in-ai.png)

De volgende query wordt weer gegeven. U kunt zien dat de aanroep lijst is beperkt tot de afgelopen 30 dagen. De lijst bevat niet meer dan 20 rijen (`where timestamp > ago(30d) | take 20`). De lijst aanroep Details is voor de afgelopen 30 dagen zonder limiet.

![Application Insights analyse aanroep lijst](media/functions-monitoring/ai-analytics-invocation-list.png)

Zie voor meer informatie [Query's uitvoeren op telemetriegegevens](#query-telemetry-data) verderop in dit artikel.

## <a name="view-telemetry-in-application-insights"></a>Telemetrie in Application Insights weer geven

Als u Application Insights vanuit een functie-app in de Azure Portal wilt openen, gaat u naar de **overzichts** pagina van de functie-app. Onder **geconfigureerde onderdelen**selecteert u **Application Insights**.

![Open Application Insights op de pagina overzicht van functie-app](media/functions-monitoring/ai-link.png)

Zie de [Application Insights-documentatie](https://docs.microsoft.com/azure/application-insights/)voor meer informatie over het gebruik van Application Insights. In deze sectie ziet u enkele voor beelden van het weer geven van gegevens in Application Insights. Als u al bekend bent met Application Insights, gaat u rechtstreeks naar [de secties over het configureren en aanpassen van de telemetriegegevens](#configure-categories-and-log-levels).

![Tabblad Overzicht van Application Insights](media/functions-monitoring/metrics-explorer.png)

De volgende gebieden van Application Insights kunnen nuttig zijn bij het evalueren van het gedrag, de prestaties en de fouten in uw functies:

| Tab-toets | Beschrijving |
| ---- | ----------- |
| **[Masters](../azure-monitor/app/asp-net-exceptions.md)** |  Grafieken en waarschuwingen maken op basis van functie fouten en server uitzonderingen. De **naam** van de bewerking is de naam van de functie. Storingen in afhankelijkheden worden niet weer gegeven, tenzij u aangepaste telemetrie implementeert voor afhankelijkheden. |
| **[Performance](../azure-monitor/app/performance-counters.md)** | Analyseer prestatie problemen. |
| **Servers** | Het resource gebruik en de door Voer per server weer geven. Deze gegevens kunnen nuttig zijn voor het opsporen van fouten in scenario's waarbij functies worden bogging van uw onderliggende resources. Servers worden aangeduid als instanties in de **Cloud**. |
| **[Metrische gegevens](../azure-monitor/app/metrics-explorer.md)** | Grafieken en waarschuwingen maken op basis van metrische gegevens. Metrische gegevens bevatten het aantal functie-aanroepen, uitvoerings tijd en succes percentages. |
| **[Live Metrics Stream](../azure-monitor/app/live-stream.md)** | Metrische gegevens weer geven terwijl deze in realtime worden gemaakt. |

## <a name="query-telemetry-data"></a>Telemetrie-gegevens opvragen

[Application Insights Analytics](../azure-monitor/app/analytics.md) geeft u toegang tot alle telemetriegegevens in de vorm van tabellen in een Data Base. Analytics biedt een query taal voor het uitpakken, bewerken en visualiseren van de gegevens.

![Analyse selecteren](media/functions-monitoring/select-analytics.png)

![Voor beeld van analyse](media/functions-monitoring/analytics-traces.png)

Hier volgt een voor beeld van een query waarin de distributie van aanvragen per werk nemer in de afgelopen 30 minuten wordt weer gegeven.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

De tabellen die beschikbaar zijn, worden weer gegeven op het tabblad **schema** aan de linkerkant. In de volgende tabellen vindt u gegevens die zijn gegenereerd door functie aanroepen:

| Tabel | Beschrijving |
| ----- | ----------- |
| **traceringen** | Logboeken die zijn gemaakt door de runtime en de functie code. |
| **aanvragen** | Eén aanvraag voor elke functie aanroep. |
| **uitzonderingen** | Eventuele uitzonde ringen die worden veroorzaakt door de runtime. |
| **customMetrics** | Het aantal geslaagde en mislukte aanroepen, succes percentage en duur. |
| **customEvents** | Gebeurtenissen die worden bijgehouden door de runtime, bijvoorbeeld: HTTP-aanvragen die een functie activeren. |
| **performanceCounters** | Informatie over de prestaties van de servers waarop de functies worden uitgevoerd. |

De andere tabellen zijn voor beschikbaarheids testen en voor client-en browser-telemetrie. U kunt aangepaste telemetrie implementeren om er gegevens aan toe te voegen.

In elke tabel bevindt zich een deel van de functions-specifieke gegevens in een `customDimensions` veld.  De volgende query haalt bijvoorbeeld alle traceringen op die logboek niveau `Error`hebben.

```
traces 
| where customDimensions.LogLevel == "Error"
```

De runtime voorziet in de velden `customDimensions.LogLevel` en `customDimensions.Category`. U kunt extra velden opgeven in de logboeken die u in uw functie code schrijft. Zie [gestructureerde logboek registratie](#structured-logging) verderop in dit artikel.

## <a name="configure-categories-and-log-levels"></a>Categorieën en logboek niveaus configureren

U kunt Application Insights gebruiken zonder aangepaste configuratie. De standaard configuratie kan leiden tot grote hoeveel heden gegevens. Als u een Visual Studio Azure-abonnement gebruikt, kunt u uw gegevens limiet voor Application Insights hebben bereikt. Verderop in dit artikel vindt u informatie over het configureren en aanpassen van de gegevens die uw functies naar Application Insights verzenden. Voor een functie-app wordt logboek registratie geconfigureerd in het bestand [host. json] .

### <a name="categories"></a>Categorieën

De Azure Functions-logboeken bevatten een *categorie* voor elk logboek. De categorie geeft aan welk deel van de runtime code of uw functie code het logboek heeft geschreven. 

Met de functie runtime worden logboeken gemaakt met een categorie die begint met ' host '. In versie 1. x, de `function started`, `function executed`en `function completed` logboeken hebben de categorie `Host.Executor`. Vanaf versie 2. x hebben deze logboeken de categorie `Function.<YOUR_FUNCTION_NAME>`.

Als u Logboeken in uw functie code schrijft, wordt de categorie `Function` in versie 1. x van de functions-runtime. In versie 2. x is de categorie `Function.<YOUR_FUNCTION_NAME>.User`.

### <a name="log-levels"></a>Logboekniveaus

De Azure Functions logger bevat ook een *logboek niveau* voor elk logboek. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) is een inventarisatie en de gehele code geeft het relatieve belang aan:

|Logniveau    |Coderen|
|------------|---|
|Tracering       | 0 |
|Foutopsporing       | 1 |
|Informatie | 2 |
|Waarschuwing     | 3 |
|Fout       | 4 |
|Kritiek    | 5 |
|Geen        | 6 |

Het `None` van het logboek niveau wordt uitgelegd in de volgende sectie. 

### <a name="log-configuration-in-hostjson"></a>Logboek configuratie in host. json

In het bestand [host. json] wordt geconfigureerd hoeveel logboek registratie van een functie-app naar Application Insights verzendt. Voor elke categorie geeft u het minimale logboek niveau op dat moet worden verzonden. Er zijn twee voor beelden: in het eerste voor beeld is [versie 2. x en hoger](functions-versions.md#version-2x) van de functions runtime (met .net core) en het tweede voor beeld voor de versie 1. x runtime.

### <a name="version-2x-and-higher"></a>Versie 2. x en hoger

Versie v2. x en latere versies van de functions runtime gebruiken de [.net core logging filter-hiërarchie](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>Versie 1. x

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

In dit voor beeld worden de volgende regels ingesteld:

* Voor logboeken met categorie-`Host.Results` of `Function`, alleen `Error` niveau en hoger verzenden naar Application Insights. Logboeken voor `Warning` niveau en hieronder worden genegeerd.
* Voor logboeken met categorie `Host.Aggregator`stuurt u alle logboeken naar Application Insights. Het `Trace` logboek niveau is hetzelfde als wat sommige logboeken aanroept `Verbose`, maar gebruik `Trace` in het bestand [host. json] .
* Voor alle andere logboeken moet u alleen `Information` niveau en hoger verzenden naar Application Insights.

De categorie waarde in [host. json] bepaalt de logboek registratie voor alle categorieën die met dezelfde waarde beginnen. `Host` in [host. json] bepaalt de logboek registratie voor `Host.General`, `Host.Executor`, `Host.Results`, enzovoort.

Als [host. json] meerdere categorieën bevat die beginnen met dezelfde teken reeks, worden de langere reeksen eerst vergeleken. Stel dat u wilt dat alles uit de runtime, behalve `Host.Aggregator` zich op `Error` niveau aanmeldt, maar u `Host.Aggregator` wilt aanmelden op het `Information` niveau:

### <a name="version-2x-and-later"></a>Versie 2. x en hoger

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>Versie 1. x 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Als u alle logboeken voor een categorie wilt onderdrukken, kunt u `None`op logboek niveau gebruiken. Er zijn geen logboeken met deze categorie geschreven en hiervoor is geen logboek niveau meer beschikbaar.

In de volgende secties worden de belangrijkste categorieën Logboeken beschreven die door de runtime worden gemaakt. 

### <a name="category-hostresults"></a>Categorie-host. resultaten

Deze logboeken worden weer gegeven als ' aanvragen ' in Application Insights. Hiermee wordt aangegeven of een functie is geslaagd of mislukt.

![Grafiek aanvragen](media/functions-monitoring/requests-chart.png)

Al deze logboeken worden op `Information` niveau geschreven. Als u filtert op `Warning` of hoger, worden deze gegevens niet weer gegeven.

### <a name="category-hostaggregator"></a>Host. aggregator van categorie

Deze logboeken bieden aantallen en gemiddelden van functie aanroepen gedurende een [Configureer bare](#configure-the-aggregator) periode. De standaard periode is 30 seconden of 1.000 resultaten, afhankelijk van wat het eerste komt. 

De logboeken zijn beschikbaar in de **customMetrics** -tabel in Application Insights. Voor beelden zijn het aantal uitvoeringen, het slagings percentage en de duur.

![customMetrics-query](media/functions-monitoring/custom-metrics-query.png)

Al deze logboeken worden op `Information` niveau geschreven. Als u filtert op `Warning` of hoger, worden deze gegevens niet weer gegeven.

### <a name="other-categories"></a>Andere categorieën

Alle logboeken voor andere categorieën dan de items die al worden weer gegeven, zijn beschikbaar in de **tracerings** tabel in Application Insights.

![Traces-query](media/functions-monitoring/analytics-traces.png)

Alle logboeken met categorieën die beginnen met `Host` worden geschreven door de functions-runtime. De logboeken functie gestart en functie voltooid bevatten categorie `Host.Executor`. Voor geslaagde uitvoeringen zijn deze logboeken `Information` niveau. Uitzonde ringen worden op `Error` niveau vastgelegd. De runtime maakt ook `Warning`-niveau logboeken, bijvoorbeeld: wachtrij berichten die worden verzonden naar de verontreinigde wachtrij.

De logboeken die door de functie code zijn geschreven, hebben categorie `Function` en kunnen elk logboek niveau zijn.

## <a name="configure-the-aggregator"></a>De aggregator configureren

Zoals u in de vorige sectie hebt genoteerd, verzamelt de runtime gegevens over functie-uitvoeringen gedurende een bepaalde periode. De standaard periode is 30 seconden of 1.000 wordt uitgevoerd, afhankelijk van wat het eerste komt. U kunt deze instelling configureren in het bestand [host. json] .  Hier volgt een voorbeeld:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Sampling configureren

Application Insights heeft een [bemonsterings](../azure-monitor/app/sampling.md) functie waarmee u kunt voor komen dat er te veel telemetriegegevens worden geproduceerd tijdens voltooide uitvoeringen op momenten van piek belasting. Wanneer de frequentie van binnenkomende uitvoeringen een opgegeven drempel overschrijdt, wordt Application Insights wille keurig een aantal binnenkomende uitvoeringen negeren. De standaard instelling voor het maximum aantal uitvoeringen per seconde is 20 (vijf in versie 1. x). U kunt steek proeven configureren in [host. json].  Hier volgt een voorbeeld:

### <a name="version-2x-and-later"></a>Versie 2. x en hoger

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20
      }
    }
  }
}
```

### <a name="version-1x"></a>Versie 1. x 

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

> [!NOTE]
> [Steek proeven](../azure-monitor/app/sampling.md) zijn standaard ingeschakeld. Als er gegevens ontbreken, moet u mogelijk de sampling-instellingen aanpassen aan uw specifieke bewakings scenario.

## <a name="write-logs-in-c-functions"></a>Logboeken schrijven C# in functions

U kunt Logboeken schrijven in uw functie code die wordt weer gegeven als traceringen in Application Insights.

### <a name="ilogger"></a>ILogger

Gebruik een [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) -para meter in uw functies in plaats van een `TraceWriter` para meter. Logboeken die zijn gemaakt met behulp van `TraceWriter`, gaan naar Application Insights, maar met `ILogger` kunt u [gestructureerde logboek registratie](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)uitvoeren.

Met een `ILogger`-object roept u `Log<level>` [extensie methoden aan op ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) om logboeken te maken. Met de volgende code worden `Information`-logboeken geschreven met de functie category.

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Gestructureerde logboek registratie

De volg orde van tijdelijke aanduidingen, niet de namen, bepaalt welke para meters worden gebruikt in het logboek bericht. Stel dat u de volgende code hebt:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Als u dezelfde bericht teken reeks behoudt en de volg orde van de para meters omkeert, zouden de resulterende bericht tekst de waarden op de verkeerde plaatsen zouden hebben.

Tijdelijke aanduidingen worden op deze manier verwerkt, zodat u gestructureerde logboek registratie kunt uitvoeren. Application Insights slaat de parameter naam/waarde-paren en de bericht teken reeks op. Het resultaat is dat de bericht argumenten worden velden waarop u kunt zoeken.

Als de aanroep van de traceer methode lijkt op het vorige voor beeld, kunt u een query uitvoeren op het veld `customDimensions.prop__rowKey`. Het `prop__`-voor voegsel wordt toegevoegd om ervoor te zorgen dat er geen conflicten zijn tussen velden die door de runtime worden toegevoegd en de velden die door de functie code worden toegevoegd.

U kunt ook een query uitvoeren op de oorspronkelijke bericht teken reeks door te verwijzen naar het veld `customDimensions.prop__{OriginalFormat}`.  

Hier volgt een voor beeld van een JSON-weer gave van `customDimensions` gegevens:

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Logboek registratie voor aangepaste metrische gegevens

In C# script functies kunt u de `LogMetric` extensie methode op `ILogger` gebruiken om aangepaste metrische gegevens in Application Insights te maken. Dit is een voor beeld van een methode aanroep:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Deze code is een alternatief voor het aanroepen van `TrackMetric` met behulp van de Application Insights-API voor .NET.

## <a name="write-logs-in-javascript-functions"></a>Logboeken schrijven in Java script-functies

Gebruik in node. js-functies `context.log` om logboeken te schrijven. Structured logging is niet ingeschakeld.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Logboek registratie voor aangepaste metrische gegevens

Wanneer u werkt met [versie 1. x](functions-versions.md#creating-1x-apps) van de functions runtime, kunnen node. js-functies de `context.log.metric` methode gebruiken om aangepaste metrische gegevens in Application Insights te maken. Deze methode wordt momenteel niet ondersteund in versie 2. x en hoger. Dit is een voor beeld van een methode aanroep:

```javascript
context.log.metric("TestMetric", 1234);
```

Deze code is een alternatief voor het aanroepen van `trackMetric` met behulp van de node. js SDK voor Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Aangepaste telemetrie vastleggen C# in functies

U kunt het NuGet-pakket van [micro soft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) gebruiken om aangepaste telemetriegegevens naar Application Insights te verzenden. In het C# volgende voor beeld wordt de [aangepaste telemetrie-API](../azure-monitor/app/api-custom-events-metrics.md)gebruikt. Het voor beeld is voor een .NET-klassebibliotheek, maar de Application Insights code is hetzelfde voor C# het script.

### <a name="version-2x-and-later"></a>Versie 2. x en hoger

Versie 2. x en latere versies van de runtime gebruiken nieuwere functies in Application Insights om telemetrie automatisch te correleren met de huidige bewerking. U hoeft de `Id`-, `ParentId`-of `Name` velden van de bewerking niet hand matig in te stellen.

```cs
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

### <a name="version-1x"></a>Versie 1. x

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

Roep `TrackRequest` of `StartOperation<RequestTelemetry>` niet aan omdat er dubbele aanvragen voor een functie aanroep worden weer geven.  Met de functie runtime worden aanvragen automatisch bijgehouden.

Stel `telemetryClient.Context.Operation.Id`niet in. Deze globale instelling veroorzaakt een onjuiste correlatie wanneer er meerdere functies tegelijkertijd worden uitgevoerd. Maak in plaats daarvan een nieuwe telemetrie-instantie (`DependencyTelemetry`, `EventTelemetry`) en wijzig de `Context` eigenschap. Geef vervolgens de telemetrie-instantie door aan de bijbehorende `Track` methode op `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Deze methode zorgt ervoor dat de telemetrie de juiste correlatie details heeft voor de huidige functie aanroep.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Aangepaste telemetrie vastleggen in Java script-functies

Hier volgt een voor beeld van een code fragment waarmee aangepaste telemetrie wordt verzonden met de [Application Insights node. js SDK](https://github.com/microsoft/applicationinsights-node.js):

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    context.done();
};
```

Met de para meter `tagOverrides` wordt de `operation_Id` ingesteld op de aanroep-ID van de functie. Met deze instelling kunt u alle automatisch gegenereerde en aangepaste telemetrie voor een bepaalde functie aanroep correleren.

## <a name="dependencies"></a>Afhankelijkheden

Met functies v2 worden automatisch afhankelijkheden verzameld voor HTTP-aanvragen, ServiceBus en SQL.

U kunt aangepaste code schrijven om de afhankelijkheden weer te geven. Zie voor voor beelden de voorbeeld code in de [ C# sectie Aangepaste telemetrie](#log-custom-telemetry-in-c-functions). De voorbeeld code resulteert in een *toepassings toewijzing* in Application Insights die eruitziet als de volgende afbeelding:

![Overzicht van de toepassing](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>Problemen melden

Als u een probleem wilt melden met Application Insights integratie in functions, of als u een suggestie of aanvraag wilt doen, [maakt u een probleem in github](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Streaming-logboeken

Tijdens het ontwikkelen van een toepassing wilt u vaak zien wat er in bijna realtime naar de logboeken wordt geschreven wanneer u in azure uitvoert.

Er zijn twee manieren om een stroom weer te geven van de logboek bestanden die worden gegenereerd door uw functie-uitvoeringen.

* **Ingebouwde logboek streaming**: met het app service-platform kunt u een stroom weer geven van de logboek bestanden van uw toepassing. Dit komt overeen met de uitvoer die wordt weer gegeven wanneer u tijdens de [lokale ontwikkeling](functions-develop-local.md) fouten opspoort in uw functies en wanneer u het tabblad **testen** in de portal gebruikt. Alle gegevens op basis van het logboek worden weer gegeven. Zie [Stream-logboeken](../app-service/troubleshoot-diagnostic-logs.md#stream-logs)voor meer informatie. Deze streaming-methode ondersteunt slechts één exemplaar en kan niet worden gebruikt met een app die wordt uitgevoerd op Linux in een verbruiks abonnement.

* **Live Metrics stream**: als uw functie-app is [verbonden met Application Insights](#enable-application-insights-integration), kunt u de logboek gegevens en andere gegevens in bijna realtime weer geven in de Azure Portal met [Live Metrics stream](../azure-monitor/app/live-stream.md). Gebruik deze methode wanneer u functies bewaken die worden uitgevoerd op meerdere exemplaren of op Linux in een verbruiks abonnement. Met deze methode worden [voorbeeld gegevens](#configure-sampling)gebruikt.

Logboek stromen kunnen zowel in de portal als in de meeste lokale ontwikkel omgevingen worden weer gegeven. 

### <a name="portal"></a>Portal

U kunt beide typen logboek stromen weer geven in de portal.

#### <a name="built-in-log-streaming"></a>Ingebouwde logboek streaming

Als u streaming-logboeken wilt weer geven in de portal, selecteert u het tabblad **platform functies** in uw functie-app. Klik vervolgens onder **bewaking**op **logboek streaming**.

![Streaming-logboeken inschakelen in de portal](./media/functions-monitoring/enable-streaming-logs-portal.png)

Hiermee wordt uw app verbonden met de service logboek streaming en worden toepassings Logboeken in het venster weer gegeven. U kunt scha kelen tussen **toepassings logboeken** en **webserver logboeken**.  

![Streaming-logboeken weer geven in de portal](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Als u de Live Metrics Stream voor uw app wilt weer geven, selecteert u het tabblad **overzicht** van de functie-app. Wanneer u Application Insights hebt ingeschakeld, ziet u een **Application Insights** koppeling onder **geconfigureerde onderdelen**. Met deze koppeling gaat u naar de Application Insights-pagina voor uw app.

Selecteer **Live Metrics stream**in Application Insights. [Voorbeeld logboek vermeldingen](#configure-sampling) worden weer gegeven onder voor **beeld-telemetrie**.

![Live Metrics Stream weer geven in de portal](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Kern Hulpprogramma's

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>Azure CLI

U kunt streaming-logboeken inschakelen met behulp van de [Azure cli](/cli/azure/install-azure-cli). Gebruik de volgende opdrachten om u aan te melden, uw abonnement te kiezen en logboek bestanden te streamen:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

U kunt streaming-logboeken inschakelen met behulp van [Azure PowerShell](/powershell/azure/overview). Gebruik voor Power shell de volgende opdrachten om uw Azure-account toe te voegen, uw abonnement te kiezen en logboek bestanden te streamen:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Ingebouwde logboek registratie uitschakelen

Als u Application Insights inschakelt, schakelt u de ingebouwde logboek registratie uit die gebruikmaakt van Azure Storage. De ingebouwde logboek registratie is handig voor het testen met lichte werk belastingen, maar is niet bedoeld voor productie gebruik met een hoge belasting. Voor productie bewaking wordt aangeraden Application Insights. Als ingebouwde logboek registratie in productie wordt gebruikt, is de registratie record mogelijk onvolledig vanwege beperking op Azure Storage.

Als u ingebouwde logboek registratie wilt uitschakelen, verwijdert u de `AzureWebJobsDashboard` app-instelling. Voor informatie over het verwijderen van app-instellingen in de Azure Portal, zie de sectie **Toepassings instellingen** in [hoe u een functie-app beheert](functions-how-to-use-azure-function-app-settings.md#settings). Voordat u de app-instelling verwijdert, moet u ervoor zorgen dat er voor geen van de bestaande functies in dezelfde functie-app de instelling voor Azure Storage triggers of bindingen wordt gebruikt.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie:

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core logboek registratie](/aspnet/core/fundamentals/logging/)

[host. json]: functions-host-json.md
