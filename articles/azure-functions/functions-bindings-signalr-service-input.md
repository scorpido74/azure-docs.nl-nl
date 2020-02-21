---
title: Invoer binding van de Azure Functions signalerings service
description: Meer informatie over het retour neren van de eind punt-URL en toegangs token van de seingevings service in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 53d336aff3177a76c5e02266ffb8484bd9945119
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77530261"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>Invoer binding van de signalerings service voor Azure Functions

Voordat een client verbinding kan maken met de Azure signalerings service, moet deze de URL van het service-eind punt en een geldig toegangs Token ophalen. De *SignalRConnectionInfo* -invoer binding produceert de eind punt-URL van de seingevings service en een geldig token dat wordt gebruikt om verbinding te maken met de service. Omdat het token een beperkte tijd is en kan worden gebruikt om een specifieke gebruiker te verifiëren bij een verbinding, moet u het token niet in de cache opslaan of delen tussen clients. Een HTTP-trigger die gebruikmaakt van deze binding, kan door clients worden gebruikt om de verbindings gegevens op te halen.

Zie voor meer informatie over hoe deze binding wordt gebruikt voor het maken van een "Negotiate"-functie die kan worden verbruikt door een seingevings client-SDK het [artikel Azure functions ontwikkeling en configuratie](../azure-signalr/signalr-concept-serverless-development-config.md) in de documentatie over de concepten van de signalerings service.

Zie het [overzicht](functions-bindings-signalr-service.md)voor meer informatie over de installatie-en configuratie details.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

In het volgende voor beeld ziet u een ingangs verbindings gegevens invoer binding in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding om de verbindings gegevens te retour neren.

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

Dit is de C# script code:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld ziet u een ingangs verbindings gegevens invoer binding in een *Function. json* -bestand en een [python-functie](functions-reference-python.md) die gebruikmaakt van de binding om de verbindings gegevens te retour neren.

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

Dit is de python-code:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

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

---

## <a name="authenticated-tokens"></a>Geverifieerde tokens

Als de functie wordt geactiveerd door een geverifieerde client, kunt u een claim voor een gebruikers-ID toevoegen aan het gegenereerde token. U kunt eenvoudig verificatie toevoegen aan een functie-app met behulp van [app service-verificatie](../app-service/overview-authentication-authorization.md).

App Service-verificatie sets HTTP-headers met de naam `x-ms-client-principal-id` en `x-ms-client-principal-name` die respectievelijk de client-Principal-ID en-naam van de geverifieerde gebruiker bevatten.

# <a name="c"></a>[C#](#tab/csharp)

U kunt de eigenschap `UserId` van de binding met de waarde uit een van beide kopteksten instellen met behulp van een [bindings expressie](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` of `{headers.x-ms-client-principal-name}`.

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

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

U kunt de eigenschap `userId` van de binding met de waarde uit een van beide kopteksten instellen met behulp van een [bindings expressie](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` of `{headers.x-ms-client-principal-name}`.

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

Dit is de C# script code:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

U kunt de eigenschap `userId` van de binding met de waarde uit een van beide kopteksten instellen met behulp van een [bindings expressie](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` of `{headers.x-ms-client-principal-name}`.

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

# <a name="python"></a>[Python](#tab/python)

U kunt de eigenschap `userId` van de binding met de waarde uit een van beide kopteksten instellen met behulp van een [bindings expressie](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` of `{headers.x-ms-client-principal-name}`.

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

Dit is de python-code:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

U kunt de eigenschap `userId` van de binding met de waarde uit een van beide kopteksten instellen met behulp van een [bindings expressie](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` of `{headers.x-ms-client-principal-name}`.

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

---

## <a name="next-steps"></a>Volgende stappen

- [Signaal service berichten verzenden (uitvoer binding)](./functions-bindings-signalr-service-output.md) 
