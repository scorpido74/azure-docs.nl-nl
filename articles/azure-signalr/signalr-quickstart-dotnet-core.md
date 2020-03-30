---
title: 'Snelstart: Leren werken met Azure SignalR Service'
description: Een snelstart waarin u leert hoe u met de Azure SignalR-service een chatruimte maakt met ASP.NET Core MVC-apps.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: zhshang
ms.openlocfilehash: 022780f2b37c8bed49c81774d443b69bae41e5e7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73476757"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Snelstart: een chatroom maken met Behulp van SignalR Service


De Azure SignalR-service is een Azure-service waarmee ontwikkelaars eenvoudig webtoepassingen met realtime-functies kunnen bouwen. Deze service is gebaseerd op [SignalR voor ASP.NET Core 2.1,](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-2.1)maar ondersteunt ook [SignalR voor ASP.NET Core 3.0](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.0).

In dit artikel leest u hoe u aan de slag gaat met de Azure SignalR-service. In deze quickstart maakt u een chattoepassing met behulp van een ASP.NET Core MVC-webapp. Deze app maakt verbinding met de resource van de Azure SignalR-service om inhoud in realtime bij te werken. U host de webtoepassing lokaal en maakt verbinding met meerdere browserclients. Elke client kan bijgewerkte inhoud pushen naar alle andere clients. 

U kunt elke code-editor gebruiken om de stappen in deze snelstart uit te voeren. Een optie is [Visual Studio Code](https://code.visualstudio.com/), die beschikbaar is op de Windows-, macOS- en Linux-platforms.

De code voor deze zelfstudie is beschikbaar als download in de [GitHub-opslagplaats met AzureSignalR-voorbeelden](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). U ook de Azure-resources maken die in deze quickstart worden gebruikt door [een SignalR-servicescript maken](scripts/signalr-cli-create-service.md)te volgen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Vereisten

* Installeer de [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* Download of kloon het [Azure SignalR-voorbeeld](https://github.com/aspnet/AzureSignalR-samples) in de GitHub-opslagplaats. 

## <a name="create-an-azure-signalr-resource"></a>Een Azure SignalR-resource maken

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Een ASP.NET Core-web-app maken

In deze sectie gebruikt u de [.NET Core command-line interface (CLI)](https://docs.microsoft.com/dotnet/core/tools/) om een ASP.NET Core MVC webapp project te maken. Het voordeel van het gebruik van de .NET Core CLI ten opzichte van Visual Studio is dat het beschikbaar is op de Windows-, macOS- en Linux-platforms. 

1. Maak een map voor uw project. Deze quickstart maakt gebruik van de map *E:\Testing\chattest.*

2. Voer in de nieuwe map de volgende opdracht uit om het project te maken:

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>Secret Manager toevoegen aan het project

In deze sectie voegt u de [tool Geheim beheer](https://docs.microsoft.com/aspnet/core/security/app-secrets) toe aan uw project. De tool Secret Manager slaat gevoelige gegevens op voor ontwikkelingswerk buiten uw projectstructuur. Deze aanpak helpt voorkomen dat het per ongeluk delen van app-geheimen in broncode.

1. Open het bestand *.csproj*. Voeg een element `DotNetCliToolReference` toe voor *Microsoft.Extensions.SecretManager.Tools*. Voeg ook `UserSecretsId` een element toe zoals weergegeven in de volgende code voor *chattest.csproj*en sla het bestand op.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
    </ItemGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
    </Project>    
    ```

## <a name="add-azure-signalr-to-the-web-app"></a>Azure SignalR toevoegen aan de web-app

1. Voeg een verwijzing `Microsoft.Azure.SignalR` toe aan het pakket NuGet door de volgende opdracht uit te voeren:

        dotnet add package Microsoft.Azure.SignalR

2. Voer de volgende opdracht uit om pakketten voor uw project te herstellen:

        dotnet restore

3. Voeg een geheim met de naam *Azure:SignalR:ConnectionString* toe aan Secret Manager. 

    Dit geheim bevat straks de verbindingsreeks voor toegang tot de resource van de SignalR-service. *Azure:SignalR:ConnectionString* is de standaardconfiguratiesleutel waar Naar SignalR wordt gezocht om een verbinding tot stand te brengen. Vervang de waarde in de volgende opdracht door de verbindingstekenreeks voor uw SignalR Service-bron.

    U moet deze opdracht uitvoeren in dezelfde map als het *.csproj-bestand.*

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"    
    ```

    Secret Manager wordt alleen gebruikt voor het testen van de web-app terwijl deze lokaal wordt gehost. In een latere zelfstudie implementeert u de chatweb-app naar Azure. Nadat de web-app is geïmplementeerd in Azure, gebruikt u een toepassingsinstelling in plaats van de verbindingstekenreeks op te slaan met Secret Manager.

    Dit geheim is toegankelijk met de Configuration API. Een dikke darm (:) werkt in de configuratienaam met de Configuratie-API op alle ondersteunde platforms. Zie [Configuratie per omgeving](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0). 


4. Open *Startup.cs* en `ConfigureServices` werk de methode bij om `services.AddSignalR().AddAzureSignalR()` Azure SignalR Service te gebruiken door alleen de methode voor ASP.NET Core 2 aan te roepen:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```
    Voor ASP.NET Core 3+ is er `ConfigureServices` geen verandering nodig voor de methode.

    Als u een `AddAzureSignalR()`parameter niet doorgeeft aan , gebruikt deze code de standaardconfiguratiesleutel voor de verbindingstekenreeks van de SignalR Service-bron. De standaardconfiguratiesleutel is *Azure:SignalR:ConnectionString*.

5. Ook in *Startup.cs,* de `Configure` methode bijwerken `app.UseStaticFiles()` door de aanroep te vervangen door de volgende code en het bestand op te slaan, alleen voor ASP.NET Core 2.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            
    Vervang voor ASP.NET Core 3+ de bovenstaande code door:

    ```csharp
    app.UseFileServer();
    app.UseRouting();
    app.UseAuthorization();

    app.UseEndpoints(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```

### <a name="add-a-hub-class"></a>Een hub-klasse toevoegen

In SignalR is een hub een kerncomponent die een reeks methoden blootlegt die vanuit de client kunnen worden aangeroepen. In dit gedeelte gaat u een hub-klasse met twee methoden definiëren: 

* `Broadcast`: met deze methode verstuurt u een bericht naar alle clients.
* `Echo`: met deze methode stuurt u een bericht terug naar de aanroepende functie.

Beide methoden maken `Clients` gebruik van de interface die de ASP.NET Core SignalR SDK biedt. Deze interface geeft u toegang tot alle verbonden clients, zodat u inhoud naar uw klanten pushen.

1. Voeg in de projectmap een nieuwe map toe met de naam *Hub*. Voeg een nieuw bestand met hub-code met de naam *Chat.cs* toe aan de nieuwe map.

2. Voeg de volgende code toe aan *Chat.cs* om uw hubklasse te definiëren en het bestand op te slaan. 

    Werk de naamruimte voor deze klasse bij als u een andere projectnaam dan *chattest* hebt gebruikt.

    ```csharp
    using Microsoft.AspNetCore.SignalR;

    namespace chattest
    {

        public class Chat : Hub
        {
            public void BroadcastMessage(string name, string message)
            {
                Clients.All.SendAsync("broadcastMessage", name, message);
            }

            public void Echo(string name, string message)
            {
                Clients.Client(Context.ConnectionId).SendAsync("echo", name, message + " (echo from server)");
            }
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>De clientinterface voor de web-app toevoegen

De clientgebruikersinterface voor deze chatroom-app bestaat uit HTML en JavaScript in een bestand met de naam *index.html* in de *map wwwroot.*

Kopieer het *bestand index.html,* de *map css* en de map *scripts* uit de *map wwwroot* van de [opslagplaats voorbeelden.](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot) Plak ze in de *wwwroot-map* van uw project.

Hier is de belangrijkste code van *index.html:* 

```javascript
var connection = new signalR.HubConnectionBuilder()
                            .withUrl('/chat')
                            .build();
bindConnectionMessage(connection);
connection.start()
    .then(function () {
        onConnected(connection);
    })
    .catch(function (error) {
        console.error(error.message);
    });
```    

De code in *index.html* roept `HubConnectionBuilder.build()` om een HTTP-verbinding te maken met de Azure SignalR-bron.

Als de verbinding tot stand is gebracht, wordt de verbinding doorgegeven aan `bindConnectionMessage`. Deze functie voegt gebeurtenis-handlers toe om binnenkomende inhoud naar de client te pushen. 

`HubConnection.start()` start de communicatie met de hub. Hiermee `onConnected()` voegt u vervolgens de gebeurtenishandlers van de knop toe. Deze handlers gebruiken de verbinding om deze client in staat te stellen updates van inhoud naar alle verbonden clients te pushen.

## <a name="add-a-development-runtime-profile"></a>Een profiel voor een runtime-ontwikkelomgeving toevoegen

In deze sectie voegt u een runtime-omgeving voor ontwikkeling toe voor ASP.NET Core. Zie [Werken met meerdere omgevingen in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments)voor meer informatie.

1. Maak een map met de naam *Eigenschappen* in uw project.

2. Voeg een nieuw bestand met de naam *launchSettings.json* toe aan de map, met de volgende inhoud, en sla het bestand op.

    ```json
    {
        "profiles" : 
        {
            "ChatRoom": 
            {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": 
                {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```


## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Als u de app wilt bouwen met de .NET Core CLI, voert u de volgende opdracht uit in de opdrachtshell:

        dotnet build

2. Voer de volgende opdracht uit om de web-app lokaal uit te voeren nadat de build is voltooid:

        dotnet run

    De app wordt lokaal gehost op poort 5000, zoals geconfigureerd in ons runtime-profiel voor ontwikkeling:

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. Open twee browservensters. Ga in elke `http://localhost:5000`browser naar . Je wordt gevraagd je naam in te voeren. Voer een clientnaam in voor beide clients en test het indrukken van berichtinhoud tussen beide clients met behulp van de knop **Verzenden.**

    ![Voorbeeld van een Azure SignalR-groepschat](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Resources opschonen

Als u doorgaat naar de volgende zelfstudie, u de bronnen die in deze quickstart zijn gemaakt, behouden en deze opnieuw gebruiken.

Als u klaar bent met de voorbeeldtoepassing snel start, u de Azure-resources verwijderen die in deze quickstart zijn gemaakt om kosten te voorkomen. 

> [!IMPORTANT]
> Het verwijderen van een resourcegroep is onomkeerbaar en bevat alle resources in die groep. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de resources hebt gemaakt voor het hosten van dit voorbeeld in een bestaande resourcegroep die resources bevat die u wilt behouden, u elke bron afzonderlijk uit het blad verwijderen in plaats van de brongroep te verwijderen.
> 
> 

Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Resourcegroepen**.

Typ in het tekstvak **Filteren op naam** de naam van uw resourcegroep. In de instructies voor deze snelstart is een resourcegroep met de naam *SignalRTestResources* gebruikt. Selecteer in uw resourcegroep in de resultatenlijst de ellips (**...**) > **Brongroep verwijderen**.

   
![Selecties voor het verwijderen van een resourcegroep](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


U wordt gevraagd om het verwijderen van de resourcegroep te bevestigen. Voer de naam van uw resourcegroep in om te bevestigen en selecteer **Verwijderen**.
   
Na enkele ogenblikken worden de resourcegroep en alle bijbehorende resources verwijderd.



## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een nieuwe Azure SignalR Service-bron gemaakt. U gebruikte het vervolgens met een ASP.NET Core-web-app om inhoudsupdates in realtime naar meerdere verbonden clients te pushen. Ga voor meer informatie over het gebruik van Azure SignalR-service verder naar de zelfstudie die verificatie demonstreert.

> [!div class="nextstepaction"]
> [verificatie van Azure SignalR Service](./signalr-concept-authenticate-oauth.md)


