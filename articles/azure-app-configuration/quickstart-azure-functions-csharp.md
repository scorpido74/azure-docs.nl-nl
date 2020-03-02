---
title: Quickstart voor Azure-app-configuratie met Azure Functions | Microsoft Docs
description: Een quickstart voor het gebruik van Azure-app-configuratie met Azure Functions.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 71a330523f1d3393a365fec29fb66f5c9773b6cc
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207061"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Snelstartgids: een Azure Functions-app maken met Azure-app configuratie

In deze Snelstartgids neemt u de Azure-app Configuration-service op in een Azure Functions-app om opslag en beheer van alle toepassings instellingen gescheiden van uw code te centraliseren.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) met de werk belasting **Azure Development** .
- [Azure Functions-hulpprogram ma's](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Een app-configuratie archief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecteer **configuratie verkenner** >  **+ maken** om de volgende sleutel-waardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure App Configuration |

    Laat het **Label** en het **inhouds type** nu leeg.

## <a name="create-a-functions-app"></a>Een functie-app maken

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een app-configuratie archief

1. Klik met de rechter muisknop op het project en selecteer **NuGet-pakketten beheren**. Zoek op het tabblad **Bladeren** naar het `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet-pakket en voeg dit toe aan uw project. Als u deze niet kunt vinden, schakelt u het selectie vakje **include Prerelease** in.

2. Open *Function1.cs*en voeg de naam ruimten van de .net core-configuratie en de configuratie provider voor de configuratie van de app toe.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. Voeg een `static` eigenschap met de naam `Configuration` toe om een singleton-exemplaar van `IConfiguration`te maken. Voeg vervolgens een `static`-constructor toe om verbinding te maken met de app-configuratie door `AddAzureAppConfiguration()`aan te roepen. Hiermee wordt de configuratie eenmaal geladen bij het opstarten van de toepassing. Hetzelfde configuratie-exemplaar wordt later gebruikt voor alle functions-aanroepen.

    ```csharp
    private static IConfiguration Configuration { set; get; }

    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        Configuration = builder.Build();
    }
    ```

4. Werk de `Run`-methode bij om waarden te lezen uit de configuratie.

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

1. Stel een omgevings variabele met de naam **Connections Tring**in en stel deze in op de toegangs sleutel voor uw app-configuratie archief. Als u de Windows-opdracht prompt gebruikt, voert u de volgende opdracht uit en start u de opdracht prompt zodat de wijziging kan worden doorgevoerd:

    ```CLI
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Als u Windows Power shell gebruikt, voert u de volgende opdracht uit:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Druk op F5 om de functie te testen. Accepteer desgevraagd de aanvraag van Visual Studio om **Azure Functions Core-hulpprogramma's (CLI)** te downloaden en installeren. Mogelijk moet u ook een firewall-uitzonde ring inschakelen, zodat de hulpprogram ma's HTTP-aanvragen kunnen afhandelen.

3. Kopieer de URL van uw functie vanuit de uitvoer van de Azure Functions-runtime.

    ![Quickstart over foutopsporing in functies in Visual Studio](./media/quickstarts/function-visual-studio-debugging.png)

4. Plak de URL van de HTTP-aanvraag in de adresbalk van uw browser. In de volgende afbeelding ziet u het antwoord in de browser op de lokale GET-aanvraag die door de functie wordt geretourneerd.

    ![Quickstart over lokaal opstarten functies](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u een nieuwe app-configuratie opgeslagen gemaakt en gebruikt met een Azure Functions-app via de [app-configuratie provider](https://go.microsoft.com/fwlink/?linkid=2074664). Ga door naar de volgende zelf studie voor meer informatie over het configureren van uw Azure Functions-app om configuratie-instellingen dynamisch te vernieuwen.

> [!div class="nextstepaction"]
> [Dynamische configuratie inschakelen](./enable-dynamic-configuration-azure-functions-csharp.md)
