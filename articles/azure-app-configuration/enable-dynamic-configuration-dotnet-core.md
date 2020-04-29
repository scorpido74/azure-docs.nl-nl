---
title: 'Zelf studie: dynamische configuratie in een .NET core-app gebruiken'
titleSuffix: Azure App Configuration
description: In deze zelf studie leert u hoe u de configuratie gegevens voor .NET Core-Apps dynamisch kunt bijwerken
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75433683"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Zelf studie: dynamische configuratie in een .NET core-app gebruiken

De app-configuratie .NET core client library ondersteunt het bijwerken van een set configuratie-instellingen op aanvraag zonder dat een toepassing opnieuw moet worden opgestart. Dit kan worden geïmplementeerd door eerst een instantie van `IConfigurationRefresher` te verkrijgen van de opties voor de configuratie provider en vervolgens `Refresh` te bellen naar dat exemplaar op een wille keurige plaats in uw code.

Als u de instellingen wilt bijwerken en te veel aanroepen naar de configuratie opslag wilt voor komen, wordt er voor elke instelling een cache gebruikt. Totdat de in de cache opgeslagen waarde van een instelling is verlopen, wordt de waarde niet door de vernieuwings bewerking bijgewerkt, zelfs niet wanneer de waarde is gewijzigd in de configuratie opslag. De standaard verval tijd voor elke aanvraag is 30 seconden, maar kan indien nodig worden overschreven.

In deze zelfstudie leert hoe u dynamische configuratie-updates kunt implementeren in uw code. Het is gebaseerd op de app die is geïntroduceerd in de Quick starts. Voordat u doorgaat, moet u eerst [een .net core-app maken met behulp van app-configuratie](./quickstart-dotnet-core-app.md) .

U kunt elke code-editor gebruiken om de stappen in deze zelf studie uit te voeren. [Visual Studio code](https://code.visualstudio.com/) is een uitstekende optie die beschikbaar is op de Windows-, macOS-en Linux-platformen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Stel uw .NET core-app in om de configuratie bij te werken als reactie op wijzigingen in een app-configuratie archief.
> * Gebruik de nieuwste configuratie in uw toepassing.

## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt uitvoeren, installeert u de [.net core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Gegevens opnieuw laden vanuit app-configuratie

Open *Program.cs* en werk het bestand bij om een verwijzing naar de `System.Threading.Tasks` naam ruimte toe te voegen, om `AddAzureAppConfiguration` de configuratie van vernieuwen in de methode op te `Refresh` geven en hand matige vernieuwing te activeren met behulp van de methode.

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

De `ConfigureRefresh` -methode wordt gebruikt om de instellingen op te geven die worden gebruikt voor het bijwerken van de configuratie gegevens met het app-configuratie archief wanneer een vernieuwings bewerking wordt geactiveerd. Een exemplaar van `IConfigurationRefresher` kan worden opgehaald door de aanroep `GetRefresher` methode voor de opties die aan `AddAzureAppConfiguration` de methode worden door `Refresh` gegeven en de methode op dit exemplaar kan worden gebruikt om een vernieuwings bewerking overal in uw code te activeren.
    
> [!NOTE]
> De standaard waarde voor de verval tijd van de cache voor een configuratie-instelling is 30 seconden, maar kan `SetCacheExpiration` worden overschreven door het aanroepen van de methode voor de `ConfigureRefresh` initialisatie functie voor opties die als een argument voor de methode wordt door gegeven.

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Stel een omgevings variabele met de naam **Connections Tring**in en stel deze in op de toegangs sleutel voor uw app-configuratie archief. Als u de Windows-opdracht prompt gebruikt, voert u de volgende opdracht uit en start u de opdracht prompt zodat de wijziging kan worden doorgevoerd:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Als u Windows Power shell gebruikt, voert u de volgende opdracht uit:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

        export ConnectionString='connection-string-of-your-app-configuration-store'

1. Voer de volgende opdracht uit om de console-app te bouwen:

        dotnet build

1. Nadat de build is voltooid, voert u de volgende opdracht uit om de app lokaal uit te voeren:

        dotnet run

    ![Quickstart voor het lokaal starten van een app](./media/quickstarts/dotnet-core-app-run.png)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **alle resources**en selecteer de app-configuratie Store-instantie die u hebt gemaakt in de Quick Start.

1. Selecteer **Configuration Explorer**en werk de waarden van de volgende sleutels bij:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure-app configuratie-bijgewerkt |

1. Druk op ENTER om een vernieuwen te activeren en de bijgewerkte waarde af te drukken in de opdracht prompt of Power shell-venster.

    ![Quickstart voor het lokaal vernieuwen van een app](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Omdat de verval tijd van de cache is ingesteld op 10 seconden `SetCacheExpiration` met behulp van de methode terwijl u de configuratie voor de vernieuwings bewerking opgeeft, wordt de waarde voor de configuratie-instelling alleen bijgewerkt als er ten minste tien seconden zijn verstreken sinds de laatste vernieuwing voor die instelling.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u uw .NET core-app ingeschakeld voor het dynamisch vernieuwen van configuratie-instellingen van de app-configuratie. Ga verder met de volgende zelf studie als u wilt weten hoe u een door Azure beheerde identiteit kunt gebruiken om de toegang tot de app-configuratie te stroom lijnen.

> [!div class="nextstepaction"]
> [Beheerde identiteits integratie](./howto-integrate-azure-managed-service-identity.md)
