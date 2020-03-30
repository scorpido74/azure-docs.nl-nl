---
title: Quickstart voor Azure-app-configuratie met ASP.NET Core | Microsoft Docs
description: Snel aan de slag voor het gebruik van Azure App-configuratie met ASP.NET Core-apps
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 537dabe09c41012b9e15998ce3af8198dcfb62d3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245771"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Snelstart: een ASP.NET Core-app maken met Azure-app-configuratie

In deze quickstart gebruikt u Azure App Configuration om de opslag en het beheer van toepassingsinstellingen voor een ASP.NET Core-toepassing te centraliseren. ASP.NET Core bouwt één configuratieobject op basis van sleutelwaarde met behulp van instellingen uit een of meer gegevensbronnen die door een toepassing zijn opgegeven. Deze gegevensbronnen staan bekend als *configuratieproviders.* Omdat de .NET Core-client van App Configuration is geïmplementeerd als configuratieprovider, wordt de service weergegeven als een andere gegevensbron.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
- [.NET Core-SDK](https://dotnet.microsoft.com/download)

>[!TIP]
> De Azure Cloud Shell is een gratis interactieve shell die u gebruiken om de instructies voor de opdrachtregel in dit artikel uit te voeren.  Er zijn veelvoorkomende Azure-hulpprogramma's vooraf geïnstalleerd, waaronder de .NET Core SDK. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud Shell](https://shell.azure.com) vanaf shell.azure.com.  U meer informatie over Azure Cloud Shell lezen door [onze documentatie te lezen](../cloud-shell/overview.md)

## <a name="create-an-app-configuration-store"></a>Een app-configuratiearchief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecteer **Configuratieverkenner** > **Sleutelwaarde maken** > **Key-value** om de volgende sleutelwaardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:BackgroundColor | Wit |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Zwart |
    | TestApp:Settings:Message | Gegevens van Azure App Configuration |

    Laat **label en** **inhoudstype** voorlopig leeg. Selecteer **Toepassen**.

## <a name="create-an-aspnet-core-web-app"></a>Een ASP.NET Core-web-app maken

Gebruik de [.NET Core command-line interface (CLI)](https://docs.microsoft.com/dotnet/core/tools/) om een nieuw ASP.NET Core MVC web app project te maken. De [Azure Cloud Shell](https://shell.azure.com) biedt deze tools voor u.  Ze zijn ook beschikbaar op de Windows-, macOS- en Linux-platforms.

1. Maak een nieuwe map voor uw project. Voor deze quickstart, noem het *TestAppConfig*.

1. Voer in de nieuwe map de volgende opdracht uit om een nieuw ASP.NET Core MVC-webappproject te maken:

```dotnetcli
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
> Zie [Veilige opslag van app-geheimen in ontwikkeling in ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets) voor meer informatie over Secret Manager

## <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een app-configuratiearchief

1. Voeg een verwijzing `Microsoft.Azure.AppConfiguration.AspNetCore` toe aan het pakket NuGet door de volgende opdracht uit te voeren:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Voer de volgende opdracht uit om pakketten voor uw project te herstellen:

    ```dotnetcli
    dotnet restore
    ```

1. Voeg een geheim met de naam *ConnectionStrings:AppConfig* toe aan Secret Manager.

    Dit geheim bevat de verbindingstekenreeks om toegang te krijgen tot uw App Configuration Store. Vervang de waarde in de volgende opdracht door de verbindingstekenreeks voor uw App Configuration Store. U de verbindingstekenreeks vinden onder **Toegangssleutels** in de Azure-portal.

    Deze opdracht moet worden uitgevoerd in de map met het bestand *.csproj*.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Sommige shells zullen de verbindingskoord afkappen tenzij het tussen aanhalingstekens wordt ingesloten. Controleer of de `dotnet user-secrets` uitvoer van de opdracht de volledige verbindingstekenreeks weergeeft. Als dit niet het zo is, voert u de opdracht opnieuw uit en sluit u de verbindingstekenreeks in aanhalingstekens.

    Secret Manager wordt alleen gebruikt om de web-app lokaal te testen. Wanneer de app wordt geïmplementeerd in [Azure App Service,](https://azure.microsoft.com/services/app-service/web)gebruikt u bijvoorbeeld de toepassingsinstelling **Verbindingstekenreeksen** in App Service in plaats van Secret Manager om de verbindingstekenreeks op te slaan.

    Toegang tot dit geheim met behulp van de configuratie-API. Een dikke darm (:) werkt in de configuratienaam met de configuratie-API op alle ondersteunde platforms. Zie [Configuratie per omgeving](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

1. Open *Program.cs*en voeg een verwijzing toe naar de .NET Core App Configuration provider.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

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
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
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
            config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
        })
        .UseStartup<Startup>());
    ```

    ---

1. Navigeer naar * <app root>/Views/Home* en open *Index.cshtml*. Vervang de inhoud ervan door de volgende code:

    ```HTML
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]</h1>
    ```

1. Navigeer naar * <app root>/Views/Shared* en open *_Layout.cshtml*. Vervang de inhoud ervan door de volgende code:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Als u de app wilt bouwen met de .NET Core CLI, navigeert u naar de hoofdmap van uw toepassing en voert u de volgende opdracht uit in de opdrachtmap:

    ```dotnetcli
    dotnet build
    ```

1. Nadat de build is voltooid, voert u de volgende opdracht uit om de web-app lokaal uit te voeren:

    ```dotnetcli
    dotnet run
    ```

1. Als u aan uw lokale machine werkt, gebruikt `http://localhost:5000`u een browser om naar . Dit is de standaard-URL voor de web-app die lokaal wordt gehost.  

Als u in de Azure Cloud Shell werkt, selecteert u de knop *Webvoorbeeld,* gevolgd door *Configureren*.  

![De knop Webvoorbeeld zoeken](./media/quickstarts/cloud-shell-web-preview.png)

Wanneer u wordt gevraagd om de poort voor voorbeeld te configureren, voert u '5000' in en selecteert u *Openen en bladeren*.  Op de webpagina leest u 'Gegevens uit Azure-appconfiguratie'.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een nieuwe app-configuratiewinkel gemaakt en deze gebruikt met een ASP.NET Core-webapp via de [app-configuratieprovider.](https://go.microsoft.com/fwlink/?linkid=2074664) Ga verder naar de volgende zelfstudie voor meer informatie over het configureren van uw ASP.NET Core-app om de configuratie-instellingen dynamisch te vernieuwen.

> [!div class="nextstepaction"]
> [Dynamische configuratie inschakelen](./enable-dynamic-configuration-aspnet-core.md)
