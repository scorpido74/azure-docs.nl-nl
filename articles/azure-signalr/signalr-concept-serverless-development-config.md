---
title: Azure Functions-app ontwikkelen & configureren - Azure SignalR
description: Details over het ontwikkelen en configureren van serverloze realtime toepassingen met Azure-functies en Azure SignalR-service
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: e1157a695d34c75b237391427b37365421366ef8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523167"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Ontwikkeling en configuratie van Azure Functions met Azure SignalR Service

Azure Functions-toepassingen kunnen gebruikmaken van de [Azure SignalR Service-bindingen](../azure-functions/functions-bindings-signalr-service.md) om realtime mogelijkheden toe te voegen. Clienttoepassingen gebruiken client-SDK's die beschikbaar zijn in verschillende talen om verbinding te maken met Azure SignalR Service en realtime berichten te ontvangen.

In dit artikel worden de concepten beschreven voor het ontwikkelen en configureren van een Azure Function-app die is geïntegreerd met SignalR Service.

## <a name="signalr-service-configuration"></a>Configuratie van SignalR-service

Azure SignalR Service kan in verschillende modi worden geconfigureerd. Bij gebruik met Azure Functions moet de service zijn geconfigureerd in *de serverloze* modus.

Zoek in de Azure-portal de pagina *Instellingen* van uw SignalR Service-bron. Stel de *servicemodus* in op *Serverloos*.

![SignalR-servicemodus](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions-ontwikkeling

Een serverloze toepassing in realtime die is gebouwd met Azure Functions en Azure SignalR Service heeft over het algemeen twee Azure Functions nodig:

* Een functie 'negotiate' die de client aanroept voor een geldig SignalR Service-toegangstoken en service-eindpunt-URL
* Een of meer functies die berichten sturen of een groepslidmaatschap beheren

### <a name="negotiate-function"></a>onderhandelen functie

Een clienttoepassing vereist een geldig toegangstoken om verbinding te maken met Azure SignalR Service. Een toegangstoken kan anoniem zijn of worden geverifieerd voor een bepaalde gebruikersnaam. Serverless SignalR Service-toepassingen vereisen een HTTP-eindpunt met de naam "onderhandelen" om een token en andere verbindingsgegevens te verkrijgen, zoals de URL van het eindpunt van de SignalR-service.

Gebruik een HTTP-geactiveerde Azure-functie en de *inputbinding van SignalRConnectionInfo* om het object verbindingsinformatie te genereren. De functie moet een HTTP-route hebben die eindigt in `/negotiate`.

Zie de inputbinding van [ *SignalRConnectionInfo* ](../azure-functions/functions-bindings-signalr-service-input.md)voor meer informatie over het maken van de onderhandelingsfunctie.

Zie Verificatie [van appservice gebruiken](#using-app-service-authentication)voor meer informatie over het maken van een geverifieerd token.

### <a name="sending-messages-and-managing-group-membership"></a>Berichten verzenden en groepslidmaatschap beheren

Gebruik de *signaalbinding van SignalR* om berichten te verzenden naar clients die zijn verbonden met Azure SignalR Service. U berichten uitzenden naar alle clients, of u ze verzenden naar een subset van clients die zijn geverifieerd met een specifieke gebruikersnaam of zijn toegevoegd aan een specifieke groep.

Gebruikers kunnen worden toegevoegd aan een of meer groepen. U ook de *SignalR-uitvoerbinding* gebruiken om gebruikers toe te voegen of te verwijderen aan/uit groepen.

Zie voor meer informatie de [ *signaalbindende* referentie van SignalR output](../azure-functions/functions-bindings-signalr-service-output.md).

### <a name="signalr-hubs"></a>SignalR-hubs

SignalR heeft een concept van "hubs". Elke clientverbinding en elk bericht dat vanuit Azure-functies wordt verzonden, wordt naar een specifieke hub verzonden. U hubs gebruiken als een manier om uw verbindingen en berichten te scheiden in logische naamruimten.

## <a name="client-development"></a>Klantontwikkeling

SignalR-clienttoepassingen kunnen de SDK van de SignalR-client in een van de verschillende talen gebruiken om eenvoudig verbinding te maken met berichten van Azure SignalR Service en deze te ontvangen.

### <a name="configuring-a-client-connection"></a>Een clientverbinding configureren

Als u verbinding wilt maken met SignalR Service, moet een client een succesvolle verbindingsonderhandeling voltooien die uit de volgende stappen bestaat:

1. Een verzoek indienen bij *het* hierboven besproken HTTP-eindpunt om geldige verbindingsgegevens te verkrijgen
1. Verbinding maken met SignalR-service met behulp van de URL van het serviceeindpunt en het toegangstoken dat is verkregen uit het *onderhandelingseindpunt*

SDK's van de SignalR-client bevatten al de logica die nodig is om de onderhandelingshanddruk uit te voeren. Geef de URL van het onderhandelingseindpunt, minus het `negotiate` `HubConnectionBuilder`segment, door aan de SDK's. Hier is een voorbeeld in JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Op conventie voegt de SDK `/negotiate` automatisch aan de URL toe en gebruikt deze om de onderhandeling te starten.

> [!NOTE]
> Als u de JavaScript/TypeScript SDK in een browser gebruikt, moet u [cors (cross-origin resource sharing) inschakelen](#enabling-cors) in uw functie-app.

Raadpleeg de documentatie voor uw taal voor meer informatie over het gebruik van de SignalR client SDK:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [Javascript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Berichten van een client naar de service verzenden

Hoewel de SignalR SDK clienttoepassingen toestaat om backendlogica in een SignalR-hub aan te roepen, wordt deze functionaliteit nog niet ondersteund wanneer u SignalR-service met Azure-functies gebruikt. Gebruik HTTP-aanvragen om Azure-functies aan te roepen.

## <a name="azure-functions-configuration"></a>Configuratie van Azure-functies

Azure Function-apps die zijn geïntegreerd met Azure SignalR Service kunnen worden geïmplementeerd zoals elke typische Azure-functie-app, met behulp van technieken zoals [continue implementatie,](../azure-functions/functions-continuous-deployment.md) [zip-implementatie](../azure-functions/deployment-zip-push.md)en [uitvoeren vanaf pakket](../azure-functions/run-functions-from-deployment-package.md).

Er zijn echter een paar speciale overwegingen voor apps die gebruik maken van de SignalR Service bindingen. Als de client in een browser wordt uitgevoerd, moet CORS zijn ingeschakeld. En als de app verificatie vereist, u het onderhandelingseindpunt integreren met App Service Authentication.

### <a name="enabling-cors"></a>CorS inschakelen

De JavaScript/TypeScript-client voert HTTP-aanvragen uit voor de onderhandelingsfunctie om de verbindingsonderhandeling te starten. Wanneer de clienttoepassing wordt gehost op een ander domein dan de Azure Function-app, moet cross-origin resource sharing (CORS) zijn ingeschakeld in de functie-app of de browser de aanvragen blokkeert.

#### <a name="localhost"></a>Localhost

Wanneer u de functie-app uitvoert op `Host` uw lokale computer, u een sectie toevoegen aan *local.settings.json* om CORS in te schakelen. Voeg `Host` in de sectie twee eigenschappen toe:

* `CORS`- voer de basis-URL in die de oorsprong is van de clienttoepassing
* `CORSCredentials`- stel `true` het in om "withCredentials"-aanvragen toe te staan

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

#### <a name="cloud---azure-functions-cors"></a>Cloud - CorS voor Azure-functies

Als u CORS wilt inschakelen in een Azure Function-app, gaat u naar het CORS-configuratiescherm onder het tabblad *Platformfuncties* van uw functie-app in de Azure-portal.

> [!NOTE]
> CORS-configuratie is nog niet beschikbaar in het Linux-beheerplan voor Azure-functies. Gebruik [Azure API Management](#cloud---azure-api-management) om CORS in te schakelen.

CORS met Access-Control-Allow-Credentials moet ingeschakeld zijn voor de SignalR-client om de onderhandelingsfunctie aan te roepen. Schakel het selectievakje in om het in te schakelen.

Voeg in de sectie *Toegestane oorsprong* een vermelding toe met de URL van de oorsprongsbasis van uw webtoepassing.

![CORS configureren](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Cloud - Azure API-beheer

Azure API Management biedt een API-gateway die mogelijkheden toevoegt aan bestaande back-endservices. U het gebruiken om CORS toe te voegen aan uw functie-app. Het biedt een consumptielaag met pay-per-action-prijzen en een maandelijkse gratis subsidie.

Raadpleeg de API-beheerdocumentatie voor informatie over het [importeren van een Azure Function-app.](../api-management/import-function-app-as-api.md) Eenmaal geïmporteerd, u een binnenkomend beleid toevoegen om CORS in te schakelen met ondersteuning voor Access-Control-Allow-Credentials.

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

Configureer uw SignalR-clients om de URL apibeheer te gebruiken.

### <a name="using-app-service-authentication"></a>Verificatie van app-service gebruiken

Azure Functions heeft ingebouwde verificatie en ondersteunt populaire providers zoals Facebook, Twitter, Microsoft-account, Google en Azure Active Directory. Deze functie kan worden geïntegreerd met de *Binding SignalRConnectionInfo* om verbindingen met Azure SignalR-service te maken die zijn geverifieerd naar een gebruikers-id. Uw toepassing kan berichten verzenden met behulp van de *SignalR-uitvoerbinding* die zijn gericht op die gebruikersnaam.

Open in de Azure-portal op het tabblad *Platformfuncties* van uw functie-app het venster *Verificatie/autorisatie-instellingen.* Volg de documentatie voor [Verificatie van appservice](../app-service/overview-authentication-authorization.md) om verificatie te configureren met behulp van een identiteitsprovider naar keuze.

Na configuratie bevatten `x-ms-client-principal-name` geverifieerde HTTP-aanvragen `x-ms-client-principal-id` respectievelijk kopteksten met respectievelijk de gebruikersnaam en gebruikersnaam van de geverifieerde identiteit.

U deze koppen gebruiken in uw *bindingsconfiguratie van SignalRConnectionInfo* om geverifieerde verbindingen te maken. Hier is een voorbeeld C# `x-ms-client-principal-id` onderhandelen functie die de header gebruikt.

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

U vervolgens berichten naar die `UserId` gebruiker verzenden door de eigenschap van een SignalR-bericht in te stellen.

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

Zie de azure [SignalR Service-bindingen](../azure-functions/functions-bindings-signalr-service.md) voor Azure-functies voor informatie over andere talen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u serverloze SignalR Service-toepassingen ontwikkelen en configureren met Azure-functies. Probeer zelf een toepassing te maken met een van de snelle starts of zelfstudies op de [overzichtspagina van de SignalR Service.](index.yml)