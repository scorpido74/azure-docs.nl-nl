---
title: '.NET Framework-zelfstudie: dynamische configuratie in Azure-app-configuratie'
description: In deze zelfstudie leert u hoe u de configuratiegegevens voor .NET Framework-apps dynamisch bijwerken met Azure App Configuration.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 7780bdbc92868f62e8d066d171b2a04fe06a981d
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80245800"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Zelfstudie: Dynamische configuratie gebruiken in een .NET Framework-app

De app-configuratie .net-clientbibliotheek ondersteunt het bijwerken van een set configuratie-instellingen op aanvraag zonder dat een toepassing opnieuw wordt opgestart. Dit kan worden geïmplementeerd door eerst `IConfigurationRefresher` een instantie van de opties `Refresh` voor de configuratieprovider te krijgen en vervolgens die instantie overal in uw code aan te roepen.

Om de instellingen up-to-date te houden en te veel oproepen naar het configuratiearchief te voorkomen, wordt voor elke instelling een cache gebruikt. Totdat de in de cache opgeslagen waarde van een instelling is verlopen, wordt de waarde niet bijgewerkt door de vernieuwingsbewerking, zelfs niet wanneer de waarde in het configuratiearchief is gewijzigd. De standaardvervaldatum voor elke aanvraag is 30 seconden, maar kan indien nodig worden overschreven.

In deze zelfstudie leert hoe u dynamische configuratie-updates kunt implementeren in uw code. Het bouwt voort op de app geïntroduceerd in de quickstarts. Voordat u verdergaat, [voltooit u eerst een .NET Framework-app maken met app-configuratie.](./quickstart-dotnet-app.md)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Stel uw .NET Framework-app in om de configuratie bij te werken als reactie op wijzigingen in een App Configuration Store.
> * Injecteer de nieuwste configuratie in uw toepassing.
## <a name="prerequisites"></a>Vereisten

- Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 of hoger](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Een app-configuratiearchief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecteer **Configuratieverkenner** > **+ Sleutelwaarde maken** > **Key-value** om de volgende sleutelwaardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure App Configuration |

    Laat **label en** **inhoudstype** voorlopig leeg.

7. Selecteer **Toepassen**.

## <a name="create-a-net-framework-console-app"></a>Een .NET Framework-console-app maken

1. Start Visual Studio en selecteer Nieuw**New** > **project** **bestand** > .

1. Filter **in Een nieuw project maken**op het **projecttype Console** en klik op **Console-app (.NET Framework).** Klik op **Volgende**.

1. Voer in **Uw nieuwe project configureren**een projectnaam in. Selecteer **onder Framework** **.NET Framework 4.7.1** of hoger. Klik **op Maken**.

## <a name="reload-data-from-app-configuration"></a>Gegevens opnieuw laden vanuit app-configuratie
1. Klik met de rechtermuisknop op uw project en selecteer **NuGet-pakketten beheren.** Zoek **op het** tabblad Bladeren en voeg het nuGet-pakket *Microsoft.Extensions.Configuration.AzureAppConfiguration* NuGet toe aan uw project. Als u deze niet vinden, schakelt u het selectievakje **Prerelease opnemen** in.

1. Open *Program.cs*en voeg een verwijzing toe naar de .NET Core App Configuration provider.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Voeg twee variabelen toe om configuratiegerelateerde objecten op te slaan.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Werk `Main` de methode bij om verbinding te maken met app-configuratie met de opgegeven vernieuwingsopties.

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
    De `ConfigureRefresh` methode wordt gebruikt om de instellingen op te geven die worden gebruikt om de configuratiegegevens bij te werken met het App Configuration Store wanneer een vernieuwingsbewerking wordt geactiveerd. Een instantie `IConfigurationRefresher` van kan worden `GetRefresher` opgehaald door aanroepen methode op de opties die aan `AddAzureAppConfiguration` de methode, en de `Refresh` methode op deze instantie kan worden gebruikt om een vernieuwing operatie overal in uw code te activeren.

    > [!NOTE]
    > De standaardcacheverlooptijd voor een configuratie-instelling is 30 seconden, `SetCacheExpiration` maar kan worden overschreven door de `ConfigureRefresh` methode aan te roepen op de opties initializer doorgegeven als argument aan de methode.

1. Voeg een `PrintMessage()` methode toe die wordt aangeroepen om een handmatige vernieuwing van configuratiegegevens uit app-configuratie te maken.

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

1. Stel een omgevingsvariabele met de naam **ConnectionString**in en stel deze in op de toegangssleutel voor uw App Configuration Store. Als u de opdrachtprompt van Windows gebruikt, voert u de volgende opdracht uit en start u de opdrachtprompt opnieuw om de wijziging van kracht te laten worden:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Als u Windows PowerShell gebruikt, voert u de volgende opdracht uit:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Start Visual Studio opnieuw op om de wijziging van kracht te laten worden. 

1. Druk op Ctrl + F5 om de console-app te bouwen en uit te voeren.

    ![App lokaal starten](./media/dotnet-app-run.png)

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Alle bronnen**en selecteer de instantie app-configuratiearchief die u in de snelstart hebt gemaakt.

1. Selecteer **Configuratieverkenner**en werk de waarden van de volgende toetsen bij:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens uit Azure App-configuratie - Bijgewerkt |

1. Druk terug in de lopende toepassing op de enter-toets om een vernieuwing te activeren en de bijgewerkte waarde af te drukken in het venster Opdrachtprompt of PowerShell.

    ![App lokaal vernieuwen](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Aangezien de verlooptijd van de cache `SetCacheExpiration` is ingesteld op 10 seconden met behulp van de methode terwijl de configuratie voor de vernieuwingsbewerking is opgegeven, wordt de waarde voor de configuratie-instelling alleen bijgewerkt als er ten minste 10 seconden zijn verstreken sinds de laatste vernieuwing voor die instelling.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de .NET Framework-app ingeschakeld om de configuratie-instellingen van app-configuratie dynamisch te vernieuwen. Ga door naar de volgende zelfstudie voor meer informatie over het gebruik van een door Azure beheerde identiteit om de toegang tot app-configuratie te stroomlijnen.

> [!div class="nextstepaction"]
> [Beheerde identiteitsintegratie](./howto-integrate-azure-managed-service-identity.md)
