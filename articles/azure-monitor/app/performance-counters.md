---
title: Prestatiemeteritems in Application Insights | Microsoft Documenten
description: Monitor systeem- en aangepaste .NET-prestatiemeteritems in Application Insights.
ms.topic: conceptual
ms.date: 12/13/2018
ms.openlocfilehash: 94d2520c17867f6d70caffd002a76365a425986f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669876"
---
# <a name="system-performance-counters-in-application-insights"></a>Systeemprestatiemeteritems in Application Insights

Windows biedt een grote verscheidenheid aan [prestatiemeteritems](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters), bijvoorbeeld voor CPU-bezetting, geheugen, schijfruimte en netwerkgebruik. U ook uw eigen prestatiemeteritems definiëren. Het verzamelen van prestatietellers wordt ondersteund zolang uw toepassing onder IIS wordt uitgevoerd op een on-premises host of virtuele machine waartoe u beheerderstoegang hebt. Hoewel toepassingen die worden uitgevoerd als Azure Web Apps geen directe toegang hebben tot prestatiemeteritems, wordt een subset van beschikbare tellers verzameld door Application Insights.

## <a name="view-counters"></a>Tellers weergeven

In het deelvenster Statistieken wordt de standaardset met prestatiemeteritems weergegeven.

![Prestatiemeteritems gerapporteerd in Application Insights](./media/performance-counters/performance-counters.png)

De huidige standaardtellers die zijn geconfigureerd om te worden verzameld voor ASP.NET/ASP.NET Core-webtoepassingen zijn:
- %\\procesprocessortijd
- %\\Procesprocessortijd genormaliseerd
- Beschikbare\\bytes voor geheugen
- ASP.NET verzoeken/sec
- .NET CLR Uitzonderingen gegooid / sec
- ASP.NET uitvoeringstijd van toepassingenaanvragen
- Privébytes\\verwerken
- Io-gegevensbytes\\per seconde verwerken
- aanvragen\\ASP.NET toepassingen in de toepassingswachtrij
- Processor(_Total)\\% processortijd

## <a name="add-counters"></a>Tellers toevoegen

Als het gewenste prestatiemeterat niet in de lijst met statistieken wordt opgenomen, u deze toevoegen.

1. Ontdek welke tellers beschikbaar zijn in uw server met deze PowerShell-opdracht op de lokale server:

    `Get-Counter -ListSet *`

    (Zie [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx).)
2. Open ApplicationInsights.config.

   * Als u Application Insights tijdens de ontwikkeling aan uw app hebt toegevoegd, bewerkt u ApplicationInsights.config in uw project en implementeert u deze vervolgens opnieuw naar uw servers.
3. Bewerk de richtlijn prestatieverzamelaar:

    ```XML

        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <Counters>
            <Add PerformanceCounter="\Objects\Processes"/>
            <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
          </Counters>
        </Add>
    ```

> [!NOTE]
> ASP.NET Kerntoepassingen niet `ApplicationInsights.config`hebben, en daarom is de bovengenoemde methode niet geldig voor ASP.NET KernToepassingen.

U zowel standaardtellers als de standaardtellers vastleggen die u zelf hebt geïmplementeerd. `\Objects\Processes`is een voorbeeld van een standaardteller die beschikbaar is op alle Windows-systemen. `\Sales(photo)\# Items Sold`is een voorbeeld van een aangepaste teller die kan worden geïmplementeerd in een webservice.

De indeling `\Category(instance)\Counter"`is of voor categorieën die geen `\Category\Counter`instanties hebben, alleen .

`ReportAs`is vereist voor tellernamen `[a-zA-Z()/-_ \.]+` die niet overeenkomen - dat wil zeggen dat ze tekens bevatten die niet in de volgende sets staan: letters, ronde haakjes, slash vooruit, koppelteken, underscore, ruimte, punt.

Als u een instantie opgeeft, wordt deze verzameld als een dimensie "CounterInstanceName" van de gerapporteerde statistiek.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Prestatiemeteritems verzamelen in code voor ASP.NET webtoepassingen of .NET/.NET Core Console-toepassingen
Als u systeemprestatiemeteritems wilt verzamelen en deze naar Application Insights wilt verzenden, u het onderstaande fragment aanpassen:


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

U hetzelfde doen met aangepaste statistieken die u hebt gemaakt:

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Prestatiemeteritems verzamelen in code voor ASP.NET Core-webtoepassingen

Wijzig `ConfigureServices` de `Startup.cs` methode in uw klasse zoals hieronder.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>Prestatiemeteritems in Analytics
U prestatiemeterrapporten zoeken en weergeven in [Analytics.](../../azure-monitor/app/analytics.md)

Het schema **PerformanceCounters** `category`toont `counter` de `instance` naam en de naam van elke prestatiemeter.  In de telemetrie voor elke toepassing ziet u alleen de tellers voor die toepassing. Bijvoorbeeld om te zien welke tellers beschikbaar zijn: 

![Prestatiemeteritems in Application Insights-analyses](./media/performance-counters/analytics-performance-counters.png)

('Instantie' verwijst hier naar de instantie van de prestatieteller, niet naar de instantie van de servermachine. De naam van de prestatietellerinstantie segmentert doorgaans tellers zoals processortijd op naam van het proces of de toepassing.)

Ga als lid van de afgelopen periode op een overzicht van beschikbare geheugens: 

![Geheugentijddiagram in Application Insights-analyses](./media/performance-counters/analytics-available-memory.png)

Net als andere telemetrie heeft `cloud_RoleInstance` **performanceCounters** ook een kolom die de identiteit aangeeft van het hostserverexemplaar waarop uw app wordt uitgevoerd. Bijvoorbeeld om de prestaties van uw app op de verschillende machines te vergelijken: 

![Prestaties gesegmenteerd op rolinstantie in Application Insights-analyses](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET en Application Insights telt

*Wat is het verschil tussen de statistieken Uitzondering en Uitzonderingen?*

* *Uitzonderingspercentage* is een systeemprestatiemeter. De CLR telt alle afgehandelde en onverwerkte uitzonderingen die worden gegooid en verdeelt het totaal in een bemonsteringsinterval door de lengte van het interval. De Application Insights SDK verzamelt dit resultaat en stuurt het naar de portal.

* *Uitzonderingen* is een telling van de TrackException-rapporten die door de portal zijn ontvangen in het bemonsteringsinterval van de grafiek. Het bevat alleen de afgehandelde uitzonderingen wanneer u TrackException-aanroepen in uw code hebt geschreven en bevat niet alle [onverwerkte uitzonderingen.](../../azure-monitor/app/asp-net-exceptions.md) 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Prestatiemeteritems voor toepassingen die worden uitgevoerd in Azure Web Apps

Zowel ASP.NET als ASP.NET Core-toepassingen die zijn geïmplementeerd in Azure Web Apps, worden uitgevoerd in een speciale sandbox-omgeving. Deze omgeving biedt geen directe toegang tot systeemprestatiemeteritems. Echter, een beperkte subset van tellers worden blootgesteld als omgevingsvariabelen zoals [hier](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables)beschreven . Application Insights SDK voor ASP.NET en ASP.NET Core verzamelt prestatiemeteritems van Azure Web Apps van deze speciale omgevingsvariabelen. Alleen een subset van tellers zijn beschikbaar in deze omgeving, en de volledige lijst is hier te [vinden.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Prestatiemeteritems in ASP.NET Core-toepassingen

Ondersteuning voor prestatiemeteritems in ASP.NET Core is beperkt:

* [SDK-versies](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.4.1 en hoger verzamelen prestatiemeteritems als de toepassing wordt uitgevoerd in Azure Web Apps (Windows).
* SDK-versies 2.7.1 en hoger verzamelen prestatiemeteritems als `NETSTANDARD2.0` de toepassing wordt uitgevoerd in Windows en doelen of hoger.
* Voor toepassingen die zich richten op het .NET Framework, worden alle versies van de prestatiemeteritems van SDK-ondersteuning ondersteund.
* SDK Versies 2.8.0 en hoger ondersteunen cpu/memory teller in Linux. Geen enkele andere teller wordt ondersteund in Linux. De aanbevolen manier om systeemtellers in Linux (en andere niet-Windows-omgevingen) te krijgen, is door [EventCounters te gebruiken](eventcounters.md)

## <a name="alerts"></a>Waarschuwingen
Net als andere statistieken u [een waarschuwing instellen](../../azure-monitor/app/alerts.md) om u te waarschuwen als een prestatiemeter buiten een door u opgegeven limiet gaat. Open het deelvenster Waarschuwingen en klik op Waarschuwing toevoegen.

## <a name="next-steps"></a><a name="next"></a>Volgende stappen

* [Afhankelijkheidstracking](../../azure-monitor/app/asp-net-dependencies.md)
* [Uitzonderingstracking](../../azure-monitor/app/asp-net-exceptions.md)

