---
title: Hoe kan ik ... in Azure Application Insights | Microsoft Documenten
description: Veelgestelde vragen in Application Insights.
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 8d4b1e79c48b14ed7dce756468e4c48d633c3f04
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536859"
---
# <a name="how-do-i--in-application-insights"></a>Hoe kan ik ... in Application Insights?
## <a name="get-an-email-when-"></a>Ontvang een e-mail wanneer ...
### <a name="email-if-my-site-goes-down"></a>E-mail als mijn site naar beneden gaat
Stel een [beschikbaarheidswebtest](../../azure-monitor/app/monitor-web-app-availability.md)in .

### <a name="email-if-my-site-is-overloaded"></a>E-mail als mijn site overbelast is
Stel een [waarschuwing](../../azure-monitor/app/alerts.md) in op **de reactietijd van de server**. Een drempel tussen 1 en 2 seconden moet werken.

![](./media/how-do-i/030-server.png)

Uw app kan ook tekenen van spanning vertonen door foutcodes terug te sturen. Stel een waarschuwing in op **mislukte aanvragen**.

Als u een waarschuwing wilt instellen voor **serveruitzonderingen,** moet u mogelijk [een extra installatie](../../azure-monitor/app/asp-net-exceptions.md) doen om gegevens te kunnen zien.

### <a name="email-on-exceptions"></a>E-mail over uitzonderingen
1. [Uitzonderingsbewaking instellen](../../azure-monitor/app/asp-net-exceptions.md)
2. [Een waarschuwing instellen](../../azure-monitor/app/alerts.md) op de statistiek Uitzonderingstelling

### <a name="email-on-an-event-in-my-app"></a>E-mail over een evenement in mijn app
Stel dat u een e-mail wilt ontvangen wanneer een specifieke gebeurtenis plaatsvindt. Application Insights biedt deze faciliteit niet rechtstreeks, maar kan [wel een waarschuwing verzenden wanneer een statistiek een drempel overschrijdt.](../../azure-monitor/app/alerts.md)

Waarschuwingen kunnen worden ingesteld op [aangepaste statistieken,](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric)maar niet op aangepaste gebeurtenissen. Schrijf een code om een statistiek te verhogen wanneer de gebeurtenis optreedt:

    telemetry.TrackMetric("Alarm", 10);

of:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Omdat waarschuwingen twee statussen hebben, moet u een lage waarde verzenden wanneer u van mening bent dat de waarschuwing is beëindigd:

    telemetry.TrackMetric("Alarm", 0.5);

Maak een grafiek in [de statistiekverkenner](../../azure-monitor/platform/metrics-charts.md) om uw alarm te zien:

![](./media/how-do-i/010-alarm.png)

Stel nu een waarschuwing in om te worden afgestoken wanneer de statistiek gedurende een korte periode boven een gemiddelde waarde gaat:

![](./media/how-do-i/020-threshold.png)

Stel de gemiddelde periode in op het minimum.

U ontvangt e-mails, zowel wanneer de statistiek boven als onder de drempelwaarde gaat.

Enkele punten om in overweging te nemen:

* Een waarschuwing heeft twee toestanden ('alert' en 'gezond'). De status wordt alleen geëvalueerd wanneer een statistiek wordt ontvangen.
* Een e-mail wordt alleen verzonden wanneer de status verandert. Dit is de reden waarom je zowel hoge als lage waarde statistieken moet verzenden.
* Om de waarschuwing te evalueren, wordt het gemiddelde genomen van de ontvangen waarden in de voorgaande periode. Dit gebeurt elke keer dat een statistiek wordt ontvangen, zodat e-mails vaker kunnen worden verzonden dan de periode die u instelt.
* Aangezien e-mails worden verzonden zowel op "alert" en "gezond", wilt u misschien overwegen opnieuw te denken uw one-shot gebeurtenis als een twee-state conditie. Bijvoorbeeld, in plaats van een "job completed" gebeurtenis, hebben een "job in progress" voorwaarde, waar je e-mails aan het begin en einde van een taak.

### <a name="set-up-alerts-automatically"></a>Waarschuwingen automatisch instellen
[PowerShell gebruiken om nieuwe waarschuwingen te maken](../../azure-monitor/app/alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>PowerShell gebruiken om toepassingsinzichten te beheren
* [Nieuwe bronnen maken](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically)
* [Nieuwe waarschuwingen maken](../../azure-monitor/app/alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Gescheiden telemetrie van verschillende versies

* Meerdere rollen in een app: gebruik één Application Insights-bron en filter op [cloud_Rolename.](../../azure-monitor/app/app-map.md)
* Ontwikkel-, test- en releaseversies scheiden: gebruik verschillende Application Insights-bronnen. Pak de instrumentatietoetsen van web.config. [Meer weten?](../../azure-monitor/app/separate-resources.md)
* Build-versies rapporteren: een eigenschap toevoegen met behulp van een telemetrieinitialisator. [Meer informatie](../../azure-monitor/app/separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Backend-servers en bureaublad-apps bewaken
[Gebruik de Windows Server SDK-module](../../azure-monitor/app/windows-desktop.md).

## <a name="visualize-data"></a>Gegevens visualiseren
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Dashboard met statistieken uit meerdere apps
* Pas in [Metric Explorer](../../azure-monitor/platform/metrics-charts.md)uw grafiek aan en sla deze op als favoriet. Maak deze vast aan het Azure-dashboard.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Dashboard met gegevens uit andere bronnen en Application Insights
* [Telemetrie exporteren naar Power BI](../../azure-monitor/app/export-power-bi.md ).

of

* Gebruik SharePoint als uw dashboard en geef gegevens weer in SharePoint-webonderdelen. [Gebruik continue export en Stream Analytics om naar SQL te exporteren.](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md)  Gebruik PowerView om de database te onderzoeken en maak een SharePoint-webonderdeel voor PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Anonieme of geverifieerde gebruikers filteren
Als uw gebruikers zich aanmelden, u de [geverifieerde gebruikersnaam](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)instellen. (Het gebeurt niet automatisch.)

U kunt dan:

* Zoeken op specifieke gebruikersnamen

![](./media/how-do-i/110-search.png)

* Statistieken filteren op anonieme of geverifieerde gebruikers

![](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Eigenschapnamen of -waarden wijzigen
Een [filter maken](../../azure-monitor/app/api-filtering-sampling.md#filtering). Hiermee u telemetrie wijzigen of filteren voordat deze vanuit uw app naar Application Insights wordt verzonden.

## <a name="list-specific-users-and-their-usage"></a>Specifieke gebruikers en hun gebruik weergeven
Als u alleen naar specifieke gebruikers wilt [zoeken,](#search-specific-users)u de [geverifieerde gebruikersnaam](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)instellen.

Als u een lijst met gebruikers met gegevens wilt, zoals naar welke pagina's ze kijken of hoe vaak ze inloggen, hebt u twee opties:

* [Stel geverifieerde gebruikersnaam in,](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) [exporteer naar een database](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) en gebruik geschikte tools om daar uw gebruikersgegevens te analyseren.
* Als u slechts een klein aantal gebruikers hebt, verzendt u aangepaste gebeurtenissen of statistieken, gebruikt u de gegevens van belang als metrische waarde of gebeurtenisnaam en stelt u de gebruikers-id in als eigenschap. Als u paginaweergaven wilt analyseren, vervangt u de standaard JavaScript-trackPageView-aanroep. Als u telemetrie aan de serverzijde wilt analyseren, gebruikt u een telemetrieinitialisator om de gebruikers-id toe te voegen aan alle servertelemetrie. U vervolgens statistieken en zoekopdrachten filteren en segmenteren op de gebruikersnaam.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Verkeer van mijn app naar Toepassingsinzichten verminderen
* In [ApplicationInsights.config,](../../azure-monitor/app/configuration-with-applicationinsights-config.md)alle modules die u niet nodig hebt, zoals de prestaties teller collector.
* Gebruik [Sampling en filtering](../../azure-monitor/app/api-filtering-sampling.md) bij de SDK.
* Beperk in uw webpagina's het aantal Ajax-oproepen dat wordt gerapporteerd voor elke paginaweergave. In het scriptfragment `instrumentationKey:...` na `,maxAjaxCallsPerView:3` , invoegen: (of een geschikt nummer).
* Als u [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric)gebruikt, berekent u het totaal van batches metrische waarden voordat u het resultaat verzendt. Er is een overbelasting van TrackMetric() die daarvoor zorgt.

Meer informatie over [prijzen en quota](../../azure-monitor/app/pricing.md).

## <a name="disable-telemetry"></a>Telemetrie uitschakelen
Ga als u de verzameling en overdracht van telemetrie vanaf de server **dynamisch stop en start:**

### <a name="aspnet-classic-applications"></a>ASP.NET Classic-toepassingen

```csharp
    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>Andere toepassingen
Het wordt afgeraden `TelemetryConfiguration.Active` om singleton te gebruiken op console- of ASP.NET Core-toepassingen.
als u `TelemetryConfiguration` zelf een `DisableTelemetry` `true`exemplaar hebt gemaakt - ingesteld op .

Voor ASP.NET Core-toepassingen `TelemetryConfiguration` u toegang krijgen tot instantie met behulp van [ASP.NET Core-afhankelijkheidsinjectie.](/aspnet/core/fundamentals/dependency-injection/) Meer informatie vindt u in het artikel [Van ApplicationInsights voor ASP.NET Core-toepassingen.](../../azure-monitor/app/asp-net-core.md)

## <a name="disable-selected-standard-collectors"></a>Geselecteerde standaardverzamelobjecten uitschakelen
U standaardverzamelingen uitschakelen (bijvoorbeeld prestatiemeteritems, HTTP-aanvragen of afhankelijkheden)

* **ASP.NET toepassingen** - Verwijder of reageer op de relevante regels in [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)
* **ASP.NET Core-toepassingen** - Volg de configuratieopties voor telemetriemodules in [ApplicationInsights ASP.NET Core](../../azure-monitor/app/asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="view-system-performance-counters"></a>Systeemprestatiemeteritems weergeven
Een van de statistieken die u weergeven in metrics explorer zijn een set systeemprestatiemeteritems. Er is een vooraf gedefinieerd blad met de titel **Servers** dat een aantal van hen weergeeft.

![Uw Application Insights-bron openen en op Servers klikken](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Als u geen prestatiemetergegevens ziet
* **IIS-server** op uw eigen machine of op een VM. [Statusmonitor installeren](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Azure-website** - we ondersteunen nog geen prestatiemeteritems. Er zijn verschillende statistieken die u als standaardonderdeel van het azure-beheerpaneel van de website krijgen.
* **Unix-server** - [installeren verzameld](../../azure-monitor/app/java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Meer prestatiemeteritems weergeven
* Voeg eerst [een nieuwe grafiek toe](../../azure-monitor/platform/metrics-charts.md) en kijk of de teller zich in de basisset bevindt die we aanbieden.
* Zo niet, [voeg dan de teller toe aan de set die wordt verzameld door de prestatietellermodule](../../azure-monitor/app/performance-counters.md).
