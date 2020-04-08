---
title: Toepassingsinzichten verwijderen in Visual Studio - Azure-monitor
description: Application Insights SDK verwijderen voor ASP.NET en ASP.NET Core in Visual Studio.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 1c9ff8d3d305645ac7d113421e2c6c5f8451bd2b
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80805103"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>Toepassingsinzichten verwijderen in Visual Studio

In dit artikel ziet u hoe u de ASP.NET en ASP.NET Core Application Insights SDK in Visual Studio verwijderen.

Als u Application Insights wilt verwijderen, moet u de NuGet-pakketten en -verwijzingen uit de API in uw toepassing verwijderen. U NuGet-pakketten verwijderen met behulp van de Package Management Console of NuGet-oplossing beheren in Visual Studio. In de volgende secties worden twee manieren weergegeven om NuGet-pakketten te verwijderen en wat automatisch in uw project is toegevoegd. Zorg ervoor dat de toegevoegde bestanden en gebieden met in uw eigen code waarin u gesprekken naar de API worden verwijderd.

## <a name="uninstall-using-the-package-management-console"></a>Verwijderen met de package management console

# <a name="net"></a>[.NET](#tab/net)

1. Als u de Package Management Console wilt openen, selecteert u in het bovenste menu Extra > NuGet Package Manager > Package Manager Console.
     
    ![Klik in het bovenste menu op Extra > NuGet Package Manager > Package Manager Console](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > Als traceerverzameling is ingeschakeld, moet u microsoft.ApplicationInsights.TraceListener eerst verwijderen. Voer `Uninstall-package Microsoft.ApplicationInsights.TraceListener` vervolgens de onderstaande stap in om Microsoft.ApplicationInsights.Web te verwijderen.

1. Voer de volgende opdracht in: `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    Nadat u de opdracht hebt ingevoerd, worden het pakket Application Insights en alle afhankelijkheden van toepassing verwijderd uit het project.
    
    ![Opdracht invoeren in console](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Als u de Package Management Console wilt openen, selecteert u in het bovenste menu Extra > NuGet Package Manager > Package Manager Console.

    ![Klik in het bovenste menu op Extra > NuGet Package Manager > Package Manager Console](./media/remove-application-insights/package-manager.png)

1. Voer de volgende opdracht in: ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    Nadat u de opdracht hebt ingevoerd, worden het pakket Application Insights en alle afhankelijkheden van toepassing verwijderd uit het project.

---

## <a name="uninstall-using-the-visual-studio-nugetui"></a>Verwijderen met de Visual Studio NuGet-gebruikersinterface

# <a name="net"></a>[.NET](#tab/net)

1. Klik in de *Solution Explorer* aan de rechterkant met de rechtermuisknop op **Oplossing** en selecteer **NuGet-pakketten voor oplossingen beheren.**

    Je ziet dan een scherm waarmee je alle NuGet-pakketten bewerken die deel uitmaken van het project.
    
     ![Klik met de rechtermuisknop op Oplossing in de Oplossingsverkenner en selecteer NuGet-pakketten voor oplossing beheren](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > Als traceerverzameling is ingeschakeld, moet u Microsoft.ApplicationInsights.TraceListener eerst verwijderen zonder geselecteerde afhankelijkheden te verwijderen en vervolgens de onderstaande stappen te volgen om Microsoft.ApplicationInsights.Web te verwijderen met geselecteerde afhankelijkheden te verwijderen.
    
1. Klik op het pakket "Microsoft.ApplicationInsights.Web".Schakel aan de rechterkant het selectievakje naast *Project* in om alle projecten te selecteren.
    
1. Als u alle afhankelijkheden wilt verwijderen bij het verwijderen, selecteert u de vervolgkeuzeknop **Opties** onder de sectie waar u project hebt geselecteerd.

    Schakel *onder Opties verwijderen*het selectievakje naast *Afhankelijkheden verwijderen*in.

1. Selecteer **Verwijderen**.
    
    ![Verwijder afhankelijkheden en verwijder de verwijdering](./media/remove-application-insights/uninstall-framework.png)

    Er wordt een dialoogvenster weergegeven waarin alle afhankelijkheden worden weergegeven die uit de toepassing moeten worden verwijderd.Selecteer **ok** om de installatie te verwijderen.
    
    ![Verwijder afhankelijkheden en verwijder de verwijdering](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  Nadat alles is verwijderd, u nog steeds ApplicationInsights.config en AiHandleErrorAttribute.cs in de *Solution Explorer zien.*U de twee bestanden handmatig verwijderen.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Klik in de *Solution Explorer* aan de rechterkant met de rechtermuisknop op **Oplossing** en selecteer **NuGet-pakketten voor oplossingen beheren.**

    Je ziet dan een scherm waarmee je alle NuGet-pakketten bewerken die deel uitmaken van het project.

    ![Klik met de rechtermuisknop op Oplossing in de Oplossingsverkenner en selecteer NuGet-pakketten voor oplossing beheren](./media/remove-application-insights/manage-nuget-core.png)

1. Klik op het pakket "Microsoft.ApplicationInsights.AspNetCore". Schakel aan de rechterkant het selectievakje naast *Project* in om alle projecten te selecteren en selecteer **Verwijderen**.

    ![Verwijder afhankelijkheden en verwijder de verwijdering](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>Wat wordt gemaakt wanneer u Toepassingsinzichten toevoegt

Wanneer u Application Insights aan uw project toevoegt, worden bestanden en voegt code toe aan sommige bestanden. Alleen het verwijderen van de NuGet-pakketten zal niet altijd de bestanden en code verwijderen. Als u Application Insights volledig wilt verwijderen, moet u de toegevoegde code of bestanden controleren en handmatig verwijderen, samen met eventuele API-aanroepen die u in uw project hebt toegevoegd.

# <a name="net"></a>[.NET](#tab/net)

Wanneer u Telemetrie van Application Insights toevoegt aan een Visual Studio-ASP.NET project, worden de volgende bestanden toegevoegd:

- ApplicationInsights.config
- AiHandleErrorAttribute.cs

De volgende codestukken worden toegevoegd:

- [De naam van uw project].csproj

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- Pakketten.config

    ```xml
    <packages>
    ...
    
      <package id="Microsoft.ApplicationInsights" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Agent.Intercept" version="2.4.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.DependencyCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.PerfCounterCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Web" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" version="2.12.0" targetFramework="net472" />
    
      <package id="Microsoft.AspNet.TelemetryCorrelation" version="1.0.7" targetFramework="net472" />
    
      <package id="System.Buffers" version="4.4.0" targetFramework="net472" />
      <package id="System.Diagnostics.DiagnosticSource" version="4.6.0" targetFramework="net472" />
      <package id="System.Memory" version="4.5.3" targetFramework="net472" />
      <package id="System.Numerics.Vectors" version="4.4.0" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
    ...
    </packages>
    ```

- Layout.cshtml

    Als uw project een Lay-cshtml-bestand heeft, wordt de onderstaande code toegevoegd.
    
    ```html
    <head>
    ...
        <script type = 'text/javascript' >
            var appInsights=window.appInsights||function(config)
            {
                function r(config){ t[config] = function(){ var i = arguments; t.queue.push(function(){ t[config].apply(t, i)})} }
                var t = { config:config},u=document,e=window,o='script',s=u.createElement(o),i,f;for(s.src=config.url||'//az416426.vo.msecnd.net/scripts/a/ai.0.js',u.getElementsByTagName(o)[0].parentNode.appendChild(s),t.cookie=u.cookie,t.queue=[],i=['Event','Exception','Metric','PageView','Trace','Ajax'];i.length;)r('track'+i.pop());return r('setAuthenticatedUserContext'),r('clearAuthenticatedUserContext'),config.disableExceptionTracking||(i='onerror',r('_'+i),f=e[i],e[i]=function(config, r, u, e, o) { var s = f && f(config, r, u, e, o); return s !== !0 && t['_' + i](config, r, u, e, o),s}),t
            }({
                instrumentationKey:'00000000-0000-0000-0000-000000000000'
            });
            
            window.appInsights=appInsights;
            appInsights.trackPageView();
        </script>
    ...
    </head>
    ```

- ConnectedService.json

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=613413"
      }
    }
    ```

- FilterConfig.cs

    ```csharp
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());// This line was added
            }
    ```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Wanneer u Telemetrie van Application Insights toevoegt aan een Visual Studio ASP.NET Core-sjabloonproject, wordt de volgende code toegevoegd:

- [De naam van uw project].csproj

    ```csharp
      <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4core</ApplicationInsightsResourceId>
      </PropertyGroup>
    
      <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
      </ItemGroup>
    
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    ```

- Appsettings.json:

    ```json
    "ApplicationInsights": {
        "InstrumentationKey": "00000000-0000-0000-0000-000000000000"
    ```

- ConnectedService.json
    
    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```
- Startup.cs

    ```csharp
       public void ConfigureServices(IServiceCollection services)
            {
                services.AddRazorPages();
                services.AddApplicationInsightsTelemetry(); // This is added
            }
    ```

---

## <a name="next-steps"></a>Volgende stappen

- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)