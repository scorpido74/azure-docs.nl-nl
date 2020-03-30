---
title: Snelstart voor het toevoegen van functievlaggen aan ASP.NET Core
description: Functievlaggen toevoegen aan ASP.NET Core-apps en deze beheren met Azure App-configuratie
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: lcozzens
ms.openlocfilehash: d8582dfc796fe3e87b8bdc5be763dddfb5d0176b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245409"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Snelstart: functievlaggen toevoegen aan een ASP.NET Core-app

In deze quickstart maakt u een end-to-end implementatie van functiebeheer in een ASP.NET Core-toepassing met Azure App Configuration. U gebruikt de app-configuratieservice om al uw functievlaggen centraal op te slaan en hun status te beheren. 

De .NET Core Feature Management-bibliotheken breiden het framework uit met uitgebreide ondersteuning voor functievlag. Deze bibliotheken zijn gebouwd bovenop het .NET Core-configuratiesysteem. Ze integreren naadloos met app-configuratie via de .NET Core-configuratieprovider.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Een app-configuratiearchief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecteer **Functiebeheer** > **+Toevoegen** om een `Beta`functievlag toe te voegen, genaamd .

    > [!div class="mx-imgBorder"]
    > ![Functievlag met de naam Bèta inschakelen](media/add-beta-feature-flag.png)

    Laat `label` ongedefinieerd voor nu. Selecteer **Toepassen** om de nieuwe functievlag op te slaan.

## <a name="create-an-aspnet-core-web-app"></a>Een ASP.NET Core-web-app maken

Gebruik de [.NET Core command-line interface (CLI)](https://docs.microsoft.com/dotnet/core/tools/) om een nieuw ASP.NET Core MVC web app project te maken. Het voordeel van het gebruik van de .NET Core CLI in plaats van Visual Studio is dat de .NET Core CLI beschikbaar is op de Windows-, macOS- en Linux-platforms.

1. Maak een nieuwe map voor uw project. Voor deze quickstart, noem het *TestFeatureFlags*.

1. Voer in de nieuwe map de volgende opdracht uit om een nieuw ASP.NET Core MVC-webappproject te maken:

   ```    
   dotnet new mvc --no-https
   ```

## <a name="add-secret-manager"></a>Secret Manager toevoegen

Als u Secret Manager `UserSecretsId` wilt gebruiken, voegt u een element toe aan uw *.csproj-bestand.*

1. Open het *.csproj-bestand.*

1.  Voeg `UserSecretsId` een element toe zoals hier wordt weergegeven. U dezelfde GUID gebruiken, of u deze waarde vervangen door uw eigen waarde.

    > [!IMPORTANT]
    > `CreateHostBuilder`vervangt `CreateWebHostBuilder` in .NET Core 3.0.  Selecteer de juiste syntaxis op basis van uw omgeving.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>

        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>

    </Project>
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

        <PropertyGroup>
            <TargetFramework>netcoreapp3.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>

    </Project>
    ```
    ---

1. Sla het *.csproj-bestand* op.

Dit hulpprogramma slaat gevoelige gegevens voor ontwikkeltaken op buiten de projectstructuur. Deze aanpak voorkomt dat er per ongeluk appgeheimen worden gedeeld in de broncode.

> [!TIP]
> Zie [Veilige opslag van app-geheimen in ontwikkeling in ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets)voor meer informatie over Secret Manager.

## <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een app-configuratiearchief

1. Voeg een `Microsoft.Azure.AppConfiguration.AspNetCore` verwijzing `Microsoft.FeatureManagement.AspNetCore` toe naar de pakketten en NuGet door de volgende opdrachten uit te voeren:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. Voer de volgende opdracht uit om pakketten voor uw project te herstellen:

    ```dotnetcli
    dotnet restore
    ```

1. Voeg een geheim met de naam **ConnectionStrings:AppConfig** toe aan Secret Manager.

    Dit geheim bevat de verbindingstekenreeks om toegang te krijgen tot uw App Configuration Store. Vervang `<your_connection_string>` de waarde in de volgende opdracht door de verbindingstekenreeks voor uw App Configuration Store. U de verbindingstekenreeks vinden onder **Toegangssleutels** in de Azure-portal.

    Deze opdracht moet worden uitgevoerd in de map met het bestand *.csproj*.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    U gebruikt Secret Manager alleen om de web-app lokaal te testen. Wanneer u de app implementeert naar [Azure App Service,](https://azure.microsoft.com/services/app-service)gebruikt u bijvoorbeeld een toepassingsinstelling met de naam **Connection Strings** in App Service in plaats van Secret Manager te gebruiken om de verbindingstekenreeks op te slaan.

    U dit geheim openen met de App Configuration API. Een dikke darm (:) werkt in de configuratienaam met de App Configuration API op alle ondersteunde platforms. Zie [Configuratie per omgeving](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. Werk `CreateWebHostBuilder` de methode bij om `config.AddAzureAppConfiguration()` app-configuratie te gebruiken door de methode aan te roepen.
    
    > [!IMPORTANT]
    > `CreateHostBuilder`vervangt `CreateWebHostBuilder` in .NET Core 3.0.  Selecteer de juiste syntaxis op basis van uw omgeving.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    
    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                        .UseFeatureFlags();
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    
    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options => {
                options.Connect(settings["ConnectionStrings:AppConfig"])
                    .UseFeatureFlags();
            });
        })
        .UseStartup<Startup>());
    ```
    ---

1. Open *Startup.cs*en voeg verwijzingen toe naar de functiemanager .NET Core:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Werk `ConfigureServices` de methode bij om ondersteuning `services.AddFeatureManagement()` voor functievlagtoe te voegen door de methode aan te roepen. Optioneel u elk filter opnemen dat met `services.AddFeatureFilter<FilterType>()`functievlaggen kan worden gebruikt door aan te roepen:

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);        
        services.AddFeatureManagement();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }
    ```
    ---

1. Werk `Configure` de methode bij om een middleware toe te voegen zodat de waarden van de functievlag met een terugkerend interval kunnen worden vernieuwd terwijl de ASP.NET Core-web-app aanvragen blijft ontvangen.
    
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }

            app.UseStaticFiles();
            app.UseCookiePolicy();
            app.UseAzureAppConfiguration();
            app.UseMvc(routes =>
            {
                routes.MapRoute(
                    name: "default",
                    template: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }
            app.UseStaticFiles();
            app.UseRouting();
            app.UseAuthorization();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
            app.UseAzureAppConfiguration();
    }
    ```
    ---

1. Een *MyFeatureFlags.cs-bestand* toevoegen:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Voeg *BetaController.cs* toe aan de map *Controllers:*

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager)
            {
                _featureManager = featureManager;
            }

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index()
            {
                return View();
            }
        }
    }
    ```

1. Open *_ViewImports.cshtml* in de map *Weergaven* en voeg de taghelper voor functiebeheer toe:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Open *_Layout.cshtml* in de map*Gedeelde* *weergaven*\\ `<body>`  >  `<header>` en vervang de `<nav>` streepjescode onder de volgende code:

    ```html
    <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
        <div class="container">
            <a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">TestFeatureFlags</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target=".navbar-collapse" aria-controls="navbarSupportedContent"
            aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="navbar-collapse collapse d-sm-inline-flex flex-sm-row-reverse">
                <ul class="navbar-nav flex-grow-1">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Index">Home</a>
                    </li>
                    <feature name="Beta">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Beta" asp-action="Index">Beta</a>
                    </li>
                    </feature>
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
    ```

1. Maak een *bètamap* onder *Weergaven* en voeg *er Index.cshtml* aan toe:

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Als u de app wilt bouwen met de .NET Core CLI, voert u de volgende opdracht uit in de opdrachtshell:

    ```
    dotnet build
    ```

1. Nadat de build is voltooid, voert u de volgende opdracht uit om de web-app lokaal uit te voeren:

    ```
    dotnet run
    ```

1. Open een browservenster en `https://localhost:5000`ga naar , wat de standaard-URL is voor de web-app die lokaal wordt gehost.
    Als u in de Azure Cloud Shell werkt, selecteert u de knop *Webvoorbeeld,* gevolgd door *Configureren*.  Selecteer poort 5000 wanneer u daarom wordt gevraagd.

    ![De knop Webvoorbeeld zoeken](./media/quickstarts/cloud-shell-web-preview.png)

    Uw browser moet een pagina weergeven die vergelijkbaar is met de afbeelding hieronder.
    ![Quickstart voor het lokaal starten van een app](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Alle bronnen**en selecteer de instantie app-configuratiearchief die u in de snelstart hebt gemaakt.

1. Selecteer **Functiebeheer**en wijzig de status van de **bètasleutel** in **Aan**.

1. Ga terug naar de opdrachtprompt en annuleer het lopende `dotnet` proces door op `Ctrl-C`.  Start de `dotnet run`toepassing opnieuw met behulp van .

1. Vernieuw de browserpagina om de nieuwe configuratie-instellingen te zien.

    ![Quickstart voor het lokaal starten van een app](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een nieuwe app-configuratiewinkel gemaakt en deze gebruikt om functies in een ASP.NET Core-webapp te beheren via de [functiesbeheerbibliotheken.](https://go.microsoft.com/fwlink/?linkid=2074664)

- Meer informatie over [functiebeheer](./concept-feature-management.md).
- [Functievlaggen beheren](./manage-feature-flags.md).
- [Functievlaggen gebruiken in een ASP.NET Core-app](./use-feature-flags-dotnet-core.md).
- [Dynamische configuratie in een ASP.NET Core-app gebruiken](./enable-dynamic-configuration-aspnet-core.md)
