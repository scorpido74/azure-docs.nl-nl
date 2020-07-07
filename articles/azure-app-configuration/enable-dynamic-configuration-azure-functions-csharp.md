---
title: Zelfstudie voor het gebruik van dynamische configuratie van Azure App Configuration in een Azure Functions-app | Microsoft Docs
description: In deze zelfstudie leert u hoe u de configuratiegegevens voor Azure Functions-apps dynamisch bijwerkt.
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: e8f5b21189007d2b15c2ff31b778144d9a672318
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85856475"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Zelfstudie: Dynamische configuratie gebruiken in een Azure Functions-app

De configuratieprovider .NET Standard van App Configuration ondersteunt het dynamisch cachen en vernieuwen van een configuratie op basis van toepassingsactiviteit. In deze zelfstudie leert hoe u dynamische configuratie-updates kunt implementeren in uw code. We maken gebruik van de Azure Functions-app die is geïntroduceerd in de quickstarts. Voordat u verdergaat, moet u eerst de quickstart [Een Azure Functions-app maken met Azure App Configuration](./quickstart-azure-functions-csharp.md) voltooien als u dat nog niet hebt gedaan.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Uw Azure Functions-app instellen voor het bijwerken van de configuratie als reactie op wijzigingen in een archief van App Configuration.
> * De nieuwste configuratie injecteren in uw Azure Functions-aanroepen.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) met de workload **Azure development**
- [Hulpprogramma's van Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Neem de quickstart [Een Azure Functions-app maken met Azure App Configuration](./quickstart-azure-functions-csharp.md) door voordat u verdergaat.

## <a name="reload-data-from-app-configuration"></a>Gegevens opnieuw laden vanuit app-configuratie

1. Open *Function1.cs*. Voeg als aanvulling op de `static` eigenschap `Configuration` een nieuwe `static` eigenschap `ConfigurationRefresher` toe om een singleton-instantie van `IConfigurationRefresher` te behouden die wordt gebruikt om later tijdens Functions-aanroepen configuratie-updates aan te geven.

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. Werk de constructor bij en gebruik de methode `ConfigureRefresh` om de instelling op te geven die moet worden vernieuwd vanuit het App Configuration-archief. Er wordt een instantie van `IConfigurationRefresher` opgehaald met de methode `GetRefresher`. Desgewenst wijzigen we de periode waarna de configuratiecache verloopt van 1 minuut in de standaardwaarde van 30 seconden.

    ```csharp
    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .ConfigureRefresh(refreshOptions =>
                        refreshOptions.Register("TestApp:Settings:Message")
                                      .SetCacheExpiration(TimeSpan.FromSeconds(60))
            );
            ConfigurationRefresher = options.GetRefresher();
        });
        Configuration = builder.Build();
    }
    ```

3. Werk de methode `Run` bij en geef aan het begin van de Functions-aanroep aan dat de configuratie moet worden bijgewerkt met de methode `TryRefreshAsync`. Er gebeurt niks (no-op) als de verloopperiode van de cache niet wordt bereikt. Verwijder de operator `await` als u de configuratie liever wilt bijwerken zonder blokkering.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await ConfigurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>De functie lokaal testen

1. Stel een omgevingsvariabele met de naam **ConnectionString** in en stel deze in op de toegangssleutel van het App Configuration-archief. Als u de Windows-opdrachtprompt gebruikt, voert u de volgende opdracht uit en start u de opdrachtprompt opnieuw om de wijziging door te voeren:

    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Als u Windows PowerShell gebruikt, voert u de volgende opdracht uit:

    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Druk op F5 om de functie testen. Accepteer desgevraagd de aanvraag van Visual Studio om **Azure Functions Core-hulpprogramma's (CLI)** te downloaden en installeren. Mogelijk moet u ook een firewall-uitzondering inschakelen, zodat de hulpprogramma's HTTP-aanvragen kunnen afhandelen.

3. Kopieer de URL van uw functie vanuit de uitvoer van de Azure Functions-runtime.

    ![Quickstart over foutopsporing in functies in Visual Studio](./media/quickstarts/function-visual-studio-debugging.png)

4. Plak de URL van de HTTP-aanvraag in de adresbalk van uw browser. In de afbeelding hieronder ziet u de reactie in de browser op de lokale GET-aanvraag die door de functie wordt geretourneerd.

    ![Quickstart over lokaal opstarten functies](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Alle resources** en selecteer de instantie van het App Configuration-archief dat u in de quickstart hebt gemaakt.

6. Selecteer **Configuratieverkenner** en werk de waarde van de volgende sleutel bij:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure App Configuration - bijgewerkt |

7. Vernieuw de browser een paar keer. Wanneer de cache-instelling na een minuut verloopt, ziet u op de pagina de reactie van de Functions-aanroep met de bijgewerkte waarde.

    ![Functions-app lokaal bijwerken](./media/quickstarts/dotnet-core-function-refresh-local.png)

De voorbeeldcode in deze zelfstudie kan worden gedownload van de opslagplaats [App Configuration op GitHub](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction).

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u uw Azure Functions-app ingesteld voor het dynamisch vernieuwen van configuratie-instellingen vanuit App Configuration. Als u wilt weten hoe u een door Azure beheerde identiteit kunt gebruiken om de toegang tot App Configuration te stroomlijnen, gaat u verder met de volgende zelfstudie.

> [!div class="nextstepaction"]
> [Integratie van beheerde identiteit](./howto-integrate-azure-managed-service-identity.md)
