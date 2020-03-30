---
title: .NET trace logs in Application Insights verkennen
description: Zoeklogboeken gegenereerd door Trace, NLog of Log4Net.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: 273d5a2f4e1155541e159332312bdaa68aa175d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276268"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Verkennen .NET/.NET Core- en Python-tracelogs in Application Insights

Stuur diagnostische traceringslogboeken voor uw ASP.NET/ASP.NET Core-toepassing van ILogger, NLog, log4Net of System.Diagnostics.Trace naar [Azure Application Insights.][start] Voor Python-toepassingen verzendt u diagnostische traceringslogboeken met AzureLogHandler in OpenCensus Python voor Azure Monitor. U ze vervolgens verkennen en doorzoeken. Deze logboeken worden samengevoegd met de andere logboekbestanden van uw toepassing, zodat u sporen identificeren die aan elk gebruikersverzoek zijn gekoppeld en deze correleren met andere gebeurtenissen en uitzonderingsrapporten.

> [!NOTE]
> Heeft u de log-capture module nodig? Het is een handige adapter voor loggers van derden. Maar als u NLog, log4Net of System.Diagnostics.Trace nog niet gebruikt, u overwegen om alleen [**Application Insights TrackTrace()**](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) rechtstreeks te bellen.
>
>
## <a name="install-logging-on-your-app"></a>Logboekregistratie installeren op uw app
Installeer uw gekozen logging framework in uw project, wat moet resulteren in een vermelding in app.config of web.config.

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Toepassingsinzichten configureren om logboeken te verzamelen
[Voeg Toepassingsinzichten toe aan uw project](../../azure-monitor/app/asp-net.md) als u dat nog niet hebt gedaan. Je ziet een optie om het logboekverzamelaar op te nemen.

Of klik met de rechtermuisknop op uw project in Solution Explorer om toepassingsinzichten te **configureren.** Selecteer de optie **Traceverzameling configureren.**

> [!NOTE]
> Geen Application Insights menu of log collector optie? Probeer [probleemoplossing .](#troubleshooting)

## <a name="manual-installation"></a>Handmatige installatie
Gebruik deze methode als uw projecttype niet wordt ondersteund door het installatieprogramma Application Insights (bijvoorbeeld een Windows-bureaubladproject).

1. Als u van plan bent log4Net of NLog te gebruiken, installeert u deze in uw project.
2. Klik in Solution Explorer met de rechtermuisknop op uw project en selecteer **NuGet-pakketten beheren.**
3. Zoek naar 'Application Insights'.
4. Selecteer een van de volgende pakketten:

   - Voor iLogger: [Microsoft.extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Voor NLog: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Voor Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Voor System.Diagnostics: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

Het NuGet-pakket installeert de nodige samenstellingen en wijzigt web.config of app.config als dat van toepassing is.

## <a name="ilogger"></a>ILogger

Voor voorbeelden van het gebruik van de Application Insights ILogger implementatie met console applicaties en ASP.NET Core, zie [ApplicationInsightsLoggerProvider voor .NET Core ILogger logs](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Diagnostische logboekaanroepen invoegen
Als u System.Diagnostics.Trace gebruikt, is een typische oproep:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Als u liever log4net of NLog geeft, gebruikt u:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>EventSource-gebeurtenissen gebruiken
U [System.Diagnostics.Tracing.EventSource-gebeurtenissen](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) configureren om als traces naar Application Insights te worden verzonden. Installeer eerst `Microsoft.ApplicationInsights.EventSourceListener` het NuGet-pakket. Bewerk vervolgens `TelemetryModules` het gedeelte van het [bestand ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Voor elke bron u de volgende parameters instellen:
 * **Naam** geeft de naam op van de EventSource die moet worden verzameld.
 * **Niveau** geeft het te verzamelen logboekniveau op: *Kritiek*, *Fout*, *Informatief*, *LogAlways*, *Verbose*of *Waarschuwing*.
 * **Trefwoorden** (optioneel) geven de gehele waarde op van trefwoordcombinaties die moeten worden gebruikt.

## <a name="use-diagnosticsource-events"></a>DiagnosticSource-gebeurtenissen gebruiken
U [System.Diagnostics.DiagnosticSource-gebeurtenissen](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) configureren om naar Application Insights als traces te worden verzonden. Installeer eerst [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) het NuGet-pakket. Bewerk vervolgens het gedeelte 'Telemetriemodules' van het [bestand ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Voeg voor elke DiagnosticSource die u wilt traceren een vermelding toe met het kenmerk **Naam** dat is ingesteld op de naam van uw DiagnosticSource.

## <a name="use-etw-events"></a>ETW-gebeurtenissen gebruiken
U Event Tracing for Windows (ETW)-gebeurtenissen configureren om als traces naar Application Insights te worden verzonden. Installeer eerst `Microsoft.ApplicationInsights.EtwCollector` het NuGet-pakket. Bewerk vervolgens het gedeelte 'Telemetriemodules' van het [bestand ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

> [!NOTE] 
> ETW-gebeurtenissen kunnen alleen worden verzameld als het proces dat de SDK host, wordt uitgevoerd onder een identiteit die lid is van gebruikers of beheerders van prestatielogboeken.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Voor elke bron u de volgende parameters instellen:
 * **ProviderName** is de naam van de ETW-provider om te verzamelen.
 * **ProviderGuid** specificeert de GUID van de ETW-provider om te verzamelen. Het kan worden `ProviderName`gebruikt in plaats van .
 * **Niveau** stelt het logboekniveau in om te verzamelen. Het kan *kritisch*zijn , *fout,* *informatief*, *logalways,* *verbose*, of *waarschuwing*.
 * **Trefwoorden** (optioneel) stellen de gehele waarde van trefwoordcombinaties in.

## <a name="use-the-trace-api-directly"></a>De Trace-API direct gebruiken
U de Application Insights trace API rechtstreeks aanroepen. De logboekadapters maken gebruik van deze API.

Bijvoorbeeld:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Een voordeel van TrackTrace is dat je relatief lange gegevens in het bericht plaatsen. U daar bijvoorbeeld POST-gegevens coderen.

U ook een ernstniveau aan uw bericht toevoegen. En, net als andere telemetrie, u eigenschapswaarden toevoegen om te helpen filteren of zoeken naar verschillende sets van sporen. Bijvoorbeeld:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Dit zou u toelaten om gemakkelijk uit te filteren in [Zoeken alle][diagnostic] berichten van een bepaald ernstniveau die betrekking hebben op een bepaalde database.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler voor OpenCensus Python
Met de Azure Monitor Log Handler u Python-logboeken exporteren naar Azure Monitor.

Instrumenter uw toepassing met de [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md) voor Azure Monitor.

In dit voorbeeld ziet u hoe u een logboek op waarschuwingsniveau verzendt naar Azure Monitor.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Ontdek je logboeken
Voer uw app uit in de foutopsporingsmodus of implementeer deze live.

Selecteer [Zoeken][diagnostic]in het overzichtsvenster van uw app in [de portal Application Insights][portal].

U bijvoorbeeld:

* Filter op logboeksporen of op items met specifieke eigenschappen.
* Controleer een specifiek item in detail.
* Zoek andere systeemlogboekgegevens die betrekking hebben op dezelfde gebruikersaanvraag (heeft dezelfde OperationId).
* Sla de configuratie van een pagina op als favoriet.

> [!NOTE]
>Als uw toepassing veel gegevens verzendt en u de Application Insights SDK gebruikt voor ASP.NET versie 2.0.0-beta3 of hoger, kan de *functie adaptieve bemonstering* slechts een deel van uw telemetrie bedienen en verzenden. [Meer informatie over steekproeven.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Problemen oplossen
### <a name="how-do-i-do-this-for-java"></a>Hoe doe ik dit voor Java?
Gebruik de [Java-logadapters.](../../azure-monitor/app/java-trace-logs.md)

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Er is geen optie Toepassingsinzichten in het contextmenu van het project
* Zorg ervoor dat Developer Analytics Tools is geïnstalleerd op de ontwikkelmachine. Bij Visual Studio **Tools** > **Extensies en updates,** kijk voor **Developer Analytics Tools**. Als deze niet op het tabblad **Geïnstalleerd** staat, opent u het tabblad **Online** en installeert u het.
* Dit kan een projecttype zijn dat Devloper Analytics Tools niet ondersteunt. Handmatige [installatie gebruiken](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Er is geen optie voor een logadapter in het configuratieprogramma
* Installeer eerst het logging framework.
* Als u System.Diagnostics.Trace gebruikt, moet u ervoor zorgen dat u deze [hebt geconfigureerd in *web.config.*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx)
* Zorg ervoor dat u de nieuwste versie van Application Insights hebt. Ga in Visual Studio naar > **Extra-extensies en -updates**en open het tabblad **Updates.** **Tools** Als **er hulpmiddelen voor Ontwikkelaarsanalyse** aanwezig zijn, selecteert u deze om deze bij te werken.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>Ik krijg de foutmelding 'Instrumentatiesleutel kan niet leeg zijn'
U hebt waarschijnlijk de logging adapter Nuget pakket geïnstalleerd zonder Application Insights te installeren. Klik in Solution Explorer met de rechtermuisknop op *ApplicationInsights.config*en selecteer **Toepassingsinzichten bijwerken**. U wordt gevraagd zich aan te melden bij Azure en een Application Insights-bron te maken of een bestaande bron opnieuw te gebruiken. Dat zou het probleem moeten oplossen.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Ik zie sporen, maar niet andere gebeurtenissen in diagnostisch zoeken
Het kan even duren voordat alle gebeurtenissen en aanvragen door de pijplijn kunnen komen.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Hoeveel gegevens worden bewaard?
Verschillende factoren zijn van invloed op de hoeveelheid gegevens die wordt bewaard. Zie de sectie [limieten](../../azure-monitor/app/api-custom-events-metrics.md#limits) van de pagina met statistieken voor klantgebeurtenissen voor meer informatie.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Ik zie geen logboekvermeldingen die ik had verwacht
Als uw toepassing grote hoeveelheden gegevens verzendt en u de Application Insights SDK gebruikt voor ASP.NET versie 2.0.0-beta3 of hoger, kan de functie adaptieve bemonstering slechts een deel van uw telemetrie bedienen en verzenden. [Meer informatie over steekproeven.](../../azure-monitor/app/sampling.md)

## <a name="next-steps"></a><a name="add"></a>Volgende stappen

* [Fouten en uitzonderingen in ASP.NET diagnosticeren][exceptions]
* [Meer informatie over zoeken][diagnostic]
* [Beschikbaarheids- en responsiviteitstests instellen][availability]
* [Probleemoplossing][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
