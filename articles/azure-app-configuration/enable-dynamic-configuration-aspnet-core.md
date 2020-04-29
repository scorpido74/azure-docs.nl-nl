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
ms.openlocfilehash: e9df6d2e7a8219d16e7b60f7c3b8d826a87e6110
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80348859"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Zelf studie: dynamische configuratie in een ASP.NET Core-app gebruiken

ASP.NET Core heeft een pluggable configuratie systeem waarmee configuratie gegevens kunnen worden gelezen uit verschillende bronnen. Het kan dynamisch wijzigingen verwerken zonder dat de toepassing opnieuw wordt gestart. ASP.NET Core ondersteunt de binding van configuratie-instellingen tot sterk getypeerde .NET-klassen. Ze worden in uw code ingevoegd met behulp van de `IOptions<T>` verschillende patronen. Een van deze patronen, in `IOptionsSnapshot<T>`het bijzonder, laadt de configuratie van de toepassing automatisch opnieuw wanneer de onderliggende gegevens worden gewijzigd. U kunt `IOptionsSnapshot<T>` in domeincontrollers in uw toepassing invoeren voor toegang tot de meest recente configuratiegegevens die zijn opgeslagen in Azure-app-configuratie.

U kunt ook de app-configuratie ASP.NET Core client bibliotheek instellen om een set configuratie-instellingen dynamisch te vernieuwen met behulp van een middleware. De configuratie-instellingen worden elke keer bijgewerkt met de configuratie opslag, zolang de web-app aanvragen ontvangt.

Bij app-configuratie wordt elke instelling automatisch in de cache opgeslagen om te veel aanroepen naar het configuratie archief te voor komen. De vernieuwings bewerking wacht totdat de waarde in de cache van een instelling verloopt om deze instelling bij te werken, zelfs wanneer de waarde ervan in het configuratie archief wordt gewijzigd. De standaard waarde voor de verval tijd van de cache is 30 seconden. U kunt deze verloop tijd, indien nodig, overschrijven.

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

## <a name="add-a-sentinel-key"></a>Een Sentinel-sleutel toevoegen

Een *Sentinel-sleutel* is een speciale sleutel die wordt gebruikt om te Signa leren wanneer de configuratie is gewijzigd. Uw app controleert de verklikker sleutel op wijzigingen. Wanneer een wijziging wordt gedetecteerd, vernieuwt u alle configuratie waarden. Deze aanpak vermindert het totale aantal aanvragen dat door uw app naar de app-configuratie wordt gemaakt, vergeleken met het bewaken van alle sleutels voor wijzigingen.

1. Selecteer in de Azure Portal **configuratie Explorer > > sleutel waarde te maken**.

1. Voer voor **sleutel** *TestApp: instellingen: Sentinel*in. Voer voor **waarde**1 in. Laat het **Label** en het **inhouds type** leeg.

1. Selecteer **Toepassen**.

## <a name="reload-data-from-app-configuration"></a>Gegevens opnieuw laden vanuit app-configuratie

1. Voeg een verwijzing naar het `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet-pakket toe door de volgende opdracht uit te voeren:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Open *Program.cs*en werk de `CreateWebHostBuilder` methode bij om de `config.AddAzureAppConfiguration()` methode toe te voegen.

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
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
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
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    ---

    De `ConfigureRefresh` -methode wordt gebruikt om de instellingen op te geven die worden gebruikt voor het bijwerken van de configuratie gegevens met het app-configuratie archief wanneer een vernieuwings bewerking wordt geactiveerd. De `refreshAll` para meter voor `Register` de methode geeft aan dat alle configuratie waarden moeten worden vernieuwd als de Sentinel-sleutel wordt gewijzigd.

    De `SetCacheExpiration` methode overschrijft ook de standaard verval tijd van de cache van 30 seconden, waarbij een tijd van vijf minuten wordt opgegeven. Dit beperkt het aantal aanvragen voor de app-configuratie.

    > [!NOTE]
    > Voor test doeleinden wilt u de verval tijd van de cache wellicht verlagen.

    Als u een vernieuwings bewerking daad werkelijk wilt activeren, moet u een middleware voor het vernieuwen van de toepassing configureren om de configuratie gegevens te vernieuwen wanneer er wijzigingen optreden. In een latere stap ziet u hoe u dit doet.

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

3. Open *Startup.cs*en gebruik `IServiceCollection.Configure<T>` in de `ConfigureServices` methode om configuratie gegevens te binden aan de `Settings` klasse.

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

4. Werk de `Configure` -methode bij, `UseAzureAppConfiguration` waarbij de middleware wordt toegevoegd zodat de configuratie-instellingen die zijn geregistreerd voor vernieuwen, kunnen worden bijgewerkt terwijl de ASP.net core web-app aanvragen blijft ontvangen.


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
    
    De middleware maakt gebruik van de vernieuwings `AddAzureAppConfiguration` configuratie die `Program.cs` is opgegeven in de methode in om een vernieuwing te activeren voor elke aanvraag die wordt ontvangen door de ASP.net core web-app. Voor elke aanvraag wordt een vernieuwings bewerking geactiveerd en controleert de client bibliotheek of de in de cache opgeslagen waarde voor de geregistreerde configuratie-instelling is verlopen. Als de service is verlopen, wordt deze vernieuwd.

## <a name="use-the-latest-configuration-data"></a>De meest recente configuratiegegevens gebruiken

1. Open *HomeController.cs* in de map controllers en voeg een verwijzing naar het `Microsoft.Extensions.Options` pakket toe.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Werk de `HomeController` klasse bij om `Settings` deze via afhankelijkheids injectie te ontvangen en gebruik de waarden ervan.

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
            font-size: @ViewData["FontSize"]px;
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

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Als u de app wilt bouwen met behulp van de .NET Core SLI, voert u de volgende opdracht uit in de opdracht shell:

        dotnet build

1. Wanneer de build is voltooid, voert u de volgende opdracht uit om de web-app lokaal uit te voeren:

        dotnet run
1. Open een browser venster en ga naar de URL die wordt weer gegeven `dotnet run` in de uitvoer.

    ![De Quick Start-app wordt lokaal gestart](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **alle resources**en selecteer de app-configuratie Store-instantie die u hebt gemaakt in de Quick Start.

1. Selecteer **Configuration Explorer**en werk de waarden van de volgende sleutels bij:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Gegevens uit Azure-app-configuratie - nu met live updates! |
    | TestApp: instellingen: Sentinel | 2 |

1. Vernieuw de browserpagina om de nieuwe configuratie-instellingen te zien. Mogelijk moet u meer dan één keer vernieuwen voordat de wijzigingen worden doorgevoerd.

    ![De bijgewerkte Snelstartgids-app lokaal starten](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u uw ASP.NET Core web-app ingeschakeld om de configuratie-instellingen van de app-configuratie dynamisch te vernieuwen. Ga verder met de volgende zelf studie als u wilt weten hoe u een door Azure beheerde identiteit kunt gebruiken om de toegang tot de app-configuratie te stroom lijnen.

> [!div class="nextstepaction"]
> [Beheerde identiteits integratie](./howto-integrate-azure-managed-service-identity.md)
