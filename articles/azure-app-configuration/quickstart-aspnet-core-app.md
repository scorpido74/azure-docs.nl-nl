---
title: Quickstart voor Azure-app-configuratie met ASP.NET Core | Microsoft Docs
description: Quickstart voor het gebruik van Azure App Configuration met ASP.NET Core-apps
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 069bce6f2f8e46679cbc82dd92c9fd73bfecde85
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373518"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Quickstart: Een ASP.NET Core-app maken met Azure-app-configuratie

In deze quickstart gaat u met Azure App Configuration de opslag en het beheer van toepassingsinstellingen voor een ASP.NET Core-toepassing centraliseren. ASP.NET Core bouwt een configuratieobject met één-sleutelwaarde met behulp van instellingen van een of meer gegevensbronnen, die worden opgegeven door een toepassing. Deze gegevensbronnen worden *configuratieproviders* genoemd. Omdat de .NET Core-client van App Configuration wordt geïmplementeerd als configuratieprovider, wordt de service als elke andere gegevensbron weergegeven.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- [.NET Core-SDK](https://dotnet.microsoft.com/download)

>[!TIP]
> Azure Cloud Shell is een gratis interactieve shell waarmee u de opdrachtregelinstructies in dit artikel kunt uitvoeren.  Deze heeft algemene Azure-hulpprogramma's die vooraf zijn geïnstalleerd, met inbegrip van de .NET Core SDK. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud Shell](https://shell.azure.com) vanuit shell.azure.com.  Meer informatie over Azure Cloud Shell vindt u door [onze documentatie te lezen](../cloud-shell/overview.md)

## <a name="create-an-app-configuration-store"></a>Een App Configuration-archief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecteer **Configuratieverkenner** > **Maken** > **Sleutelwaarde** om de volgende sleutel-waardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:BackgroundColor | Wit |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Zwart |
    | TestApp:Settings:Message | Gegevens van Azure App Configuration |

    Laat **Label** en **Inhoudstype** nog even leeg. Selecteer **Toepassen**.

## <a name="create-an-aspnet-core-web-app"></a>Een ASP.NET Core-web-app maken

Gebruik de [opdrachtregelinterface (CLI) van .NET Core](https://docs.microsoft.com/dotnet/core/tools/) om een nieuw web-app-project van ASP.NET Core MVC te maken. De [Azure Cloud Shell](https://shell.azure.com) biedt u deze hulpprogramma's.  Ze zijn ook beschikbaar op het Windows-, macOS- en Linux-platform.

1. Maak een nieuwe map voor uw project. Noem deze *TestAppConfig* voor deze quickstart.

1. Voer in de nieuwe map de volgende opdracht uit om een nieuw web-app-project van ASP.NET Core MVC te maken:

```dotnetcli
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
> Raadpleeg [Veilige opslag van app-geheimen in ontwikkeling in ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets) voor meer informatie over Secret Manager

## <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een App Configuration-archief

1. Voeg een verwijzing toe naar het NuGet-pakket `Microsoft.Azure.AppConfiguration.AspNetCore` door de volgende opdracht uit te voeren:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Voer de volgende opdracht uit om pakketten voor uw project te herstellen:

    ```dotnetcli
    dotnet restore
    ```

1. Voeg een geheim met de naam *ConnectionStrings:AppConfig* toe aan Secret Manager.

    Dit geheim bevat straks de verbindingsreeks voor toegang tot uw App Configuration-archief. Vervang de waarde in de volgende opdracht door de verbindingsreeks voor uw App Configuration-archief. U vindt de verbindingsreeks onder **Toegangssleutels** in Azure Portal.

    Deze opdracht moet worden uitgevoerd in de map met het bestand *.csproj*.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Bij sommige shells wordt de verbindingsreeks afgekapt, tenzij deze tussen aanhalingstekens staat. Zorg ervoor dat de uitvoer van de `dotnet user-secrets`-opdracht de volledige verbindingsreeks toont. Als dat niet het geval is, voert u de opdracht opnieuw uit en plaatst u de verbindingsreeks tussen aanhalingstekens.

    Secret Manager wordt alleen gebruikt om de web-app lokaal te testen. Wanneer de app is geïmplementeerd bijvoorbeeld naar [Azure App Service](https://azure.microsoft.com/services/app-service/web), gebruikt u een toepassingsinstelling **Verbindingsreeksen** in App Service in plaats van Secret Manager om de verbindingsreeks op te slaan.

    Open dit geheim met behulp van de configuratie-API. Een dubbele punt (:) werkt in de configuratienaam met de configuratie-API op alle ondersteunde platforms. Zie [Configuratie per omgeving](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

1. Open *Program.cs* en voeg een verwijzing naar de .NET Core App Configuration-provider toe.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Werk de methode `CreateWebHostBuilder` bij voor het gebruik van App Configuration door de methode `config.AddAzureAppConfiguration()` aan te roepen.

    > [!IMPORTANT]
    > `CreateHostBuilder` wordt vervangen door `CreateWebHostBuilder` in .NET Core 3.0.  Selecteer de juiste syntaxis op basis van uw omgeving.

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

1. Navigeer naar *<app root>/Views/Home* en open *index.cshtml*. Vervang de inhoud ervan door de volgende code:

    ```HTML
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]</h1>
    ```

1. Navigeer naar *<app root>/Views/Shared* en open *_Layout.cshtml*. Vervang de inhoud ervan door de volgende code:

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

1. Als u de app wilt bouwen met behulp van de .NET Core CLI, gaat u naar de hoofdmap van uw toepassing en voert u de volgende opdracht uit in de opdrachtshell:

    ```dotnetcli
    dotnet build
    ```

1. Nadat het bouwen is voltooid, voert u de volgende opdracht uit om de web-app lokaal uit te voeren:

    ```dotnetcli
    dotnet run
    ```

1. Als u op uw lokale computer werkt, gebruikt u een browser om naar `http://localhost:5000` te navigeren. Dit is de standaard-URL voor de web-app die lokaal wordt gehost.  

Als u werkt in Azure Cloud Shell, selecteert u de knop *Web-preview* gevolgd door *Configureren*.  

![De knop Web-preview zoeken](./media/quickstarts/cloud-shell-web-preview.png)

Wanneer u wordt gevraagd om de poort voor de preview te configureren, voert u 5000 in en selecteert u *Openen en bladeren*.  Op de webpagina staat Gegevens van Azure App Configuration.

![Quickstart-app starten](./media/quickstarts/aspnet-core-app-launch-local-before.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een nieuw App Configuration-archief gemaakt en dit via de [App Configuration-provider](https://go.microsoft.com/fwlink/?linkid=2074664) gebruikt met een ASP.NET Core-web-app. Ga door naar de volgende zelfstudie voor meer informatie over het configureren van uw ASP.NET Core-app om configuratie-instellingen dynamisch te vernieuwen.

> [!div class="nextstepaction"]
> [Dynamische configuratie inschakelen](./enable-dynamic-configuration-aspnet-core.md)
