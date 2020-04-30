---
title: Ontwikkelen met ASP.NET-Azure signalerings service
description: Een Snelstartgids voor het gebruik van de Azure signalerings service voor het maken van een chat room met ASP.NET Framework.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/20/2019
ms.author: zhshang
ms.openlocfilehash: ec5b7a75bced4b7cd81a120925558b8c1be57818
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74158171"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Snelstartgids: een chat ruimte maken met ASP.NET en de signaal service

De Azure signalerings service is gebaseerd op de [Signa lering voor ASP.NET Core 2,0](https://docs.microsoft.com/aspnet/core/signalr/introduction), wat **niet** 100% compatibel is met ASP.net-Signa lering. De Azure signalerings service heeft het ASP.NET signalerings gegevens protocol opnieuw geïmplementeerd op basis van de nieuwste ASP.NET Core technologieën. Wanneer u de Azure signalerings service gebruikt voor ASP.NET-Signa lering, worden sommige functies van de ASP.NET-Signa lering niet meer ondersteund, bijvoorbeeld Azure-Signa lering voert geen berichten opnieuw af wanneer de client opnieuw verbinding maakt. Daarnaast worden de permanente frame transport en JSONP niet ondersteund. Er zijn enkele code wijzigingen en de juiste versie van de afhankelijke bibliotheken nodig om ervoor te zorgen dat de ASP.NET-seingevings toepassing werkt met de seingevings service. 

Raadpleeg het [document versie verschillen](https://docs.microsoft.com/aspnet/core/signalr/version-differences?view=aspnetcore-2.2) voor een volledige lijst met functie vergelijking tussen ASP.net signalr en ASP.net core signalr.

In deze Quick Start leert u hoe u aan de slag kunt gaan met de ASP.NET en de Azure signalerings service voor een soort gelijke [Chat Room-toepassing](./signalr-quickstart-dotnet-core.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
## <a name="prerequisites"></a>Vereisten

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET-4.6.1](https://www.microsoft.com/net/download/windows)
* [ASP.NET-signaalsterkte 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure Portal](https://portal.azure.com/) met uw Azure-account.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

De *serverloze* modus wordt niet ondersteund voor ASP.net-seingevings toepassingen. Gebruik altijd *standaard* of *klassiek* voor het exemplaar van de Azure signalerings service.

U kunt ook Azure-resources maken die worden gebruikt in deze Quick start met [een signalerings service script maken](scripts/signalr-cli-create-service.md).

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Terwijl de service wordt geïmplementeerd, gaan we met code werken. Kloon de [voorbeeld-app vanuit GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), stel de SignalR Service-verbindingsreeks in en voer de toepassing lokaal uit.

1. Open een nieuw git-terminalvenster. Ga naar een map waarin u het voorbeeldproject wilt klonen.

1. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="configure-and-run-chat-room-web-app"></a>Web-app voor chat room configureren en uitvoeren

1. Start Visual Studio en open de oplossing in de map *ASPNET-samples/ChatRoom/* van de gekloonde opslag plaats.

1. Zoek en selecteer het exemplaar dat u hebt gemaakt in de browser waar de Azure Portal is geopend.

1. Selecteer **Sleutels** om de verbindingsreeksen voor het service-exemplaar van SignalR weer te geven.

1. Selecteer en kopieer de primaire verbindingsreeks.

1. Stel nu de connection string in het bestand Web. config.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. In *Startup.cs*, in plaats van `MapSignalR()`aanroepen, moet u `MapAzureSignalR({your_applicationName})` Connection String aanroepen en door geven om ervoor te zorgen dat de toepassing verbinding maakt met de service in plaats van de signaal code zelf te hosten. Vervang `{YourApplicationName}` door de naam van uw toepassing. Deze naam is een unieke naam om deze toepassing te onderscheiden van uw andere toepassingen. U kunt gebruiken `this.GetType().FullName` als de-waarde.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    U moet ook verwijzen naar de Service-SDK voordat u deze Api's kunt gebruiken. Open de **Hulpprogram ma's | NuGet-pakket beheer | Package Manager-console** en voert u de volgende opdracht uit:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Met uitzonde ring van deze wijzigingen blijft alles hetzelfde, maar u kunt nog steeds de hub-interface gebruiken waarmee u al bekend bent om bedrijfs logica te schrijven.

    > [!NOTE]
    > In de implementatie wordt een `/signalr/negotiate` eind punt weer gegeven voor onderhandelingen door de Azure signalerings Service-SDK. Er wordt een speciaal onderhandelings antwoord geretourneerd wanneer clients proberen verbinding te maken en clients om te leiden naar een service-eind punt dat is gedefinieerd in de connection string.

1. Druk op **F5** om het project uit te voeren in de foutopsporingsmodus. U kunt zien dat de toepassing lokaal wordt uitgevoerd. In plaats van een seingevings runtime op zichzelf te hosten, wordt nu verbinding gemaakt met de Azure signalerings service.

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

In deze Quick Start hebt u een nieuwe Azure signalering service-resource gemaakt en gebruikt met een ASP.NET-Web-app. Vervolgens leert u hoe u in realtime toepassingen kunt ontwikkelen met behulp van de Azure signalerings service met ASP.NET Core.

> [!div class="nextstepaction"]
> [Azure signalerings service met ASP.NET Core](./signalr-quickstart-dotnet-core.md)
