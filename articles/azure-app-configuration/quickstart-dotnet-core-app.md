---
title: Quickstart voor Azure App Configuration met .NET Core | Microsoft Docs
description: Een quickstart voor het gebruik van Azure App Configuration met .NET Core-apps
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 420d9b48013f5f6debe588667fe1cc0390517e66
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245375"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Snelstart: een .NET Core-app maken met app-configuratie

In deze quickstart neemt u Azure App Configuration op in een .NET Core-console-app om de opslag en het beheer van toepassingsinstellingen te centraliseren, gescheiden van uw code.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download) - ook beschikbaar in de [Azure Cloud Shell](https://shell.azure.com).

## <a name="create-an-app-configuration-store"></a>Een app-configuratiearchief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecteer **Configuratieverkenner** > **Sleutelwaarde maken** > **Key-value** om de volgende sleutelwaardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure App Configuration |

    Laat **label en** **inhoudstype** voorlopig leeg.

7. Selecteer **Toepassen**.

## <a name="create-a-net-core-console-app"></a>Een .NET Core-console-app maken

U gebruikt de [.NET Core command-line interface (CLI)](https://docs.microsoft.com/dotnet/core/tools/) om een nieuw .NET Core-console-app-project te maken. Het voordeel van het gebruik van de .NET Core CLI ten opzichte van Visual Studio is dat het beschikbaar is op de Windows-, macOS- en Linux-platforms.  U ook de vooraf geïnstalleerde hulpprogramma's gebruiken die beschikbaar zijn in de [Azure Cloud Shell.](https://shell.azure.com)

1. Maak een nieuwe map voor uw project.

2. Voer in de nieuwe map de volgende opdracht uit om een nieuw project van de ASP.NET Core-console-app te maken:

    ```dotnetcli
    dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een app-configuratiearchief

1. Voeg een verwijzing `Microsoft.Extensions.Configuration.AzureAppConfiguration` toe aan het pakket NuGet door de volgende opdracht uit te voeren:

    ```dotnetcli
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration
    ```

2. Voer de volgende opdracht uit om pakketten voor uw project te herstellen:

    ```dotnetcli
    dotnet restore
    ```

3. Open *Program.cs*en voeg een verwijzing toe naar de .NET Core App Configuration provider.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Werk `Main` de methode bij om `builder.AddAzureAppConfiguration()` app-configuratie te gebruiken door de methode aan te roepen.

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

1. Stel een omgevingsvariabele met de naam **ConnectionString**in en stel deze in op de toegangssleutel voor uw App Configuration Store. Voer bij de opdrachtregel de volgende opdracht uit:

    ```cmd
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Als u Windows PowerShell gebruikt, voert u de volgende opdracht uit:

    ```azurepowershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

        export ConnectionString='connection-string-of-your-app-configuration-store'

    Start de opdrachtprompt opnieuw om de wijziging van kracht te laten worden. Print de waarde van de omgevingsvariabele uit om te valideren dat deze correct is ingesteld.

2. Voer de volgende opdracht uit om de console-app te bouwen:

    ```dotnetcli
    dotnet build
    ```

3. Nadat de build is voltooid, voert u de volgende opdracht uit om de app lokaal uit te voeren:

    ```dotnetcli
    dotnet run
    ```

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een nieuwe app-configuratiewinkel gemaakt en deze gebruikt met een .NET Core-console-app via de [app-configuratieprovider.](https://go.microsoft.com/fwlink/?linkid=2074664) Ga verder naar de volgende zelfstudie voor meer informatie over het configureren van de .NET Core-app om configuratie-instellingen dynamisch te vernieuwen.

> [!div class="nextstepaction"]
> [Dynamische configuratie inschakelen](./enable-dynamic-configuration-dotnet-core.md)
