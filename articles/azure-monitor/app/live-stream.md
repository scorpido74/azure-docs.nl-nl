---
title: Live Metrics Stream met aangepaste metrische gegevens en diagnostische gegevens in Azure-toepassing inzichten | Microsoft Docs
description: Bewaak uw web-app in realtime met aangepaste metrische gegevens en stel problemen vast met een live feed van fouten, traceringen en gebeurtenissen.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: d85688d297eb0df00e71f388b2a3350eabe5f6d5
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817196"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: controleren & diagnose met een latentie van 1 seconde

Test het opvallende hart van uw Live, in-productie webtoepassing met behulp van Live Metrics Stream van [Application Insights](../../azure-monitor/app/app-insights-overview.md). Selecteer en filter metrische gegevens en prestatie meter items die u in realtime wilt bekijken, zonder dat u uw service hoeft te verstoren. Inspecteer stack traceringen van voor beelden van mislukte aanvragen en uitzonde ringen. Samen met [Profiler](../../azure-monitor/app/profiler.md)is het [fout opsporingsprogramma voor moment opnamen](../../azure-monitor/app/snapshot-debugger.md). Live Metrics Stream biedt een krachtig en niet-invasief diagnostisch hulp programma voor uw Live website.

Met Live Metrics Stream kunt u het volgende doen:

* Valideer een oplossing tijdens de release, door prestatie-en fout aantallen te bekijken.
* Bekijk het effect van de test belasting en stel problemen vast. 
* Richt u op bepaalde test sessies of filter bekende problemen door de metrische gegevens die u wilt bekijken, te selecteren en te filteren.
* Ontvang uitzonderings traceringen wanneer ze plaatsvinden.
* Experimenteer met filters om de meest relevante Kpi's te vinden.
* Bewaak elk Windows-prestatie meter item Live.
* U kunt eenvoudig een server identificeren die problemen ondervindt en alle KPI/live-feed filteren op alleen die server.

[video van![Live Metrics Stream](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Live metrics worden momenteel ondersteund voor ASP.NET-, ASP.NET Core-, Azure Functions-, Java-en node. js-apps.

## <a name="get-started"></a>Aan de slag

1. Als u Application Insights nog niet hebt [geïnstalleerd](../../azure-monitor/azure-monitor-app-hub.md) in uw web-app, doet u dat nu.
2. Naast de Standard Application Insights-pakketten [micro soft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) is vereist voor het inschakelen van Live Metrics stream.
3. **Update naar de nieuwste versie** van het Application Insights-pakket. Klik in Visual Studio met de rechter muisknop op het project en kies **Nuget-pakketten beheren**. Open het tabblad **updates** en selecteer alle micro soft. ApplicationInsights. *-pakketten.

    Implementeer uw app opnieuw.

3. Open in de [Azure Portal](https://portal.azure.com)de Application Insights resource voor uw app en open Live Stream.

4. [Beveilig het besturings kanaal](#secure-the-control-channel) als u gevoelige gegevens zoals klant namen in uw filters kunt gebruiken.

### <a name="nodejs"></a>Node.js

Als u live metrieken met node. js wilt gebruiken, moet u bijwerken naar versie 1,30 of hoger van de SDK. Dynamische metrische gegevens worden standaard uitgeschakeld in de node. js-SDK. Als u live metrics wilt inschakelen, voegt u `setSendLiveMetrics(true)` toe aan uw [configuratie methoden](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) tijdens het initialiseren van de SDK.

### <a name="no-data-check-your-server-firewall"></a>Geen gegevens? Controleer de firewall van uw server

Controleer of de [uitgaande poorten voor Live Metrics stream](../../azure-monitor/app/ip-addresses.md#outgoing-ports) zijn geopend in de firewall van uw servers. 

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Hoe verschilt Live Metrics Stream van Metrics Explorer en Analytics?

| |Live Stream | Metrics Explorer en analyse |
|---|---|---|
|Latentie|Gegevens die binnen één seconde worden weer gegeven|Geaggregeerd over minuten|
|Geen Bewaar periode|De gegevens blijven behouden in de grafiek en vervolgens verwijderd|[Gegevens die gedurende 90 dagen worden bewaard](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|Op aanvraag|Gegevens worden gestreamd tijdens het openen van Live metrieken|Er worden gegevens verzonden wanneer de SDK is geïnstalleerd en ingeschakeld|
|Gratis|Er worden geen kosten in rekening gebracht voor Live Stream gegevens|Onderworpen aan [prijzen](../../azure-monitor/app/pricing.md)
|Sampling|Alle geselecteerde metrische gegevens en tellers worden verzonden. Voor beelden van fouten en stack traceringen zijn. TelemetryProcessors worden niet toegepast.|Gebeurtenissen kunnen worden [bemonsterd](../../azure-monitor/app/api-filtering-sampling.md)|
|Besturings kanaal|Filter besturings signalen worden naar de SDK verzonden. U wordt aangeraden dit kanaal te beveiligen.|Communicatie in één richting, naar de portal|

## <a name="select-and-filter-your-metrics"></a>Uw metrische gegevens selecteren en filteren

(Beschikbaar met ASP.NET, ASP.NET Core en Azure Functions (v2).)

U kunt een aangepaste KPI Live bewaken door wille keurige filters toe te passen op Application Insights telemetrie vanuit de portal. Klik op het filter besturings element dat laat zien wanneer u met de muis aanwijzer op een van de grafieken klikt. In het volgende diagram wordt een KPI van een aangepast aanvraag aantal getekend met filters voor de kenmerken URL en duration. Valideer uw filters met behulp van de sectie voor beeld van de stream, waarin een live-feed van de telemetrie wordt weer gegeven die overeenkomt met de criteria die u op een bepaald moment hebt opgegeven. 

![KPI aangepaste aanvraag](./media/live-stream/live-stream-filteredMetric.png)

U kunt een andere waarde dan aantal bewaken. De opties zijn afhankelijk van het type stream. Dit kan elke Application Insights telemetrie zijn: aanvragen, afhankelijkheden, uitzonde ringen, traceringen, gebeurtenissen of metrische gegevens. Dit kan uw eigen [aangepaste meting](../../azure-monitor/app/api-custom-events-metrics.md#properties)zijn:

![Waarde-opties](./media/live-stream/live-stream-valueoptions.png)

Naast Application Insights telemetrie, kunt u ook elk prestatie meter item van Windows controleren door dat te selecteren in de stroom opties en de naam van het prestatie meter item op te geven.

Live-metrische gegevens worden op twee punten geaggregeerd: lokaal op elke server en vervolgens op alle servers. U kunt de standaard instelling wijzigen door andere opties te selecteren in de vervolg keuzelijsten.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Voor beeld-telemetrie: aangepaste Live diagnostische gebeurtenissen
De live feed van gebeurtenissen toont standaard voor beelden van mislukte aanvragen en afhankelijkheids aanroepen, uitzonde ringen, gebeurtenissen en traceringen. Klik op het filter pictogram om de toegepaste criteria op elk gewenst moment weer te geven. 

![Standaard live-feed](./media/live-stream/live-stream-eventsdefault.png)

Net als bij metrische gegevens kunt u wille keurige criteria opgeven voor elk van de Application Insights typen telemetrie. In dit voor beeld selecteren we specifieke aanvraag fouten, traceringen en gebeurtenissen. We selecteren ook alle uitzonde ringen en afhankelijkheids fouten.

![Aangepaste live-feed](./media/live-stream/live-stream-events.png)

Opmerking: op dit moment kunt u voor op berichten gebaseerde criteria het buitenste uitzonderings bericht gebruiken. In het vorige voor beeld kunt u de onschadelijke uitzonde ring filteren met een intern uitzonderings bericht (de ' <--' scheidings teken) ' de client heeft de verbinding verbroken '. Gebruik een bericht dat niet de criteria ' fout bij het lezen van aanvragen van inhoud ' bevat.

Bekijk de details van een item in de live feed door erop te klikken. U kunt de feed onderbreken door te klikken op **onderbreken** of omlaag schuiven of op een item te klikken. Live feed wordt hervat wanneer u weer naar boven schuift of door te klikken op het item van items die worden verzameld terwijl het is onderbroken.

![Voorbeeld Live-fouten](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filteren op Server exemplaar

Als u een bepaalde serverrol wilt bewaken, kunt u filteren op server.

![Voorbeeld Live-fouten](./media/live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>SDK-vereisten

### <a name="net"></a>.NET
Aangepaste Live Metrics Stream is beschikbaar met versie 2.4.0-beta2 of hoger van [Application INSIGHTS SDK voor Internet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Vergeet niet om de optie ' include Prerelease ' te selecteren in NuGet Package Manager.

### <a name="nodejs"></a>Node.js
Live Metrics Stream is beschikbaar met versie 1.3.0 of hoger van de [Application INSIGHTS SDK voor node. js](https://npmjs.com/package/applicationinsights). Vergeet niet om `setSendLiveMetrics(true)` te gebruiken tijdens het configureren van de SDK in uw code.

## <a name="secure-the-control-channel"></a>Het besturings kanaal beveiligen
De criteria voor aangepaste filters die u opgeeft, worden teruggestuurd naar het onderdeel Live Metrics in de SDK van Application Insights. De filters kunnen mogelijk gevoelige informatie bevatten, zoals customerIDs. U kunt het kanaal veilig maken met een geheime API-sleutel naast de instrumentatie sleutel.
### <a name="create-an-api-key"></a>Een API-sleutel maken

![API-sleutel maken](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>API-sleutel aan configuratie toevoegen

### <a name="classic-aspnet"></a>Klassieke ASP.NET

Voeg in het bestand applicationinsights. config de AuthenticationApiKey toe aan de QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Of in code, stelt u deze in op de QuickPulseTelemetryModule:

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

### <a name="azure-function-apps"></a>Azure function-apps

Voor Azure function-apps (v2) die het kanaal beveiligen met een API-sleutel kan worden uitgevoerd met een omgevings variabele. 

Maak een API-sleutel vanuit uw Application Insights-resource en ga naar **Toepassings instellingen** voor uw functie-app. Selecteer **nieuwe instelling toevoegen** en voer een naam in voor `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` en een waarde die overeenkomt met uw API-sleutel.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-beta-or-greater"></a>ASP.NET Core (vereist Application Insights ASP.NET Core SDK 2.3.0-bèta of hoger)

Wijzig uw startup.cs-bestand als volgt:

Eerst toevoegen

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Vervolgens voegt u de methode ConfigureServices toe:

```csharp
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Als u echter alle verbonden servers herkent en vertrouwt, kunt u de aangepaste filters proberen zonder het geverifieerde kanaal. Deze optie is zes maanden beschikbaar. Deze onderdrukking is vereist na elke nieuwe sessie of wanneer een nieuwe server online is.

![Verificatie opties voor Live Metrics](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>We raden u ten zeerste aan het geverifieerde kanaal in te stellen voordat u mogelijk gevoelige informatie, zoals KlantId, in de filter criteria invoert.
>

## <a name="troubleshooting"></a>Problemen oplossen

Geen gegevens? Als uw toepassing zich in een beveiligd netwerk bevindt: Live Metrics Stream gebruikt een andere IP-adres dan andere Application Insights telemetrie. Zorg ervoor dat [deze IP-adressen](../../azure-monitor/app/ip-addresses.md) zijn geopend in uw firewall.



## <a name="next-steps"></a>Volgende stappen
* [Gebruik controleren met Application Insights](../../azure-monitor/app/usage-overview.md)
* [Diagnostische Zoek opdrachten gebruiken](../../azure-monitor/app/diagnostic-search.md)
* [Profiler](../../azure-monitor/app/profiler.md)
* [Fout opsporing voor moment opnamen](../../azure-monitor/app/snapshot-debugger.md)
