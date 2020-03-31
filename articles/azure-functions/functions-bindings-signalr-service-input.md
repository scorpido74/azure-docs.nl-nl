---
title: Invoerbinding van Azure Functions SignalR Service
description: Lees hier de URL van een SignalR-service-eindpunt en toegangstoken in Azure-functies.
author: craigshoemaker
ms.topic: reference
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 53d336aff3177a76c5e02266ffb8484bd9945119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77530261"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>SignalR-serviceinvoerbinding voor Azure-functies

Voordat een client verbinding kan maken met Azure SignalR Service, moet deze de URL van het serviceeindpunt en een geldig toegangstoken ophalen. De *inputbinding van SignalRConnectionInfo* produceert de URL van het eindpunt van de SignalR-service en een geldig token dat wordt gebruikt om verbinding te maken met de service. Omdat het token beperkt is in de tijd en kan worden gebruikt om een specifieke gebruiker te verifiëren voor een verbinding, moet u het token niet in de cache opslaan of delen tussen clients. Een HTTP-trigger met deze binding kan door clients worden gebruikt om de verbindingsgegevens op te halen.

Zie het artikel over de [ontwikkeling en configuratie](../azure-signalr/signalr-concept-serverless-development-config.md) van Azure Functions in de documentatie van de SignalR Service-concepten voor meer informatie over hoe deze binding wordt gebruikt om een onderhandelingsfunctie te maken die kan worden verbruikt door een SDK van de SignalR-client.

Zie het [overzicht](functions-bindings-signalr-service.md)voor informatie over de installatie en configuratiedetails.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C #](#tab/csharp)

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die signalr-verbindingsgegevens verwerft met behulp van de invoerbinding en deze retourneert via HTTP.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld wordt een signalr-verbindingsgegevensbinding weergegeven in een *function.json-bestand* en een [C#Script-functie](functions-reference-csharp.md) die de binding gebruikt om de verbindingsgegevens terug te sturen.

Hier vindt u bindende gegevens in het *bestand function.json:*

Voorbeeld functie.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Hier is de C# Script code:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld wordt een Input voor de SignalR-verbindingsinformatie weergegeven in een *function.json-bestand* en een [JavaScript-functie](functions-reference-node.md) die de binding gebruikt om de verbindingsgegevens terug te sturen.

Hier vindt u bindende gegevens in het *bestand function.json:*

Voorbeeld functie.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Hier is de JavaScript-code:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voorbeeld wordt een SignalR-verbindingsgegevensbinding weergegeven in een *function.json-bestand* en een [Python-functie](functions-reference-python.md) die de binding gebruikt om de verbindingsgegevens terug te sturen.

Hier vindt u bindende gegevens in het *bestand function.json:*

Voorbeeld functie.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Hier is de Python-code:

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

In het volgende voorbeeld wordt een [Java-functie](functions-reference-java.md) weergegeven die signalr-verbindingsgegevens verwerft met behulp van de invoerbinding en deze retourneert via HTTP.

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

Als de functie wordt geactiveerd door een geverifieerde client, u een claim voor gebruikersnaam toevoegen aan het gegenereerde token. U eenvoudig verificatie toevoegen aan een functie-app met [app-serviceverificatie.](../app-service/overview-authentication-authorization.md)

App Service Authentication stelt `x-ms-client-principal-id` respectievelijk `x-ms-client-principal-name` DE NAAM EN de clientprincipal-id en naam van de geverifieerde gebruiker in.

# <a name="c"></a>[C #](#tab/csharp)

U `UserId` de eigenschap van de binding instellen op de `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`waarde van een koptekst met behulp van een [bindende expressie](./functions-bindings-expressions-patterns.md): of.

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

U `userId` de eigenschap van de binding instellen op de `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`waarde van een koptekst met behulp van een [bindende expressie](./functions-bindings-expressions-patterns.md): of.

Voorbeeld functie.json:

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

Hier is de C# Script code:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

U `userId` de eigenschap van de binding instellen op de `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`waarde van een koptekst met behulp van een [bindende expressie](./functions-bindings-expressions-patterns.md): of.

Voorbeeld functie.json:

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

Hier is de JavaScript-code:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

U `userId` de eigenschap van de binding instellen op de `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`waarde van een koptekst met behulp van een [bindende expressie](./functions-bindings-expressions-patterns.md): of.

Voorbeeld functie.json:

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

Hier is de Python-code:

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

U `userId` de eigenschap van de binding instellen op de `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`waarde van een koptekst met behulp van een [bindende expressie](./functions-bindings-expressions-patterns.md): of.

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

- [Berichten van SignalR-service verzenden (uitvoerbinding)](./functions-bindings-signalr-service-output.md) 
