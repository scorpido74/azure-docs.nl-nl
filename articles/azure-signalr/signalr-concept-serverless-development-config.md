---
title: Azure Functions app-Azure-Signaalr ontwikkelen & configureren
description: Meer informatie over het ontwikkelen en configureren van serverloze realtime-toepassingen met behulp van Azure Functions en de Azure signalerings service
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: f86a63315798d982f7e78fd1ff293061daf50132
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786772"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure Functions ontwikkeling en configuratie met de Azure signalerings service

Azure Functions toepassingen kunnen gebruikmaken van de [service bindingen van Azure signalering](../azure-functions/functions-bindings-signalr-service.md) om realtime-functies toe te voegen. Client toepassingen gebruiken client-Sdk's die beschikbaar zijn in verschillende talen om verbinding te maken met de Azure signalerings service en real-time berichten te ontvangen.

In dit artikel worden de concepten beschreven voor het ontwikkelen en configureren van een Azure function-app die is geïntegreerd met de signalerings service.

## <a name="signalr-service-configuration"></a>Signaal service configuratie

De Azure signalerings service kan in verschillende modi worden geconfigureerd. Bij gebruik in combi natie met Azure Functions moet de service worden geconfigureerd in de *serverloze* modus.

Ga in het Azure Portal naar de pagina *instellingen* van de bron van de signaal service. Stel de *Service modus* in op *serverloos*.

![Seingevings service-modus](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions ontwikkeling

Een serverloze realtime-toepassing die is gebouwd met Azure Functions en de Azure signalerings service vereist meestal twee Azure Functions:

* Een ' Negotiate '-functie die de client aanroept om een geldig toegangs token en service-eind punt-URL voor de signaal server te verkrijgen
* Een of meer functies die berichten verzenden of groepslid maatschap beheren

### <a name="negotiate-function"></a>functie onderhandelen

Een client toepassing vereist een geldig toegangs token om verbinding te maken met de Azure signalerings service. Een toegangs token kan anoniem zijn of worden geverifieerd op basis van een gebruikers-ID. Voor serverloze seingevings signalen is een HTTP-eind punt met de naam ' onderhandelen ' vereist voor het verkrijgen van een token en andere verbindings informatie, zoals de URL van het seingevings service-eind punt.

Gebruik een door HTTP geactiveerde Azure-functie en de *SignalRConnectionInfo* -invoer binding om het object verbindings gegevens te genereren. De functie moet een HTTP-route hebben die eindigt op `/negotiate`.

Zie voor meer informatie over het maken van de onderhandelings functie de referentie voor [ *SignalRConnectionInfo* -invoer binding](../azure-functions/functions-bindings-signalr-service.md#input).

Zie [app service-verificatie gebruiken](#using-app-service-authentication)voor meer informatie over het maken van een geverifieerd token.

### <a name="sending-messages-and-managing-group-membership"></a>Berichten verzenden en groepslid maatschap beheren

De *signaal* uitvoer binding gebruiken om berichten te verzenden naar clients die zijn verbonden met de Azure signalerings service. U kunt berichten uitzenden naar alle clients of u kunt ze verzenden naar een subset van clients die zijn geverifieerd met een specifieke gebruikers-ID of die is toegevoegd aan een specifieke groep.

Gebruikers kunnen worden toegevoegd aan een of meer groepen. U kunt ook de uitvoer binding van de *signaal sterkte* gebruiken om gebruikers toe te voegen aan of te verwijderen uit groepen.

Zie voor meer informatie de referentie voor de [ *signaal* uitvoer binding](../azure-functions/functions-bindings-signalr-service.md#output).

### <a name="signalr-hubs"></a>Seingevings hubs

Signa lering heeft een concept van ' hubs '. Elke client verbinding en elk bericht dat wordt verzonden vanuit Azure Functions, zijn gericht op een specifieke hub. U kunt hubs gebruiken als een manier om uw verbindingen en berichten te scheiden in logische naam ruimten.

## <a name="client-development"></a>Client ontwikkeling

Seingevings client toepassingen kunnen gebruikmaken van de seingevings client-SDK in een van de verschillende talen om eenvoudig verbinding te maken met en berichten te ontvangen van de Azure signalerings service.

### <a name="configuring-a-client-connection"></a>Een client verbinding configureren

Een client kan alleen verbinding maken met de signalerings service als een geslaagde verbindings onderhandeling bestaat uit de volgende stappen:

1. Een aanvraag indienen bij het *onderhandelings* -http-eind punt dat hierboven wordt beschreven om geldige verbindings gegevens op te halen
1. Verbinding maken met de signaal service via de URL van het service-eind punt en het toegangs token dat is verkregen van het *Negotiate* -eind punt

De client-Sdk's van de seingevings bevatten al de logica die nodig is om de onderhandelings-Handshake uit te voeren. Geef de URL van het onderhandelings eindpunt, min het `negotiate` segment, door aan de `HubConnectionBuilder`van de SDK. Hier volgt een voor beeld in Java script:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Per Conventie voegt de SDK automatisch `/negotiate` toe aan de URL en gebruikt deze om de onderhandeling te starten.

> [!NOTE]
> Als u de Java script-type script-SDK in een browser gebruikt, moet u de [functie Cross-Origin Resource Sharing (CORS) inschakelen](#enabling-cors) op uw functie-app.

Raadpleeg de documentatie voor uw taal voor meer informatie over het gebruik van de seingevings client-SDK:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Berichten verzenden van een client naar de service

Hoewel de Signa lering-SDK toestaat dat client toepassingen back-end-logica aanroepen in een Signalr hub, wordt deze functionaliteit nog niet ondersteund wanneer u de seingevings service gebruikt met Azure Functions. HTTP-aanvragen gebruiken om Azure Functions aan te roepen.

## <a name="azure-functions-configuration"></a>Azure Functions configuratie

Azure function-apps die zijn geïntegreerd met de Azure signalerings service kunnen worden geïmplementeerd zoals een typische Azure function-app, met technieken zoals continue [implementatie](../azure-functions/functions-continuous-deployment.md), [zip-implementatie](../azure-functions/deployment-zip-push.md)en [uitvoeren vanuit pakket](../azure-functions/run-functions-from-deployment-package.md).

Er zijn echter enkele speciale overwegingen voor apps die gebruikmaken van de signalerings service bindingen. Als de client wordt uitgevoerd in een browser, moet CORS zijn ingeschakeld. En als de app verificatie vereist, kunt u het Negotiate-eind punt integreren met App Service-verificatie.

### <a name="enabling-cors"></a>CORS inschakelen

De Java script-type script-client maakt HTTP-aanvragen aan de onderhandelings functie om de verbindings onderhandelingen te initiëren. Wanneer de client toepassing wordt gehost op een ander domein dan de Azure function-app, moet CORS (cross-Origin Resource Sharing) zijn ingeschakeld op de functie-app of worden de aanvragen geblokkeerd door de browser.

#### <a name="localhost"></a>Lokalehost

Wanneer u de functie-app op uw lokale computer uitvoert, kunt u een sectie `Host` toevoegen aan *lokaal. settings. json* om CORS in te scha kelen. Voeg in de sectie `Host` twee eigenschappen toe:

* `CORS`: Voer de basis-URL in die de oorsprong is van de client toepassing
* `CORSCredentials`: Stel dit in op `true` om "withCredentials"-aanvragen toe te staan

Voorbeeld:

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="cloud---azure-functions-cors"></a>CORS voor Cloud-Azure Functions

Als u CORS wilt inschakelen voor een Azure function-app, gaat u naar het configuratie scherm CORS op het tabblad *platform functies* van de functie-app in de Azure Portal.

> [!NOTE]
> CORS-configuratie is nog niet beschikbaar in Azure Functions Linux-verbruiks abonnement. Gebruik [Azure API Management](#cloud---azure-api-management) om CORS in te scha kelen.

CORS met Access-Control-Allow-referenties moet zijn ingeschakeld voor de seingevings client om de functie Negotiate aan te roepen. Schakel het selectie vakje in om dit in te scha kelen.

Voeg in de sectie *toegestane oorsprong* een vermelding toe met de basis-URL van de oorsprong van uw webtoepassing.

![CORS configureren](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Cloud-Azure-API Management

Azure API Management biedt een API-gateway die mogelijkheden toevoegt aan bestaande back-end-services. U kunt deze gebruiken om CORS toe te voegen aan uw functie-app. Het biedt een verbruiks laag met betalen per actie en een maandelijkse gratis toekenning.

Raadpleeg de API Management-documentatie voor informatie over het [importeren van een Azure-functie-app](../api-management/import-function-app-as-api.md). Nadat het is geïmporteerd, kunt u een inkomend beleid toevoegen om CORS in te scha kelen met de ondersteuning voor Access-Control-Allow-credentials.

```xml
<cors allow-credentials="true">
  <allowed-origins>
    <origin>https://azure-samples.github.io</origin>
  </allowed-origins>
  <allowed-methods>
    <method>GET</method>
    <method>POST</method>
  </allowed-methods>
  <allowed-headers>
    <header>*</header>
  </allowed-headers>
  <expose-headers>
    <header>*</header>
  </expose-headers>
</cors>
```

Configureer uw seingevings clients om de API Management URL te gebruiken.

### <a name="using-app-service-authentication"></a>App Service-verificatie gebruiken

Azure Functions heeft ingebouwde verificatie, die populaire providers ondersteunt, zoals Facebook, Twitter, micro soft-account, Google en Azure Active Directory. Deze functie kan worden geïntegreerd met de *SignalRConnectionInfo* -binding voor het maken van verbindingen met de Azure signalerings service die zijn geverifieerd voor een gebruikers-id. Uw toepassing kan berichten verzenden met behulp van de *signaal* uitvoer binding die is gericht op die gebruikers-id.

Open het venster *verificatie/autorisatie-* instellingen in het Azure Portal op het tabblad *platform functies* van uw functie-app. Volg de documentatie voor [app service verificatie](../app-service/overview-authentication-authorization.md) voor het configureren van verificatie met behulp van een id-provider van uw keuze.

Eenmaal geconfigureerd, bevatten geverifieerde HTTP-aanvragen `x-ms-client-principal-name` en `x-ms-client-principal-id` headers die respectievelijk de gebruikers naam en gebruikers-ID van de geverifieerde identiteit bevatten.

U kunt deze headers gebruiken in uw *SignalRConnectionInfo* bindings configuratie om geverifieerde verbindingen te maken. Hier volgt een voor C# beeld van een functie die gebruikmaakt van de `x-ms-client-principal-id`-header.

```csharp
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

U kunt vervolgens berichten naar die gebruiker verzenden door de eigenschap `UserId` van een signaal bericht in te stellen.

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

Zie voor informatie over andere talen de [Azure signalerings service bindingen](../azure-functions/functions-bindings-signalr-service.md) voor Azure functions referentie.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u serverloze signalerings service toepassingen kunt ontwikkelen en configureren met behulp van Azure Functions. Probeer zelf een toepassing te maken met behulp van een van de Quick starts of zelf studies op de [overzichts pagina van de signalerings service](index.yml).