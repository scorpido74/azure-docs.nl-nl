---
title: Service bindingen van Azure Functions signalering
description: Meer informatie over het gebruik van seingevings service bindingen met Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
editor: ''
tags: ''
keywords: Azure functions, functies, gebeurtenisverwerking, dynamische Computing, serverloze architectuur
ms.service: azure-functions
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 82bbd728bfd51595f0b6eacf344bc95ea5db1ea8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086163"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>SignalR-servicebindingen voor Azure Functions

In dit artikel wordt uitgelegd hoe u real-time berichten kunt verifiëren en verzenden naar clients die zijn verbonden met de [Azure signalerings service](https://azure.microsoft.com/services/signalr-service/) door gebruik te maken van de service bindingen van de seingevings modus in azure functions. Azure Functions ondersteunt invoer-en uitvoer bindingen voor de signalerings service.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pakketten - functies 2.x

De signalerings service bindingen worden gegeven in het [micro soft. Azure. webjobs. Extensions. SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) NuGet-pakket, versie 1. *. De bron code voor het pakket bevindt zich in de [Azure-functions-signalrservice-extension github-](https://github.com/Azure/azure-functions-signalrservice-extension) opslag plaats.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Java-aantekeningen

Als u de aantekeningen van de seingevings service in Java-functies wilt gebruiken, moet u een afhankelijkheid toevoegen aan de *Azure-functions-Java-Library-seingevings-* artefact (versie 1,0 of hoger) aan uw pom. XML.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> Als u de signalerings service bindingen in Java wilt gebruiken, moet u versie 2.4.419 of hoger van de Azure Functions Core Tools (host versie 2.0.12332) gebruiken.

## <a name="using-signalr-service-with-azure-functions"></a>De Signa lering-service gebruiken met Azure Functions

Raadpleeg [Azure functions ontwikkeling en configuratie met de Azure signalerings service](../azure-signalr/signalr-concept-serverless-development-config.md)voor meer informatie over het configureren en gebruiken van de seingevings service en Azure functions samen.

## <a name="signalr-connection-info-input-binding"></a>Invoer binding voor signaal verbindings gegevens

Voordat een client verbinding kan maken met de Azure signalerings service, moet deze de URL van het service-eind punt en een geldig toegangs Token ophalen. De *SignalRConnectionInfo* -invoer binding produceert de eind punt-URL van de seingevings service en een geldig token dat wordt gebruikt om verbinding te maken met de service. Omdat het token een beperkte tijd is en kan worden gebruikt om een specifieke gebruiker te verifiëren bij een verbinding, moet u het token niet in de cache opslaan of delen tussen clients. Een HTTP-trigger die gebruikmaakt van deze binding, kan door clients worden gebruikt om de verbindings gegevens op te halen.

Zie het voorbeeld taalspecifieke:

* [2.x C#](#2x-c-input-examples)
* [2. x java script](#2x-javascript-input-examples)
* [2. x java](#2x-java-input-examples)

Zie voor meer informatie over hoe deze binding wordt gebruikt voor het maken van een "Negotiate"-functie die kan worden verbruikt door een seingevings client-SDK het [artikel Azure functions ontwikkeling en configuratie](../azure-signalr/signalr-concept-serverless-development-config.md) in de documentatie over de concepten van de signalerings service.

### <a name="2x-c-input-examples"></a>2. x C# invoer voorbeelden

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die signaal verbindings gegevens ophaalt met behulp van de invoer binding en retourneert deze over http.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Geverifieerde tokens

Als de functie wordt geactiveerd door een geverifieerde client, kunt u een claim voor een gebruikers-ID toevoegen aan het gegenereerde token. U kunt eenvoudig verificatie toevoegen aan een functie-app met behulp van [app service-verificatie](../app-service/overview-authentication-authorization.md).

App service-verificatie sets http- `x-ms-client-principal-id` headers `x-ms-client-principal-name` met de naam en die respectievelijk de client-Principal-id en-naam van de geverifieerde gebruiker bevatten. U kunt de `UserId` eigenschap van de binding met de waarde uit een van beide kopteksten instellen met behulp `{headers.x-ms-client-principal-name}`van een bindings [expressie](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` of. 

```cs
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

### <a name="2x-javascript-input-examples"></a>2. x java script-invoer voorbeelden

In het volgende voor beeld ziet u een ingangs verbindings gegevens invoer binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding om de verbindings gegevens op te halen.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

Voor beeld van function. json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Dit is de JavaScript-code:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

#### <a name="authenticated-tokens"></a>Geverifieerde tokens

Als de functie wordt geactiveerd door een geverifieerde client, kunt u een claim voor een gebruikers-ID toevoegen aan het gegenereerde token. U kunt eenvoudig verificatie toevoegen aan een functie-app met behulp van [app service-verificatie](../app-service/overview-authentication-authorization.md).

App service-verificatie sets http- `x-ms-client-principal-id` headers `x-ms-client-principal-name` met de naam en die respectievelijk de client-Principal-id en-naam van de geverifieerde gebruiker bevatten. U kunt de `userId` eigenschap van de binding met de waarde uit een van beide kopteksten instellen met behulp `{headers.x-ms-client-principal-name}`van een bindings [expressie](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` of. 

Voor beeld van function. json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Dit is de JavaScript-code:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

### <a name="2x-java-input-examples"></a>2. x Java-invoer voorbeelden

In het volgende voor beeld ziet u een [Java-functie](functions-reference-java.md) die signaal verbindings gegevens ophaalt met behulp van de invoer binding en retourneert deze over http.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Geverifieerde tokens

Als de functie wordt geactiveerd door een geverifieerde client, kunt u een claim voor een gebruikers-ID toevoegen aan het gegenereerde token. U kunt eenvoudig verificatie toevoegen aan een functie-app met behulp van [app service-verificatie](../app-service/overview-authentication-authorization.md).

App service-verificatie sets http- `x-ms-client-principal-id` headers `x-ms-client-principal-name` met de naam en die respectievelijk de client-Principal-id en-naam van de geverifieerde gebruiker bevatten. U kunt de `UserId` eigenschap van de binding met de waarde uit een van beide kopteksten instellen met behulp `{headers.x-ms-client-principal-name}`van een bindings [expressie](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` of.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

## <a name="signalr-output-binding"></a>Signaal uitvoer binding

De *signaal* uitvoer binding gebruiken om een of meer berichten te verzenden met behulp van de Azure signalerings service. U kunt een bericht uitzenden naar alle verbonden clients of u kunt het alleen uitzenden naar verbonden clients die zijn geverifieerd voor een bepaalde gebruiker.

U kunt dit ook gebruiken voor het beheren van de groepen waartoe een gebruiker behoort.

Zie het voorbeeld taalspecifieke:

* [2.x C#](#2x-c-send-message-output-examples)
* [2. x java script](#2x-javascript-send-message-output-examples)
* [2. x java](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>2. x C# uitvoer-voor beelden van berichten verzenden

#### <a name="broadcast-to-all-clients"></a>Broadcasten naar alle clients

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die een bericht verzendt met behulp van de uitvoer binding naar alle verbonden clients. De `Target` is de naam van de methode die op elke client moet worden aangeroepen. De `Arguments` eigenschap is een matrix met nul of meer objecten die moeten worden door gegeven aan de client methode.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

#### <a name="send-to-a-user"></a>Verzenden naar een gebruiker

U kunt alleen een bericht verzenden naar verbindingen die zijn geverifieerd voor een gebruiker door de `UserId` eigenschap van het signaal bericht in te stellen.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

#### <a name="send-to-a-group"></a>Naar een groep verzenden

U kunt alleen een bericht verzenden naar verbindingen die zijn toegevoegd aan een groep door de `GroupName` eigenschap van het signaal bericht in te stellen.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

### <a name="2x-c-group-management-output-examples"></a>2. x C# -uitvoer voorbeelden voor groeps beheer

Met de seingevings service kunnen gebruikers worden toegevoegd aan groepen. Berichten kunnen vervolgens naar een groep worden verzonden. U kunt de `SignalRGroupAction` klasse met de `SignalR` uitvoer binding gebruiken om het groepslid maatschap van een gebruiker te beheren.

#### <a name="add-user-to-a-group"></a>Gebruiker toevoegen aan een groep

In het volgende voor beeld wordt een gebruiker toegevoegd aan een groep.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>Gebruiker verwijderen uit een groep

In het volgende voor beeld wordt een gebruiker verwijderd uit een groep.

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> Als u de `ClaimsPrincipal` juiste binding wilt krijgen, moet u de verificatie-instellingen hebben geconfigureerd in azure functions.

### <a name="2x-javascript-send-message-output-examples"></a>2. x java script uitvoer voorbeelden van berichten verzenden

#### <a name="broadcast-to-all-clients"></a>Broadcasten naar alle clients

In het volgende voor beeld ziet u een signaal uitvoer binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding om een bericht te verzenden met de Azure signalerings service. Stel de uitvoer binding in op een matrix van een of meer signaal berichten. Een signaal bericht bestaat uit een `target` eigenschap die de naam specificeert van de methode die moet worden aangeroepen op elke client, en een `arguments` eigenschap die een matrix is met objecten die worden door gegeven aan de client methode als argumenten.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

Voor beeld van function. json:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Dit is de JavaScript-code:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-user"></a>Verzenden naar een gebruiker

U kunt alleen een bericht verzenden naar verbindingen die zijn geverifieerd voor een gebruiker door de `userId` eigenschap van het signaal bericht in te stellen.

*Function. json* blijft hetzelfde. Dit is de JavaScript-code:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-group"></a>Naar een groep verzenden

U kunt alleen een bericht verzenden naar verbindingen die zijn toegevoegd aan een groep door de `groupName` eigenschap van het signaal bericht in te stellen.

*Function. json* blijft hetzelfde. Dit is de JavaScript-code:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

### <a name="2x-javascript-group-management-output-examples"></a>2. x java script-uitvoer voorbeelden voor groeps beheer

Met de seingevings service kunnen gebruikers worden toegevoegd aan groepen. Berichten kunnen vervolgens naar een groep worden verzonden. U kunt de `SignalR` uitvoer binding gebruiken om het groepslid maatschap van een gebruiker te beheren.

#### <a name="add-user-to-a-group"></a>Gebruiker toevoegen aan een groep

In het volgende voor beeld wordt een gebruiker toegevoegd aan een groep.

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

#### <a name="remove-user-from-a-group"></a>Gebruiker verwijderen uit een groep

In het volgende voor beeld wordt een gebruiker verwijderd uit een groep.

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

### <a name="2x-java-send-message-output-examples"></a>2. x Java-uitvoer voorbeeld berichten verzenden

#### <a name="broadcast-to-all-clients"></a>Broadcasten naar alle clients

In het volgende voor beeld ziet u een [Java-functie](functions-reference-java.md) die een bericht verzendt met behulp van de uitvoer binding naar alle verbonden clients. De `target` is de naam van de methode die op elke client moet worden aangeroepen. De `arguments` eigenschap is een matrix met nul of meer objecten die moeten worden door gegeven aan de client methode.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-user"></a>Verzenden naar een gebruiker

U kunt alleen een bericht verzenden naar verbindingen die zijn geverifieerd voor een gebruiker door de `userId` eigenschap van het signaal bericht in te stellen.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-group"></a>Naar een groep verzenden

U kunt alleen een bericht verzenden naar verbindingen die zijn toegevoegd aan een groep door de `groupName` eigenschap van het signaal bericht in te stellen.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

### <a name="2x-java-group-management-output-examples"></a>2. x Java Group Management uitvoer-voor beelden

Met de seingevings service kunnen gebruikers worden toegevoegd aan groepen. Berichten kunnen vervolgens naar een groep worden verzonden. U kunt de `SignalRGroupAction` klasse met de `SignalROutput` uitvoer binding gebruiken om het groepslid maatschap van een gebruiker te beheren.

#### <a name="add-user-to-a-group"></a>Gebruiker toevoegen aan een groep

In het volgende voor beeld wordt een gebruiker toegevoegd aan een groep.

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

#### <a name="remove-user-from-a-group"></a>Gebruiker verwijderen uit een groep

In het volgende voor beeld wordt een gebruiker verwijderd uit een groep.

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

## <a name="configuration"></a>Configuratie

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

De volgende tabel beschrijft de binding configuratie-eigenschappen die u instelt in de *function.json* bestand en de `SignalRConnectionInfo` kenmerk.

|de eigenschap Function.JSON | De kenmerkeigenschap |Description|
|---------|---------|----------------------|
|**type**|| Moet worden ingesteld op `signalRConnectionInfo`.|
|**direction**|| Moet worden ingesteld op `in`.|
|**name**|| De naam van de variabele die wordt gebruikt in de functie code voor het verbindings info-object. |
|**hubName**|**HubName**| Deze waarde moet worden ingesteld op de naam van de seingevings hub waarvoor de verbindings gegevens worden gegenereerd.|
|**userId**|**Naam**| Optioneel: De waarde van de claim voor de gebruikers-id die moet worden ingesteld in het toegangs sleutel token. |
|**connectionStringSetting**|**connectionStringSetting**| De naam van de app-instelling die de seingevings service connection string bevat (standaard ingesteld op ' AzureSignalRConnectionString ') |

### <a name="signalr"></a>SignalR

De volgende tabel beschrijft de binding configuratie-eigenschappen die u instelt in de *function.json* bestand en de `SignalR` kenmerk.

|de eigenschap Function.JSON | De kenmerkeigenschap |Description|
|---------|---------|----------------------|
|**type**|| Moet worden ingesteld op `signalR`.|
|**direction**|| Moet worden ingesteld op `out`.|
|**name**|| De naam van de variabele die wordt gebruikt in de functie code voor het verbindings info-object. |
|**hubName**|**HubName**| Deze waarde moet worden ingesteld op de naam van de seingevings hub waarvoor de verbindings gegevens worden gegenereerd.|
|**connectionStringSetting**|**connectionStringSetting**| De naam van de app-instelling die de seingevings service connection string bevat (standaard ingesteld op ' AzureSignalRConnectionString ') |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en bindingen](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Ontwikkeling en configuratie van Azure Functions met Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)
