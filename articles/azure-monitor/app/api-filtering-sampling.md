---
title: Filteren en voorbewerking in de Azure Application Insights SDK | Microsoft Documenten
description: Schrijf telemetrieprocessors en telemetrieinitializers voor de SDK om eigenschappen aan de gegevens te filteren of toe te voegen voordat de telemetrie naar de Portal Application Insights wordt verzonden.
ms.topic: conceptual
ms.date: 11/23/2016
ms.openlocfilehash: 8f2064f73821a017046cbb552a8dcf592ce13267
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983755"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Telemetrie filteren en voorbewerken in de Application Insights SDK

U plug-ins voor de Application Insights SDK schrijven en configureren om aan te passen hoe telemetrie kan worden verrijkt en verwerkt voordat deze naar de Application Insights-service wordt verzonden.

* [Sampling](sampling.md) vermindert het volume van telemetrie zonder dat dit gevolgen heeft voor uw statistieken. Het houdt gerelateerde gegevenspunten bij elkaar, zodat u tussen hen navigeren bij het diagnosticeren van een probleem. In het portaal worden de totale tellingen vermenigvuldigd om de bemonstering te compenseren.
* Met filteren met telemetrieprocessors u telemetrie in de SDK filteren voordat deze naar de server wordt verzonden. U bijvoorbeeld het volume van telemetrie verminderen door aanvragen van robots uit te sluiten. Filteren is een meer fundamentele benadering van het verminderen van het verkeer dan bemonstering. Het geeft u meer controle over wat wordt verzonden, maar u moet zich ervan bewust zijn dat het van invloed is op uw statistieken - bijvoorbeeld als u alle succesvolle aanvragen filtert.
* [Telemetrie Initialisators voegen of wijzigen eigenschappen](#add-properties) aan telemetrie die vanuit uw app worden verzonden, inclusief telemetrie van de standaardmodules. U bijvoorbeeld berekende waarden toevoegen. of versienummers waarmee de gegevens in de portal kunnen worden gefilterd.
* [De SDK API](../../azure-monitor/app/api-custom-events-metrics.md) wordt gebruikt om aangepaste gebeurtenissen en statistieken te verzenden.

Voordat u begint:

* Installeer de juiste SDK voor uw toepassing: [ASP.NET,](asp-net.md) [ASP.NET Core](asp-net-core.md), [Non HTTP/Worker voor .NET/.NET Core](worker-service.md)of [JavaScript](javascript.md)

<a name="filtering"></a>

## <a name="filtering"></a>Filteren

Deze techniek geeft u directe controle over wat is opgenomen of uitgesloten van de telemetrie stroom. Filteren kan worden gebruikt om telemetrie-items te laten vallen vanaf het verzenden naar Application Insights. U het gebruiken in combinatie met Sampling, of afzonderlijk.

Als u telemetrie wilt filteren, schrijft u `TelemetryConfiguration`een telemetrieprocessor en registreert u deze met de . Alle telemetrie gaat via uw processor, en u ervoor kiezen om het te laten vallen uit de stream of geef het aan de volgende processor in de keten. Dit omvat telemetrie van de standaardmodules zoals de HTTP-aanvraagverzamelaar en de afhankelijkheidsverzamelaar en telemetrie die u zelf hebt bijgehouden. U bijvoorbeeld telemetrie filteren over aanvragen van robots of succesvolle afhankelijkheidsoproepen.

> [!WARNING]
> Als u de telemetrie filtert die vanuit de SDK wordt verzonden met behulp van processors, kunnen de statistieken die u in de portal ziet scheeftrekken en kan het moeilijk zijn om gerelateerde items te volgen.
>
> In plaats daarvan u overwegen [steekproeven](../../azure-monitor/app/sampling.md)te gebruiken.
>
>

### <a name="create-a-telemetry-processor-c"></a>Een telemetrieprocessor maken (C#)

1. Als u een `ITelemetryProcessor`filter wilt maken, implementeert u .

    Merk op dat telemetrieprocessors een verwerkingsketen construeren. Wanneer u een telemetrieprocessor instantiate, krijgt u een verwijzing naar de volgende processor in de keten. Wanneer een telemetriegegevenspunt wordt doorgegeven aan de procesmethode, doet het zijn werk en roept vervolgens de volgende telemetrieprocessor in de keten aan (of niet).

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

**ASP.NET-apps** Voeg dit fragment in applicationInsights.config in:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

U tekenreekswaarden uit het bestand .config doorgeven door openbare eigenschappen in uw klasse op te geven.

> [!WARNING]
> Zorg ervoor dat de typenaam en eventuele eigenschapsnamen in het bestand .config overeenkomen met de klasse- en eigenschapnamen in de code. Als het .config-bestand verwijst naar een niet-bestaand type of eigenschap, kan het zijn dat de SDK in stilte geen telemetrie verzendt.
>

**U het** filter ook initialiseren in code. In een geschikte initialisatieklasse - `Global.asax.cs` bijvoorbeeld AppStart in - plaats je je processor in de keten:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

TelemetrieClients die na dit punt zijn gemaakt, gebruiken uw processors.

**ASP.NET Core/ Worker Service-apps**

> [!NOTE]
> Het toevoegen `ApplicationInsights.config` van `TelemetryConfiguration.Active` processorgebruik of -gebruik is niet geldig voor ASP.NET Core-toepassingen of als u Microsoft.ApplicationInsights.WorkerService SDK gebruikt.

Voor apps die zijn geschreven met [ASP.NET Core](asp-net-core.md#adding-telemetry-processors) of `AddApplicationInsightsTelemetryProcessor` [WorkerService,](worker-service.md#adding-telemetry-processors)wordt het toevoegen van een nieuwe `TelemetryProcessor` gedaan met behulp van extensiemethode op `IServiceCollection`, zoals hieronder wordt weergegeven. Deze methode wordt `ConfigureServices` aangeroepen `Startup.cs` in de methode van uw klasse.

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

### <a name="example-filters"></a>Voorbeeldfilters

#### <a name="synthetic-requests"></a>Synthetische verzoeken

Filter bots en webtests uit. Hoewel Metrics Explorer u de mogelijkheid biedt om synthetische bronnen uit te filteren, vermindert deze optie de grootte van het verkeer en de opname door ze te filteren op de SDK zelf.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Mislukte verificatie

Filter aanvragen uit met een "401"-antwoord.

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

#### <a name="filter-out-fast-remote-dependency-calls"></a>Snelle afhankelijkheidsoproepen op afstand filteren

Als u alleen oproepen wilt diagnosticeren die traag zijn, filtert u de snelle oproepen eruit.

> [!NOTE]
> Dit zal de statistieken die u ziet op de portal scheeftrekken.
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

#### <a name="diagnose-dependency-issues"></a>Afhankelijkheidsproblemen diagnosticeren

[In dit blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) wordt een project beschreven om afhankelijkheidsproblemen te diagnosticeren door automatisch regelmatige pings naar afhankelijkheden te verzenden.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>JavaScript-webtoepassingen

**Filteren met ITelemetryInitializer**

1. Maak een telemetrie initializer callback-functie. De callback-functie neemt `ITelemetryItem` als parameter, dat is de gebeurtenis die wordt verwerkt. Als `false` u terugkeert van deze terugroep, wordt het telemetrie-item dat moet worden uitgefilterd.  

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
         return false;
     }
  
     return true;
   };
   ```

2. Voeg uw telemetrieinitialisator callback toe:

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>Eigenschappen toevoegen/wijzigen: ITelemetryInitializer


Gebruik telemetrieinitialisators om telemetrie te verrijken met aanvullende informatie en/of telemetrie-eigenschappen die zijn ingesteld door de standaard telemetriemodules te overschrijven.

Het Application Insights for Web-pakket verzamelt bijvoorbeeld telemetrie over HTTP-aanvragen. Standaard wordt elke aanvraag als mislukt als mislukt met een antwoordcode >= 400. Maar als u 400 als een succes wilt behandelen, u een telemetrieinitialisator bieden die de eigenschap Succes instelt.

Als u een telemetrieinitialisator opgeeft, wordt deze aangeroepen wanneer een van de methoden Track*() wordt aangeroepen. Dit `Track()` omvat methoden die worden aangeroepen door de standaard telemetriemodules. Volgens de overeenkomst stellen deze modules geen eigendommen in die al door een initialisator zijn ingesteld. Telemetrieinitialisten worden aangeroepen voordat telemetrieprocessors worden aangeroepen. Dus alle verrijkingen gedaan door initialisators zijn zichtbaar voor processors.

**Uw initialisator definiëren**

*C #*

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

**ASP.NET-apps: uw initialisator laden**

In ApplicationInsights.config:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

*U* de initialisator ook instantiate in code, bijvoorbeeld in Global.aspx.cs:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[Bekijk meer van dit voorbeeld.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

**ASP.NET Core/ Worker Service-apps: uw initialisator laden**

> [!NOTE]
> Het toevoegen `ApplicationInsights.config` van `TelemetryConfiguration.Active` initializer met behulp van of met gebruik is niet geldig voor ASP.NET Core-toepassingen of als u Microsoft.ApplicationInsights.WorkerService SDK gebruikt.

Voor apps die zijn geschreven met ASP.NET `TelemetryInitializer` [Core](asp-net-core.md#adding-telemetryinitializers) of [WorkerService,](worker-service.md#adding-telemetryinitializers)wordt het toevoegen van een nieuwe gedaan door deze toe te voegen aan de container Afhankelijkheidsinjectie, zoals hieronder wordt weergegeven. Dit gebeurt `Startup.ConfigureServices` in de methode.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```
### <a name="javascript-telemetry-initializers"></a>JavaScript-telemetrieinitialisators
*Javascript*

Plaats een telemetrieinitialisator onmiddellijk na de initialisatiecode die u van de portal hebt gekregen:

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

Zie [Toepassingsinzichten Exportdatamodel](../../azure-monitor/app/export-data-model.md)voor een overzicht van de niet-aangepaste eigenschappen die beschikbaar zijn op het telemetrieitem.

U zoveel initialisatoren toevoegen als u wilt, en ze worden aangeroepen in de volgorde waarin ze worden toegevoegd.

### <a name="opencensus-python-telemetry-processors"></a>OpenCensus Python-telemetrieprocessors

Telemetrieprocessors in OpenCensus Python zijn gewoon callback-functies die worden opgeroepen om telemetrie te verwerken voordat ze worden geëxporteerd. De terugbelfunctie moet een [envelopgegevenstype](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) als parameter accepteren. Als u telemetrie wilt filteren op export, `False`controleert u of de terugbelfunctie terugkeert. U het schema voor Azure Monitor-gegevenstypen hier in de enveloppen [bekijken.](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py)

> [!NOTE]
> U `cloud_RoleName` het kenmerk `ai.cloud.role` wijzigen `tags` door het kenmerk in het veld te wijzigen.

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
U zoveel processors toevoegen als u wilt, en ze worden aangeroepen in de volgorde waarin ze worden toegevoegd. Als één processor een uitzondering moet maken, heeft dit geen invloed op de volgende processors.

### <a name="example-telemetryinitializers"></a>Voorbeeld telemetrieinitializers

#### <a name="add-custom-property"></a>Aangepaste eigenschap toevoegen

De volgende voorbeeldinitialisator voegt een aangepaste eigenschap toe aan elke bijgehouden telemetrie.

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

#### <a name="add-cloud-role-name"></a>Naam van een cloudrol toevoegen

In de volgende voorbeeldinitialisator wordt de naam van de cloudrol ingesteld op elke bijgehouden telemetrie.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if(string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor en ITelemetryInitializer

Wat is het verschil tussen telemetrieprocessors en telemetrieinitialisators?

* Er zijn enkele overlappingen in wat u met hen doen: beide kunnen worden gebruikt om eigenschappen van telemetrie toe te voegen of te wijzigen, hoewel het wordt aanbevolen om initialisatoren voor dat doel te gebruiken.
* TelemetrieInitializers draaien altijd vóór TelemetrieProcessors.
* TelemetrieInitialisators kunnen meer dan eens worden genoemd. Volgens de conventie stellen ze geen eigendommen in die al zijn ingesteld.
* TelemetrieProcessors stellen u in staat om een telemetrie-item volledig te vervangen of te verwijderen.
* Alle geregistreerde telemetrieInitializers zijn gegarandeerd te worden opgeroepen voor elke telemetrie item. Voor telemetrieprocessors garandeert SDK het aanroepen van de allereerste telemetrieprocessor. Of de rest van de processors worden aangeroepen of niet, wordt bepaald door de voorgaande telemetrieprocessors.
* Gebruik telemetrieInitializers om telemetrie te verrijken met extra eigenschappen of bestaande eigenschappen te overschrijven. Gebruik TelemetrieProcessor om telemetrie uit te filteren.

## <a name="troubleshooting-applicationinsightsconfig"></a>Probleemoplossing ApplicationInsights.config

* Controleer of de volledig gekwalificeerde typenaam en de naam van de assemblage juist zijn.
* Controleer of het bestand applicationinsights.config zich in uw uitvoermap bevindt en recente wijzigingen bevat.

## <a name="reference-docs"></a>Referentiedocumenten

* [API-overzicht](../../azure-monitor/app/api-custom-events-metrics.md)
* [ASP.NET referentie](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>SDK-code

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>Volgende stappen
* [Zoekgebeurtenissen en logboeken](../../azure-monitor/app/diagnostic-search.md)
* [Steekproeven](../../azure-monitor/app/sampling.md)
* [Problemen oplossen](../../azure-monitor/app/troubleshoot-faq.md)
