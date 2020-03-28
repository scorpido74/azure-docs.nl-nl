---
title: 'Zelfstudie: Dynamische configuratie gebruiken in een .NET Core-app'
titleSuffix: Azure App Configuration
description: In deze zelfstudie leert u hoe u de configuratiegegevens voor .NET Core-apps dynamisch bijwerken
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: abarora
ms.openlocfilehash: afecc84748ae8ce85c07e3b482bd9b596bdca251
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75433683"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Zelfstudie: Dynamische configuratie gebruiken in een .NET Core-app

De app configuration .NET Core-clientbibliotheek ondersteunt het updaten van een set configuratie-instellingen op aanvraag zonder dat een toepassing opnieuw wordt opgestart. Dit kan worden geïmplementeerd door eerst `IConfigurationRefresher` een instantie van de opties `Refresh` voor de configuratieprovider te krijgen en vervolgens die instantie overal in uw code aan te roepen.

Om de instellingen up-to-date te houden en te veel oproepen naar het configuratiearchief te voorkomen, wordt voor elke instelling een cache gebruikt. Totdat de in de cache opgeslagen waarde van een instelling is verlopen, wordt de waarde niet bijgewerkt door de vernieuwingsbewerking, zelfs niet wanneer de waarde in het configuratiearchief is gewijzigd. De standaardvervaldatum voor elke aanvraag is 30 seconden, maar kan indien nodig worden overschreven.

In deze zelfstudie leert hoe u dynamische configuratie-updates kunt implementeren in uw code. Het bouwt voort op de app geïntroduceerd in de quickstarts. Voordat u verdergaat, [voltooit u eerst een .NET Core-app maken met app-configuratie.](./quickstart-dotnet-core-app.md)

U elke codeeditor gebruiken om de stappen in deze zelfstudie uit te voeren. [Visual Studio Code](https://code.visualstudio.com/) is een uitstekende optie die beschikbaar is op de Windows-, macOS- en Linux-platforms.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Stel uw .NET Core-app in om de configuratie bij te werken als reactie op wijzigingen in een App Configuration Store.
> * Gebruik de nieuwste configuratie in uw toepassing.

## <a name="prerequisites"></a>Vereisten

Installeer de [.NET Core SDK](https://dotnet.microsoft.com/download)om deze zelfstudie te doen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Gegevens opnieuw laden vanuit app-configuratie

Open *Program.cs* en werk het bestand `System.Threading.Tasks` bij om een verwijzing naar `AddAzureAppConfiguration` de naamruimte toe te `Refresh` voegen, om vernieuwingsconfiguratie in de methode op te geven en handmatig vernieuwen te activeren met de methode.

```csharp
using System;
using System.Threading.Tasks;

namespace TestConsole
{
class Program
{
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;

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

    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
}
}
```

De `ConfigureRefresh` methode wordt gebruikt om de instellingen op te geven die worden gebruikt om de configuratiegegevens bij te werken met het App Configuration Store wanneer een vernieuwingsbewerking wordt geactiveerd. Een instantie `IConfigurationRefresher` van kan worden `GetRefresher` opgehaald door aanroepen methode op de opties die aan `AddAzureAppConfiguration` de methode, en de `Refresh` methode op deze instantie kan worden gebruikt om een vernieuwing operatie overal in uw code te activeren.
    
> [!NOTE]
> De standaardcacheverlooptijd voor een configuratie-instelling is 30 seconden, `SetCacheExpiration` maar kan worden overschreven door de `ConfigureRefresh` methode aan te roepen op de opties initializer doorgegeven als argument aan de methode.

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Stel een omgevingsvariabele met de naam **ConnectionString**in en stel deze in op de toegangssleutel voor uw App Configuration Store. Als u de opdrachtprompt van Windows gebruikt, voert u de volgende opdracht uit en start u de opdrachtprompt opnieuw om de wijziging van kracht te laten worden:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Als u Windows PowerShell gebruikt, voert u de volgende opdracht uit:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

        export ConnectionString='connection-string-of-your-app-configuration-store'

1. Voer de volgende opdracht uit om de console-app te bouwen:

        dotnet build

1. Nadat de build is voltooid, voert u de volgende opdracht uit om de app lokaal uit te voeren:

        dotnet run

    ![Quickstart voor het lokaal starten van een app](./media/quickstarts/dotnet-core-app-run.png)

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Alle bronnen**en selecteer de instantie app-configuratiearchief die u in de snelstart hebt gemaakt.

1. Selecteer **Configuratieverkenner**en werk de waarden van de volgende toetsen bij:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens uit Azure App-configuratie - Bijgewerkt |

1. Druk op Enter om een vernieuwing te activeren en de bijgewerkte waarde af te drukken in het venster Opdrachtprompt of PowerShell.

    ![Quickstart voor het lokaal vernieuwen van een app](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Aangezien de verlooptijd van de cache `SetCacheExpiration` is ingesteld op 10 seconden met behulp van de methode terwijl de configuratie voor de vernieuwingsbewerking is opgegeven, wordt de waarde voor de configuratie-instelling alleen bijgewerkt als er ten minste 10 seconden zijn verstreken sinds de laatste vernieuwing voor die instelling.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de .NET Core-app ingeschakeld om de configuratie-instellingen van app-configuratie dynamisch te vernieuwen. Ga door naar de volgende zelfstudie voor meer informatie over het gebruik van een door Azure beheerde identiteit om de toegang tot app-configuratie te stroomlijnen.

> [!div class="nextstepaction"]
> [Beheerde identiteitsintegratie](./howto-integrate-azure-managed-service-identity.md)
