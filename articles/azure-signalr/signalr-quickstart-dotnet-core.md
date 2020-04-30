---
title: 'Snelstart: Leren werken met Azure SignalR Service'
description: Een snelstart waarin u leert hoe u met de Azure SignalR-service een chatruimte maakt met ASP.NET Core MVC-apps.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: zhshang
ms.openlocfilehash: f87625fe4f56b369f2bf4aade3ef5424084b6fe8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81254883"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Quick Start: een chat ruimte maken met de seingevings service


De Azure SignalR-service is een Azure-service waarmee ontwikkelaars eenvoudig webtoepassingen met realtime-functies kunnen bouwen. Deze service is gebaseerd op de [seingevings voor ASP.NET Core 2,1](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-2.1), maar biedt ook ondersteuning [voor de signa lering voor ASP.net Core 3,0](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.0).

In dit artikel leest u hoe u aan de slag gaat met de Azure SignalR-service. In deze Quick Start maakt u een chat-toepassing met behulp van een ASP.NET Core MVC-Web-app. Deze app maakt verbinding met de resource van de Azure SignalR-service om inhoud in realtime bij te werken. U gaat de webtoepassing lokaal hosten en verbinding maken met meerdere browser-clients. Elke client kan bijgewerkte inhoud pushen naar alle andere clients. 

U kunt elke code-editor gebruiken om de stappen in deze snelstart uit te voeren. Een optie is [Visual Studio code](https://code.visualstudio.com/), die beschikbaar is op het Windows-, macOS-en Linux-platform.

De code voor deze zelfstudie is beschikbaar als download in de [GitHub-opslagplaats met AzureSignalR-voorbeelden](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). U kunt ook de Azure-resources maken die in deze Quick Start worden gebruikt door het [script voor een signalerings service](scripts/signalr-cli-create-service.md)te volgen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Vereisten

* Installeer de [.net core SDK](https://www.microsoft.com/net/download/windows).
* Download of kloon het [Azure SignalR-voorbeeld](https://github.com/aspnet/AzureSignalR-samples) in de GitHub-opslagplaats. 

## <a name="create-an-azure-signalr-resource"></a>Een Azure SignalR-resource maken

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Een ASP.NET Core-web-app maken

In deze sectie gebruikt u de [.net core-opdracht regel interface (CLI)](https://docs.microsoft.com/dotnet/core/tools/) om een ASP.net core MVC-Web-app-project te maken. Het voor deel van het gebruik van de .NET Core SLI over Visual Studio is dat het beschikbaar is via de Windows-, macOS-en Linux-platformen. 

1. Maak een map voor uw project. In deze Quick Start wordt de map *E:\Testing\chattest* gebruikt.

2. Voer in de nieuwe map de volgende opdracht uit om het project te maken:

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>Secret Manager toevoegen aan het project

In deze sectie voegt u het [hulp programma voor geheime beheer](https://docs.microsoft.com/aspnet/core/security/app-secrets) toe aan uw project. Het hulp programma voor de geheime beheerder slaat gevoelige gegevens op voor ontwikkelings werkzaamheden buiten de project structuur. Deze benadering voor komt het onbedoeld delen van app-geheimen in de bron code.

1. Open het bestand *.csproj*. Voeg een element `DotNetCliToolReference` toe voor *Microsoft.Extensions.SecretManager.Tools*. Voeg ook een `UserSecretsId` element toe, zoals wordt weer gegeven in de volgende code voor *chattest. csproj*, en sla het bestand op.

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

1. Voeg een verwijzing naar het `Microsoft.Azure.SignalR` NuGet-pakket toe door de volgende opdracht uit te voeren:

        dotnet add package Microsoft.Azure.SignalR

2. Voer de volgende opdracht uit om de pakketten voor uw project te herstellen:

        dotnet restore

3. Voeg een geheim met de naam *Azure:SignalR:ConnectionString* toe aan Secret Manager. 

    Dit geheim bevat straks de verbindingsreeks voor toegang tot de resource van de SignalR-service. *Azure: signaal sterkte: Connections Tring* is de standaard configuratie sleutel die signaalr zoekt om een verbinding tot stand te brengen. Vervang de waarde in de volgende opdracht door de connection string voor uw seingevings service-resource.

    U moet deze opdracht uitvoeren in dezelfde map als het *. csproj* -bestand.

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"    
    ```

    De geheime beheerder wordt alleen gebruikt voor het testen van de web-app terwijl deze lokaal wordt gehost. In een latere zelf studie implementeert u de chat-web-app in Azure. Wanneer de web-app is geïmplementeerd in azure, gebruikt u een toepassings instelling in plaats van de connection string op te slaan met behulp van geheime beheerder.

    Dit geheim wordt geopend met de configuratie-API. Een dubbele punt (:) werkt in de configuratie naam met de configuratie-API op alle ondersteunde platforms. Zie [configuratie per omgeving](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0). 


4. Open *Startup.cs* en werk de methode `ConfigureServices` bij voor het gebruik van de Azure SignalR-service door de methode `services.AddSignalR().AddAzureSignalR()` aan te roepen:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```

    Als er geen para meter aan `AddAzureSignalR()`wordt door gegeven, gebruikt deze code de standaard configuratie sleutel voor de Signa lering Service Resource Connection String. De standaard configuratie sleutel is *Azure: signalr: Connections Tring*.

5. Werk in *Startup.cs*ook de `Configure` methode bij door de aanroep door te `app.UseStaticFiles()` vervangen door de volgende code en sla het bestand alleen op ASP.net Core 2.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            
    Vervang voor ASP.NET Core 3 + de bovenstaande code door het volgende:

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

In Signaler is een hub een kern onderdeel dat een set methoden beschrijft die van de client kan worden aangeroepen. In dit gedeelte gaat u een hub-klasse met twee methoden definiëren: 

* `Broadcast`: met deze methode verstuurt u een bericht naar alle clients.
* `Echo`: met deze methode stuurt u een bericht terug naar de aanroepende functie.

Bij beide methoden wordt `Clients` gebruikgemaakt van de interface die de ASP.net core signalr SDK biedt. Deze interface geeft u toegang tot alle verbonden clients, zodat u inhoud naar uw clients kunt pushen.

1. Voeg in de projectmap een nieuwe map toe met de naam *Hub*. Voeg een nieuw bestand met hub-code met de naam *Chat.cs* toe aan de nieuwe map.

2. Voeg de volgende code toe aan *Chat.cs* om uw hub-klasse te definiëren en het bestand op te slaan. 

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

### <a name="add-the-client-interface-for-the-web-app"></a>De client interface voor de web-app toevoegen

De gebruikers interface van de client voor deze chat room-app bestaat uit HTML en Java script in een bestand met de naam *index. html* in de map *wwwroot* .

Kopieer het bestand *index. html* , de *CSS* -map en de map *scripts* uit de map *wwwroot* van de [opslag plaats voor beelden](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Plak ze in de map *wwwroot* van uw project.

Hier volgt de hoofd code van *index. html*: 

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

De code in *index. html-* aanroepen `HubConnectionBuilder.build()` om een http-verbinding met de Azure signalerings resource tot stand te brengen.

Als de verbinding tot stand is gebracht, wordt de verbinding doorgegeven aan `bindConnectionMessage`. Deze functie voegt gebeurtenis-handlers toe om binnenkomende inhoud naar de client te pushen. 

`HubConnection.start()` start de communicatie met de hub. Vervolgens worden `onConnected()` de knop gebeurtenis-handlers toegevoegd. Deze handlers gebruiken de verbinding om deze client in staat te stellen updates van inhoud naar alle verbonden clients te pushen.

## <a name="add-a-development-runtime-profile"></a>Een profiel voor een runtime-ontwikkelomgeving toevoegen

In deze sectie voegt u een ontwikkel runtime-omgeving voor ASP.NET Core toe. Zie [werken met meerdere omgevingen in ASP.net core](https://docs.microsoft.com/aspnet/core/fundamentals/environments)voor meer informatie.

1. Maak een map met de naam *Eigenschappen* in uw project.

2. Voeg een nieuw bestand met de naam *launchSettings. json* toe aan de map, met de volgende inhoud en sla het bestand op.

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

1. Als u de app wilt bouwen met behulp van de .NET Core SLI, voert u de volgende opdracht uit in de opdracht shell:

        dotnet build

2. Nadat de build is voltooid, voert u de volgende opdracht uit om de web-app lokaal uit te voeren:

        dotnet run

    De app wordt lokaal gehost op poort 5000, zoals is geconfigureerd in het profiel voor ontwikkelings runtime:

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. Open twee browser vensters. Ga in elke browser naar `http://localhost:5000`. U wordt gevraagd om uw naam in te voeren. Voer een client naam in voor beide clients en test de inhoud van het push bericht tussen beide clients met behulp van de knop **verzenden** .

    ![Voor beeld van een Azure signalerings groep chat](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelf studie, kunt u de resources die u in deze Quick Start hebt gemaakt, behouden en ze opnieuw gebruiken.

Als u klaar bent met de Quick Start-voorbeeld toepassing, kunt u de Azure-resources die u in deze Quick Start hebt gemaakt, verwijderen om kosten te voor komen. 

> [!IMPORTANT]
> Het verwijderen van een resource groep is onomkeerbaar en bevat alle resources in die groep. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de resources hebt gemaakt voor het hosten van dit voor beeld in een bestaande resource groep die resources bevat die u wilt blijven gebruiken, kunt u elke resource afzonderlijk verwijderen van de Blade in plaats van de resource groep te verwijderen.
> 
> 

Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Resourcegroepen**.

Typ in het tekstvak **filteren op naam** de naam van de resource groep. In de instructies voor deze snelstart is een resourcegroep met de naam *SignalRTestResources* gebruikt. Selecteer in de lijst met resultaten van de resource groep het weglatings teken (**...**) > **resource groep verwijderen**.

   
![Selecties voor het verwijderen van een resource groep](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


U wordt gevraagd om het verwijderen van de resourcegroep te bevestigen. Voer de naam van de resource groep in om te bevestigen en selecteer **verwijderen**.
   
Na enkele ogenblikken worden de resourcegroep en alle bijbehorende resources verwijderd.



## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een nieuwe service resource van Azure signalering gemaakt. Vervolgens gebruikt u dit met een ASP.NET Core web-app om inhouds updates in realtime naar meerdere verbonden clients te pushen. Als u meer wilt weten over het gebruik van de Azure signalerings service, gaat u verder met de zelf studie die verificatie laat zien.

> [!div class="nextstepaction"]
> [verificatie van Azure SignalR Service](./signalr-concept-authenticate-oauth.md)


