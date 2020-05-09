---
title: Afhankelijkheden bijhouden in Azure-toepassing Insights | Microsoft Docs
description: Afhankelijkheids aanroepen bewaken vanaf uw on-premises of Microsoft Azure webtoepassing met Application Insights.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 2b7a20731fa5eae8313adcf07d877626fcaa4dce
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980844"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Afhankelijkheden bijhouden in Azure-toepassing Insights 

Een *afhankelijkheid* is een extern onderdeel dat wordt aangeroepen door uw toepassing. Het is doorgaans een service met de naam HTTP, een Data Base of een bestands systeem. [Application Insights](../../azure-monitor/app/app-insights-overview.md) meet de duur van afhankelijkheids aanroepen, of het nu niet lukt, samen met aanvullende informatie, zoals de naam van de afhankelijkheid, enzovoort. U kunt specifieke afhankelijkheids aanroepen onderzoeken en deze correleren aan aanvragen en uitzonde ringen.

## <a name="automatically-tracked-dependencies"></a>Automatisch bijgehouden afhankelijkheden

Application Insights Sdk's voor .NET en .NET core wordt geleverd `DependencyTrackingTelemetryModule` met een telemetrie-module waarmee automatisch afhankelijkheden worden verzameld. Deze afhankelijkheids verzameling wordt automatisch ingeschakeld voor [ASP.net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) -en [ASP.net core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) -toepassingen, wanneer deze zijn geconfigureerd volgens de gekoppelde officiële docs. `DependencyTrackingTelemetryModule` wordt verzonden als [Dit](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) NuGet-pakket en wordt automatisch geactiveerd wanneer een van de NuGet-pakketten `Microsoft.ApplicationInsights.Web` of `Microsoft.ApplicationInsights.AspNetCore`wordt gebruikt.

 `DependencyTrackingTelemetryModule`momenteel worden de volgende afhankelijkheden automatisch bijgehouden:

|Afhankelijkheden |Details|
|---------------|-------|
|Http/https | Lokale of externe http/https-aanroepen |
|WCF-aanroepen| Wordt alleen automatisch bijgehouden als op http gebaseerde bindingen worden gebruikt.|
|SQL | Aanroepen van `SqlClient`. Zie [dit voor het](#advanced-sql-tracking-to-get-full-sql-query) vastleggen van SQL-query's.  |
|[Azure Storage (BLOB, tabel, wachtrij)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Aanroepen van Azure Storage-client. |
|[EventHub-client-SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Versie 1.1.0 en hoger. |
|[ServiceBus-client-SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Versie 3.0.0 en hoger. |
|Azure Cosmos DB | Wordt alleen automatisch bijgehouden als HTTP/HTTPS wordt gebruikt. De TCP-modus wordt niet vastgelegd door Application Insights. |

Als u een afhankelijkheid mist of een andere SDK gebruikt, moet u ervoor zorgen dat deze is opgenomen in de lijst met [automatisch verzamelde afhankelijkheden](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Als de afhankelijkheid niet automatisch wordt verzameld, kunt u deze nog steeds hand matig bijhouden met een aanroep voor het [bijhouden van afhankelijkheden](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Automatische tracking van afhankelijkheden instellen in console-apps

Als u afhankelijkheden van .NET-console-apps automatisch wilt bijhouden `Microsoft.ApplicationInsights.DependencyCollector`, installeert u het `DependencyTrackingTelemetryModule` Nuget-pakket en initialiseert u als volgt:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

Voor .NET core console-apps TelemetryConfiguration. Active is verouderd. Raadpleeg de richt lijnen in de [documentatie van de Worker-service](https://docs.microsoft.com/azure/azure-monitor/app/worker-service) en de [documentatie over ASP.net core bewaking](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)

### <a name="how-automatic-dependency-monitoring-works"></a>Hoe werkt automatische afhankelijkheid controleren?

Afhankelijkheden worden automatisch verzameld met behulp van een van de volgende technieken:

* Het gebruik van byte code instrumentatie rondom Select-methoden. (InstrumentationEngine van StatusMonitor of extensie van Azure-web-app)
* Call backs van Event source
* DiagnosticSource-retour aanroepen (in de nieuwste Sdk's voor .NET/.NET core)

## <a name="manually-tracking-dependencies"></a>Afhankelijkheden hand matig bijhouden

Hier volgen enkele voor beelden van afhankelijkheden die niet automatisch worden verzameld, en waarvoor hand matige tracering is vereist.

* Azure Cosmos DB wordt alleen automatisch bijgehouden als [http/https](../../cosmos-db/performance-tips.md#networking) wordt gebruikt. De TCP-modus wordt niet vastgelegd door Application Insights.
* Redis

Voor de afhankelijkheden die niet automatisch door SDK worden verzameld, kunt u ze hand matig bijhouden met behulp van de [TrackDependency-API](api-custom-events-metrics.md#trackdependency) die wordt gebruikt door de standaard modules voor automatisch verzamelen.

Als u bijvoorbeeld uw code opbouwt met een assembly die u niet zelf hebt geschreven, kunt u alle aanroepen ernaar door lopen om erachter te komen welke bijdrage het biedt aan uw reactie tijden. Als u wilt dat deze gegevens worden weer gegeven in de afhankelijkheids grafieken in Application Insights `TrackDependency`, verzendt u deze met.

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

U kunt ook `TelemetryClient` uitbreidings methoden `StartOperation` bieden `StopOperation` die kunnen worden gebruikt voor het hand matig bijhouden van afhankelijkheden, zoals [hier](custom-operations-tracking.md#outgoing-dependencies-tracking) wordt weer gegeven

Als u de standaard tracerings module voor afhankelijkheden wilt uitschakelen, verwijdert u de verwijzing naar DependencyTrackingTelemetryModule in [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) voor ASP.NET-toepassingen. Volg [de instructies in](asp-net-core.md#configuring-or-removing-default-telemetrymodules)voor ASP.net core toepassingen.

## <a name="tracking-ajax-calls-from-web-pages"></a>AJAX-aanroepen bijhouden vanaf webpagina's

Voor webpagina's worden Application Insights java script SDK automatisch AJAX-aanroepen verzameld als afhankelijkheden.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Geavanceerde SQL-tracking voor het verkrijgen van een volledige SQL-query

Voor SQL-aanroepen wordt de naam van de server en data base altijd verzameld en opgeslagen als de naam `DependencyTelemetry`van de verzamelde. Er is een extra veld met de naam ' gegevens ', dat de volledige SQL-query tekst kan bevatten.

Voor ASP.NET Core toepassingen is er geen extra stap vereist voor het ophalen van de volledige SQL-query.

Voor ASP.NET-toepassingen wordt volledige SQL-query verzameld met behulp van byte code Instrumentation, waarvoor instrumentatie-engine vereist is of met behulp van het NuGet-pakket [micro soft. data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) in plaats van de System. data. SqlClient-bibliotheek. Aanvullende,, zoals hieronder beschreven, specifieke platformspecifieke stappen zijn vereist.

| Platform | Er zijn een of meer stappen nodig om een volledige SQL-query te verkrijgen |
| --- | --- |
| Azure Web App |Open in het configuratie scherm van uw web-app [de blade Application Insights](../../azure-monitor/app/azure-web-apps.md) en Schakel SQL-opdrachten in onder .net |
| IIS-server (Azure VM, on-premises, enzovoort) | Gebruik het pakket [micro soft. data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) NuGet of gebruik de status monitor Power shell-module om [de instrumentatie-engine te installeren](../../azure-monitor/app/status-monitor-v2-api-reference.md) en IIS opnieuw te starten. |
| Azure Cloud Service | [Opstart taak toevoegen om StatusMonitor te installeren](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Uw app moet worden uitgevoerd tot ApplicationInsights SDK tijdens het bouwen door NuGet-pakketten te installeren voor [ASP.net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) -of [ASP.net core-toepassingen](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express | Het NuGet-pakket [micro soft. data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) gebruiken

In de bovenstaande gevallen wordt de juiste methode voor het valideren van de instrumentatie-engine correct geïnstalleerd door te controleren of de SDK-versie van `DependencyTelemetry` verzamelde ' rddp ' is. ' rdddsd ' of ' rddf ' geeft aan dat afhankelijkheden worden verzameld via DiagnosticSource of event source-retour aanroepen, en daarom wordt volledige SQL-query niet vastgelegd.

## <a name="where-to-find-dependency-data"></a>Waar u de afhankelijkheids gegevens kunt vinden

* [Toepassings toewijzing](app-map.md) visualeert afhankelijkheden tussen uw app en naburige onderdelen.
* In [transactie diagnostiek](transaction-diagnostics.md) worden Unified, gecorreleerde Server gegevens weer gegeven.
* Op het [tabblad browsers](javascript.md) worden Ajax-aanroepen weer gegeven in de browser van uw gebruikers.
* Klik van trage of mislukte aanvragen om de afhankelijkheids aanroepen te controleren.
* [Analyses](#logs-analytics) kunnen worden gebruikt voor het opvragen van afhankelijkheids gegevens.

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a>Langzame aanvragen diagnosticeren

Elke aanvraag gebeurtenis is gekoppeld aan de afhankelijkheids aanroepen, uitzonde ringen en andere gebeurtenissen die worden bijgehouden terwijl uw app de aanvraag verwerkt. Als sommige aanvragen niet goed worden uitgevoerd, kunt u nagaan of het gaat om een trage reactie van een afhankelijkheid.

### <a name="tracing-from-requests-to-dependencies"></a>Traceren op basis van aanvragen naar afhankelijkheden

Open het tabblad **prestaties** en navigeer naar het tabblad **afhankelijkheden** boven naast bewerkingen.

Klik onder algemeen op een **afhankelijkheids naam** . Nadat u een afhankelijkheid hebt geselecteerd, wordt aan de rechter kant een grafiek weer gegeven met de verdeling van de duur van de afhankelijkheid.

![Klik op het tabblad prestaties op het tabblad afhankelijkheid aan de bovenkant en vervolgens op de naam van de afhankelijkheid in de grafiek](./media/asp-net-dependencies/2-perf-dependencies.png)

Klik op de knop met blauwe **steek proeven** aan de rechter kant en klik vervolgens op een voor beeld om de end-to-end-transactie details te bekijken.

![Klik op een voor beeld om de details van de end-to-end-trans actie te bekijken](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Uw live site profileren

Geen idee waar de tijd overgaat? De [Application Insights Profiler](../../azure-monitor/app/profiler.md) traceert http-aanroepen naar uw live-site en toont u de functies in uw code die de langste tijd hebben geduurd.

## <a name="failed-requests"></a>Mislukte aanvragen

Mislukte aanvragen kunnen ook worden gekoppeld aan mislukte aanroepen van afhankelijkheden.

Ga naar het tabblad **fouten** aan de linkerkant en klik vervolgens op het tabblad **afhankelijkheden** bovenaan.

![Klik op de grafiek mislukte aanvragen](./media/asp-net-dependencies/4-fail.png)

Hier ziet u het aantal mislukte afhankelijkheden. Als u meer informatie wilt over een mislukt voorval, klikt u op de naam van een afhankelijkheid in de onderste tabel. U kunt op de knop met blauwe **afhankelijkheden** rechtsonder aan de rechter kant klikken om de end-to-end-transactie gegevens op te halen.

## <a name="logs-analytics"></a>Logboeken (Analytics)

U kunt afhankelijkheden volgen in de [Kusto-query taal](/azure/kusto/query/). Hier volgen enkele voorbeelden.

* Alle mislukte afhankelijkheids aanroepen zoeken:

``` Kusto

    dependencies | where success != "True" | take 10
```

* AJAX-aanroepen zoeken:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Afhankelijkheids aanroepen zoeken die zijn gekoppeld aan aanvragen:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* AJAX-aanroepen zoeken die zijn gekoppeld aan pagina weergaven:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Hoe werkt het automatisch rapport van een afhankelijkheids verzamelaar bij het aanroepen van afhankelijkheden niet?*

* Voor mislukte afhankelijkheids aanroepen is het veld geslaagd ingesteld op ONWAAR. `DependencyTrackingTelemetryModule`rapporteert `ExceptionTelemetry`niet. Het volledige gegevens model voor afhankelijkheid wordt [hier](data-model-dependency-telemetry.md)beschreven.

## <a name="open-source-sdk"></a>Open-Source-SDK
Net als elke Application Insights SDK is de module afhankelijkheids verzameling ook open-source. Lees en bijdragen aan de code of Meld problemen bij [de officiële github-opslag plaats](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Volgende stappen

* [Uitzonderingen](../../azure-monitor/app/asp-net-exceptions.md)
* [Pagina gegevens van gebruiker &](../../azure-monitor/app/javascript.md)
* [Beschikbaarheid](../../azure-monitor/app/monitor-web-app-availability.md)
