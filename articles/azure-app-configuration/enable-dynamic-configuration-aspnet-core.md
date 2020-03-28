---
title: 'Zelfstudie: Dynamische configuratie van app-configuratie gebruiken in ASP.NET Core'
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
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348859"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Zelfstudie: Dynamische configuratie gebruiken in een ASP.NET Core-app

ASP.NET Core heeft een pluggable configuratiesysteem dat configuratiegegevens uit verschillende bronnen kan lezen. Het kan wijzigingen dynamisch verwerken zonder dat een toepassing opnieuw wordt opgestart. ASP.NET Core ondersteunt de binding van configuratie-instellingen om sterk getypte .NET-klassen te typen. Het injecteert ze in uw `IOptions<T>` code met behulp van de verschillende patronen. Een van deze `IOptionsSnapshot<T>`patronen, in het bijzonder, automatisch herlaadt de configuratie van de toepassing wanneer de onderliggende gegevens verandert. U kunt `IOptionsSnapshot<T>` in domeincontrollers in uw toepassing invoeren voor toegang tot de meest recente configuratiegegevens die zijn opgeslagen in Azure-app-configuratie.

U ook de app-configuratie ASP.NET Core-clientbibliotheek instellen om een set configuratie-instellingen dynamisch te vernieuwen met behulp van een middleware. De configuratie-instellingen worden telkens bijgewerkt met het configuratiearchief, zolang de web-app verzoeken ontvangt.

App-configuratie slaat elke instelling automatisch in de cache om te voorkomen dat er te veel oproepen naar het configuratiearchief worden gepleegd. De vernieuwingsbewerking wacht totdat de in de cache opgeslagen waarde van een instelling is verlopen om die instelling bij te werken, zelfs wanneer de waarde in het configuratiearchief verandert. De standaardvervaldatum voor de cache is 30 seconden. U deze vervaldatum, indien nodig, overschrijven.

In deze zelfstudie leert hoe u dynamische configuratie-updates kunt implementeren in uw code. Dit is gebaseerd op de web-app die is geïntroduceerd in de quickstarts. Voordat u verdergaat, [voltooit u eerst een ASP.NET Core-app met app-configuratie.](./quickstart-aspnet-core-app.md)

U elke codeeditor gebruiken om de stappen in deze zelfstudie uit te voeren. [Visual Studio Code](https://code.visualstudio.com/) is een uitstekende optie die beschikbaar is op de Windows-, macOS- en Linux-platforms.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Stel uw toepassing in om de configuratie bij te werken als reactie op wijzigingen in een App Configuration Store.
> * Injecteer de nieuwste configuratie in de controllers van uw toepassing.

## <a name="prerequisites"></a>Vereisten

Installeer de [.NET Core SDK](https://dotnet.microsoft.com/download)om deze zelfstudie te doen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Voordat u verdergaat, [voltooit u eerst een ASP.NET Core-app met app-configuratie.](./quickstart-aspnet-core-app.md)

## <a name="add-a-sentinel-key"></a>Een schildwachtsleutel toevoegen

Een *sentinel-sleutel* is een speciale sleutel die wordt gebruikt om aan te geven wanneer de configuratie is gewijzigd. Uw app controleert de sentinel-sleutel op wijzigingen. Wanneer een wijziging wordt gedetecteerd, vernieuwt u alle configuratiewaarden. Deze aanpak vermindert het totale aantal aanvragen van uw app naar App-configuratie, in vergelijking met het controleren van alle sleutels voor wijzigingen.

1. Selecteer **configuratieverkenner > > sleutelwaarde maken**in de Azure-portal.

1. Voer *TestApp:Instellingen:Sentinel*in voor **Sleutel.** Voer **voor waarde**1 in. **Label-** en **inhoudstype** leeg laten.

1. Selecteer **Toepassen**.

## <a name="reload-data-from-app-configuration"></a>Gegevens opnieuw laden vanuit app-configuratie

1. Voeg een verwijzing `Microsoft.Azure.AppConfiguration.AspNetCore` toe aan het pakket NuGet door de volgende opdracht uit te voeren:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Open *Program.cs*en `CreateWebHostBuilder` werk de `config.AddAzureAppConfiguration()` methode bij om de methode toe te voegen.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

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

    De `ConfigureRefresh` methode wordt gebruikt om de instellingen op te geven die worden gebruikt om de configuratiegegevens bij te werken met het App Configuration Store wanneer een vernieuwingsbewerking wordt geactiveerd. De `refreshAll` parameter `Register` van de methode geeft aan dat alle configuratiewaarden moeten worden vernieuwd als de sentinel-sleutel verandert.

    De methode `SetCacheExpiration` overschrijft ook de standaardcacheverlooptijd van 30 seconden, waarbij in plaats daarvan een tijd van 5 minuten wordt opgegeven. Dit vermindert het aantal aanvragen voor app-configuratie.

    > [!NOTE]
    > Voor testdoeleinden u de vervaldatum van de cache verlagen.

    Als u een vernieuwingsbewerking wilt activeren, moet u een vernieuwingsmiddleware configureren voor de toepassing om de configuratiegegevens te vernieuwen wanneer er een wijziging optreedt. U zult zien hoe dit te doen in een latere stap.

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

3. Open *Startup.cs*en `IServiceCollection.Configure<T>` gebruik `ConfigureServices` in de methode `Settings` om configuratiegegevens aan de klasse te binden.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
    }
    ```
    ---

4. Werk `Configure` de methode `UseAzureAppConfiguration` bij en voeg de middleware toe zodat de configuratie-instellingen die zijn geregistreerd voor verversing kunnen worden bijgewerkt terwijl de ASP.NET Core-web-app aanvragen blijft ontvangen.


    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

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
    
    De middleware maakt gebruik van `AddAzureAppConfiguration` de `Program.cs` vernieuwingsconfiguratie die in de methode is opgegeven om een vernieuwing te activeren voor elke aanvraag die wordt ontvangen door de ASP.NET Core-web-app. Voor elk verzoek wordt een vernieuwingsbewerking geactiveerd en controleert de clientbibliotheek of de in de cache opgeslagen waarde voor de geregistreerde configuratie-instelling is verlopen. Als het is verlopen, wordt het vernieuwd.

## <a name="use-the-latest-configuration-data"></a>De meest recente configuratiegegevens gebruiken

1. Open *HomeController.cs* in de map Controllers en `Microsoft.Extensions.Options` voeg een verwijzing toe aan het pakket.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Werk `HomeController` de klasse `Settings` bij om te ontvangen door middel van afhankelijkheidsinjectie en maak gebruik van de waarden.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

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



3. Open *Index.cshtml* in de map Weergaven > start en vervang de inhoud ervan door het volgende script:

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

1. Als u de app wilt bouwen met de .NET Core CLI, voert u de volgende opdracht uit in de opdrachtshell:

        dotnet build

1. Nadat de build is voltooid, voert u de volgende opdracht uit om de web-app lokaal uit te voeren:

        dotnet run
1. Open een browservenster en ga naar `dotnet run` de URL in de uitvoer.

    ![Quickstart-app lokaal starten](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Alle bronnen**en selecteer de instantie app-configuratiearchief die u in de snelstart hebt gemaakt.

1. Selecteer **Configuratieverkenner**en werk de waarden van de volgende toetsen bij:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Gegevens uit Azure-app-configuratie - nu met live updates! |
    | TestApp:Instellingen:Sentinel | 2 |

1. Vernieuw de browserpagina om de nieuwe configuratie-instellingen te zien. Mogelijk moet u meerdere keer vernieuwen om de wijzigingen weer te kunnen vinden.

    ![Updated quickstart app lokaal starten](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u uw ASP.NET Core-web-app ingeschakeld om de configuratie-instellingen van app-configuratie dynamisch te vernieuwen. Ga door naar de volgende zelfstudie voor meer informatie over het gebruik van een door Azure beheerde identiteit om de toegang tot app-configuratie te stroomlijnen.

> [!div class="nextstepaction"]
> [Beheerde identiteitsintegratie](./howto-integrate-azure-managed-service-identity.md)
