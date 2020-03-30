---
title: Afhankelijkheidstracking in Azure-toepassingsinzichten | Microsoft Documenten
description: Controleer afhankelijkheidsoproepen vanuit uw on-premises of Microsoft Azure-webtoepassing met Application Insights.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 1d4e8d1a0482257c92f47a00bd440e786c09c7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292129"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Afhankelijkheidstracking in Azure-toepassingsinzichten 

Een *afhankelijkheid* is een externe component die door uw toepassing wordt aangeroepen. Het is meestal een service genaamd met behulp van HTTP, of een database, of een bestandssysteem. [Application Insights](../../azure-monitor/app/app-insights-overview.md) meet de duur van afhankelijkheidsoproepen, ongeacht of deze zijn mislukt of niet, samen met aanvullende informatie zoals de naam van afhankelijkheid enzovoort. U specifieke afhankelijkheidsoproepen onderzoeken en deze correleren met aanvragen en uitzonderingen.

## <a name="automatically-tracked-dependencies"></a>Automatisch bijgehouden afhankelijkheden

Application Insights SDKs voor .NET- `DependencyTrackingTelemetryModule` en .NET Core-schepen waarmee een telemetriemodule wordt verzonden die automatisch afhankelijkheden verzamelt. Deze afhankelijkheidsverzameling wordt automatisch ingeschakeld voor [ASP.NET-](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) en [ASP.NET Core-toepassingen,](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) wanneer deze zijn geconfigureerd volgens de gekoppelde officiële documenten. `DependencyTrackingTelemetryModule` wordt verzonden als [dit](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) NuGet-pakket en wordt automatisch gebracht `Microsoft.ApplicationInsights.Web` `Microsoft.ApplicationInsights.AspNetCore`bij het gebruik van een van de NuGet-pakketten of .

 `DependencyTrackingTelemetryModule`houdt momenteel automatisch de volgende afhankelijkheden bij:

|Afhankelijkheden |Details|
|---------------|-------|
|Http/https | Lokale of externe http/https-oproepen |
|WCF-gesprekken| Alleen automatisch bijgehouden als op Http gebaseerde bindingen worden gebruikt.|
|SQL | Gesprekken gemaakt `SqlClient`met . Zie [dit](#advanced-sql-tracking-to-get-full-sql-query) voor het vastleggen van SQL-query.  |
|[Azure-opslag (Blob, Tabel, Wachtrij)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Gesprekken met Azure Storage Client. |
|[EventHub-client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Versie 1.1.0 en hoger. |
|[ServiceBus-client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Versie 3.0.0 en hoger. |
|Azure Cosmos DB | Alleen automatisch bijgehouden als HTTP/HTTPS wordt gebruikt. De TCP-modus wordt niet vastgelegd door Application Insights. |

Als u een afhankelijkheid mist of als u een andere SDK gebruikt, moet u ervoor zorgen dat deze in de lijst met [automatisch verzamelde afhankelijkheden staat.](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies) Als de afhankelijkheid niet automatisch wordt verzameld, u deze nog steeds handmatig bijhouden met een [oproep voor spoorafhankelijkheid](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Automatische afhankelijkheidstracking instellen in Console-apps

Als u afhankelijkheden van .NET-console-apps automatisch `Microsoft.ApplicationInsights.DependencyCollector`wilt bijhouden, installeert u het Nuget-pakket en initialiseert u `DependencyTrackingTelemetryModule` het volgende:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

Voor .NET Core-console-apps telemetrieconfiguratie.active is verouderd. Raadpleeg de richtlijnen in de documentatie van de [werknemersservice](https://docs.microsoft.com/azure/azure-monitor/app/worker-service) en de [ASP.NET Core-documentatie voor monitoring](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)

### <a name="how-automatic-dependency-monitoring-works"></a>Hoe werkt automatische afhankelijkheidsbewaking?

Afhankelijkheden worden automatisch verzameld met behulp van een van de volgende technieken:

* Met behulp van byte code instrumentatie rond geselecteerde methoden. (InstrumentationEngine, van StatusMonitor of Azure Web App Extension)
* EventSource-terugroepen
* DiagnosticSource callbacks (in de nieuwste .NET/.NET Core SDKs)

## <a name="manually-tracking-dependencies"></a>Afhankelijkheden handmatig bijhouden

De volgende zijn enkele voorbeelden van afhankelijkheden, die niet automatisch worden verzameld, en vereisen dus handmatige tracking.

* Azure Cosmos DB wordt alleen automatisch bijgehouden als [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) wordt gebruikt. De TCP-modus wordt niet vastgelegd door Application Insights.
* Redis

Voor afhankelijkheden die niet automatisch door SDK worden verzameld, u ze handmatig volgen met behulp van de [TrackDependency API](api-custom-events-metrics.md#trackdependency) die wordt gebruikt door de standaard modules voor automatische verzameling.

Als u bijvoorbeeld uw code bouwt met een vergadering die u zelf niet hebt geschreven, u alle oproepen naar deze code timen om erachter te komen welke bijdrage het levert aan uw reactietijden. Als u deze gegevens wilt weergeven in de afhankelijkheidsgrafieken in Application Insights, verzendt u deze met behulp van `TrackDependency`.

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

U kunt `TelemetryClient` ook `StartOperation` uitbreidingsmethoden `StopOperation` bieden en die kunnen worden gebruikt om afhankelijkheden handmatig bij te houden, zoals [hier](custom-operations-tracking.md#outgoing-dependencies-tracking) wordt weergegeven

Als u de standaardmodule voor het bijhouden van afhankelijkheid wilt uitschakelen, verwijdert u de verwijzing naar DependencyTrackingTelemetryModule in [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) voor ASP.NET toepassingen. Voor ASP.NET Core-toepassingen, volg [hier](asp-net-core.md#configuring-or-removing-default-telemetrymodules)de instructies .

## <a name="tracking-ajax-calls-from-web-pages"></a>Ajax-oproepen volgen vanaf webpagina's

Voor webpagina's verzamelt Application Insights JavaScript SDK automatisch AJAX-aanroepen als afhankelijkheden.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Geavanceerde SQL-tracking om volledige SQL-query te krijgen

Voor SQL-aanroepen wordt de naam van de server en `DependencyTelemetry`database altijd verzameld en opgeslagen als naam van de verzamelde . Er is een extra veld genaamd 'data', dat de volledige SQL-querytekst kan bevatten.

Voor ASP.NET Core-toepassingen is er geen extra stap nodig om de volledige SQL Query te krijgen.

Voor ASP.NET toepassingen wordt volledige SQL-query verzameld met behulp van bytecode-instrumentatie, waarvoor instrumentatie-engine vereist is. Aanvullende platformspecifieke stappen, zoals hieronder beschreven, zijn vereist.

| Platform | Stap(en) nodig om volledige SQL-query te krijgen |
| --- | --- |
| Azure Web App |Open in het configuratiescherm van uw web-app [het Blade Application Insights](../../azure-monitor/app/azure-web-apps.md) en schakel SQL-opdrachten in onder .NET |
| IIS Server (Azure VM, on-prem, enzovoort.) | Gebruik de PowerShell-module statusmonitor om [de instrumentatieengine](../../azure-monitor/app/status-monitor-v2-api-enable-instrumentation-engine.md) te installeren en IIS opnieuw te starten. |
| Azure Cloud Service | Opstarttaak toevoegen [om StatusMonitor te installeren](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Uw app moet op tijd worden aangesloten op ApplicationInsights SDK door NuGet-pakketten te installeren voor [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) of [ASP.NET Core-toepassingen](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express | Niet ondersteund

In de bovenstaande gevallen is de juiste manier om te valideren dat instrumentatie-engine correct `DependencyTelemetry` is geïnstalleerd door te valideren dat de SDK-versie van de verzamelde is 'rddp'. 'rdddsd' of 'rddf' geeft aan dat afhankelijkheden worden verzameld via DiagnosticSource of EventSource callbacks, en dus volledige SQL-query wordt niet vastgelegd.

## <a name="where-to-find-dependency-data"></a>Waar vindt u afhankelijkheidsgegevens

* [Toepassingskaart](app-map.md) visualiseert afhankelijkheden tussen uw app en aangrenzende componenten.
* [Transaction Diagnostics](transaction-diagnostics.md) toont uniforme, gecorreleerde servergegevens.
* [Tabblad Browsers](javascript.md) toont AJAX-oproepen vanuit de browsers van uw gebruikers.
* Klik door van langzame of mislukte aanvragen om hun afhankelijkheidsoproepen te controleren.
* [Analytics](#logs-analytics) kan worden gebruikt om afhankelijkheidsgegevens op te vragen.

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a>Diagnose van trage aanvragen

Elke aanvraaggebeurtenis is gekoppeld aan de afhankelijkheidsoproepen, uitzonderingen en andere gebeurtenissen die worden bijgehouden terwijl uw app de aanvraag verwerkt. Dus als sommige aanvragen het slecht doen, u erachter komen of dit komt door trage reacties van een afhankelijkheid.

### <a name="tracing-from-requests-to-dependencies"></a>Tracering van aanvragen naar afhankelijkheden

Open het tabblad **Prestaties** en navigeer naar het tabblad **Afhankelijkheden** bovenaan naast bewerkingen.

Klik op een **afhankelijkheidsnaam** onder overall. Nadat u een afhankelijkheid hebt geselecteerd, wordt rechts een grafiek weergegeven van de verdeling van de duur van die afhankelijkheid.

![Klik op het tabblad Prestaties op het tabblad Afhankelijkheid bovenaan en vervolgens op een afhankelijkheidsnaam in de grafiek](./media/asp-net-dependencies/2-perf-dependencies.png)

Klik op de blauwe **knop Voorbeelden** rechtsonder en vervolgens op een voorbeeld om de end-to-end transactiedetails te bekijken.

![Klik op een voorbeeld om de end-to-end transactiegegevens te bekijken](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Profiel van uw live site

Geen idee waar de tijd gaat? De [Application Insights profiler](../../azure-monitor/app/profiler.md) traceert HTTP-oproepen naar uw live site en toont u de functies in uw code die het langst heeft geduurd.

## <a name="failed-requests"></a>Mislukte aanvragen

Mislukte aanvragen kunnen ook worden gekoppeld aan mislukte oproepen naar afhankelijkheden.

We kunnen naar het tabblad **Fouten** aan de linkerkant gaan en vervolgens bovenaan op het tabblad **afhankelijkheden** klikken.

![Klik op het diagram met mislukte aanvragen](./media/asp-net-dependencies/4-fail.png)

Hier u het aantal mislukte afhankelijkheidzien. Voor meer informatie over een mislukte gebeurtenis waarin u probeert te klikken op een afhankelijkheidsnaam in de onderste tabel. U rechtsonder op de blauwe **knop Afhankelijkheden** klikken om de end-to-end transactiegegevens te krijgen.

## <a name="logs-analytics"></a>Logboeken (Analytics)

U afhankelijkheden bijhouden in de [Kusto-querytaal.](/azure/kusto/query/) Hier volgen enkele voorbeelden.

* Zoek mislukte afhankelijkheidsoproepen:

``` Kusto

    dependencies | where success != "True" | take 10
```

* Zoek AJAX-oproepen:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Zoek afhankelijkheidsoproepen die zijn gekoppeld aan aanvragen:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Zoek AJAX-oproepen in verband met paginaweergaven:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Hoe rapporteert automatische afhankelijkheidsverzamelaar mislukte oproepen naar afhankelijkheden?*

* Mislukte afhankelijkheidsoproepen hebben het veld 'succes' ingesteld op False. `DependencyTrackingTelemetryModule`meldt `ExceptionTelemetry`niet . Het volledige gegevensmodel voor afhankelijkheid wordt [hier](data-model-dependency-telemetry.md)beschreven.

## <a name="open-source-sdk"></a>Open-source SDK
Zoals elke Application Insights SDK is ook de afhankelijkheidsverzamelingsmodule open-source. Lees en draag bij aan de code, of rapporteer problemen op [de officiële GitHub repo.](https://github.com/Microsoft/ApplicationInsights-dotnet-server)

## <a name="next-steps"></a>Volgende stappen

* [Uitzonderingen](../../azure-monitor/app/asp-net-exceptions.md)
* [Gebruikers& paginagegevens](../../azure-monitor/app/javascript.md)
* [Beschikbaarheid](../../azure-monitor/app/monitor-web-app-availability.md)
