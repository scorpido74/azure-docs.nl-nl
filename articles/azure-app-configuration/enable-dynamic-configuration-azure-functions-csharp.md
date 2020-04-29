---
title: Zelf studie voor het gebruik van Azure-app configuratie dynamische configuratie in een Azure Functions-app | Microsoft Docs
description: In deze zelf studie leert u hoe u de configuratie gegevens voor Azure Functions-apps dynamisch kunt bijwerken
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
ms.openlocfilehash: ba70d5f186c1424b2019716ab7a87aeae85f8913
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74185448"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Zelf studie: dynamische configuratie in een Azure Functions-app gebruiken

De app-configuratie .NET Standard-configuratie provider ondersteunt het in cache opslaan en vernieuwen van de configuratie die dynamisch wordt aangedreven door de toepassings activiteit. In deze zelfstudie leert hoe u dynamische configuratie-updates kunt implementeren in uw code. Het is gebaseerd op de Azure Functions-app die is geïntroduceerd in de Quick starts. Voordat u doorgaat, moet u eerst [een Azure functions-app maken met Azure-app configuratie](./quickstart-azure-functions-csharp.md) .

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Stel uw Azure Functions-app in om de configuratie bij te werken als reactie op wijzigingen in een app-configuratie archief.
> * Injecteer de nieuwste configuratie voor uw Azure Functions-aanroepen.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) met de werk belasting **Azure Development**
- [Azure Functions-hulpprogram ma's](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Snelstartgids volt ooien [een Azure functions-app maken met Azure-app configuratie](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>Gegevens opnieuw laden vanuit app-configuratie

1. Open *Function1.cs*. Voeg naast `static` de eigenschap `Configuration`een nieuwe `static` eigenschap `ConfigurationRefresher` toe om een singleton-exemplaar van `IConfigurationRefresher` te maken dat wordt gebruikt om configuratie-updates te Signa leren tijdens functie aanroepen op een later tijdstip.

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. Werk de constructor bij en gebruik de `ConfigureRefresh` -methode om de instelling op te geven die moet worden vernieuwd vanuit het configuratie archief van de app. Er wordt een `IConfigurationRefresher` exemplaar van opgehaald met `GetRefresher` de methode. U kunt ook het venster verloop tijd van de configuratie cache wijzigen in 1 minuut van de standaard 30 seconden.

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

3. Werk de `Run` methode en het signaal bij om de configuratie te `Refresh` vernieuwen met behulp van de-methode aan het begin van de functie aanroep. Dit is niet het geval wanneer het venster voor de verval tijd van de cache niet is bereikt. Verwijder de `await` operator als u de voor keur geeft aan de configuratie die wordt vernieuwd zonder te blok keren.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await ConfigurationRefresher.Refresh();

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>De functie lokaal testen

1. Stel een omgevings variabele met de naam **Connections Tring**in en stel deze in op de toegangs sleutel voor uw app-configuratie archief. Als u de Windows-opdracht prompt gebruikt, voert u de volgende opdracht uit en start u de opdracht prompt zodat de wijziging kan worden doorgevoerd:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Als u Windows Power shell gebruikt, voert u de volgende opdracht uit:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Druk op F5 om de functie testen. Als hierom wordt gevraagd, accepteert u de aanvraag van Visual Studio om **Azure functions core** -hulpprogram ma's te downloaden en installeren. Mogelijk moet u ook een firewall-uitzonde ring inschakelen, zodat de hulpprogram ma's HTTP-aanvragen kunnen afhandelen.

3. Kopieer de URL van uw functie vanuit de uitvoer van de Azure Functions-runtime.

    ![Quickstart over foutopsporing in functies in Visual Studio](./media/quickstarts/function-visual-studio-debugging.png)

4. Plak de URL van de HTTP-aanvraag in de adresbalk van uw browser. In de volgende afbeelding ziet u het antwoord in de browser op de lokale GET-aanvraag die door de functie wordt geretourneerd.

    ![Quickstart over lokaal opstarten functies](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **alle resources**en selecteer de app-configuratie Store-instantie die u hebt gemaakt in de Quick Start.

6. Selecteer **Configuration Explorer**en werk de waarden van de volgende sleutel bij:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure-app configuratie-bijgewerkt |

7. Vernieuw de browser enkele keren. Wanneer de cache-instelling na een minuut verloopt, wordt op de pagina de reactie van de functie aanroep met de bijgewerkte waarde weer gegeven.

    ![Quick Start-functie lokaal vernieuwen](./media/quickstarts/dotnet-core-function-refresh-local.png)

De voorbeeld code die in deze zelf studie wordt gebruikt, kan worden gedownload van de [app-configuratie github opslag plaats](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u uw Azure Functions-app ingeschakeld om de configuratie-instellingen van de app-configuratie dynamisch te vernieuwen. Ga verder met de volgende zelf studie als u wilt weten hoe u een door Azure beheerde identiteit kunt gebruiken om de toegang tot de app-configuratie te stroom lijnen.

> [!div class="nextstepaction"]
> [Beheerde identiteits integratie](./howto-integrate-azure-managed-service-identity.md)
