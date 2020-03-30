---
title: HTTP-trigger voor Azure-functies
description: Meer informatie over het aanroepen van een Azure-functie via HTTP.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 045f3ccdc8dc09bf657ab39ce15a0d0524c73fcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277594"
---
# <a name="azure-functions-http-trigger"></a>HTTP-trigger voor Azure-functies

Met de HTTP-trigger u een functie aanroepen met een HTTP-aanvraag. U een HTTP-trigger gebruiken om serverloze API's te bouwen en te reageren op webhooks.

De standaardretourwaarde voor een http-geactiveerde functie is:

- `HTTP 204 No Content`met een leeg lichaam in functies 2.x en hoger
- `HTTP 200 OK`met een lege behuizing in functies 1.x

Als u het HTTP-antwoord wilt wijzigen, configureert u een [uitvoerbinding](./functions-bindings-http-webhook-output.md).

Zie het [overzicht](./functions-bindings-http-webhook.md) en de [uitvoerbinding](./functions-bindings-http-webhook-output.md)voor meer informatie over HTTP-bindingen .

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C #](#tab/csharp)

In het volgende voorbeeld wordt een `name` [C#-functie](functions-dotnet-class-library.md) weergegeven die zoekt naar een parameter in de querytekenreeks of de hoofdtekst van de HTTP-aanvraag. De retourwaarde wordt gebruikt voor de uitvoerbinding, maar een toekenmerk retourwaarde is niet vereist.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] 
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld wordt een triggerbinding weergegeven in een *function.json-bestand* en een [C#-scriptfunctie](functions-reference-csharp.md) die de binding gebruikt. De functie zoekt `name` naar een parameter in de querytekenreeks of de hoofdtekst van de HTTP-aanvraag.

Hier is het *function.json* bestand:

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is C# scriptcode die `HttpRequest`zich bindt aan:

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

U binden aan een `HttpRequest`aangepast object in plaats van. Dit object wordt gemaakt van de hoofdtekst van de aanvraag en ontleed als JSON. Op dezelfde manier kan een type worden doorgegeven aan de HTTP-antwoorduitvoerbinding en worden geretourneerd als de antwoordinstantie, samen met een `200` statuscode.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld wordt een triggerbinding weergegeven in een *function.json-bestand* en een [JavaScript-functie](functions-reference-node.md) die de binding gebruikt. De functie zoekt `name` naar een parameter in de querytekenreeks of de hoofdtekst van de HTTP-aanvraag.

Hier is het *function.json* bestand:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is de JavaScript-code:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voorbeeld wordt een triggerbinding weergegeven in een *function.json-bestand* en een [Python-functie](functions-reference-python.md) die de binding gebruikt. De functie zoekt `name` naar een parameter in de querytekenreeks of de hoofdtekst van de HTTP-aanvraag.

Hier is het *function.json* bestand:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is de Python-code:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

# <a name="java"></a>[Java](#tab/java)

* [Parameter lezen uit de querytekenreeks](#read-parameter-from-the-query-string)
* [Lees de body van een POST-verzoek](#read-body-from-a-post-request)
* [Parameter van een route lezen](#read-parameter-from-a-route)
* [Lees POJO body van een POST verzoek](#read-pojo-body-from-a-post-request)

In de volgende voorbeelden wordt de HTTP-triggerbinding weergegeven.

#### <a name="read-parameter-from-the-query-string"></a>Parameter lezen uit de querytekenreeks

In dit voorbeeld wordt `id`een parameter met de naam van de querytekenreeks gelezen en wordt `application/json`deze gebruikt om een JSON-document te maken dat naar de client is geretourneerd, met inhoudstype .

```java
@FunctionName("TriggerStringGet")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<String>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("GET parameters are: " + request.getQueryParameters());

    // Get named parameter
    String id = request.getQueryParameters().getOrDefault("id", "");

    // Convert and display
    if (id.isEmpty()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String name = "fake_name";
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-body-from-a-post-request"></a>Lees de body van een POST-verzoek

In dit voorbeeld wordt de hoofdtekst `String`van een POST-aanvraag als een , wordt gelezen `application/json`en wordt gebruikt om een JSON-document te bouwen dat naar de client is geretourneerd, met inhoudstype .

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route"></a>Parameter van een route lezen

In dit voorbeeld wordt `id`een verplichte parameter `name` met de naam en een optionele parameter uit het routepad `application/json`gelezen en wordt deze gebruikt om een JSON-document te maken dat naar de client is geretourneerd, met inhoudstype . T

```java
@FunctionName("TriggerStringRoute")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS,
            route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
        HttpRequestMessage<Optional<String>> request,
        @BindingName("id") String id,
        @BindingName("name") String name,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Route parameters are: " + id);

    // Convert and display
    if (id == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-pojo-body-from-a-post-request"></a>Lees POJO body van een POST verzoek

Hier is de `ToDoItem` code voor de klasse, waarnaar in dit voorbeeld wordt verwezen:

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

Dit voorbeeld leest de inhoud van een POST-verzoek. De aanvraaginstantie wordt automatisch gedeserialiseerd in een `ToDoItem` object en wordt `application/json`teruggestuurd naar de client, met inhoudstype . De `ToDoItem` parameter wordt geserialiseerd door de runtime `body` van `HttpMessageResponse.Builder` functies, omdat deze is toegewezen aan de eigenschap van de klasse.

```java
@FunctionName("TriggerPojoPost")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.POST}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<ToDoItem>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Request body is: " + request.getBody().orElse(null));

    // Check request body
    if (!request.getBody().isPresent()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final ToDoItem body = request.getBody().get();
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(body)
                        .build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en annotaties

In [c#-klassebibliotheken](functions-dotnet-class-library.md) en `HttpTrigger` Java is het kenmerk beschikbaar om de functie te configureren.

U het autorisatieniveau en de toegestane HTTP-methoden instellen in kenmerkconstructorparameters, webhooktype en een routesjabloon. Zie [Configuratie](#configuration)voor meer informatie over deze instellingen.

# <a name="c"></a>[C #](#tab/csharp)

In dit voorbeeld wordt uitgelegd hoe u het kenmerk [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) gebruikt.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Zie het [voorbeeld van](#example)de trigger voor een volledig voorbeeld .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

In dit voorbeeld wordt uitgelegd hoe u het kenmerk [HttpTrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java) gebruikt.

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

Zie het [voorbeeld van](#example)de trigger voor een volledig voorbeeld .

---

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `HttpTrigger` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
| **Type** | N.v.t.| Vereist - moet `httpTrigger`worden ingesteld op . |
| **direction** | N.v.t.| Vereist - moet `in`worden ingesteld op . |
| **Naam** | N.v.t.| Vereist - de variabele naam die wordt gebruikt in de functiecode voor de aanvraag- of aanvraaginstantie. |
| <a name="http-auth"></a>**authLevel authLevel** |  **AuthLevel (AuthLevel)** |Hiermee bepaalt u welke toetsen mogelijk aanwezig moeten zijn op de aanvraag om de functie aan te roepen. Het autorisatieniveau kan een van de volgende waarden zijn: <ul><li><code>anonymous</code>&mdash;Er is geen API-sleutel vereist.</li><li><code>function</code>&mdash;Er is een functiespecifieke API-sleutel vereist. Dit is de standaardwaarde als er geen wordt opgegeven.</li><li><code>admin</code>&mdash;De hoofdsleutel is vereist.</li></ul> Zie de sectie over [autorisatiesleutels](#authorization-keys)voor meer informatie. |
| **Methoden** |**Methoden** | Een array van de HTTP-methoden waarop de functie reageert. Als dit niet is opgegeven, reageert de functie op alle HTTP-methoden. Zie [het HTTP-eindpunt aanpassen](#customize-the-http-endpoint). |
| **Route** | **Route** | Hiermee definieert u de routesjabloon en bepaalt u op welke aanvraag-URL's uw functie reageert. De standaardwaarde als er `<functionname>`geen wordt opgegeven, is . Zie [het HTTP-eindpunt aanpassen](#customize-the-http-endpoint)voor meer informatie. |
| **webHookType** | **WebHookType** | _Alleen ondersteund voor de uitvoering 1.x runtime._<br/><br/>Hiermee configureert u de HTTP-trigger om te fungeren als [een webhook-ontvanger](https://en.wikipedia.org/wiki/Webhook) voor de opgegeven provider. Stel de `methods` accommodatie niet in als u deze accommodatie instelt. Het webhooktype kan een van de volgende waarden zijn:<ul><li><code>genericJson</code>&mdash;Een webhook-eindpunt voor algemene doeleinden zonder logica voor een specifieke provider. Met deze instelling worden aanvragen beperkt tot `application/json` alleen aanvragen die HTTP POST gebruiken en met het inhoudstype.</li><li><code>github</code>&mdash;De functie reageert op [GitHub webhooks](https://developer.github.com/webhooks/). Gebruik de eigenschap _authLevel_ niet met GitHub webhooks. Zie de sectie GitHub webhooks later in dit artikel voor meer informatie.</li><li><code>slack</code>&mdash;De functie reageert op [Slack webhooks](https://api.slack.com/outgoing-webhooks). Gebruik de eigenschap _authLevel_ niet met Slack-webhooks. Zie de sectie Slack webhooks later in dit artikel voor meer informatie.</li></ul>|

## <a name="payload"></a>Nettolading

Het invoertype trigger wordt `HttpRequest` gedeclareerd als een of een aangepast type. Als u `HttpRequest`kiest, krijgt u volledige toegang tot het aanvraagobject. Voor een aangepast type probeert de runtime de JSON-aanvraaginstantie te ontleden om de objecteigenschappen in te stellen.

## <a name="customize-the-http-endpoint"></a>Het HTTP-eindpunt aanpassen

Wanneer u standaard een functie voor een HTTP-trigger maakt, is de functie adresseerbaar met een route van het formulier:

    http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>

U deze route `route` aanpassen met de optionele eigenschap op de invoerbinding van de HTTP-trigger. Als voorbeeld definieert het volgende *functie.json-bestand* een `route` eigenschap voor een HTTP-trigger:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Met deze configuratie is de functie nu adresseerbaar met de volgende route in plaats van de oorspronkelijke route.

```
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Met deze configuratie kan de functiecode twee parameters in het adres, de _categorie_ en de _id_ondersteunen.

# <a name="c"></a>[C #](#tab/csharp)

U elke [web-API-routebeperking](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) gebruiken met uw parameters. De volgende C#-functiecode maakt gebruik van beide parameters.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

U elke [web-API-routebeperking](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) gebruiken met uw parameters. De volgende C#-functiecode maakt gebruik van beide parameters.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

In Node biedt de runtime Functies `context` de aanvraaginstantie van het object. Zie het voorbeeld van de [JavaScript-trigger voor](#example)meer informatie .

In het volgende voorbeeld ziet `context.bindingData`u hoe u routeparameters lezen van.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="python"></a>[Python](#tab/python)

De functieuitvoeringscontext wordt weergegeven via `func.HttpRequest`een parameter die is gedeclareerd als . Met deze instantie heeft een functie toegang tot parameters voor gegevensroute, querytekenreekswaarden en methoden waarmee u HTTP-antwoorden retourneren.

Eenmaal gedefinieerd, zijn de routeparameters beschikbaar voor `route_params` de functie door de methode aan te roepen.

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

# <a name="java"></a>[Java](#tab/java)

De functieuitvoeringscontext is eigenschappen `HttpTrigger` zoals aangegeven in het kenmerk. Met het kenmerk u routeparameters, autorisatieniveaus, HTTP-werkwoorden en de inkomende aanvraaginstantie definiëren.

Routeparameters worden gedefinieerd `HttpTrigger` via het kenmerk.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

---

Standaard zijn alle functieroutes vooraf gekoppeld aan *api.* U het voorvoegsel ook `http.routePrefix` aanpassen of verwijderen met behulp van de eigenschap in uw [host.json-bestand.](functions-host-json.md) In het volgende voorbeeld wordt het voorvoegsel van de *api-route* verwijderd met behulp van een lege tekenreeks voor het voorvoegsel in het *bestand host.json.*

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

## <a name="using-route-parameters"></a>Routeparameters gebruiken

Routeparameters die het patroon `route` van een functie hebben gedefinieerd, zijn beschikbaar voor elke binding. Als u bijvoorbeeld een route hebt `"route": "products/{id}"` gedefinieerd als vervolgens kan een `{id}` tabelopslagbinding de waarde van de parameter in de bindingsconfiguratie gebruiken.

In de volgende `{id}` configuratie ziet u hoe `rowKey`de parameter wordt doorgegeven aan de binding.

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```

## <a name="working-with-client-identities"></a>Werken met klantidentiteiten

Als uw functie-app verificatie / autorisatie van [appservice](../app-service/overview-authentication-authorization.md)gebruikt, u informatie over geverifieerde clients bekijken vanuit uw code. Deze informatie is beschikbaar als [verzoek headers geïnjecteerd door het platform](../app-service/app-service-authentication-how-to.md#access-user-claims). 

U deze informatie ook lezen op uit bindende gegevens. Deze mogelijkheid is alleen beschikbaar voor de runtime van de functies in 2.x en hoger. Het is momenteel ook alleen beschikbaar voor .NET-talen.

# <a name="c"></a>[C #](#tab/csharp)

Informatie over geverifieerde clients is beschikbaar als [claimprincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). De Claimprincipal is beschikbaar als onderdeel van de aanvraagcontext zoals weergegeven in het volgende voorbeeld:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Als alternatief kan de ClaimPrincipal eenvoudig worden opgenomen als een extra parameter in de functiehandtekening:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Informatie over geverifieerde clients is beschikbaar als [claimprincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). De Claimprincipal is beschikbaar als onderdeel van de aanvraagcontext zoals weergegeven in het volgende voorbeeld:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Als alternatief kan de ClaimPrincipal eenvoudig worden opgenomen als een extra parameter in de functiehandtekening:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

De geverifieerde gebruiker is beschikbaar via [HTTP-headers.](../app-service/app-service-authentication-how-to.md#access-user-claims)

# <a name="python"></a>[Python](#tab/python)

De geverifieerde gebruiker is beschikbaar via [HTTP-headers.](../app-service/app-service-authentication-how-to.md#access-user-claims)

# <a name="java"></a>[Java](#tab/java)

De geverifieerde gebruiker is beschikbaar via [HTTP-headers.](../app-service/app-service-authentication-how-to.md#access-user-claims)

---

## <a name="authorization-keys"></a>Autorisatiesleutels

Met functies u toetsen gebruiken om het moeilijker te maken om tijdens de ontwikkeling toegang te krijgen tot de eindpunten van uw HTTP-functie.  Tenzij het HTTP-autorisatieniveau op een `anonymous`HTTP-geactiveerde functie is ingesteld op , moeten aanvragen een API-sleutel in de aanvraag bevatten. 

> [!IMPORTANT]
> Hoewel toetsen tijdens de ontwikkeling kunnen helpen bij het verdoezelen van uw HTTP-eindpunten, zijn ze niet bedoeld als een manier om een HTTP-trigger in productie te beveiligen. Zie [Een HTTP-eindpunt in productie beveiligen](#secure-an-http-endpoint-in-production)voor meer informatie.

> [!NOTE]
> In de functies 1.x runtime kunnen webhook-providers toetsen gebruiken om aanvragen op verschillende manieren te autoriseren, afhankelijk van wat de provider ondersteunt. Dit is bedekt met [Webhooks en toetsen.](#webhooks-and-keys) De functies runtime in versie 2.x en hoger bevat geen ingebouwde ondersteuning voor webhook providers.

#### <a name="authorization-scopes-function-level"></a>Autorisatiescopes (functieniveau)

Er zijn twee autorisatiescopes voor toetsen op functieniveau:

* **Functie**: Deze toetsen zijn alleen van toepassing op de specifieke functies waaronder ze zijn gedefinieerd. Bij gebruik als API-sleutel bieden deze alleen toegang tot die functie.

* **Host:** Toetsen met een hostbereik kunnen worden gebruikt om toegang te krijgen tot alle functies binnen de functie-app. Bij gebruik als API-sleutel bieden deze toegang tot elke functie binnen de functie-app. 

Elke sleutel wordt genoemd naar referentie en er is een standaardsleutel (met de naam 'standaard') op functie- en hostniveau. Functietoetsen hebben voorrang op hosttoetsen. Wanneer twee toetsen met dezelfde naam worden gedefinieerd, wordt de functiesleutel altijd gebruikt.

#### <a name="master-key-admin-level"></a>Hoofdsleutel (beheerdersniveau) 

Elke functie-app heeft ook een `_master`hostsleutel op beheerdersniveau met de naam . Naast het bieden van toegang op hostniveau tot alle functies in de app, biedt de hoofdsleutel ook administratieve toegang tot de RUNTime REST API's. Deze sleutel kan niet worden ingetrokken. Wanneer u een autorisatieniveau van `admin`, moeten aanvragen de hoofdsleutel gebruiken. andere belangrijke resultaten in autorisatie mislukking.

> [!CAUTION]  
> Vanwege de verhoogde machtigingen in uw functie-app die door de hoofdsleutel wordt verleend, moet u deze sleutel niet delen met derden of distribueren in native clienttoepassingen. Wees voorzichtig bij het kiezen van het beheerdersautorisatieniveau.

## <a name="obtaining-keys"></a>Sleutels verkrijgen

Sleutels worden opgeslagen als onderdeel van uw functie-app in Azure en worden in rust versleuteld. Als u uw sleutels wilt bekijken, nieuwe toetsen wilt maken of toetsen wilt rollen naar nieuwe waarden, navigeert u naar een van uw HTTP-geactiveerde functies in de [Azure-portal](https://portal.azure.com) en selecteert **u Beheren**.

![Functiesleutels beheren in de portal.](./media/functions-bindings-http-webhook/manage-function-keys.png)

U functietoetsen programmatisch verkrijgen met behulp van [Key management API's](https://github.com/Azure/azure-functions-host/wiki/Key-management-API).

## <a name="api-key-authorization"></a>API-sleutelautorisatie

Voor de meeste HTTP-triggersjablonen is een API-sleutel in de aanvraag vereist. Uw HTTP-aanvraag ziet er dus normaal gesproken uit als de volgende URL:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

De sleutel kan worden opgenomen in `code`een variabele voor querytekenreeksen met de naam , zoals hierboven. Het kan ook worden `x-functions-key` opgenomen in een HTTP-header. De waarde van de sleutel kan elke functiesleutel zijn die is gedefinieerd voor de functie of een hostsleutel.

U anonieme verzoeken toestaan, waarvoor geen sleutels nodig zijn. U ook eisen dat de hoofdsleutel wordt gebruikt. U wijzigt het standaardautorisatieniveau door de `authLevel` eigenschap in de bindende JSON te gebruiken. Zie [Trigger - configuratie](#configuration)voor meer informatie.

> [!NOTE]
> Wanneer de functie lokaal wordt uitgevoerd, wordt de autorisatie uitgeschakeld, ongeacht de opgegeven instelling voor autorisatieniveau. Na publicatie in `authLevel` Azure wordt de instelling in de trigger afgedwongen. Sleutels zijn nog steeds vereist wanneer u [lokaal in een container wordt](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally)uitgevoerd.


## <a name="secure-an-http-endpoint-in-production"></a>Een HTTP-eindpunt in productie beveiligen

Als u uw functieeindpunten in productie volledig wilt beveiligen, u overwegen een van de volgende beveiligingsopties op functie-app-niveau te implementeren:

* Schakel Verificatie van appservice / autorisatie voor uw functie-app in. Met het App Service-platform u Azure Active Directory (AAD) en verschillende identiteitsproviders van derden gebruiken om clients te verifiëren. U deze strategie gebruiken om aangepaste autorisatieregels voor uw functies te implementeren en u werken met gebruikersgegevens uit uw functiecode. Zie [Verificatie en autorisatie in Azure App Service en](../app-service/overview-authentication-authorization.md) Werken met [clientidentiteiten](#working-with-client-identities)voor meer informatie.

* Gebruik Azure API Management (APIM) om aanvragen te verifiëren. APIM biedt verschillende API-beveiligingsopties voor binnenkomende aanvragen. Zie [Verificatiebeleid API-beheer](../api-management/api-management-authentication-policies.md)voor meer informatie. Met APIM op zijn plaats u uw functie-app configureren om alleen aanvragen te accepteren vanaf het IP-adres van uw APIM-exemplaar. Zie [IP-adresbeperkingen](ip-addresses.md#ip-address-restrictions)voor meer informatie.

* Implementeer uw functie-app in een Azure App Service Environment (ASE). ASE biedt een speciale hostingomgeving waarin u uw functies uitvoeren. Met ASE u één front-end gateway configureren die u gebruiken om alle binnenkomende aanvragen te verifiëren. Zie [Een Web Application Firewall (WAF) configureren voor app-serviceomgeving voor](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)meer informatie.

Wanneer u een van deze beveiligingsmethoden op functie-app-niveau gebruikt, `anonymous`moet u het http-geactiveerde functieautorisatieniveau instellen op .

## <a name="webhooks"></a>Webhooks

> [!NOTE]
> De Webhook-modus is alleen beschikbaar voor versie 1.x van de runtime Functies. Deze wijziging is aangebracht om de prestaties van HTTP-triggers in versie 2.x en hoger te verbeteren.

In versie 1.x bieden webhook-sjablonen extra validatie voor webhook-payloads. In versie 2.x en hoger werkt de http-trigger nog steeds en is het de aanbevolen aanpak voor webhooks. 

### <a name="github-webhooks"></a>GitHub webhooks

Als u wilt reageren op GitHub-webhooks, maakt u eerst uw `github`functie met een HTTP-trigger en stelt u de eigenschap **webHookType** in op . Kopieer vervolgens de URL- en API-sleutel naar de **webpagina Toevoegen** van uw GitHub-opslagplaats. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Slack webhooks

De Slack-webhook genereert een token voor u in plaats van u dit te laten opgeven, dus u moet een functiespecifieke sleutel configureren met het token van Slack. Zie [Autorisatiesleutels](#authorization-keys).

## <a name="webhooks-and-keys"></a>Webhooks en toetsen

Webhook autorisatie wordt behandeld door de webhook ontvanger component, onderdeel van de HTTP trigger, en het mechanisme varieert op basis van de webhook type. Elk mechanisme is gebaseerd op een sleutel. Standaard wordt de functiesleutel met de naam 'standaard' gebruikt. Als u een andere sleutel wilt gebruiken, configureert u de webhookprovider om de sleutelnaam met het verzoek op een van de volgende manieren te verzenden:

* **Querytekenreeks:** de provider geeft de `clientid` sleutelnaam door `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`in de parameter querytekenreeks, zoals .
* **Koptekst aanvragen**: De provider `x-functions-clientid` geeft de sleutelnaam in de koptekst door.

## <a name="limits"></a>Limieten

De http-aanvraaglengte is beperkt tot 100 MB (104.857.600 bytes) en de URL-lengte is beperkt tot 4 KB (4.096 bytes). Deze limieten worden `httpRuntime` opgegeven door het element van het [Web.config-bestand](https://github.com/Azure/azure-functions-host/blob/3.x/src/WebJobs.Script.WebHost/web.config)van de runtime.

Als een functie die de HTTP-trigger gebruikt niet binnen 230 seconden wordt voltooid, geeft de [Azure Load Balancer](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) een time-out en retourneert u een HTTP 502-fout. De functie blijft actief, maar kan geen HTTP-antwoord retourneren. Voor langlopende functies raden we u aan asyncpatronen te volgen en een locatie terug te sturen waar u de status van de aanvraag pingen. Zie [Schalen en hosten](functions-scale.md#timeout)voor informatie over hoe lang een functie kan worden uitgevoerd.


## <a name="next-steps"></a>Volgende stappen

- [Een HTTP-antwoord van een functie retourneren](./functions-bindings-http-webhook-output.md)
