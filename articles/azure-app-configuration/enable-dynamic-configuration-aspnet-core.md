---
title: 'Zelf studie: app-configuratie dynamische configuratie in ASP.NET Core gebruiken'
titleSuffix: Azure App Configuration
description: In deze zelfstudie leert u hoe u de configuratiegegevens voor ASP.NET Core-apps dynamisch bijwerkt
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 3c461e543e3b01501ec47589a9eab3d74820491a
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77500237"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Zelf studie: dynamische configuratie in een ASP.NET Core-app gebruiken

ASP.NET Core heeft een pluggable configuratie systeem waarmee configuratie gegevens kunnen worden gelezen uit verschillende bronnen. Het kan wijzigingen aan de vlucht afhandelen zonder dat de toepassing opnieuw wordt gestart. ASP.NET Core ondersteunt de binding van configuratie-instellingen tot sterk getypeerde .NET-klassen. Ze worden in uw code ingevoegd met behulp van de verschillende `IOptions<T>` patronen. Een van deze patronen, met name `IOptionsSnapshot<T>`, laadt automatisch de configuratie van de toepassing wanneer de onderliggende gegevens worden gewijzigd. U kunt `IOptionsSnapshot<T>` in domeincontrollers in uw toepassing invoeren voor toegang tot de meest recente configuratiegegevens die zijn opgeslagen in Azure-app-configuratie.

U kunt ook de app-configuratie ASP.NET Core client bibliotheek instellen om een set configuratie-instellingen dynamisch te vernieuwen met behulp van een middleware. Zolang de web-app aanvragen blijft ontvangen, blijven de configuratie-instellingen bijgewerkt met de configuratie opslag.

Als u de instellingen wilt bijwerken en te veel aanroepen naar de configuratie opslag wilt voor komen, wordt er voor elke instelling een cache gebruikt. Totdat de in de cache opgeslagen waarde van een instelling is verlopen, wordt de waarde niet door de vernieuwings bewerking bijgewerkt, zelfs niet wanneer de waarde is gewijzigd in de configuratie opslag. De standaard verval tijd voor elke aanvraag is 30 seconden, maar kan indien nodig worden overschreven.

In deze zelfstudie leert hoe u dynamische configuratie-updates kunt implementeren in uw code. Dit is gebaseerd op de web-app die is geïntroduceerd in de quickstarts. Voordat u doorgaat, moet u eerst [een ASP.net core-app maken met de app-configuratie](./quickstart-aspnet-core-app.md) .

U kunt elke code-editor gebruiken om de stappen in deze zelf studie uit te voeren. [Visual Studio code](https://code.visualstudio.com/) is een uitstekende optie die beschikbaar is op de Windows-, macOS-en Linux-platformen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Stel uw toepassing in om de configuratie bij te werken als reactie op wijzigingen in een app-configuratie archief.
> * Injecteer de meest recente configuratie in de controllers van uw toepassing.

## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt uitvoeren, installeert u de [.net core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Voordat u doorgaat, moet u eerst [een ASP.net core-app maken met de app-configuratie](./quickstart-aspnet-core-app.md) .

## <a name="reload-data-from-app-configuration"></a>Gegevens opnieuw laden vanuit app-configuratie

1. Voeg een verwijzing naar het NuGet-pakket van `Microsoft.Azure.AppConfiguration.AspNetCore` toe door de volgende opdracht uit te voeren:

    ```CLI
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Open *Program.cs*en werk de methode `CreateWebHostBuilder` bij om de methode `config.AddAzureAppConfiguration()` toe te voegen.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                           {
                               refresh.Register("TestApp:Settings:BackgroundColor")
                                      .Register("TestApp:Settings:FontColor")
                                      .Register("TestApp:Settings:Message");
                           });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {   
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:BackgroundColor")
                                            .Register("TestApp:Settings:FontColor")
                                            .Register("TestApp:Settings:Message");
                                });
                    });
                })
            .UseStartup<Startup>());
    ```
    ---

    De methode `ConfigureRefresh` wordt gebruikt om de instellingen op te geven die worden gebruikt voor het bijwerken van de configuratie gegevens met het app-configuratie archief wanneer een vernieuwings bewerking wordt geactiveerd. Als u een vernieuwings bewerking daad werkelijk wilt activeren, moet er een vernieuwde middleware worden geconfigureerd voor de toepassing om de configuratie gegevens te vernieuwen wanneer er wijzigingen optreden.

2. Voeg een *Settings.cs*-bestand toe dat een nieuwe `Settings`-klasse definieert en implementeert.

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. Open *Startup.cs*en gebruik `IServiceCollection.Configure<T>` in de methode `ConfigureServices` om configuratie gegevens te binden aan de `Settings`-klasse.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
    }
    ```
    ---

4. Werk de `Configure`-methode bij, waarbij de `UseAzureAppConfiguration` middleware wordt toegevoegd zodat de configuratie-instellingen die voor vernieuwen zijn geregistreerd, kunnen worden bijgewerkt terwijl de ASP.NET Core web-app aanvragen blijft ontvangen.


    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    ---
    
    De middleware maakt gebruik van de vernieuwings configuratie die is opgegeven in de `AddAzureAppConfiguration` methode in `Program.cs` om een vernieuwing te activeren voor elke aanvraag die wordt ontvangen door de ASP.NET Core web-app. Voor elke aanvraag wordt een vernieuwings bewerking geactiveerd en controleert de client bibliotheek of de in de cache opgeslagen waarde voor de geregistreerde configuratie-instellingen is verlopen. Voor de in de cache opgeslagen waarden die zijn verlopen, worden de waarden voor de instellingen bijgewerkt met de app-configuratie opslag en blijven de resterende waarden ongewijzigd.
    
    > [!NOTE]
    > De standaard waarde voor de verval tijd van de cache voor een configuratie-instelling is 30 seconden, maar kan worden overschreven door de `SetCacheExpiration`-methode aan te roepen voor de initialisatie functie voor opties die als een argument aan de `ConfigureRefresh`-methode is door gegeven.

## <a name="use-the-latest-configuration-data"></a>De meest recente configuratiegegevens gebruiken

1. Open *HomeController.cs* in de map controllers en voeg een verwijzing naar het `Microsoft.Extensions.Options`-pakket toe.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Werk de `HomeController` klasse bij om `Settings` te ontvangen via afhankelijkheids injectie en gebruik van de waarden.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
    ```
    ---



3. Open *index. cshtml* in de weer gaven > basismap en vervang de inhoud door het volgende script:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>De app lokaal bouwen en uitvoeren

1. Als u de app wilt bouwen met behulp van de .NET Core SLI, voert u de volgende opdracht uit in de opdracht shell:

        dotnet build

2. Wanneer de build is voltooid, voert u de volgende opdracht uit om de web-app lokaal uit te voeren:

        dotnet run

3. Open een browser venster en ga naar `http://localhost:5000`. Dit is de standaard-URL voor de web-app die lokaal wordt gehost.

    ![Quickstart voor het lokaal starten van een app](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **alle resources**en selecteer de app-configuratie Store-instantie die u hebt gemaakt in de Quick Start.

5. Selecteer **Configuration Explorer**en werk de waarden van de volgende sleutels bij:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:BackgroundColor | groen |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Gegevens uit Azure-app-configuratie - nu met live updates! |

6. Vernieuw de browserpagina om de nieuwe configuratie-instellingen te zien. Het kan zijn dat er meer dan één vernieuwing van de browser pagina vereist is om de wijzigingen weer te Spie laten.

    ![Quickstart voor het lokaal vernieuwen van een app](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > Omdat de configuratie-instellingen worden opgeslagen in de cache met een standaard verloop tijd van 30 seconden, worden wijzigingen die zijn aangebracht in de instellingen in de app-configuratie opslag, alleen weer gegeven in de web-app wanneer de cache is verlopen.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u uw ASP.NET Core web-app ingeschakeld om de configuratie-instellingen van de app-configuratie dynamisch te vernieuwen. Ga verder met de volgende zelf studie als u wilt weten hoe u een door Azure beheerde identiteit kunt gebruiken om de toegang tot de app-configuratie te stroom lijnen.

> [!div class="nextstepaction"]
> [Beheerde identiteits integratie](./howto-integrate-azure-managed-service-identity.md)
