---
title: Ontwikkelen met ASP.NET - Azure SignalR-service
description: Een snelle start voor het gebruik van Azure SignalR Service om een chatroom te maken met ASP.NET framework.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/20/2019
ms.author: zhshang
ms.openlocfilehash: ec5b7a75bced4b7cd81a120925558b8c1be57818
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74158171"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Snelstart: maak een chatroom met ASP.NET en SignalR Service

Azure SignalR Service is gebaseerd op [SignalR voor ASP.NET Core 2.0,](https://docs.microsoft.com/aspnet/core/signalr/introduction)die **niet** 100% compatibel is met ASP.NET SignalR. Azure SignalR Service opnieuw geïmplementeerd ASP.NET SignalR data protocol op basis van de nieuwste ASP.NET Core technologieën. Bij het gebruik van Azure SignalR-service voor ASP.NET SignalR worden sommige ASP.NET SignalR-functies niet meer ondersteund, bijvoorbeeld wanneer Azure SignalR berichten niet opnieuw opnieuw afspeelt wanneer de client opnieuw verbinding maakt. Ook worden het Forever Frame transport en JSONP niet ondersteund. Sommige codewijzigingen en de juiste versie van afhankelijke bibliotheken zijn nodig om ASP.NET SignalR-toepassing te laten werken met SignalR Service. 

Raadpleeg het [document voor versieverschillen](https://docs.microsoft.com/aspnet/core/signalr/version-differences?view=aspnetcore-2.2) voor een volledige lijst met functievergelijkingtussen ASP.NET SignalR en ASP.NET Core SignalR.

In deze quickstart leert u hoe u aan de slag met de ASP.NET en Azure SignalR-service voor een vergelijkbare [chatroomtoepassing.](./signalr-quickstart-dotnet-core.md)


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
## <a name="prerequisites"></a>Vereisten

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [ASP.NET SignalR 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com/) met uw Azure-account.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

*De serverloze* modus wordt niet ondersteund voor ASP.NET SignalR-toepassingen. Gebruik *altijd Standaard* of *Klassiek* voor de instantie Azure SignalR Service.

U ook Azure-resources maken die in deze quickstart worden gebruikt met [Een SignalR-servicescript maken.](scripts/signalr-cli-create-service.md)

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Terwijl de service wordt geïmplementeerd, gaan we met code werken. Kloon de [voorbeeld-app vanuit GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), stel de SignalR Service-verbindingsreeks in en voer de toepassing lokaal uit.

1. Open een nieuw git-terminalvenster. Ga naar een map waarin u het voorbeeldproject wilt klonen.

1. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="configure-and-run-chat-room-web-app"></a>Web-app Chatruimte configureren en uitvoeren

1. Start Visual Studio en open de oplossing in de *aspnet-samples/ChatRoom/map* van de gekloonde repository.

1. Zoek en selecteer in de browser waar de Azure-portal wordt geopend de instantie die u hebt gemaakt.

1. Selecteer **Sleutels** om de verbindingsreeksen voor het service-exemplaar van SignalR weer te geven.

1. Selecteer en kopieer de primaire verbindingsreeks.

1. Stel nu de verbindingstekenreeks in het web.config-bestand in.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. In *Startup.cs*, `MapSignalR()`in plaats van `MapAzureSignalR({your_applicationName})` te bellen , moet u bellen en doorgeven in verbinding string om de toepassing verbinding te maken met de service in plaats van hosting SignalR zelf. Vervang `{YourApplicationName}` de naam van uw toepassing. Deze naam is een unieke naam om deze toepassing te onderscheiden van uw andere toepassingen. U kunt `this.GetType().FullName` gebruiken als de waarde.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    U moet ook verwijzen naar de SDK van de service voordat u deze API's gebruikt. Open de **gereedschappen | NuGet Package Manager | Pakketbeheerconsole** en run, opdracht:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Anders dan deze wijzigingen, blijft al het andere hetzelfde, u nog steeds gebruik maken van de hub-interface die u al kent om bedrijfslogica te schrijven.

    > [!NOTE]
    > In de implementatie `/signalr/negotiate` wordt een eindpunt belicht voor onderhandeling door Azure SignalR Service SDK. Het retourneert een speciale onderhandelingsreactie wanneer clients proberen klanten te verbinden en om te leiden naar serviceeindpunt dat is gedefinieerd in de verbindingstekenreeks.

1. Druk op **F5** om het project uit te voeren in de foutopsporingsmodus. U zien dat de toepassing lokaal wordt uitgevoerd. In plaats van een SignalR-runtime per toepassing zelf te hosten, maakt deze nu verbinding met de Azure SignalR-service.

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]



> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle bijbehorende resources worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de resources voor het hosten van dit voorbeeld in een bestaande resourcegroep hebt gemaakt en deze groep ook resources bevat die u wilt behouden, kunt u elke resource afzonderlijk verwijderen via hun respectievelijke blade.
> 
> 

Meld u aan bij de [Azure-portal](https://portal.azure.com) en klik op **Resourcegroepen**.

Typ de naam van de resourcegroep in het tekstvak **Filteren op naam...**. In de instructies voor deze snelstart is een resourcegroep met de naam *SignalRTestResources* gebruikt. Klik in de resourcegroep in de lijst met resultaten op **...** en vervolgens op **Resourcegroep verwijderen**.

   
![Verwijderen](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Na enkele ogenblikken worden de resourcegroep en alle resources in de groep verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een nieuwe Azure SignalR Service-bron gemaakt en deze gebruikt met een web-app ASP.NET. Leer vervolgens hoe u realtime toepassingen ontwikkelen met Azure SignalR-service met ASP.NET Core.

> [!div class="nextstepaction"]
> [Azure SignalR-service met ASP.NET Core](./signalr-quickstart-dotnet-core.md)
