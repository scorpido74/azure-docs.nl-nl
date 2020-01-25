---
title: Quickstart voor Azure App Configuration met .NET Core | Microsoft Docs
description: Een quickstart voor het gebruik van Azure App Configuration met .NET Core-apps
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 4d9ea8c7cc64a27ec6033ff084d5771328f89699
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714674"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Quick Start: een .NET core-app maken met app-configuratie

In deze Snelstartgids neemt u Azure-app configuratie op in een .NET Core-Console-app om opslag en beheer van toepassings instellingen gescheiden van uw code te centraliseren.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
- [.Net core SDK](https://dotnet.microsoft.com/download) -ook beschikbaar in de [Azure Cloud shell](https://shell.azure.com).

## <a name="create-an-app-configuration-store"></a>Een app-configuratie archief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecteer **configuratie verkenner** > **maken** om de volgende sleutel-waardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure App Configuration |

    Laat het **Label** en het **inhouds type** nu leeg.

## <a name="create-a-net-core-console-app"></a>Een .NET Core-console-app maken

U gebruikt de [.net core-opdracht regel interface (CLI)](https://docs.microsoft.com/dotnet/core/tools/) om een nieuw .net Core Console-app-project te maken. Het voor deel van het gebruik van de .NET Core SLI over Visual Studio is dat het beschikbaar is via de Windows-, macOS-en Linux-platformen.  U kunt ook de vooraf geïnstalleerde hulpprogram ma's gebruiken die beschikbaar zijn in de [Azure Cloud shell](https://shell.azure.com).

1. Maak een nieuwe map voor uw project.

2. Voer in de nieuwe map de volgende opdracht uit om een nieuw ASP.NET Core Console-app-project te maken:

    ```CLI
        dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een app-configuratie archief

1. Voeg een verwijzing naar het NuGet-pakket van `Microsoft.Extensions.Configuration.AzureAppConfiguration` toe door de volgende opdracht uit te voeren:

    ```CLI
        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 3.0.0-preview-011100001-1152
    ```

2. Voer de volgende opdracht uit om de pakketten voor uw project te herstellen:

    ```CLI
        dotnet restore
    ```

3. Open *Program.cs*en voeg een verwijzing toe naar de .net core-app configuratie provider.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Werk de `Main`-methode bij om app-configuratie te gebruiken door de `builder.AddAzureAppConfiguration()`-methode aan te roepen.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Stel een omgevings variabele met de naam **Connections Tring**in en stel deze in op de toegangs sleutel voor uw app-configuratie archief. Voer op de opdracht regel de volgende opdracht uit en start de opdracht prompt om de wijziging toe te passen:

    ```CLI
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Als u Windows Power shell gebruikt, voert u de volgende opdracht uit:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Voer de volgende opdracht uit om de console-app te bouwen:

    ```CLI
        dotnet build
    ```

3. Nadat de build is voltooid, voert u de volgende opdracht uit om de app lokaal uit te voeren:

    ```CLI
        dotnet run
    ```

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u een nieuwe app-configuratie opgeslagen gemaakt en gebruikt met een .NET Core-Console-app via de [app-configuratie provider](https://go.microsoft.com/fwlink/?linkid=2074664). Ga door naar de volgende zelf studie voor meer informatie over het configureren van uw .NET core-app om configuratie-instellingen dynamisch te vernieuwen.

> [!div class="nextstepaction"]
> [Dynamische configuratie inschakelen](./enable-dynamic-configuration-dotnet-core.md)
