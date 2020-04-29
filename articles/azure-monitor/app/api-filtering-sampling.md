---
title: Filters en voor verwerking in de Azure-toepassing Insights-SDK | Microsoft Docs
description: U kunt telemetrie-processors en initialisatie functies voor telemetrie voor de SDK schrijven om eigenschappen te filteren of toe te voegen aan de gegevens voordat de telemetrie naar de Application Insights portal wordt verzonden.
ms.topic: conceptual
ms.date: 11/23/2016
ms.openlocfilehash: 8b81849726ad546a24ce1bb56a139b384eb54c42
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81405363"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Telemetrie filteren en voor verwerking in de Application Insights SDK

U kunt invoeg toepassingen schrijven en configureren voor de Application Insights SDK om aan te passen hoe telemetrie kan worden verrijkt en verwerkt voordat het naar de Application Insights-service wordt verzonden.

* Door [steek proeven](sampling.md) wordt het aantal telemetrie verminderd zonder dat dit van invloed is op uw statistieken. Het houdt gerelateerde gegevens punten bij zodat u ertussen kunt navigeren bij het vaststellen van een probleem. In de portal worden de totale aantallen vermenigvuldigd om de steek proeven te compenseren.
* Met filters met telemetrie-processors kunt u telemetrie in de SDK filteren voordat deze naar de server wordt verzonden. U kunt bijvoorbeeld het volume van de telemetrie verminderen door aanvragen van robots uit te sluiten. Filteren is een meer algemene benadering van het verminderen van het verkeer dan de steek proef. Hiermee hebt u meer controle over wat er wordt verzonden, maar u moet er rekening mee houden dat dit van invloed is op uw statistieken, bijvoorbeeld als u alle succes volle aanvragen filtert.
* Met de [initialisatie functies voor telemetrie kunt u eigenschappen toevoegen of wijzigen](#add-properties) voor alle telemetrie die vanuit uw app worden verzonden, inclusief telemetrie uit de standaard modules. U kunt bijvoorbeeld berekende waarden toevoegen. of de versie nummers waarmee de gegevens in de portal worden gefilterd.
* [De SDK-API](../../azure-monitor/app/api-custom-events-metrics.md) wordt gebruikt voor het verzenden van aangepaste gebeurtenissen en metrische gegevens.

Voordat u begint:

* Installeer de juiste SDK voor uw toepassing: [ASP.net](asp-net.md), [ASP.net core](asp-net-core.md), [niet http/worker voor .net/.net core](worker-service.md)of [Java script](javascript.md)

<a name="filtering"></a>

## <a name="filtering"></a>Filteren

Met deze techniek hebt u direct controle over wat er wordt opgenomen of uitgesloten van de telemetrie-stroom. Filteren kan worden gebruikt om telemetriegegevens te verwijderen van verzen ding naar Application Insights. U kunt deze gebruiken in combi natie met steek proeven of afzonderlijk.

Als u telemetrie wilt filteren, schrijft u een telemetrie-processor `TelemetryConfiguration`en registreert u deze bij de. Alle telemetrie verloopt via uw processor en u kunt ervoor kiezen om deze te verwijderen uit de stroom of door te geven aan de volgende processor in de keten. Dit omvat telemetrie van de standaard modules, zoals de HTTP-aanvraag verzamelaar en de afhankelijkheids verzamelaar, en telemetrie die u zelf hebt gevolgd. U kunt bijvoorbeeld telemetrie filteren op aanvragen van robots of geslaagde afhankelijkheids aanroepen.

> [!WARNING]
> Filteren van de telemetrie die via de SDK is verzonden met behulp van processors, kunnen de statistieken die u in de portal ziet, worden schuingetrokken en kan het lastig zijn om verwante items te volgen.
>
> Overweeg in plaats daarvan [steek proeven](../../azure-monitor/app/sampling.md)te gebruiken.
>
>

### <a name="create-a-telemetry-processor-c"></a>Een telemetrie-processor maken (C#)

1. Implementeren `ITelemetryProcessor`om een filter te maken.

    U ziet dat telemetrie-processors een keten van verwerking bouwen. Wanneer u een telemetrie-processor maakt, krijgt u een verwijzing naar de volgende processor in de keten. Wanneer een telemetrie-gegevens punt wordt door gegeven aan de proces methode, wordt het werk uitgevoerd en wordt vervolgens de volgende telemetrie-processor in de keten aangeroepen (of niet).

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. Voeg uw processor toe.

**ASP.net-apps** Voeg dit fragment in ApplicationInsights. config in:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

U kunt teken reeks waarden uit het. config-bestand door geven door open bare benoemde eigenschappen in uw klasse op te geven.

> [!WARNING]
> Zorg ervoor dat u de naam van het type en de namen van eigenschappen in het. config-bestand overeenkomt met de klasse-en eigenschaps namen in de code. Als het. config-bestand verwijst naar een niet-bestaand type of een ongeldige eigenschap, kan de SDK een telemetrie niet op de achtergrond verzenden.
>

U kunt het filter **ook** in code initialiseren. In een geschikte initialisatie klasse, bijvoorbeeld AppStart in `Global.asax.cs` -Voeg uw processor in de keten in:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

TelemetryClients gemaakt na dit punt zullen uw processors gebruiken.

**ASP.NET Core/Worker-service-apps**

> [!NOTE]
> Het toevoegen van `ApplicationInsights.config` een processor `TelemetryConfiguration.Active` met of gebruikt is niet geldig voor ASP.net core toepassingen of als u micro soft. ApplicationInsights. WorkerService SDK gebruikt.

Voor apps die zijn geschreven met behulp van [ASP.net core](asp-net-core.md#adding-telemetry-processors) of `TelemetryProcessor` [WorkerService](worker-service.md#adding-telemetry-processors), wordt het toevoegen van een `IServiceCollection`nieuwe uitgevoerd met behulp `AddApplicationInsightsTelemetryProcessor` van de uitbreidings methode ingeschakeld, zoals hieronder wordt weer gegeven. Deze methode wordt aangeroepen in `ConfigureServices` de methode van `Startup.cs` uw klasse.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>Voorbeeld filters

#### <a name="synthetic-requests"></a>Synthetische aanvragen

Wegfilteren bots en webtests. Hoewel Metrics Explorer u de mogelijkheid geeft om synthetische bronnen uit te filteren, vermindert deze optie het verkeer en de opname grootte door deze te filteren op de SDK zelf.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Mislukte verificatie

Aanvragen filteren met een antwoord van het "401".

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Snelle oproepen voor externe afhankelijkheid uitfilteren

Als u alleen aanroepen wilt opsporen die langzaam zijn, filtert u de snelle verbindingen.

> [!NOTE]
> Hierdoor worden de statistieken in de portal scheefer.
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>Afhankelijkheids problemen vaststellen

In [deze blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) wordt een project beschreven voor het vaststellen van afhankelijkheids problemen door automatisch pings naar afhankelijkheden te verzenden.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>Java script-webtoepassingen

**Filteren met behulp van ITelemetryInitializer**

1. Een call back functie voor telemetrie initialiseren maken. De call back- `ITelemetryItem` functie neemt als para meter, de gebeurtenis die wordt verwerkt. Retour `false` uit deze call back resulteert in het telemetrie-item dat moet worden gefilterd.  

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
         return false;
     }
  
     return true;
   };
   ```

2. De call back van de telemetrie-initialisatie functie toevoegen:

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>Eigenschappen toevoegen/wijzigen: ITelemetryInitializer


Gebruik initialisatie functies voor telemetrie om telemetrie te verrijken met aanvullende informatie en/of om de telemetrie-eigenschappen te overschrijven die zijn ingesteld door de standaard-telemetrie modules.

Zo kan de Application Insights voor webpakket het verzamelen van telemetrie over HTTP-aanvragen. Standaard wordt een aanvraag met een antwoord code >= 400 als mislukt. Als u 400 als een succes wilt behandelen, kunt u een telemetrie-initialisatie functie opgeven waarmee de eigenschap Success wordt ingesteld.

Als u een telemetrie-initialisatie functie opgeeft, wordt deze aangeroepen wanneer een van de methoden van de track * () wordt aangeroepen. Dit omvat `Track()` de methoden die worden aangeroepen door de standaard-telemetrie-modules. Per Conventie stellen deze modules geen eigenschap in die al is ingesteld door een initialisatie functie. Initialisatie functies voor telemetrie worden aangeroepen voordat telemetrie-processors worden aangeroepen. Verrijkingen die door initializers worden uitgevoerd, zijn dus zichtbaar voor processors.

**De initialisatie functie definiëren**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

**ASP.NET-Apps: de initialisatie functie laden**

In ApplicationInsights. config:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

U kunt *ook* een exemplaar van de initialisatie functie in code maken, bijvoorbeeld in Global.aspx.CS:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[Bekijk meer van dit voor beeld.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

**ASP.NET Core/Worker-service-apps: de initialisatie functie laden**

> [!NOTE]
> Het toevoegen van initializer `ApplicationInsights.config` met `TelemetryConfiguration.Active` of gebruikt is niet geldig voor ASP.net core-toepassingen of als u de SDK van micro soft. ApplicationInsights. WorkerService gebruikt.

Voor apps die zijn geschreven met [ASP.net core](asp-net-core.md#adding-telemetryinitializers) of [WorkerService](worker-service.md#adding-telemetryinitializers), voegt `TelemetryInitializer` u een nieuw item toe door het toe te voegen aan de container voor de injectie van de afhankelijkheid, zoals hieronder wordt weer gegeven. Dit wordt gedaan met `Startup.ConfigureServices` de methode.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```
### <a name="javascript-telemetry-initializers"></a>Initialisatie functies van Java script-telemetrie
*Javascript*

Voeg direct na de initialisatie code die u hebt ontvangen van de portal, een telemetrie-initialisatie functie toe:

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.queue.push(function () {
        appInsights.context.addTelemetryInitializer(function (envelope) {
            var telemetryItem = envelope.data.baseData;

            // To check the telemetry items type - for example PageView:
            if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                // this statement removes url from all page view documents
                telemetryItem.url = "URL CENSORED";
            }

            // To set custom properties:
            telemetryItem.properties = telemetryItem.properties || {};
            telemetryItem.properties["globalProperty"] = "boo";

            // To set custom metrics:
            telemetryItem.measurements = telemetryItem.measurements || {};
            telemetryItem.measurements["globalMetric"] = 100;
        });
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

Zie [Application Insights-gegevens model exporteren](../../azure-monitor/app/export-data-model.md)voor een samen vatting van de niet-aangepaste eigenschappen die beschikbaar zijn op de telemetryItem.

U kunt zoveel initialisatie functies toevoegen als u wilt en ze worden aangeroepen in de volg orde waarin ze worden toegevoegd.

### <a name="opencensus-python-telemetry-processors"></a>Python-telemetrie-processors van opentellingen

Telemetriegegevens in opentellingen python zijn eenvoudigweg call back-functies die worden aangeroepen om telemetriegegevens te verwerken voordat ze worden geëxporteerd. De call back-functie moet een gegevens type [envelop](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) accepteren als para meter. Als u het exporteren van telemetrie wilt uitfilteren, moet u `False`ervoor zorgen dat de call back functie retourneert. U kunt het schema voor Azure Monitor gegevens typen weer geven in de enveloppen [.](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py)

> [!NOTE]
> U kunt de `cloud_RoleName` wijzigen door het `ai.cloud.role` kenmerk in het `tags` veld te wijzigen.

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name.py'
```

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
U kunt zoveel processors toevoegen als u wilt en ze worden aangeroepen in de volg orde waarin ze worden toegevoegd. Als een processor een uitzonde ring moet genereren, heeft dit geen invloed op de volgende processors.

### <a name="example-telemetryinitializers"></a>Voor beeld TelemetryInitializers

#### <a name="add-custom-property"></a>Aangepaste eigenschap toevoegen

De volgende voor beeld-initialisatie functie voegt een aangepaste eigenschap toe aan elke getraceerde telemetrie.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.Properties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-cloud-role-name"></a>Rolnaam van Cloud toevoegen

De volgende voor beeld-initialisatie functie stelt de naam van de Cloud functie in op elke getraceerde telemetrie.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

#### <a name="add-information-from-httpcontext"></a>Informatie uit http context toevoegen

De volgende voor beeld-initialisatie functie leest gegevens [`HttpContext`](https://docs.microsoft.com/aspnet/core/fundamentals/http-context?view=aspnetcore-3.1) uit de en voegt deze toe `RequestTelemetry` aan een exemplaar. De `IHttpContextAccessor` wordt automatisch via een afhankelijkheids injectie van een constructor verschaft.

```csharp
public class HttpContextRequestTelemetryInitializer : ITelemetryInitializer
{
    private readonly IHttpContextAccessor httpContextAccessor;

    public HttpContextRequestTelemetryInitializer(IHttpContextAccessor httpContextAccessor)
    {
        this.httpContextAccessor =
            httpContextAccessor ??
            throw new ArgumentNullException(nameof(httpContextAccessor));
    }

    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        if (requestTelemetry == null) return;

        var claims = this.httpContextAccessor.HttpContext.User.Claims;
        Claim oidClaim = claims.FirstOrDefault(claim => claim.Type == "oid");
        requestTelemetry.Properties.Add("UserOid", oidClaim?.Value);
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor en ITelemetryInitializer

Wat is het verschil tussen telemetrie-processors en initialisatie functies voor telemetrie?

* Er zijn enkele overlap pingen in wat u ermee kunt doen: beide kunnen worden gebruikt om eigenschappen van telemetrie toe te voegen of te wijzigen, maar het wordt aanbevolen initialisatie functies voor dat doel te gebruiken.
* TelemetryInitializers altijd uitgevoerd vóór TelemetryProcessors.
* TelemetryInitializers kan meer dan één keer worden aangeroepen. Per Conventie hebben ze geen eigenschap ingesteld die al is ingesteld.
* Met TelemetryProcessors kunt u een telemetrie-item volledig vervangen of verwijderen.
* Alle geregistreerde TelemetryInitializers worden gegarandeerd aangeroepen voor elk telemetrie-item. Voor telemetrie-processors wordt door SDK gegarandeerd de eerste telemetrie-processor aan te roepen. Of de rest van de processors worden aangeroepen of niet, wordt bepaald door de voor gaande telemetrie-processors.
* Gebruik TelemetryInitializers om telemetrie te verrijken met aanvullende eigenschappen, of een bestaande te overschrijven. Gebruik TelemetryProcessor om telemetrie te filteren.

## <a name="troubleshooting-applicationinsightsconfig"></a>Problemen met ApplicationInsights. config oplossen

* Controleer of de FQDN-naam en de naam van de assembly juist zijn.
* Controleer of het bestand applicationinsights. config zich in de uitvoermap bevindt en of het recente wijzigingen bevat.

## <a name="reference-docs"></a>Referentie documenten

* [API-overzicht](../../azure-monitor/app/api-custom-events-metrics.md)
* [ASP.NET-verwijzing](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>SDK-code

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET-SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>Volgende stappen
* [Zoeken naar gebeurtenissen en Logboeken](../../azure-monitor/app/diagnostic-search.md)
* [Steekproeven](../../azure-monitor/app/sampling.md)
* [Problemen oplossen](../../azure-monitor/app/troubleshoot-faq.md)
