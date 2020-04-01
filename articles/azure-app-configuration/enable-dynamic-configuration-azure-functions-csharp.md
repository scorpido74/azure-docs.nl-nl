---
title: Zelfstudie voor het gebruik van dynamische configuratieconfiguratie van Azure-apps in een Azure Functions-app | Microsoft Documenten
description: In deze zelfstudie leert u hoe u de configuratiegegevens voor Azure Functions-apps dynamisch bijwerken
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74185448"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Zelfstudie: Dynamische configuratie gebruiken in een Azure Functions-app

De configuratieprovider app-configuratie .NET Standard ondersteunt caching en verfrissende configuratie die dynamisch wordt aangestuurd door toepassingsactiviteit. In deze zelfstudie leert hoe u dynamische configuratie-updates kunt implementeren in uw code. Het bouwt voort op de Azure Functions-app die in de quickstarts is geïntroduceerd. Voordat u verdergaat, [voltooit u eerst een Azure-functies-app maken met Azure App-configuratie.](./quickstart-azure-functions-csharp.md)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Stel uw Azure Functions-app in om de configuratie bij te werken als reactie op wijzigingen in een App Configuration Store.
> * Injecteer de nieuwste configuratie in uw Azure Functions-aanroepen.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) met de **Azure-ontwikkelworkload**
- [Azure-functies](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Snel klaar maken [Start Een Azure-functies-app maken met Azure App-configuratie](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>Gegevens opnieuw laden vanuit app-configuratie

1. Open *Function1.cs*. Voeg naast `static` de `Configuration`eigenschap een `static` `ConfigurationRefresher` nieuwe eigenschap toe om `IConfigurationRefresher` een singleton-exemplaar daarvan te behouden dat wordt gebruikt om configuratie-updates te signaleren tijdens functiesdie later worden aanroepen.

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. Werk de constructor `ConfigureRefresh` bij en gebruik de methode om de instelling op te geven die moet worden vernieuwd vanuit het App Configuration Store. Een instantie `IConfigurationRefresher` van wordt `GetRefresher` opgehaald met behulp van methode. Optioneel wijzigen we ook de vervaldatum van de configuratiecache in 1 minuut vanaf de standaard 30 seconden.

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

3. Werk `Run` de methode en het signaal `Refresh` bij om de configuratie te vernieuwen met behulp van de methode aan het begin van de aanroep Functies. Dit is niet op als het verloopperiode van de cache niet is bereikt. Verwijder `await` de operator als u liever ziet dat de configuratie wordt vernieuwd zonder te blokkeren.

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

1. Stel een omgevingsvariabele met de naam **ConnectionString**in en stel deze in op de toegangssleutel voor uw app-configuratiearchief. Als u de opdrachtprompt van Windows gebruikt, voert u de volgende opdracht uit en start u de opdrachtprompt opnieuw om de wijziging van kracht te laten worden:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Als u Windows PowerShell gebruikt, voert u de volgende opdracht uit:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Druk op F5 om de functie testen. Accepteer desgevraagd het verzoek van Visual Studio om **CLI-hulpprogramma's (Azure Functions Core)** te downloaden en te installeren. Mogelijk moet u ook een firewalluitzondering inschakelen, zodat de hulpprogramma's HTTP-aanvragen kunnen afhandelen.

3. Kopieer de URL van uw functie vanuit de uitvoer van de Azure Functions-runtime.

    ![Quickstart over foutopsporing in functies in Visual Studio](./media/quickstarts/function-visual-studio-debugging.png)

4. Plak de URL van de HTTP-aanvraag in de adresbalk van uw browser. In de volgende afbeelding wordt het antwoord in de browser weergegeven op de lokale GET-aanvraag die door de functie wordt geretourneerd.

    ![Quickstart over lokaal opstarten functies](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Alle bronnen**en selecteer de instantie app-configuratiearchief die u in de snelstart hebt gemaakt.

6. Selecteer **Configuratieverkenner**en werk de waarden van de volgende toets bij:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens uit Azure App-configuratie - Bijgewerkt |

7. Vernieuw de browser een paar keer. Wanneer de instelling in de cache na een minuut verloopt, wordt op de pagina het antwoord weergegeven van de aanroep Functies met bijgewerkte waarde.

    ![Quickstart-functie wordt lokaal vernieuwen](./media/quickstarts/dotnet-core-function-refresh-local.png)

De voorbeeldcode die in deze zelfstudie wordt gebruikt, kan worden gedownload van [App Configuration GitHub repo](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u uw Azure Functions-app ingeschakeld om configuratie-instellingen dynamisch te vernieuwen vanuit app-configuratie. Ga door naar de volgende zelfstudie voor meer informatie over het gebruik van een door Azure beheerde identiteit om de toegang tot app-configuratie te stroomlijnen.

> [!div class="nextstepaction"]
> [Beheerde identiteitsintegratie](./howto-integrate-azure-managed-service-identity.md)
