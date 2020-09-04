---
title: 'Quickstart: functievlaggen toevoegen aan ASP.NET Core'
description: U kunt functievlaggen toevoegen aan ASP.NET Core-apps en deze beheren met Azure App Configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: lcozzens
ms.openlocfilehash: 12b66dc173a8d3f93f97fb369ce03533299a65d7
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235261"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Quickstart: functievlaggen toevoegen aan een ASP.NET Core-app

In deze quickstart maakt u een end-to-end-implementatie van functiebeheer in een ASP.NET Core-toepassing met behulp van Azure App Configuration. U gebruikt de App Configuration-service om al uw functievlaggen centraal op te slaan en hun status te bepalen. 

De functiebeheerbibliotheken van .NET Core breiden het framework uit met uitgebreide ondersteuning voor functievlaggen. Deze bibliotheken worden boven op het .NET Core-configuratiesysteem gebouwd. Ze kunnen naadloos worden geïntegreerd met App Configuration via de configuratieprovider voor .NET Core.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Een App Configuration-archief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecteer **Functiebeheer** >  **+ Toevoegen** om een functievlag met de naam `Beta` toe te voegen.

    > [!div class="mx-imgBorder"]
    > ![Functievlag met de naam Beta inschakelen](media/add-beta-feature-flag.png)

    Laat `label` voor dit moment niet-gedefinieerd. Selecteer **Toepassen** om de nieuwe functievlag op te slaan.

## <a name="create-an-aspnet-core-web-app"></a>Een ASP.NET Core-web-app maken

Gebruik de [opdrachtregelinterface (CLI) van .NET Core](https://docs.microsoft.com/dotnet/core/tools/) om een nieuw web-app-project van ASP.NET Core MVC te maken. Het voordeel van de CLI van .NET Core ten opzichte van Visual Studio is dat de CLI van .NET Core beschikbaar is voor Windows, macOS en Linux.

1. Maak een nieuwe map voor uw project. Noem deze *TestFeatureFlags* voor deze quickstart.

1. Voer in de nieuwe map de volgende opdracht uit om een nieuw web-app-project van ASP.NET Core MVC te maken:

   ```    
   dotnet new mvc --no-https
   ```

## <a name="add-secret-manager"></a>Secret Manager toevoegen

Als u Secret Manager wilt gebruiken, voegt u een `UserSecretsId`-element toe aan uw *.csproj*-bestand.

1. Open het *.csproj*-bestand.

1.  Voeg een `UserSecretsId`-element toe, zoals hier wordt weergegeven. U kunt dezelfde GUID gebruiken, maar u kunt deze waarde ook vervangen door uw eigen waarden.

    > [!IMPORTANT]
    > `CreateHostBuilder` wordt vervangen door `CreateWebHostBuilder` in .NET Core 3.0.  Selecteer de juiste syntaxis op basis van uw omgeving.

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

1. Sla het *.csproj*-bestand op.

Dit hulpprogramma slaat gevoelige gegevens voor ontwikkeltaken op buiten de projectstructuur. Deze aanpak voorkomt dat er per ongeluk appgeheimen worden gedeeld in de broncode.

> [!TIP]
> Raadpleeg [Veilige opslag van app-geheimen in ontwikkeling in ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets) voor meer informatie over Secret Manager.

## <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een App Configuration-archief

1. Voeg een verwijzing toe naar de NuGet-pakketten `Microsoft.Azure.AppConfiguration.AspNetCore` en `Microsoft.FeatureManagement.AspNetCore` door de volgende opdrachten uit te voeren:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. Voer de volgende opdracht uit om pakketten voor uw project te herstellen:

    ```dotnetcli
    dotnet restore
    ```

1. Voeg een geheim met de naam **ConnectionStrings:AppConfig** toe aan Secret Manager.

    Dit geheim bevat straks de verbindingsreeks voor toegang tot uw App Configuration-archief. Vervang de waarde voor `<your_connection_string>` in de volgende opdracht door de verbindingsreeks voor uw App Configuration-archief. U vindt de primaire verbindingsreeks met alleen-lezensleutel onder **Toegangssleutels** in Azure Portal.

    Deze opdracht moet worden uitgevoerd in de map met het bestand *.csproj*.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    U gebruikt Secret Manager alleen om de web-app lokaal te testen. Wanneer u app bijvoorbeeld implementeert naar [Azure App Service](https://azure.microsoft.com/services/app-service), gebruikt u een toepassingsinstelling met de naam **Verbindingsreeksen** in App Service in plaats van Secret Manager om de verbindingsreeks op te slaan.

    U kunt dit geheim openen met de API van App Configuration. Een dubbele punt (:) werkt in de configuratienaam met de API van App Configuration op alle ondersteunde platforms. Zie [Configuratie per omgeving](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. Werk in *Program.cs* de methode `CreateWebHostBuilder` bij voor het gebruik van App Configuration door de methode `config.AddAzureAppConfiguration()` aan te roepen.

    > [!IMPORTANT]
    > `CreateHostBuilder` wordt vervangen door `CreateWebHostBuilder` in .NET Core 3.0.  Selecteer de juiste syntaxis op basis van uw omgeving.

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

1. Open *Startup.cs*en voeg verwijzingen toe naar functiebeheer van .NET Core:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Werk de methode `ConfigureServices` bij om ondersteuning voor functievlaggen toe te voegen door de methode `services.AddFeatureManagement()` aan te roepen. U kunt eventueel ook een filter toevoegen dat moet worden gebruikt met functievlaggen door `services.AddFeatureFilter<FilterType>()` aan te roepen:

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

    ---

1. Update the `Configure` method to add a middleware to allow the feature flag values to be refreshed at a recurring interval while the ASP.NET Core web app continues to receive requests.

    #### [.NET Core 2.x](#tab/core2x)
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

1. Voeg een bestand *MyFeatureFlags.cs*-bestand toe:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Voeg *BetaController.cs* toe aan de map *Controllers*:

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

1. Open *_ViewImports.cshtml* in de map *Views* en voeg de taghelper voor functiebeheer toe:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Open *_Layout.cshtml* in de map *Views*\\*Shared* en vervang de streepjescode `<nav>` onder `<body>` > `<header>` door de volgende code:

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

1. Maak een map *Beta* onder *Views* en voeg *Index.cshtml* eraan toe:

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Compileer de app met behulp van de .NET Core CLI door de volgende opdracht uit te voeren in de opdrachtshell:

    ```
    dotnet build
    ```

1. Nadat het bouwen is voltooid, voert u de volgende opdracht uit om de web-app lokaal uit te voeren:

    ```
    dotnet run
    ```

1. Open een browservenster en ga naar `https://localhost:5000`. Dit is de standaard-URL voor de web-app die lokaal wordt gehost.
    Als u werkt in Azure Cloud Shell, selecteert u de knop *Web-preview* gevolgd door *Configureren*.  Selecteer poort 5000 wanneer hierom wordt gevraagd.

    ![De knop Web-preview zoeken](./media/quickstarts/cloud-shell-web-preview.png)

    In uw browser wordt een pagina weergegeven die vergelijkbaar is met de onderstaande afbeelding.
    ![Quickstart voor lokaal starten van app](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Alle resources** en selecteer de instantie van het App Configuration-archief dat u in de quickstart hebt gemaakt.

1. Selecteer **Functiebeheer** en wijzig de status van de sleutel **Beta** in **Aan**:

1. Ga terug naar de opdrachtprompt en annuleer het actieve `dotnet`-proces door op `Ctrl-C` te drukken.  Start de toepassing opnieuw met `dotnet run`.

1. Vernieuw de browserpagina om de nieuwe configuratie-instellingen te zien.

    ![Quickstart voor het lokaal starten van een app](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een nieuw App Configuration-archief gemaakt en dit gebruikt om functies in een ASP.NET Core-web-app te beheren via de [functiebeheerbibliotheken](https://go.microsoft.com/fwlink/?linkid=2074664).

- Meer informatie over [functiebeheer](./concept-feature-management.md).
- [Functievlaggen beheren](./manage-feature-flags.md).
- [Functievlaggen gebruiken in een ASP.NET Core-app](./use-feature-flags-dotnet-core.md)
- [Dynamische configuratie in een ASP.NET Core-app gebruiken](./enable-dynamic-configuration-aspnet-core.md)
