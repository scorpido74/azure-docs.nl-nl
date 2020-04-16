---
title: Gebruik en kosten voor Azure Application Insights beheren | Microsoft Documenten
description: Telemetrievolumes beheren en kosten bewaken in Application Insights.
ms.topic: conceptual
author: DaleKoetke
ms.author: dalek
ms.date: 11/27/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0225484de06ae4e595f1dcbcdd520f4e0e4d53f5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405379"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Gebruik en kosten van Application Insights beheren

> [!NOTE]
> In dit artikel wordt beschreven hoe u uw kosten voor Application Insights begrijpt en beheert.  In een gerelateerd artikel worden [het gebruik en de geschatte kosten controleren](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) beschreven hoe u het gebruik en de geschatte kosten voor meerdere Azure-bewakingsfuncties voor verschillende prijsmodellen weergeven.

Application Insights is ontworpen om alles te krijgen wat u nodig hebt om de beschikbaarheid, prestaties en het gebruik van uw webtoepassingen te controleren, of ze nu worden gehost op Azure of on-premises. Application Insights ondersteunt populaire talen en frameworks, zoals .NET, Java en Node.js, en integreert met DevOps-processen en -hulpprogramma's zoals Azure DevOps, Jira en PagerDuty. Het is belangrijk om te begrijpen wat de kosten van het bewaken van uw toepassingen bepaalt. In dit artikel bekijken we wat de kosten voor het bewaken van uw toepassing stimuleert en hoe u deze proactief controleren en controleren.

Als je vragen hebt over hoe prijzen werken voor Application Insights, kun je een vraag plaatsen in ons [forum.](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights&filter=alltypes&sort=lastpostdesc)

## <a name="pricing-model"></a>Prijsmodel

De prijzen voor [Azure Application Insights][start] zijn een **Pay-As-You-Go-model** op basis van het ingenomen gegevensvolume en optioneel voor langere gegevensbewaring. Elke Application Insights-bron wordt in rekening gebracht als een afzonderlijke service en draagt bij aan de factuur voor uw Azure-abonnement. Het gegevensvolume wordt gemeten als de grootte van het niet-gecomprimeerde JSON-gegevenspakket dat wordt ontvangen door Application Insights van uw toepassing. Er worden geen kosten in rekening gebracht voor het gebruik van de [Live Metrics Stream](../../azure-monitor/app/live-stream.md).

[Multi-step webtests](../../azure-monitor/app/availability-multistep.md) brengen extra kosten met zich mee. Webtests met meerdere stappen zijn webtests die een reeks acties uitvoeren. Er zijn geen afzonderlijke kosten voor *ping-tests* van één pagina. Telemetrie van ping-tests en multi-step tests wordt in rekening gebracht hetzelfde als andere telemetrie van uw app.

De optie Toepassingsinzichten om [waarschuwingen voor aangepaste metrische dimensies in](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation) te schakelen, kan ook extra kosten genereren, omdat dit kan leiden tot het maken van extra pre-aggregatiestatistieken. [Meer informatie](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics) over op logboeken gebaseerde en vooraf geaggregeerde statistieken in Toepassingsstatistieken en over [de prijzen](https://azure.microsoft.com/pricing/details/monitor/) voor aangepaste azure-statistieken van Azure Monitor.

## <a name="estimating-the-costs-to-manage-your-application"></a>Het schatten van de kosten voor het beheren van uw aanvraag

Als u Application Insights nog niet gebruikt, u de [prijscalculator azure monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) gebruiken om de kosten van het gebruik van Application Insights te schatten. Begin met het invoeren van 'Azure Monitor' in het vak Zoeken en klik op de resulterende Azure Monitor-tegel. Schuif omlaag naar Azure Monitor en selecteer Toepassingsinzichten in de vervolgkeuzelijst Type.  Hier u het aantal GB gegevens invoeren dat u per maand verwacht te verzamelen, dus de vraag is hoeveel gegevens Application Insights zal verzamelen monitoring van uw toepassing.

Er zijn twee benaderingen om dit aan te pakken: het gebruik van standaardmonitoring en adaptieve bemonstering, die beschikbaar is in de ASP.NET SDK, of schat uw waarschijnlijke gegevensopname op basis van wat andere vergelijkbare klanten hebben gezien.

### <a name="data-collection-when-using-sampling"></a>Gegevensverzameling bij het gebruik van steekproeven

Met de adaptieve [sampling](sampling.md#adaptive-sampling)van de ASP.NET SDK wordt het gegevensvolume automatisch aangepast om binnen een opgegeven maximale verkeerssnelheid te blijven voor standaard Application Insights-monitoring. Als de toepassing een lage hoeveelheid telemetrie produceert, zoals bij het foutopsporing of vanwege een laag gebruik, worden items niet door de bemonsteringsprocessor verwijderd zolang het volume lager is dan de geconfigureerde gebeurtenissen per tweede niveau. Voor een toepassing met een hoog volume, met de standaarddrempel van vijf gebeurtenissen per seconde, beperkt adaptieve bemonstering het aantal dagelijkse gebeurtenissen tot 432.000. Met behulp van een gemiddelde gebeurtenisgrootte van 1 KB komt dit overeen met 13,4 GB telemetrie per 31-daagse maand per knooppunt dat uw toepassing host (omdat de bemonstering lokaal wordt uitgevoerd voor elk knooppunt.) 

Voor SDK's die geen adaptieve sampling ondersteunen, u gebruikmaken van [innamesteekproeven](https://docs.microsoft.com/azure/azure-monitor/app/sampling#ingestion-sampling), die monsters gebruiken wanneer de gegevens worden ontvangen door Application Insights op basis van een percentage gegevens dat moet worden bewaard, of [fixed-rate sampling voor ASP.NET, ASP.NET Core en Java-websites](sampling.md#fixed-rate-sampling) om het verkeer dat wordt verzonden vanaf uw webserver en webbrowsers te verminderen

### <a name="learn-from-what-similar-customers-collect"></a>Leer van wat vergelijkbare klanten verzamelen

Als u in de azure monitoring pricing calculator voor Application Insights de functionaliteit 'Schatting van gegevensvolume op basis van toepassingsactiviteit' inschakelt, u invoer over uw toepassing (aanvragen per maand en paginaweergaven per maand, in het geval u telemetrie aan de clientzijde verzamelt) en vervolgens vertelt de rekenmachine u de mediane en 90e percentielhoeveelheid gegevens die door vergelijkbare toepassingen worden verzameld. Deze toepassingen omvatten het bereik van Application Insights-configuratie (bijvoorbeeld sommige hebben standaard [sampling,](../../azure-monitor/app/sampling.md)sommige hebben geen bemonstering, enz.), dus je hebt nog steeds de controle om het volume van de gegevens die u inneemt ver onder het mediaanniveau met behulp van steekproeven te verminderen. Maar dit is een uitgangspunt om te begrijpen wat andere, soortgelijke klanten zien.

## <a name="understand-your-usage-and-estimate-costs"></a>Inzicht in uw gebruik en raming van de kosten

Application Insights maakt het gemakkelijk om te begrijpen wat uw kosten waarschijnlijk zijn gebaseerd op recente gebruikspatronen. Ga om aan de slag in de Azure-portal voor de resource Application Insights naar de pagina **Gebruik en geschatte kosten:**

![Kies prijzen](./media/pricing/pricing-001.png)

A. Bekijk het gegevensvolume van de maand. Dit omvat alle gegevens die worden ontvangen en bewaard (na een [bemonstering)](../../azure-monitor/app/sampling.md)van uw server- en client-apps en van beschikbaarheidstests.  
B. Er wordt een aparte toeslag gemaakt voor [webtests met meerdere stappen.](../../azure-monitor/app/availability-multistep.md) (Dit omvat geen eenvoudige beschikbaarheidstests, die zijn opgenomen in de kosten voor het gegevensvolume.)  
C. Bekijk trends in het gegevensvolume van de afgelopen maand.  
D. [Gegevensopnamebemonstering inschakelen](../../azure-monitor/app/sampling.md).
E. Stel de dagelijkse datavolumelimiet in.  

(Houd er rekening mee dat alle prijzen die in screenshots in dit artikel worden weergegeven bijvoorbeeld alleen doeleinden zijn. Zie [Prijzen voor Toepassingsstatistieken][pricing]voor actuele prijzen in uw valuta en regio.)

Als u het gebruik van toepassingsinzichten dieper wilt onderzoeken, opent u de pagina **Metrische gegevens,** voegt u de statistiek toe met de naam 'Gegevenspuntvolume' en selecteert u vervolgens de optie *Splitsen toepassen* om de gegevens te splitsen op 'Telemetrieitemtype'.

Kosten voor Application Insights worden toegevoegd aan uw Azure-factuur. U details van uw Azure-factuur bekijken in het **gedeelte Facturering** van de Azure-portal of in de [Azure-factureringsportal](https://account.windowsazure.com/Subscriptions).

![Selecteer Facturering in het linkermenu](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>Gegevensvolumestatistieken gebruiken
<a id="understanding-ingested-data-volume"></a>

Als u meer wilt weten over uw gegevensvolumes, voegt u **Statistieken** voor uw toepassingsstatistiekenbron toe en voegt u een nieuwe grafiek toe. Selecteer **gegevenspuntvolume**voor de grafiekstatistiek onder **Op logboeken gebaseerde metrische gegevens**. Klik **op Splitsen toepassen**en selecteer groep op ** `Telemetryitem` type**.

![Statistieken gebruiken om het gegevensvolume te bekijken](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>Query's om inzicht te krijgen in gegevensvolumedetails

Er zijn twee benaderingen voor het onderzoeken van gegevensvolumes voor Application Insights. De eerste maakt gebruik `systemEvents` van geaggregeerde informatie `_BilledSize` in de tabel en de tweede maakt gebruik van de eigenschap, die beschikbaar is op elke ingenomen gebeurtenis.

#### <a name="using-aggregated-data-volume-information"></a>Geaggregeerde gegevensvolumegegevens gebruiken

U de `systemEvents` tabel bijvoorbeeld gebruiken om het gegevensvolume te zien dat in de afgelopen 24 uur met de query is ingenomen:

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Of om een grafiek met gegevensvolume (in bytes) te zien op gegevenstype van de afgelopen 30 dagen, u het als:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Houd er rekening mee dat deze query kan worden gebruikt in een [Azure Log Alert](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) om waarschuwingen voor gegevensvolumes in te stellen.  

Voor meer informatie over het wijzigen van uw telemetriegegevens kunnen we het aantal gebeurtenissen op basis van de query opvragen:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>Gegevensgrootte per gebeurtenisgegevens gebruiken

Voor meer informatie over de bron van uw `_BilledSize` gegevensvolumes u de eigenschap gebruiken die aanwezig is op elke ingenomen gebeurtenis.

Als we bijvoorbeeld willen kijken welke bewerkingen het meeste gegevensvolume genereren `_BilledSize` in de afgelopen 30 dagen, kunnen we optellen voor alle afhankelijkheidsgebeurtenissen:

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Gebruik van Application Insights weergeven op uw Azure-factuur

Azure biedt veel nuttige functionaliteit in de hub [Azure Cost Management + Billing.](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Met de functionaliteit 'Kostenanalyse' u bijvoorbeeld uw uitgaven voor Azure-resources weergeven. Als u een filter toevoegt op resourcetype (aan microsoft.insights/components voor Application Insights), u uw uitgaven bijhouden.

Meer inzicht in uw gebruik kan worden verkregen door [uw gebruik te downloaden van de Azure-portal.](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)
In de gedownloade spreadsheet u het gebruik per Azure-bron per dag zien. In deze Excel-spreadsheet u het gebruik van uw resources voor Application Insights vinden door eerst te filteren op de kolom 'Metercategorie' om 'Toepassingsinzichten' en 'Logboekanalyse' weer te geven en vervolgens een filter toe te voegen aan de kolom 'Instantie-id' die 'microsoft.insights/components' bevat.  Het meeste gebruik van Application Insights wordt gerapporteerd op meters met de metercategorie van Log Analytics, omdat er één backend voor logboeken is voor alle Azure Monitor-componenten.  Alleen Application Insights-bronnen voor oudere prijsniveaus en webtests in meerdere stappen worden gerapporteerd met een metercategorie met toepassingsinzichten.  Het gebruik wordt weergegeven in de kolom 'Verbruikt aantal' en de eenheid voor elk item wordt weergegeven in de kolom 'Maateenheid'.  Meer details zijn beschikbaar om u inzicht te [geven in uw Microsoft Azure-factuur.](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)

## <a name="managing-your-data-volume"></a>Uw gegevensvolume beheren

De hoeveelheid gegevens die u verzendt, kan worden beheerd met behulp van de volgende technieken:

* **Sampling:** U steekproeven gebruiken om de hoeveelheid telemetrie die wordt verzonden vanaf uw server- en client-apps te verminderen, met minimale vervorming van metrische gegevens. Sampling is het primaire hulpmiddel dat u gebruiken om de hoeveelheid gegevens die u verzendt af te stemmen. Meer informatie over [samplingfuncties](../../azure-monitor/app/sampling.md).

* **Limiet Ajax-oproepen**: Je [het aantal Ajax-oproepen beperken dat](../../azure-monitor/app/javascript.md#configuration) in elke paginaweergave kan worden gerapporteerd, of Ajax-rapportage uitschakelen.

* **Onnodige modules uitschakelen:** [ApplicationInsights.config bewerken](../../azure-monitor/app/configuration-with-applicationinsights-config.md) om verzamelingsmodules uit te schakelen die u niet nodig hebt. U bijvoorbeeld besluiten dat prestatiemeteritems of afhankelijkheidsgegevens niet essentieel zijn.

* **Statistieken vooraf samenvoegen:** Als u oproepen naar TrackMetric in uw app plaatst, u het verkeer verminderen door de overbelasting te gebruiken die uw berekening van de gemiddelde en standaarddeviatie van een batch metingen accepteert. U ook een [pre-aggregating-pakket](https://www.myget.org/gallery/applicationinsights-sdk-labs)gebruiken.
 
* **Dagelijkse limiet:** Wanneer u een Application Insights-bron maakt in de Azure-portal, wordt de dagelijkse limiet ingesteld op 100 GB/dag. Wanneer u een Application Insights-bron maakt in Visual Studio, is de standaardinstelling klein (slechts 32,3 MB/dag). De dagelijkse cap standaard is ingesteld om het testen te vergemakkelijken. Het is de bedoeling dat de gebruiker de dagelijkse limiet verhoogt voordat de app in productie wordt genomen. 

    De maximale limiet is 1.000 GB/dag, tenzij u een hoger maximum aanvraagt voor een toepassing met veel verkeer.
    
    Waarschuwingse-mails over de dagelijkse dop worden verzonden naar account leden van deze rollen voor uw Application Insights-bron: 'ServiceAdmin', 'AccountAdmin', 'CoAdmin', 'Eigenaar'.

    Zorg bij het instellen van de dagelijkse dop. Uw bedoeling zou moeten zijn om *nooit de dagelijkse dop*te raken. Als u de dagelijkse limiet bereikt, verliest u gegevens voor de rest van de dag en u uw toepassing niet controleren. Als u de dagelijkse dop wilt wijzigen, gebruikt u de optie **Dagelijkse volumedop.** U deze optie openen in het deelvenster **Gebruik en geschatte kosten** (dit wordt later in het artikel nader beschreven).
    
    We hebben de beperking voor bepaalde abonnementstypen met krediet verwijderd dat niet kan worden gebruikt voor Application Insights. Voorheen, als het abonnement een bestedingslimiet heeft, heeft het dagelijkse limietdialoogvenster instructies om de bestedingslimiet te verwijderen en de dagelijkse limiet mogelijk te maken om verder te worden verhoogd dan 32,3 MB/dag.
    
* **Beperking**: Beperking beperkt de gegevenssnelheid tot 32.000 gebeurtenissen per seconde, gemiddeld meer dan 1 minuut per instrumentatiesleutel. De hoeveelheid gegevens die uw app verzendt, wordt elke minuut beoordeeld. Als het hoger is dan de per-seconde tarief gemiddeld over de minuut, de server weigert een aantal verzoeken. De SDK buffert de gegevens en probeert deze vervolgens opnieuw te verzenden. Het verspreidt zich een golf over enkele minuten. Als uw app consistent gegevens verzendt met meer dan de beperkingssnelheid, worden sommige gegevens verwijderd. (De ASP.NET, Java en JavaScript SDKs proberen om gegevens opnieuw te verzenden op deze manier; andere SDKs kunnen gewoon drop throttled gegevens.) Als er een beperking optreedt, waarschuwt een melding dat dit is gebeurd.

## <a name="manage-your-maximum-daily-data-volume"></a>Uw maximale dagelijkse gegevensvolume beheren

U de dagelijkse volumelimiet gebruiken om de verzamelde gegevens te beperken. Als de limiet echter wordt voldaan, treedt een verlies op van alle telemetrie die van uw toepassing wordt verzonden voor de rest van de dag. Het is *niet aan* te raden om uw toepassing te laten raken de dagelijkse dop. U de gezondheid en prestaties van uw toepassing niet bijhouden nadat deze de dagelijkse limiet heeft bereikt.

In plaats van de dagelijkse volumedop te gebruiken, gebruikt u [steekproeven](../../azure-monitor/app/sampling.md) om het gegevensvolume af te stemmen op het gewenste niveau. Gebruik vervolgens de dagelijkse dop alleen als "laatste redmiddel" voor het geval uw toepassing onverwacht veel hogere volumes telemetrie begint te verzenden.

### <a name="identify-what-daily-data-limit-to-define"></a>Bepalen welke dagelijkse gegevenslimiet u wilt definiëren

Bekijk het gebruik van toepassingsinzichten en geschatte kosten om inzicht te krijgen in de trend van gegevensopname en wat de dagelijkse volumelimiet is om te definiëren. Het moet met zorg worden overwogen, omdat u uw resources niet controleren nadat de limiet is bereikt.

### <a name="set-the-daily-cap"></a>Stel de dagelijkse limiet in

Als u de dagelijkse limiet wilt wijzigen, selecteert u in het gedeelte **Configureren** van uw toepassingsstatistieken op de pagina **Gebruik en geschatte kosten** de dagelijkse **limiet**.

![De dagelijkse telemetrievolumed aanpassen](./media/pricing/pricing-003.png)

Als [u de dagelijkse dop via Azure Resource Manager](../../azure-monitor/app/powershell.md)wilt wijzigen, is de eigenschap die moet worden gewijzigd de `dailyQuota`.  Via Azure Resource Manager kunt `dailyQuotaResetTime` u ook de `warningThreshold`en de dagelijkse dop instellen.

### <a name="create-alerts-for-the-daily-cap"></a>Waarschuwingen maken voor de dagelijkse limiet

De Dagelijkse cap voor toepassingsinzichten maakt een gebeurtenis in het Azure-activiteitenlogboek wanneer de ingenomen gegevensvolumes het waarschuwingsniveau of het dagelijkse limietniveau bereiken.  U [een waarschuwing maken op basis van deze gebeurtenissen in het activiteitenlogboek.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log#create-with-the-azure-portal) De signaalnamen voor deze gebeurtenissen zijn:

* Waarschuwingdrempel voor dagelijkse limietvan application Insights-component bereikt

* Application Insights component daily cap bereikt

## <a name="sampling"></a>Steekproeven
[Sampling](../../azure-monitor/app/sampling.md) is een methode om de snelheid waarmee telemetrie naar uw app wordt verzonden te verminderen, met behoud van de mogelijkheid om gerelateerde gebeurtenissen te vinden tijdens diagnostische zoekopdrachten. U behoudt ook de juiste gebeurtenistellingen.

Sampling is een effectieve manier om de kosten te verlagen en binnen uw maandelijkse quotum te blijven. Het bemonsteringsalgoritme behoudt gerelateerde items van telemetrie, zodat u bijvoorbeeld bij het gebruik van Zoeken de aanvraag met betrekking tot een bepaalde uitzondering vinden. Het algoritme behoudt ook de juiste tellingen, zodat u de juiste waarden in Metric Explorer ziet voor aanvraagpercentages, uitzonderingspercentages en andere tellingen.

Er zijn verschillende vormen van bemonstering.

* [Adaptieve bemonstering](../../azure-monitor/app/sampling.md) is de standaardinstelling voor de ASP.NET SDK. Adaptieve bemonstering past zich automatisch aan het volume telemetrie dat uw app verzendt. Het werkt automatisch in de SDK in uw web-app, zodat telemetrieverkeer op het netwerk wordt verminderd. 
* *Innamesampling* is een alternatief dat werkt op het punt waar telemetrie vanuit uw app de Application Insights-service binnenkomt. Innamebemonstering heeft geen invloed op het volume van telemetrie dat vanuit uw app wordt verzonden, maar vermindert het volume dat door de service wordt bewaard. U opnamebemonstering gebruiken om het quotum te verminderen dat wordt gebruikt door telemetrie van browsers en andere SDK's.

Ga naar het deelvenster **Prijzen** om innamebemonstering in te stellen:

![Selecteer in het deelvenster Quota en prijzen de tegel Voorbeelden en selecteer vervolgens een bemonsteringsfractie](./media/pricing/pricing-004.png)

> [!WARNING]
> Het **deelvenster Gegevensbemonstering** regelt alleen de waarde van de innamebemonstering. Het geeft niet de samplingsnelheid weer die wordt toegepast door de Application Insights SDK in uw app. Als de binnenkomende telemetrie al is bemonsterd in de SDK, wordt opnamebemonstering niet toegepast.
>

Als u de werkelijke bemonsteringsfrequentie wilt ontdekken, ongeacht waar deze is toegepast, gebruikt u een [Analytics-query](analytics.md). De query ziet er als volgt uit:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Geeft in elke `itemCount` bewaarde record het aantal originele records aan dat deze vertegenwoordigt. Het is gelijk aan 1 + het aantal vorige verwijderde records.

## <a name="change-the-data-retention-period"></a>De gegevensretentieperiode wijzigen

De standaardretentie voor resources voor Application Insights is 90 dagen. Voor elke Application Insights-bron kunnen verschillende bewaartermijnen worden geselecteerd. De volledige set beschikbare bewaartermijnen is 30, 60, 90, 120, 180, 270, 365, 550 of 730 dagen.

Als u de bewaarstand wilt wijzigen, gaat u vanuit uw bron Voor Toepassingsstatistieken naar de pagina **Gebruik en Geschatte kosten** en selecteert u de optie **Gegevensbehoud:**

![De dagelijkse telemetrievolumed aanpassen](./media/pricing/pricing-005.png)

Wanneer de retentie wordt verlaagd, is er een respijtperiode van meerdere dagen voordat de oudste gegevens worden verwijderd.

De retentie kan ook programmatisch worden ingesteld `retentionInDays` met behulp van [PowerShell](powershell.md#set-the-data-retention) met behulp van de parameter. Als u de gegevensbewaring instelt op 30 dagen, `immediatePurgeDataOn30Days` u een onmiddellijke zuivering van oudere gegevens activeren met behulp van de parameter, wat handig kan zijn voor nalevingsscenario's. Deze zuiveringsfunctionaliteit wordt alleen zichtbaar via Azure Resource Manager en moet met uiterste zorg worden gebruikt. De dagelijkse resettijd voor de gegevensvolumedop kan worden `dailyQuotaResetTime` geconfigureerd met Azure Resource Manager om de parameter in te stellen.

## <a name="data-transfer-charges-using-application-insights"></a>Kosten voor gegevensoverdracht met Application Insights

Het verzenden van gegevens naar Application Insights kan leiden tot kosten voor gegevensbandbreedte. Zoals beschreven op de [prijspagina azure bandbreedte](https://azure.microsoft.com/pricing/details/bandwidth/), wordt gegevensoverdracht tussen Azure-services in twee regio's in rekening gebracht als uitgaande gegevensoverdracht tegen de normale snelheid. Inkomende gegevensoverdracht is gratis. Deze heffing is echter zeer gering (weinig %) vergeleken met de kosten voor het innemen van loggegevens van Application Insights. Daarom moet het beheersen van de kosten voor Log Analytics zich richten op uw ingenomen gegevensvolume, en we hebben richtlijnen om dat [hier](https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume)te begrijpen.

## <a name="limits-summary"></a>Overzicht van limieten

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Dagelijkse cap e-mails uitschakelen

Als u de dagelijkse volumed-e-mails wilt uitschakelen, selecteert u onder het gedeelte **Configureren** van uw toepassingsstatistieken in het deelvenster **Gebruik en geschatte kosten** de dagelijkse **limiet**. Er zijn instellingen om e-mail te verzenden wanneer de dop is bereikt, evenals wanneer een verstelbaar waarschuwingsniveau is bereikt. Als u alle dagelijkse e-mails over het totale opstelvolume wilt uitschakelen, schakelt u beide selectievakjes uit.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Prijscategorie Legacy Enterprise (Per Node)

Voor early adopters van Azure Application Insights zijn er nog twee mogelijke prijsniveaus: Basic en Enterprise. De prijslaag Basic is hetzelfde als hierboven beschreven en is de standaardlaag. Het bevat alle functies van de Enterprise-laag, zonder extra kosten. De basislaag wordt voornamelijk op het volume van de gegevens die worden ingenomen.

> [!NOTE]
> Deze oudere prijsniveaus zijn hernoemd. De prijslaag voor ondernemingen wordt nu **Per knooppunt** genoemd en de prijslaag Basis wordt nu **Per GB**genoemd. Deze nieuwe namen worden hieronder en in de Azure-portal gebruikt.  

De laag Per node (voorheen Enterprise) heeft een toeslag per knooppunt en elk knooppunt ontvangt een dagelijkse gegevensvergoeding. In de prijslaag Per knooppunt worden kosten in rekening gebracht voor gegevens die boven de meegeleverde vergoeding zijn opgenomen. Als u Operations Management Suite gebruikt, kiest u de laag Per knooppunt.

Zie [Prijzen voor Toepassingsstatistieken](https://azure.microsoft.com/pricing/details/application-insights/)voor actuele prijzen in uw valuta en regio.

> [!NOTE]
> In april 2018 [introduceerden](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) we een nieuw prijsmodel voor Azure-monitoring. Dit model hanteert een eenvoudig "pay-as-you-go"-model in het volledige portfolio van monitoringservices. Meer informatie over het [nieuwe prijsmodel,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs)hoe u [de impact van de overstap naar dit model beoordelen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#understanding-your-azure-monitor-costs) op basis van uw gebruikspatronen en [hoe u zich aanmelden voor het nieuwe model](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#azure-monitor-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Abonnementsrechten per knooppunt en Operations Management Suite-abonnementsrechten

Klanten die Operations Management Suite E1 en E2 kopen, kunnen Application Insights Per Node als extra component krijgen zonder extra kosten, zoals [eerder aangekondigd.](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/) In het bijzonder bevat elke eenheid Operations Management Suite E1 en E2 een recht op één knooppunt van de laag Application Insights Per Node. Elk Application Insights-knooppunt bevat tot 200 MB aan gegevens die per dag worden ingenomen (los van de opname van Log Analytics-gegevens), waarbij gegevens zonder extra kosten worden bewaard met 90 dagen. De laag wordt later in het artikel beschreven in meer gedetailleerd.

Omdat deze laag alleen van toepassing is op klanten met een Operations Management Suite-abonnement, zien klanten die geen Operations Management Suite-abonnement hebben geen optie om deze laag te selecteren.

> [!NOTE]
> Om ervoor te zorgen dat u dit recht krijgt, moeten uw resources Voor Application Insights zich in de prijslaag Per knooppunt bevindt. Dit recht geldt alleen als knooppunten. Application Insights-bronnen in de laag Per GB realiseren geen enkel voordeel. Dit recht is niet zichtbaar in de geschatte kosten die worden weergegeven in het deelvenster **Gebruik en geschatte kosten.** Als u in april 2018 een abonnement verplaatst naar het nieuwe Azure-bewakingsprijsmodel, is de laag Per GB de enige beschikbare laag. Het is niet aan te raden om een abonnement te verplaatsen naar het nieuwe Azure-bewakingsprijsmodel als u een Operations Management Suite-abonnement hebt.

### <a name="how-the-per-node-tier-works"></a>Hoe de laag Per knooppunt werkt

* U betaalt voor elk knooppunt dat telemetrie verzendt voor alle apps in de laag Per knooppunt.
  * Een *knooppunt* is een fysieke of virtuele servermachine of een platform-as-a-service-rolinstantie die uw app host.
  * Ontwikkelmachines, clientbrowsers en mobiele apparaten tellen niet als knooppunten.
  * Als uw app meerdere onderdelen bevat die telemetrie verzenden, zoals een webservice en een back-endwerknemer, worden de onderdelen afzonderlijk geteld.
  * [Live Metrics Stream-gegevens](../../azure-monitor/app/live-stream.md) worden niet meegeteld voor prijsdoeleinden. In een abonnement zijn uw kosten per knooppunt, niet per app. Als u vijf knooppunten hebt die telemetrie verzenden voor 12 apps, is de kosten voor vijf knooppunten.
* Hoewel er kosten per maand worden in rekening gebracht, worden er alleen kosten in rekening gebracht voor elk uur waarin een knooppunt telemetrie verzendt vanuit een app. Het uurkosten zijn de vermelde maandelijkse kosten gedeeld door 744 (het aantal uren in een maand van 31 dagen).
* Voor elk knooppunt dat wordt gedetecteerd (met granulariteit per uur) wordt een gegevensvolumetoewijzing van 200 MB per dag gegeven. Ongebruikte gegevenstoewijzing wordt niet van de ene op de andere dag overgedragen.
  * Als u de prijslaag Per knooppunt kiest, krijgt elk abonnement een dagelijkse hoeveelheid gegevens op basis van het aantal knooppunten dat telemetrie verzendt naar de bronnen Application Insights in dat abonnement. Als u dus vijf knooppunten hebt die de hele dag gegevens verzenden, wordt een gepoolde vergoeding van 1 GB toegepast op alle Toepassingsinzichtenbronnen in dat abonnement. Het maakt niet uit of bepaalde knooppunten meer gegevens verzenden dan andere knooppunten, omdat de opgenomen gegevens worden gedeeld over alle knooppunten. Als de resources Application Insights op een bepaalde dag meer gegevens ontvangen dan is opgenomen in de dagelijkse gegevenstoewijzing voor dit abonnement, zijn de kosten voor overschrijding van de gegevens per GB van toepassing. 
  * De dagelijkse hoeveelheid gegevens wordt berekend als het aantal uren in de dag (met behulp van UTC) dat elk knooppunt telemetrie verzendt gedeeld door 24 vermenigvuldigd met 200 MB. Dus, als je vier knooppunten hebt die telemetrie sturen gedurende 15 van de 24 uur per dag, dan zijn de meegeleverde gegevens voor die dag ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Tegen de prijs van 2,30 USD per GB voor gegevensoverschrijding, zou de kosten 1,15 USD zijn als de knooppunten die dag 1 GB aan gegevens verzenden.
  * De dagelijkse hoeveelheid per knooppunt wordt niet gedeeld met toepassingen waarvoor u de laag Per GB hebt gekozen. Ongebruikte toelage wordt niet overgedragen van dag tot dag.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Voorbeelden van het bepalen van het aantal verschillende knooppunten

| Scenario                               | Totaal aantal dagelijkse nodes |
|:---------------------------------------|:----------------:|
| 1 toepassing met 3 Azure App Service-exemplaren en 1 virtuele server | 4 |
| 3 toepassingen die op 2 VM's draaien; de Toepassingsinzichtenbronnen voor deze toepassingen bevinden zich in hetzelfde abonnement en in de laag Per knooppunt | 2 | 
| 4 toepassingen waarvan de resources Applications Insights in hetzelfde abonnement zitten; elke toepassing met 2 exemplaren gedurende 16 daluren en 4 exemplaren gedurende 8 piekuren | 13.33 |
| Cloudservices met 1 werkrol en 1 webrol, die elk 2 exemplaren uitvoeren | 4 | 
| Een Azure Service Fabric-cluster met 5-node met 50 microservices; elke microservice met 3 exemplaren | 5|

* De exacte node telling is afhankelijk van welke Application Insights SDK uw toepassing gebruikt. 
  * In SDK-versies 2.2 en hoger rapporteren zowel de Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) als de [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) elke toepassingshost als knooppunt. Voorbeelden zijn de computernaam voor fysieke server- en VM-hosts of de instantienaam voor cloudservices.  De enige uitzondering is een toepassing die alleen de [.NET Core](https://dotnet.github.io/) en de Application Insights Core SDK gebruikt. In dat geval wordt slechts één knooppunt gerapporteerd voor alle hosts omdat de hostnaam niet beschikbaar is.
  * Voor eerdere versies van de SDK gedraagt de [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) zich als de nieuwere SDK-versies, maar de [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) rapporteert slechts één knooppunt, ongeacht het aantal toepassingshosts.
  * Als uw toepassing de SDK gebruikt om **rolinstantie** in te stellen op een aangepaste waarde, wordt diezelfde waarde standaard gebruikt om het aantal knooppunten te bepalen.
  * Als u een nieuwe SDK-versie gebruikt met een app die wordt uitgevoerd vanaf clientmachines of mobiele apparaten, kan het aantal knooppuntgegevens een groot getal retourneren (vanwege het grote aantal clientmachines of mobiele apparaten).

## <a name="automation"></a>Automation

U een script schrijven om de prijscategorie in te stellen met Azure Resource Management. [Meer informatie](powershell.md#price).

## <a name="next-steps"></a>Volgende stappen

* [Steekproeven](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/