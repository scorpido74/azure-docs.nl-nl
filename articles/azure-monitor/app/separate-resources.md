---
title: Telemetrie scheiden in Azure-toepassing Insights
description: Telemetrie omleiden naar verschillende bronnen voor ontwikkelings-, test-en productie tempels.
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 3580d162f4b3955a04ffcd0f13933221bfef3b65
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671457"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Telemetrie scheiden van ontwikkeling, testen en productie

Wanneer u de volgende versie van een webtoepassing ontwikkelt, wilt u niet de [Application Insights](../../azure-monitor/app/app-insights-overview.md) telemetrie uit de nieuwe versie en de reeds uitgebrachte versie samen stellen. Om Verwar ring te voor komen, verzendt u de telemetrie vanuit verschillende ontwikkelings fasen om Application Insights resources te scheiden, met afzonderlijke instrumentatie sleutels (ikeys). Om het gemakkelijker te maken om de instrumentatie sleutel te wijzigen wanneer een versie van de ene fase naar een andere gaat, kan het nuttig zijn om de iKey in te stellen in code in plaats van in het configuratie bestand. 

(Als uw systeem een Azure-Cloud service is, is er [een andere methode voor het instellen van afzonderlijke ikeys](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Over resources en instrumentatie sleutels

Wanneer u Application Insights bewaking voor uw web-app instelt, maakt u een Application Insights *resource* in Microsoft Azure. U opent deze resource in de Azure Portal om de telemetrie die u hebt verzameld uit uw app te bekijken en te analyseren. De resource wordt geïdentificeerd door een *instrumentatie sleutel* (Ikey). Wanneer u het Application Insights-pakket installeert om uw app te bewaken, kunt u deze configureren met de instrumentatie sleutel, zodat u weet waar de telemetrie moet worden verzonden.

Normaal gesp roken kiest u voor het gebruik van afzonderlijke resources of één gedeelde bron in verschillende scenario's:

* Verschillende, onafhankelijke toepassingen: gebruik een afzonderlijke resource-en iKey voor elke app.
* Meerdere onderdelen of rollen van één zakelijke toepassing: gebruik [één gedeelde resource](../../azure-monitor/app/app-map.md) voor alle onderdeel-apps. Telemetrie kan worden gefilterd of gesegmenteerd door de eigenschap cloud_RoleName.
* Ontwikkeling, testen en vrijgeven: gebruik een afzonderlijke resource en iKey voor versies van het systeem in ' Stamp ' of productie fase.
* A | B testen: een enkele resource gebruiken. Maak een TelemetryInitializer om een eigenschap toe te voegen aan de telemetrie waarmee de varianten worden geïdentificeerd.


## <a name="dynamic-ikey"></a>Dynamische instrumentatie sleutel

Als u de iKey eenvoudiger wilt maken, moet u deze in code instellen in plaats van in het configuratie bestand.

Stel de sleutel in een initialisatie methode in, zoals global.aspx.cs in een ASP.NET-service:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

In dit voor beeld worden de ikeys voor de verschillende bronnen in verschillende versies van het webconfiguratiebestand geplaatst. Het webconfiguratiebestand: dat u kunt doen als onderdeel van het release script, wordt de doel resource vervangen.

### <a name="web-pages"></a>Webpagina's
De iKey wordt ook gebruikt op de webpagina's van uw app, in het [script dat u hebt ontvangen van de Blade Quick](../../azure-monitor/app/javascript.md)start. In plaats van de consistentie in het script te maken, moet u deze genereren op basis van de server status. Bijvoorbeeld in een ASP.NET-app:

*Java script in haar*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>Aanvullende Application Insights-resources maken
Als u telemetrie voor verschillende toepassings onderdelen wilt scheiden of voor verschillende stem Pels (dev/test/productie) van hetzelfde onderdeel, moet u een nieuwe Application Insights-resource maken.

Voeg in het [Portal.Azure.com](https://portal.azure.com)een Application Insights resource toe:

![Klik op Nieuw > Application Insights](./media/separate-resources/01-new.png)

* Het **toepassings type** is van invloed op wat u ziet op de Blade overzicht en de eigenschappen die beschikbaar zijn in [metrische Explorer](../../azure-monitor/app/metrics-explorer.md). Als u uw app-type niet ziet, kiest u een van de webtypen voor webpagina's.
* De resource groep is een handig **hulp middel** voor het beheren van eigenschappen zoals [toegangs beheer](../../azure-monitor/app/resources-roles-access-control.md). U kunt afzonderlijke resource groepen gebruiken voor ontwikkeling, testen en productie.
* Het **abonnement** is uw betalings account in Azure.
* **Locatie** is waar we uw gegevens blijven gebruiken. Het kan momenteel niet worden gewijzigd. 
* **Aan het dash board toevoegen** wordt een tegel voor snelle toegang voor uw resource op uw Azure-start pagina geplaatst. 

Het maken van de resource duurt een paar seconden. U ziet een waarschuwing wanneer deze is voltooid.

(U kunt een [Power shell-script](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) schrijven om een resource automatisch te maken.)

### <a name="getting-the-instrumentation-key"></a>De instrumentatie sleutel ophalen
De instrumentatie sleutel identificeert de resource die u hebt gemaakt. 

![Klik op Essentials, klik op de instrumentatie sleutel, CTRL + C](./media/separate-resources/02-props.png)

U hebt de instrumentatie sleutels nodig van alle resources waarnaar de app gegevens verzendt.

## <a name="filter-on-build-number"></a>Filteren op build-nummer
Wanneer u een nieuwe versie van uw app publiceert, wilt u de telemetrie van verschillende builds kunnen scheiden.

U kunt de eigenschap toepassings versie instellen, zodat u [Zoek opdrachten](../../azure-monitor/app/diagnostic-search.md) en [metrische Explorer](../../azure-monitor/app/metrics-explorer.md) -resultaten kunt filteren.

![Filteren op een eigenschap](./media/separate-resources/050-filter.png)

Er zijn verschillende methoden voor het instellen van de eigenschap toepassings versie.

* Direct instellen:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Verpakken die regel in een [telemetrie-initialisatie functie](../../azure-monitor/app/api-custom-events-metrics.md#defaults) om ervoor te zorgen dat alle TelemetryClient-instanties consistent zijn ingesteld.
* [ASP.NET] Stel de versie in `BuildInfo.config`. De WEBMODULE haalt de versie op uit het BuildLabel-knoop punt. Neem dit bestand op in uw project en vergeet niet om de eigenschap Copy always in te stellen in Solution Explorer.

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
* [ASP.NET] Genereer BuildInfo. config automatisch in MSBuild. U kunt dit doen door een paar regels aan uw `.csproj`-bestand toe te voegen:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Hiermee wordt een bestand met de naam *yourProjectName*gegenereerd. BuildInfo. config. de naam van het publicatie proces wordt gewijzigd in BuildInfo. config.

    Het build-label bevat een tijdelijke aanduiding (AutoGen_...) wanneer u met Visual Studio bouwt. Maar wanneer het is gebouwd met MSBuild, wordt het ingevuld met het juiste versie nummer.

    Als u wilt toestaan dat MSBuild versie nummers genereert, stelt u de versie als `1.0.*` in AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Versie en release bijhouden
Als u de toepassingsversie wilt bijhouden, zorgt u ervoor dat `buildinfo.config` wordt gegenereerd door het Microsoft Build Engine-proces. Voeg het volgende toe in uw `.csproj`-bestand:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Wanneer de buildgegevens beschikbaar zijn, voegt de Application Insights-webmodule automatisch **Toepassingsversie** als eigenschap toe aan elk telemetrie-item. Dit biedt de mogelijkheid om op versie te filteren wanneer u [diagnostische zoekopdrachten](../../azure-monitor/app/diagnostic-search.md) uitvoert of [metrische gegevens verkent](../../azure-monitor/app/metrics-explorer.md).

U ziet echter dat het build-versie nummer alleen wordt gegenereerd door de micro soft build engine, niet door de ontwikkel aars versie van Visual Studio.

### <a name="release-annotations"></a>Release-aantekeningen
Als u Azure DevOps gebruikt, kunt u [een aantekeningen markering](../../azure-monitor/app/annotations.md) toevoegen aan uw diagrammen wanneer u een nieuwe versie uitgeeft. De volgende afbeelding toont hoe deze markering wordt weergegeven.

![Schermopname van aantekening bij voorbeeldrelease in een grafiek](media/separate-resources/release-annotation.png)
## <a name="next-steps"></a>Volgende stappen

* [Gedeelde bronnen voor meerdere rollen](../../azure-monitor/app/app-map.md)
* [Maak een telemetrie-initialisatie functie om onderscheid te maken tussen een | B varianten](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
