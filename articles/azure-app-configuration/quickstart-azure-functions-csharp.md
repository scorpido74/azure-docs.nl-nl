---
title: Quickstart voor Azure-app-configuratie met Azure Functions | Microsoft Docs
description: Een quickstart voor het gebruik van Azure-app-configuratie met Azure Functions.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 2f6efdad7ab0685e58d2edd73bc36b758e8dbae2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245494"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Snelstart: een Azure-app maken met Azure-app-configuratie

In deze quickstart neemt u de Azure App Configuration-service op in een Azure Functions-app om de opslag en het beheer van al uw toepassingsinstellingen te centraliseren, gescheiden van uw code.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) met de **Azure-ontwikkelworkload.**
- [Azure-functies](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Een app-configuratiearchief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecteer **Configuratieverkenner** > **+ Sleutelwaarde maken** > **Key-value** om de volgende sleutelwaardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure App Configuration |

    Laat **label en** **inhoudstype** voorlopig leeg.

7. Selecteer **Toepassen**.

## <a name="create-a-functions-app"></a>Een functie-app maken

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een app-configuratiearchief

1. Klik met de rechtermuisknop op uw project en selecteer **NuGet-pakketten beheren.** Zoek **op het** tabblad Bladeren `Microsoft.Extensions.Configuration.AzureAppConfiguration` naar en voeg het NuGet-pakket toe aan uw project. Als u deze niet vinden, schakelt u het selectievakje **Prerelease opnemen** in.

2. Open *Function1.cs*en voeg de naamruimten van de .NET Core-configuratie en de configuratieprovider appconfiguratie toe.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. Voeg `static` een `Configuration` eigenschap toe die is `IConfiguration`vernoemd naar een singleton-exemplaar van . Voeg vervolgens `static` een constructor toe om `AddAzureAppConfiguration()`verbinding te maken met app-configuratie door aan te roepen . Dit laadt de configuratie eenmaal bij het opstarten van de toepassing. Dezelfde configuratie-instantie wordt later voor alle oproepen van Functies gebruikt.

    ```csharp
    private static IConfiguration Configuration { set; get; }

    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        Configuration = builder.Build();
    }
    ```

4. Werk `Run` de methode bij om waarden uit de configuratie te lezen.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>De functie lokaal testen

1. Stel een omgevingsvariabele met de naam **ConnectionString**in en stel deze in op de toegangssleutel voor uw App Configuration Store. Als u de opdrachtprompt van Windows gebruikt, voert u de volgende opdracht uit en start u de opdrachtprompt opnieuw om de wijziging van kracht te laten worden:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Als u Windows PowerShell gebruikt, voert u de volgende opdracht uit:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Druk op F5 om uw functie te testen. Accepteer desgevraagd het verzoek van Visual Studio om **CLI-hulpprogramma's (Azure Functions Core)** te downloaden en te installeren. Mogelijk moet u ook een firewalluitzondering inschakelen, zodat de hulpprogramma's HTTP-aanvragen kunnen afhandelen.

3. Kopieer de URL van uw functie vanuit de uitvoer van de Azure Functions-runtime.

    ![Quickstart over foutopsporing in functies in Visual Studio](./media/quickstarts/function-visual-studio-debugging.png)

4. Plak de URL van de HTTP-aanvraag in de adresbalk van uw browser. In de volgende afbeelding wordt het antwoord in de browser weergegeven op de lokale GET-aanvraag die door de functie wordt geretourneerd.

    ![Quickstart over lokaal opstarten functies](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een nieuwe app-configuratiewinkel gemaakt en deze gebruikt met een Azure Functions-app via de [app-configuratieprovider.](https://go.microsoft.com/fwlink/?linkid=2074664) Ga door naar de volgende zelfstudie voor meer informatie over het configureren van de App Azure Functions om de configuratie-instellingen dynamisch te vernieuwen.

> [!div class="nextstepaction"]
> [Dynamische configuratie inschakelen](./enable-dynamic-configuration-azure-functions-csharp.md)
