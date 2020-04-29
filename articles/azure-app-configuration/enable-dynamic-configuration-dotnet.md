---
title: '.NET Framework zelf studie: dynamische configuratie in Azure-app configuratie'
description: In deze zelf studie leert u hoe u de configuratie gegevens voor .NET Framework-apps dynamisch kunt bijwerken met behulp van Azure-app-configuratie.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 7780bdbc92868f62e8d066d171b2a04fe06a981d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80245800"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Zelf studie: dynamische configuratie in een .NET Framework-app gebruiken

De app-configuratie .NET-client bibliotheek ondersteunt het bijwerken van een set configuratie-instellingen op aanvraag zonder dat een toepassing opnieuw moet worden opgestart. Dit kan worden geïmplementeerd door eerst een instantie van `IConfigurationRefresher` te verkrijgen van de opties voor de configuratie provider en vervolgens `Refresh` te bellen naar dat exemplaar op een wille keurige plaats in uw code.

Als u de instellingen wilt bijwerken en te veel aanroepen naar de configuratie opslag wilt voor komen, wordt er voor elke instelling een cache gebruikt. Totdat de in de cache opgeslagen waarde van een instelling is verlopen, wordt de waarde niet door de vernieuwings bewerking bijgewerkt, zelfs niet wanneer de waarde is gewijzigd in de configuratie opslag. De standaard verval tijd voor elke aanvraag is 30 seconden, maar kan indien nodig worden overschreven.

In deze zelfstudie leert hoe u dynamische configuratie-updates kunt implementeren in uw code. Het is gebaseerd op de app die is geïntroduceerd in de Quick starts. Voordat u doorgaat, moet u eerst [een .NET Framework-app maken met de app-configuratie](./quickstart-dotnet-app.md) .

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Stel uw .NET Framework-app in om de configuratie bij te werken als reactie op wijzigingen in een app-configuratie archief.
> * Injecteer de meest recente configuratie in uw toepassing.
## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 of hoger](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Een app-configuratie archief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecteer **configuratie Explorer** > **+** > **sleutel waarde** maken om de volgende sleutel-waardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure App Configuration |

    Laat het **Label** en het **inhouds type** nu leeg.

7. Selecteer **Toepassen**.

## <a name="create-a-net-framework-console-app"></a>Een .NET Framework-console-app maken

1. Start Visual Studio en selecteer **bestand** > **New** > **project**.

1. In **een nieuw project maken**filtert u op het type **console** project en klikt u op **console-app (.NET Framework)**. Klik op **Volgende**.

1. Voer in **uw nieuwe project configureren**een project naam in. Onder **Framework**selecteert u **.NET Framework 4.7.1** of hoger. Klik op **maken**.

## <a name="reload-data-from-app-configuration"></a>Gegevens opnieuw laden vanuit app-configuratie
1. Klik met de rechter muisknop op het project en selecteer **NuGet-pakketten beheren**. Zoek en voeg op het tabblad **Bladeren** het NuGet-pakket *micro soft. Extensions. Configuration. AzureAppConfiguration* toe aan uw project. Als u deze niet kunt vinden, schakelt u het selectie vakje **include Prerelease** in.

1. Open *Program.cs*en voeg een verwijzing toe naar de .net core-app configuratie provider.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Voeg twee variabelen toe om configuratie-gerelateerde objecten op te slaan.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Werk de `Main` methode bij om verbinding te maken met de app-configuratie met de opgegeven vernieuwings opties.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });

            _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }
    ```
    De `ConfigureRefresh` -methode wordt gebruikt om de instellingen op te geven die worden gebruikt voor het bijwerken van de configuratie gegevens met het app-configuratie archief wanneer een vernieuwings bewerking wordt geactiveerd. Een exemplaar van `IConfigurationRefresher` kan worden opgehaald door de aanroep `GetRefresher` methode voor de opties die aan `AddAzureAppConfiguration` de methode worden door `Refresh` gegeven en de methode op dit exemplaar kan worden gebruikt om een vernieuwings bewerking overal in uw code te activeren.

    > [!NOTE]
    > De standaard waarde voor de verval tijd van de cache voor een configuratie-instelling is 30 seconden, maar kan `SetCacheExpiration` worden overschreven door het aanroepen van de methode voor de `ConfigureRefresh` initialisatie functie voor opties die als een argument voor de methode wordt door gegeven.

1. Voeg een methode toe `PrintMessage()` met de naam waarmee de configuratie gegevens hand matig worden vernieuwd vanuit de app-configuratie.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Stel een omgevings variabele met de naam **Connections Tring**in en stel deze in op de toegangs sleutel voor uw app-configuratie archief. Als u de Windows-opdracht prompt gebruikt, voert u de volgende opdracht uit en start u de opdracht prompt zodat de wijziging kan worden doorgevoerd:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Als u Windows Power shell gebruikt, voert u de volgende opdracht uit:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Start Visual Studio opnieuw zodat de wijziging kan worden doorgevoerd. 

1. Druk op CTRL + F5 om de console-app te bouwen en uit te voeren.

    ![App-lokaal starten](./media/dotnet-app-run.png)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **alle resources**en selecteer de app-configuratie Store-instantie die u hebt gemaakt in de Quick Start.

1. Selecteer **Configuration Explorer**en werk de waarden van de volgende sleutels bij:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure-app configuratie-bijgewerkt |

1. Klik in de actieve toepassing op ENTER om een vernieuwen te activeren en de bijgewerkte waarde in de opdracht prompt of Power shell-venster af te drukken.

    ![App-lokale gegevens vernieuwen](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Omdat de verval tijd van de cache is ingesteld op 10 seconden `SetCacheExpiration` met behulp van de methode terwijl u de configuratie voor de vernieuwings bewerking opgeeft, wordt de waarde voor de configuratie-instelling alleen bijgewerkt als er ten minste tien seconden zijn verstreken sinds de laatste vernieuwing voor die instelling.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u uw .NET Framework-app ingeschakeld om de configuratie-instellingen van de app-configuratie dynamisch te vernieuwen. Ga verder met de volgende zelf studie als u wilt weten hoe u een door Azure beheerde identiteit kunt gebruiken om de toegang tot de app-configuratie te stroom lijnen.

> [!div class="nextstepaction"]
> [Beheerde identiteits integratie](./howto-integrate-azure-managed-service-identity.md)
