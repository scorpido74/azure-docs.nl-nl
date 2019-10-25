---
title: Zelf studie voor het gebruik van Azure-app configuratie dynamische configuratie in een .NET Framework-app | Microsoft Docs
description: In deze zelf studie leert u hoe u de configuratie gegevens voor .NET Framework-apps dynamisch kunt bijwerken
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 7e28cdacce8eac4774683013ae1c30ca34ebfaad
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821709"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Zelf studie: dynamische configuratie in een .NET Framework-app gebruiken

De app-configuratie .NET-client bibliotheek ondersteunt het bijwerken van een set configuratie-instellingen op aanvraag zonder dat een toepassing opnieuw moet worden opgestart. Dit kan worden geïmplementeerd door eerst een exemplaar van `IConfigurationRefresher` op te halen uit de opties voor de configuratie provider en vervolgens `Refresh` te roepen voor dat exemplaar, waar dan ook in uw code.

Als u de instellingen wilt bijwerken en te veel aanroepen naar de configuratie opslag wilt voor komen, wordt er voor elke instelling een cache gebruikt. Totdat de in de cache opgeslagen waarde van een instelling is verlopen, wordt de waarde niet door de vernieuwings bewerking bijgewerkt, zelfs niet wanneer de waarde is gewijzigd in de configuratie opslag. De standaard verval tijd voor elke aanvraag is 30 seconden, maar kan indien nodig worden overschreven.

In deze zelfstudie leert hoe u dynamische configuratie-updates kunt implementeren in uw code. Het is gebaseerd op de app die is geïntroduceerd in de Quick starts. Voordat u doorgaat, moet u eerst [een .NET Framework-app maken met de app-configuratie](./quickstart-dotnet-app.md) .

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Stel uw toepassing in om de configuratie bij te werken met een app-configuratie archief op aanvraag.
> * Injecteer de meest recente configuratie in de controllers van uw toepassing.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 of hoger](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Een app-configuratiearchief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecteer **configuratie verkenner** >  **+ maken** om de volgende sleutel-waardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure App Configuration |

    Laat het **Label** en het **inhouds type** nu leeg.

## <a name="create-a-net-console-app"></a>Een .NET Core-consoletoepassing maken

1. Start Visual Studio en selecteer **bestand** > **Nieuw** > -**project**.

1. In **een nieuw project maken**filtert u op het type **console** project en klikt u op **console-app (.NET Framework)** . Klik op **Volgende**.

1. Voer in **uw nieuwe project configureren**een project naam in. Onder **Framework**selecteert u **.NET Framework 4.7.1** of hoger. Klik op **Maken**.

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
    De methode `ConfigureRefresh` wordt gebruikt om de instellingen op te geven die worden gebruikt voor het bijwerken van de configuratie gegevens met het app-configuratie archief wanneer een vernieuwings bewerking wordt geactiveerd. Een instantie van `IConfigurationRefresher` kan worden opgehaald door de methode `GetRefresher` aan te roepen voor de opties die aan `AddAzureAppConfiguration` methode worden gegeven, en de methode `Refresh` op dit exemplaar kan worden gebruikt om een vernieuwings bewerking overal in uw code te activeren.

    > [!NOTE]
    > De standaard waarde voor de verval tijd van de cache voor een configuratie-instelling is 30 seconden, maar kan worden overschreven door de `SetCacheExpiration`-methode aan te roepen voor de initialisatie functie voor opties die als een argument aan de `ConfigureRefresh`-methode is door gegeven.

1. Voeg een methode toe met de naam `PrintMessage()` die de configuratie gegevens hand matig van de app-configuratie activeert.

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

## <a name="build-and-run-the-app-locally"></a>De app lokaal bouwen en uitvoeren

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
    > Omdat de verval tijd van de cache is ingesteld op 10 seconden met behulp van de `SetCacheExpiration` methode terwijl u de configuratie voor de vernieuwings bewerking opgeeft, wordt de waarde voor de configuratie-instelling alleen bijgewerkt als er ten minste tien seconden zijn verstreken sinds de laatste vernieuwing voor deze instelling.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een beheerde Azure-service-identiteit toegevoegd om toegang tot app-configuratie te stroomlijnen en het beheer van referenties voor uw app te verbeteren. Ga door naar de volgende zelf studie voor meer informatie over het toevoegen van een door Azure beheerde service-identiteit die de toegang tot de app-configuratie stroomlijnt.

> [!div class="nextstepaction"]
> [Beheerde identiteits integratie](./howto-integrate-azure-managed-service-identity.md)
