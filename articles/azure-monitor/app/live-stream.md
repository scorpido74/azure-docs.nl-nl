---
title: Diagnose maken met Live Metrics Stream - Azure Application Insights
description: Controleer uw web-app in realtime met aangepaste statistieken en diagnosticeer problemen met een live feed van fouten, sporen en gebeurtenissen.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: ea0d786d0b8b96941d791bcc8e92fad9a869c5f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670097"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: Monitor & diagnose met latentie van 1 seconde

Sonde het kloppende hart van uw live, in-productie webapplicatie met behulp van Live Metrics Stream van [Application Insights](../../azure-monitor/app/app-insights-overview.md). Selecteer en filter statistieken en prestatiemeteritems om in realtime te bekijken, zonder dat uw service wordt verstoord. Controleer stapelsporen op mislukte aanvragen en uitzonderingen. Samen met [Profiler](../../azure-monitor/app/profiler.md), [Snapshot debugger](../../azure-monitor/app/snapshot-debugger.md). Live Metrics Stream biedt een krachtige en niet-invasieve diagnostische tool voor uw live website.

Met Live Metrics Stream kun je het nieuws doen:

* Valideer een oplossing terwijl deze wordt vrijgegeven door te kijken naar prestaties en falen telt.
* Bekijk het effect van testbelastingen en diagnosticeer problemen live.
* Richt u op bepaalde testsessies of filter bekende problemen uit door de statistieken die u wilt bekijken te selecteren en te filteren.
* Krijg uitzonderingssporen als ze gebeuren.
* Experimenteer met filters om de meest relevante KPI's te vinden.
* Controleer elke Windows-prestatiemeter live.
* Identificeer eenvoudig een server die problemen heeft en filter alle KPI/live-feed naar alleen die server.

[![Live Metrics Stream video](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Live Metrics worden momenteel ondersteund voor ASP.NET, ASP.NET Core,Azure Functions, Java en Node.js apps.

## <a name="get-started"></a>Aan de slag

1. Als u Application Insights nog niet in uw web-app hebt [geïnstalleerd,](../../azure-monitor/azure-monitor-app-hub.yml) doet u dat nu.
2. Naast de standaard Application Insights-pakketten is [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) vereist om Live Metrics stream in te schakelen.
3. **Update naar de nieuwste versie** van het Application Insights-pakket. Klik in Visual Studio met de rechtermuisknop op uw project en kies **Nuget-pakketten beheren.** Open het tabblad **Updates** en selecteer alle pakketten Microsoft.ApplicationInsights.*

    Implementeer uw app opnieuw.

3. Open in de [Azure-portal](https://portal.azure.com)de bron Application Insights voor uw app en open Live Stream.

4. [Beveilig het controlekanaal](#secure-the-control-channel) als u gevoelige gegevens zoals klantnamen in uw filters gebruiken.

### <a name="no-data-check-your-server-firewall"></a>Zijn er geen gegevens? Uw serverfirewall controleren

Controleer of de [uitgaande poorten voor Live Metrics Stream](../../azure-monitor/app/ip-addresses.md#outgoing-ports) zijn geopend in de firewall van uw servers.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Hoe verschilt Live Metrics Stream van Metrics Explorer en Analytics?

| |Live Stream | Metrics Explorer en Analytics |
|---|---|---|
|Latentie|Gegevens die binnen een seconde worden weergegeven|Geaggregeerd over minuten|
|Geen retentie|Gegevens blijven bestaan terwijl deze in de grafiek staan en worden vervolgens verwijderd|[Gegevens die 90 dagen worden bewaard](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|Op aanvraag|Gegevens worden gestreamd terwijl u Live Metrics opent|Gegevens worden verzonden wanneer de SDK wordt geïnstalleerd en ingeschakeld|
|Gratis|Er zijn geen kosten verbonden aan Live Stream-gegevens|Onder voorbehoud [van prijzen](../../azure-monitor/app/pricing.md)
|Steekproeven|Alle geselecteerde statistieken en tellers worden verzonden. Fouten en stapelsporen worden bemonsterd. TelemetrieProcessors worden niet toegepast.|Gebeurtenissen kunnen worden [bemonsterd](../../azure-monitor/app/api-filtering-sampling.md)|
|Controlekanaal|Filterbesturingssignalen worden naar de SDK verzonden. We raden je aan dit kanaal te beveiligen.|Communicatie is een manier, naar het portaal|

## <a name="select-and-filter-your-metrics"></a>Uw statistieken selecteren en filteren

(Beschikbaar met ASP.NET, ASP.NET Core en Azure-functies (v2).)

U aangepaste KPI live controleren door willekeurige filters toe te passen op elke Application Insights-telemetrie van de portal. Klik op het filterbesturingselement dat wordt weergegeven wanneer u een van de grafieken met de muis overneemt. In de volgende grafiek wordt een aangepaste KPI voor het aantal aanvragen uitgezet met filters op URL- en duurkenmerken. Valideer uw filters met de sectie Stream Preview die een live feed van telemetrie weergeeft die overeenkomt met de criteria die u op elk moment hebt opgegeven.

![Aangepaste aanvraag-KPI](./media/live-stream/live-stream-filteredMetric.png)

U een andere waarde dan Count controleren. De opties zijn afhankelijk van het type stream, dat elke Telemetrie van Application Insights kan zijn: aanvragen, afhankelijkheden, uitzonderingen, traces, gebeurtenissen of metrische gegevens. Het kan uw eigen [aangepaste meting](../../azure-monitor/app/api-custom-events-metrics.md#properties)zijn:

![Waardeopties](./media/live-stream/live-stream-valueoptions.png)

Naast De telemetrie van Application Insights u ook elk Windows-prestatiemeterrecht controleren door dat uit de streamopties te selecteren en de naam van het prestatiemeterrecht op te geven.

Live metrics worden op twee punten samengevoegd: lokaal op elke server en vervolgens op alle servers. U de standaardinstelling wijzigen door andere opties in de respectievelijke vervolgkeuzeklassen te selecteren.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Voorbeeld telemetrie: aangepaste diagnostische gebeurtenissen voor live
Standaard worden in de live feed van gebeurtenissen voorbeelden weergegeven van mislukte aanvragen en afhankelijkheidsoproepen, uitzonderingen, gebeurtenissen en traces. Klik op het filterpictogram om de toegepaste criteria op elk moment weer te geven. 

![Standaard live feed](./media/live-stream/live-stream-eventsdefault.png)

Net als bij metrische gegevens u willekeurige criteria opgeven voor een van de telemetrietypen van Application Insights. In dit voorbeeld selecteren we specifieke aanvraagfouten, traces en gebeurtenissen. We selecteren ook alle uitzonderingen en afhankelijkheidsfouten.

![Aangepaste live feed](./media/live-stream/live-stream-events.png)

Opmerking: Gebruik voor criteria op basis van uitzondering momenteel het bericht voor de meest buitenste uitzondering. In het voorgaande voorbeeld, om te filteren op de goedaardige uitzondering met innerlijke uitzondering bericht (volgt de "< --" scheidingsteken) "De client losgekoppeld." gebruik een bericht dat geen criteria bevat met de criteria 'Inhoud van de foutleesaanvraag'.

Bekijk de details van een item in de live feed door erop te klikken. U de feed onderbreken door op **Onderbreken** te klikken of gewoon naar beneden te scrollen of door op een item te klikken. Live feed wordt hervat nadat je terug naar boven hebt gescrolld, of door op de teller te klikken van items die zijn verzameld terwijl deze werd onderbroken.

![Gesamplede live-fouten](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filteren op serverinstantie

Als u een bepaalde serverrolinstantie wilt controleren, u filteren op server.

![Gesamplede live-fouten](./media/live-stream/live-stream-filter.png)

## <a name="secure-the-control-channel"></a>Het bedieningskanaal beveiligen
De aangepaste filterscriteria die u opgeeft, worden teruggestuurd naar de component Live Metrics in de Application Insights SDK. De filters kunnen mogelijk gevoelige informatie bevatten, zoals klant-id's. U het kanaal beveiligen met een geheime API-sleutel naast de instrumentatiesleutel.
### <a name="create-an-api-key"></a>Een API-sleutel maken

![API-sleutel maken](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>API-sleutel toevoegen aan configuratie

### <a name="classic-aspnet"></a>Klassieke ASP.NET

Voeg in het bestand applicationinsights.config de AuthenticationApiKey toe aan de QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Of in code, stel het in op de QuickPulseTelemetryModule:

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;

             TelemetryConfiguration configuration = new TelemetryConfiguration();
            configuration.InstrumentationKey = "YOUR-IKEY-HERE";

            QuickPulseTelemetryProcessor processor = null;

            configuration.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    processor = new QuickPulseTelemetryProcessor(next);
                    return processor;
                })
                        .Build();

            var QuickPulse = new QuickPulseTelemetryModule()
            {

                AuthenticationApiKey = "YOUR-API-KEY"
            };
            QuickPulse.Initialize(configuration);
            QuickPulse.RegisterTelemetryProcessor(processor);
            foreach (var telemetryProcessor in configuration.TelemetryProcessors)
                {
                if (telemetryProcessor is ITelemetryModule telemetryModule)
                    {
                    telemetryModule.Initialize(configuration);
                    }
                }

```

### <a name="azure-function-apps"></a>Azure Function-apps

Voor Azure Function Apps (v2) kan het beveiligen van het kanaal met een API-sleutel worden uitgevoerd met een omgevingsvariabele.

Maak een API-sleutel vanuit uw Application Insights-bron en ga naar **Toepassingsinstellingen** voor uw functie-app. Selecteer **nieuwe instelling toevoegen** en `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` voer een naam in van en een waarde die overeenkomt met uw API-sleutel.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-or-greater"></a>ASP.NET Core (vereist toepassingsinzichten ASP.NET Core SDK 2.3.0 of hoger)

Wijzig het startup.cs als volgt:

Eerste toevoegen

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Voeg vervolgens binnen de methode ConfigureServices toe:

```csharp
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Als u echter alle verbonden servers herkent en vertrouwt, u de aangepaste filters proberen zonder het geverifieerde kanaal. Deze optie is beschikbaar voor zes maanden. Deze overschrijving is vereist zodra elke nieuwe sessie, of wanneer een nieuwe server online komt.

![Opties voor Live Metrics Auth](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>We raden je ten zeerste aan het geverifieerde kanaal in te stellen voordat je mogelijk gevoelige informatie zoals CustomerID in de filtercriteria invoert.
>

## <a name="supported-features-table"></a>Tabel ondersteunde functies

| Taal                         | Basisstatistieken       | Metrische gegevens voor prestaties | Aangepaste filtering    | Voorbeeld telemetrie    | CPU gesplitst per proces |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET                             | Ondersteund (V2.7.2+) | Ondersteund (V2.7.2+) | Ondersteund (V2.7.2+) | Ondersteund (V2.7.2+) | Ondersteund (V2.7.2+)  |
| .NET Core (target=.NET Framework)| Ondersteund (V2.4.1+) | Ondersteund (V2.4.1+) | Ondersteund (V2.4.1+) | Ondersteund (V2.4.1+) | Ondersteund (V2.4.1+)  |
| .NET Core (target=.NET Core)     | Ondersteund (V2.4.1+) | Ondersteund*          | Ondersteund (V2.4.1+) | Ondersteund (V2.4.1+) | **Niet ondersteund**    |
| Azure-functies v2               | Ondersteund           | Ondersteund           | Ondersteund           | Ondersteund           | **Niet ondersteund**    |
| Java                             | Ondersteund (V2.0.0+) | Ondersteund (V2.0.0+) | **Niet ondersteund**   | **Niet ondersteund**   | **Niet ondersteund**    |
| Node.js                          | Ondersteund (V1.3.0+) | Ondersteund (V1.3.0+) | **Niet ondersteund**   | Ondersteund (V1.3.0+) | **Niet ondersteund**    |

Basisstatistieken zijn aanvraag- en afhankelijkheids- en uitzonderingspercentage. Prestatiestatistieken (prestatiemeteritems) omvatten geheugen en CPU. Voorbeeldtelemetrie toont een stroom van gedetailleerde informatie voor mislukte aanvragen en afhankelijkheden, uitzonderingen, gebeurtenissen en sporen.

 \*PerfCounters-ondersteuning varieert enigszins tussen versies van .NET Core die niet op het .NET Framework zijn gericht:

- PerfCounters-statistieken worden ondersteund wanneer ze worden uitgevoerd in Azure App Service voor Windows. (AspNetCore SDK Versie 2.4.1 of hoger)
- PerfTellers worden ondersteund wanneer de app wordt uitgevoerd in Windows-machines (VM of Cloud Service of On-prem etc.) (AspNetCore SDK Versie 2.7.1 of hoger), maar voor apps die zich richten op .NET Core 2.0 of hoger.
- PerfCounters worden ondersteund wanneer de app overal draait (Linux, Windows, app-service voor Linux, containers, enz.) in de nieuwste bèta (d.w.z. AspNetCore SDK Versie 2.8.0-beta1 of hoger), maar voor apps die zich richten op .NET Core 2.0 of hoger.

Live Metrics is standaard uitgeschakeld in de Node.js SDK. Als u Live `setSendLiveMetrics(true)` Metrics wilt inschakelen, voegt u toe aan uw [configuratiemethoden](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) terwijl u de SDK initialiseert.

## <a name="troubleshooting"></a>Problemen oplossen

Zijn er geen gegevens? Als uw toepassing zich in een beveiligd netwerk bevindt: Live Metrics Stream gebruikt andere IP-adressen dan andere telemetrie van Application Insights. Zorg ervoor dat [deze IP-adressen](../../azure-monitor/app/ip-addresses.md) zijn geopend in uw firewall.

## <a name="next-steps"></a>Volgende stappen
* [Gebruik controleren met Application Insights](../../azure-monitor/app/usage-overview.md)
* [Diagnostisch zoeken gebruiken](../../azure-monitor/app/diagnostic-search.md)
* [Profiler](../../azure-monitor/app/profiler.md)
* [Snapshot Debugger](../../azure-monitor/app/snapshot-debugger.md)
