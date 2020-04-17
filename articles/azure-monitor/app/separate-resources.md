---
title: Telemetrie scheiden in Azure Application Insights
description: Direct telemetrie naar verschillende bronnen voor ontwikkeling, test en productiestempels.
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 565d51751ad50479f4e227b6855ac63b80bd949e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536774"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Telemetrie scheiden van ontwikkeling, test en productie

Wanneer u de volgende versie van een webtoepassing ontwikkelt, wilt u de Telemetrie van [Application Insights](../../azure-monitor/app/app-insights-overview.md) van de nieuwe versie en de reeds uitgebrachte versie niet door elkaar halen. Om verwarring te voorkomen, stuurt u de telemetrie vanuit verschillende ontwikkelingsfasen naar afzonderlijke Application Insights-bronnen, met afzonderlijke instrumentatietoetsen (ikeys). Om het gemakkelijker te maken om de instrumentatiesleutel te wijzigen terwijl een versie van de ene fase naar de andere wordt verplaatst, kan het handig zijn om de ikey in code in te stellen in plaats van in het configuratiebestand. 

(Als uw systeem een Azure Cloud Service is, is er [een andere methode om afzonderlijke ikeys in te stellen](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Informatie over resources en instrumentatietoetsen

Wanneer u Application Insights-monitoring instelt voor uw web-app, maakt u een Application *Insights-bron* in Microsoft Azure. U opent deze bron in de Azure-portal om de telemetrie die van uw app is verzameld, te kunnen zien en analyseren. De resource wordt geïdentificeerd door een *instrumentatiesleutel* (ikey). Wanneer u het Pakket Toepassingsinzichten installeert om uw app te controleren, configureert u deze met de instrumentatiesleutel, zodat deze weet waar de telemetrie moet worden verzonden.

U kiest er meestal voor om afzonderlijke resources of één gedeelde bron te gebruiken in verschillende scenario's:

* Verschillende, onafhankelijke toepassingen - Gebruik een aparte bron en ikey voor elke app.
* Meerdere onderdelen of rollen van één bedrijfstoepassing : gebruik [één gedeelde bron](../../azure-monitor/app/app-map.md) voor alle component-apps. Telemetrie kan worden gefilterd of gesegmenteerd door de eigenschap cloud_RoleName.
* Ontwikkeling, test en release - Gebruik een aparte bron en ikey voor versies van het systeem in 'stempel' of productiestadium.
* A | B-testen - Gebruik één resource. Maak een telemetrieInitializer om een eigenschap toe te voegen aan de telemetrie die de varianten identificeert.


## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a>Dynamische instrumentatiesleutel

Als u het gemakkelijker wilt maken om de ikey te wijzigen terwijl de code tussen productiefasen wordt verplaatst, stelt u deze in code in in plaats van in het configuratiebestand.

Stel de sleutel in een initialisatiemethode in, zoals global.aspx.cs in een ASP.NET-service:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

In dit voorbeeld worden de ikeys voor de verschillende resources in verschillende versies van het webconfiguratiebestand geplaatst. Als u het webconfiguratiebestand verwisselt - dat u doen als onderdeel van het releasescript - wordt de doelbron verwisseld.

### <a name="web-pages"></a>Webpagina's
De iKey wordt ook gebruikt in de webpagina's van uw app, in het [script dat u hebt gekregen van het quickstart-blad.](../../azure-monitor/app/javascript.md) In plaats van het letterlijk te coderen in het script, het genereren van de server status. Bijvoorbeeld in een ASP.NET-app:

*JavaScript in Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>Extra resources voor Application Insights maken
Als u telemetrie wilt scheiden voor verschillende toepassingscomponenten of voor verschillende stempels (dev/test/productie) van dezelfde component, moet u een nieuwe Application Insights-bron maken.

Voeg in de [portal.azure.com](https://portal.azure.com)een bron Voor Application Insights toe:

![Klik op Nieuw > Application Insights](./media/separate-resources/01-new.png)

* **Toepassingstype** is van invloed op wat u ziet op het overzichtsblad en de eigenschappen die beschikbaar zijn in [metrische verkenner](../../azure-monitor/platform/metrics-charts.md). Als u uw type app niet ziet, kiest u een van de webtypen voor webpagina's.
* **Resourcegroep** is een gemak voor het beheren van eigenschappen zoals [toegangscontrole.](../../azure-monitor/app/resources-roles-access-control.md) U afzonderlijke resourcegroepen gebruiken voor ontwikkeling, test en productie.
* **Abonnement** is uw betaalaccount in Azure.
* **Locatie** is waar we uw gegevens bewaren. Momenteel kan het niet worden gewijzigd. 
* **Als u toevoegt aan het dashboard,** wordt een tegel met snelle toegang voor uw bron op uw Azure-startpagina geplaatst. 

Het maken van de resource duurt enkele seconden. Je ziet een waarschuwing als het klaar is.

(U een [PowerShell-script](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) schrijven om automatisch een resource te maken.)

### <a name="getting-the-instrumentation-key"></a>De instrumentatiesleutel verkrijgen
De instrumentatiesleutel identificeert de resource die u hebt gemaakt. 

![Klik op Essentiële punten, klik op de instrumentatiesleutel, Ctrl+C](./media/separate-resources/02-props.png)

U hebt de instrumentatietoetsen nodig van alle bronnen waarnaar uw app gegevens verzendt.

## <a name="filter-on-build-number"></a>Filter op buildnummer
Wanneer u een nieuwe versie van uw app publiceert, wilt u de telemetrie van verschillende builds kunnen scheiden.

U de eigenschap Toepassingsversie zo instellen dat u [zoekresultaten](../../azure-monitor/app/diagnostic-search.md) en [metrische verkenners](../../azure-monitor/platform/metrics-charts.md) filteren.

![Filteren op een eigenschap](./media/separate-resources/050-filter.png)

Er zijn verschillende methoden voor het instellen van de eigenschap Toepassingsversie.

* Direct instellen:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Wikkel die regel in een [telemetrieinitialisator](../../azure-monitor/app/api-custom-events-metrics.md#defaults) om ervoor te zorgen dat alle telemetrieclient-exemplaren consistent zijn ingesteld.
* [ASP.NET] Stel de `BuildInfo.config`versie in . De webmodule haalt de versie op via het BuildLabel-knooppunt. Neem dit bestand op in uw project en vergeet niet de eigenschap Altijd kopiëren in Solution Explorer in te stellen.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Genereer BuildInfo.config automatisch in MSBuild. Voeg hiervoor een paar regels `.csproj` toe aan het bestand:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Hiermee wordt een bestand gegenereerd met de naam *YourProjectName*. BuildInfo.config. Het publicatieproces hernoemt het naar BuildInfo.config.

    Het buildlabel bevat een tijdelijke aanduiding (AutoGen_...) wanneer u met Visual Studio bouwt. Maar wanneer gebouwd met MSBuild, is het gevuld met het juiste versienummer.

    Als u MSBuild wilt toestaan versienummers `1.0.*` te genereren, stelt u de versie in zoals in AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Versie en release bijhouden
Als u de toepassingsversie wilt bijhouden, zorgt u ervoor dat `buildinfo.config` wordt gegenereerd door het Microsoft Build Engine-proces. Voeg `.csproj` in uw bestand het:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Wanneer de buildgegevens beschikbaar zijn, voegt de Application Insights-webmodule automatisch **Toepassingsversie** als eigenschap toe aan elk telemetrie-item. Dit biedt de mogelijkheid om op versie te filteren wanneer u [diagnostische zoekopdrachten](../../azure-monitor/app/diagnostic-search.md) uitvoert of [metrische gegevens verkent](../../azure-monitor/platform/metrics-charts.md).

Merk echter op dat het buildversienummer alleen wordt gegenereerd door de Microsoft Build Engine, niet door de ontwikkelaar die van Visual Studio is gebouwd.

### <a name="release-annotations"></a>Release-aantekeningen
Als u Azure DevOps gebruikt, u [een annotatiemarkering](../../azure-monitor/app/annotations.md) aan uw grafieken toevoegen wanneer u een nieuwe versie uitbrengt. De volgende afbeelding toont hoe deze markering wordt weergegeven.

![Schermopname van aantekening bij voorbeeldrelease in een grafiek](media/separate-resources/release-annotation.png)
## <a name="next-steps"></a>Volgende stappen

* [Gedeelde bronnen voor meerdere rollen](../../azure-monitor/app/app-map.md)
* [Een telemetrieinitialisator maken om A| te onderscheiden B-varianten](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
