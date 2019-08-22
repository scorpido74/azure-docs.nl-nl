---
title: HTTP-triggers en-bindingen Azure Functions
description: Meer informatie over het gebruik van HTTP-triggers en-bindingen in Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, functies, gebeurtenis verwerking, webhooks, dynamische compute, serverloze architectuur, HTTP, API, REST
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 5e3b96d79ddef7940fd4029f9268e3040a63d2fb
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875073"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>HTTP-triggers en-bindingen Azure Functions

In dit artikel wordt uitgelegd hoe u met HTTP-triggers en uitvoer bindingen in Azure Functions kunt werken.

Een HTTP-trigger kan worden aangepast om te [](https://en.wikipedia.org/wiki/Webhook)reageren op webhooks.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

De code in dit artikel is standaard ingesteld op de syntaxis 2. x, die gebruikmaakt van .NET core. Zie voor meer informatie over de syntaxis van 1. x de [functies sjablonen van 1. x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="packages---functions-1x"></a>Pakketten - functies 1.x

De HTTP-bindingen zijn opgenomen in het [micro soft. Azure. webjobs. Extensions. http NuGet-](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) pakket, versie 1. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http) .

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Pakketten - functies 2.x

De HTTP-bindingen zijn opgenomen in het [micro soft. Azure. webjobs. Extensions. http NuGet-](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) pakket, versie 3. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) .

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Trigger

Met de HTTP-trigger kunt u een functie aanroepen met een HTTP-aanvraag. U kunt een HTTP-trigger gebruiken om serverloze Api's te maken en te reageren op webhooks.

Een HTTP-trigger retourneert standaard HTTP 200 OK met een lege hoofd tekst in functions 1. x of HTTP 204 geen inhoud met een lege hoofd tekst in de functies 2. x. Als u het antwoord wilt wijzigen, configureert u een [http-uitvoer binding](#output).

## <a name="trigger---example"></a>Trigger - voorbeeld

Zie het voorbeeld taalspecifieke:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-examples)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Trigger - voorbeeld met C#

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die `name` zoekt naar een para meter in de query teken reeks of de hoofd tekst van de HTTP-aanvraag. U ziet dat de retour waarde wordt gebruikt voor de uitvoer binding, maar een retour waarde-kenmerk is niet vereist.

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

### <a name="trigger---c-script-example"></a>Trigger - voorbeeld van C#-script

In het volgende voor beeld ziet u een trigger binding in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie zoekt naar een `name` para meter in de query reeks of de hoofd tekst van de HTTP-aanvraag.

Hier volgt de *function.json* bestand:

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

De [configuratie](#trigger---configuration) sectie wordt uitgelegd dat deze eigenschappen.

Dit is C# de script code die wordt gekoppeld `HttpRequest`aan:

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

U kunt een verbinding maken met een aangepast object `HttpRequest`in plaats van. Dit object wordt gemaakt op basis van de hoofd tekst van de aanvraag en geparseerd als JSON. Op dezelfde manier kan een type worden door gegeven aan de HTTP-antwoord uitvoer binding en geretourneerd als de antwoord tekst, samen met een 200-status code.

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

### <a name="trigger---f-example"></a>Trigger - F# voorbeeld

In het volgende voor beeld ziet u een trigger binding in een *Function. json* -bestand en een [ F# functie](functions-reference-fsharp.md) die gebruikmaakt van de binding. De functie zoekt naar een `name` para meter in de query reeks of de hoofd tekst van de HTTP-aanvraag.

Hier volgt de *function.json* bestand:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

De [configuratie](#trigger---configuration) sectie wordt uitgelegd dat deze eigenschappen.

Hier volgt de F# code:

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

U hebt een `project.json` bestand nodig waarin NuGet wordt gebruikt om `FSharp.Interop.Dynamic` te `Dynamitey` verwijzen naar de en-assembly's, zoals wordt weer gegeven in het volgende voor beeld:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

### <a name="trigger---javascript-example"></a>Trigger - JavaScript-voorbeeld

In het volgende voor beeld ziet u een trigger binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie zoekt naar een `name` para meter in de query reeks of de hoofd tekst van de HTTP-aanvraag.

Hier volgt de *function.json* bestand:

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

De [configuratie](#trigger---configuration) sectie wordt uitgelegd dat deze eigenschappen.

Dit is de JavaScript-code:

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

### <a name="trigger---python-example"></a>Voor beeld van een trigger-python

In het volgende voor beeld ziet u een trigger binding in een *Function. json* -bestand en een [python-functie](functions-reference-python.md) die gebruikmaakt van de binding. De functie zoekt naar een `name` para meter in de query reeks of de hoofd tekst van de HTTP-aanvraag.

Hier volgt de *function.json* bestand:

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

De [configuratie](#trigger---configuration) sectie wordt uitgelegd dat deze eigenschappen.

Dit is de python-code:

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

### <a name="trigger---java-examples"></a>Trigger-Java-voor beelden

* [De para meter lezen uit de query reeks](#read-parameter-from-the-query-string-java)
* [Hoofd tekst van een POST-aanvraag lezen](#read-body-from-a-post-request-java)
* [Para meter lezen vanuit een route](#read-parameter-from-a-route-java)
* [POJO hoofd tekst van een POST-aanvraag lezen](#read-pojo-body-from-a-post-request-java)

In de volgende voor beelden ziet u de binding HTTP-trigger in een *Function. json* -bestand en de respectieve [Java-functies](functions-reference-java.md) die gebruikmaken van de binding. 

Hier volgt de *function.json* bestand:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "anonymous",
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

#### <a name="read-parameter-from-the-query-string-java"></a>De para meter lezen uit de query reeks (Java)  

In dit voor beeld wordt een para ```id```meter met de naam van de query teken reeks gelezen en wordt deze gebruikt om een JSON-document te maken dat ```application/json```wordt geretourneerd naar de client, met het inhouds type. 

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

#### <a name="read-body-from-a-post-request-java"></a>Hoofd tekst van een POST-aanvraag lezen (Java)  

In dit voor beeld wordt de hoofd tekst van een post- ```String```aanvraag gelezen als een, en wordt deze gebruikt om een JSON-document te maken dat ```application/json```wordt geretourneerd naar de client, met het inhouds type.

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

#### <a name="read-parameter-from-a-route-java"></a>De para meter lezen vanuit een route (Java)  

In dit voor beeld worden een verplichte para ```id```meter, een naam en ```name``` een optionele para meter van het routenet werk gelezen en worden deze gebruikt om een JSON-document te maken dat ```application/json```wordt geretourneerd naar de client, met het inhouds type. D

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

#### <a name="read-pojo-body-from-a-post-request-java"></a>POJO hoofd tekst van een POST-aanvraag lezen (Java)  

Hier volgt de code voor de ```ToDoItem``` klasse, waarnaar wordt verwezen in dit voor beeld:

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

In dit voor beeld wordt de hoofd tekst van een POST-aanvraag gelezen. De aanvraag tekst wordt automatisch gedeserialiseerd in een ```ToDoItem``` object en wordt geretourneerd naar de client met het inhouds type. ```application/json``` De ```ToDoItem``` para meter wordt geserialiseerd door de functions-runtime, omdat deze is ```body``` toegewezen aan de ```HttpMessageResponse.Builder``` eigenschap van de klasse.

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

## <a name="trigger---attributes"></a>Trigger - kenmerken

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)het kenmerk [http trigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) .

U kunt het autorisatie niveau en toegestane HTTP-methoden instellen in de para meters van de kenmerk-constructor en er zijn eigenschappen voor het type webhook en de route sjabloon. Zie [trigger-configuratie](#trigger---configuration)voor meer informatie over deze instellingen. Hier volgt een `HttpTrigger` kenmerk in een methode handtekening:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Zie voor een compleet voorbeeld [Trigger - voorbeeld met C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Trigger - configuratie

De volgende tabel beschrijft de binding configuratie-eigenschappen die u instelt in de *function.json* bestand en de `HttpTrigger` kenmerk.

|de eigenschap Function.JSON | De kenmerkeigenschap |Description|
|---------|---------|----------------------|
| **type** | N.v.t.| Vereist: moet worden ingesteld op `httpTrigger`. |
| **direction** | N.v.t.| Vereist: moet worden ingesteld op `in`. |
| **name** | N.v.t.| Vereist: de naam van de variabele die wordt gebruikt in de functie code voor de aanvraag of aanvraag tekst. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Bepaalt welke sleutels, indien aanwezig, moeten aanwezig zijn in de aanvraag om de functie te kunnen aanroepen. Het autorisatie niveau kan een van de volgende waarden hebben: <ul><li><code>anonymous</code>&mdash;Er is geen API-sleutel vereist.</li><li><code>function</code>&mdash;Een functie-specifieke API-sleutel is vereist. Dit is de standaard waarde als er geen is ingesteld.</li><li><code>admin</code>&mdash;De hoofd sleutel is vereist.</li></ul> Zie de sectie over [autorisatie sleutels](#authorization-keys)voor meer informatie. |
| **methods** |**Technieken** | Een matrix van de HTTP-methoden waarop de functie reageert. Als deze niet wordt opgegeven, reageert de functie op alle HTTP-methoden. Zie [het http-eind punt aanpassen](#customize-the-http-endpoint). |
| **rond** | **Rond** | Hiermee wordt de route sjabloon gedefinieerd, waarmee wordt bepaald welke Url's van aanvragen uw functie reageert. De standaard waarde als er geen wordt gegeven `<functionname>`, is. Zie [het http-eind punt aanpassen](#customize-the-http-endpoint)voor meer informatie. |
| **webHookType** | **WebHookType** | _Alleen ondersteund voor de runtime van versie 1. x._<br/><br/>Hiermee wordt de HTTP-trigger geconfigureerd om te [](https://en.wikipedia.org/wiki/Webhook) fungeren als een webhook-ontvanger voor de opgegeven provider. Stel de `methods` eigenschap niet in als u deze eigenschap instelt. Het type webhook kan een van de volgende waarden hebben:<ul><li><code>genericJson</code>&mdash;Een webhook-eind punt voor algemeen gebruik zonder logica voor een specifieke provider. Met deze instelling worden aanvragen beperkt tot gebruikers met behulp van http post en `application/json` met het inhouds type.</li><li><code>github</code>&mdash;De functie reageert op [github](https://developer.github.com/webhooks/)-webhooks. Gebruik niet de eigenschap _authLevel_ met github-webhooks. Zie de sectie GitHub-webhooks verderop in dit artikel voor meer informatie.</li><li><code>slack</code>&mdash;De functie reageert op [toegestane](https://api.slack.com/outgoing-webhooks)webhooks. Gebruik niet de eigenschap _authLevel_ met toegestane webhooks. Zie de sectie over toegestane webhooks verderop in dit artikel voor meer informatie.</li></ul>|

## <a name="trigger---usage"></a>Trigger - gebruik

Voor C# en F# -functies kunt u het type van de trigger invoer declareren als ofwel `HttpRequest` een aangepast type. Als u kiest `HttpRequest`, krijgt u volledige toegang tot het aanvraag object. Voor een aangepast type probeert de runtime de JSON-aanvraag tekst te parseren om de object eigenschappen in te stellen.

Voor Java script-functies biedt de functions-runtime de hoofd tekst van de aanvraag in plaats van het aanvraag object. Zie voor meer informatie het [voor beeld van Java script-trigger](#trigger---javascript-example).

### <a name="customize-the-http-endpoint"></a>Het HTTP-eind punt aanpassen

Wanneer u een functie voor een HTTP-trigger maakt, is de functie standaard adresseerbaar met een route van het formulier:

    http://<yourapp>.azurewebsites.net/api/<funcname>

U kunt deze route aanpassen met behulp `route` van de optionele eigenschap voor de invoer binding van de http-trigger. Als voor beeld wordt met het volgende *Function. json* -bestand `route` een eigenschap voor een http-trigger gedefinieerd:

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
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

Hierdoor kan de functie code twee para meters ondersteunen in het adres, de _categorie_ en de _id_. U kunt elke [Web-API-route beperking](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) gebruiken met de para meters. De volgende C# functie code maakt gebruik van beide para meters.

```csharp
public static Task<IActionResult> Run(HttpRequest req, string category, int? id, ILogger log)
{
    if (id == null)
    {
        return (ActionResult)new OkObjectResult($"All {category} items were requested.");
    }
    else
    {
        return (ActionResult)new OkObjectResult($"{category} item with id = {id} has been requested.");
    }
    
    // -----
    log.LogInformation($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
}
```

Hier is de code van de node. js-functie die gebruikmaakt van dezelfde route parameters.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
}
```

Standaard worden alle functie routes voorafgegaan door *API*. U kunt het voor voegsel ook aanpassen of verwijderen met `http.routePrefix` behulp van de eigenschap in het JSON-bestand van de [host](functions-host-json.md) . In het volgende voor beeld wordt het voor voegsel *API* -route verwijderd met behulp van een lege teken reeks voor het voor voegsel in het bestand *host. json* .

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="working-with-client-identities"></a>Werken met client identiteiten

Als uw functie-app gebruikmaakt van [app service verificatie/autorisatie](../app-service/overview-authentication-authorization.md), kunt u informatie weer geven over geverifieerde clients vanuit uw code. Deze informatie is beschikbaar als [aanvraag headers die zijn geïnjecteerd door het platform](../app-service/app-service-authentication-how-to.md#access-user-claims). 

U kunt deze informatie ook lezen van bindings gegevens. Deze functie is alleen beschikbaar voor de functies 2. x runtime. Het is momenteel alleen beschikbaar voor .NET-talen.

In .NET-talen is deze informatie beschikbaar als een [claimsprincipal is](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). De Claimsprincipal is is beschikbaar als onderdeel van de context van de aanvraag, zoals wordt weer gegeven in het volgende voor beeld:

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

U kunt de Claimsprincipal is ook gewoon opnemen als een extra para meter in de functie handtekening:

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

### <a name="authorization-keys"></a>Autorisatie sleutels

Met functies kunt u sleutels gebruiken om het moeilijker te maken om toegang te krijgen tot de eind punten van uw HTTP-functies tijdens het ontwikkelen.  Een HTTP-standaard trigger kan vereisen dat een dergelijke API-sleutel in de aanvraag wordt weer gegeven. 

> [!IMPORTANT]
> Hoewel sleutels uw HTTP-eind punten kunnen afleiden tijdens de ontwikkeling, zijn ze niet bedoeld als een manier om een HTTP-trigger in de productie te beveiligen. Zie [een HTTP-eind punt in productie beveiligen](#secure-an-http-endpoint-in-production)voor meer informatie.

> [!NOTE]
> In de functies 1. x runtime kunnen webhook-providers gebruikmaken van sleutels om aanvragen op verschillende manieren te autoriseren, afhankelijk van wat de provider ondersteunt. Dit wordt behandeld in [webhooks en sleutels](#webhooks-and-keys). De runtime van versie 2. x bevat geen ingebouwde ondersteuning voor webhook-providers.

Er zijn twee soorten sleutels:

* **Host sleutels**: Deze sleutels worden gedeeld door alle functies in de functie-app. Wanneer deze als een API-sleutel wordt gebruikt, is toegang tot alle functies in de functie-app toegestaan.
* **Functie toetsen**: Deze sleutels zijn alleen van toepassing op de specifieke functies waaronder ze zijn gedefinieerd. Wanneer u als een API-sleutel wordt gebruikt, is deze alleen toegankelijk voor deze functie.

Elke sleutel krijgt de naam van de verwijzing en er is een standaard sleutel (met de naam ' default ') op het niveau van de functie en de host. Functie sleutels hebben voor rang op de sleutels van de host. Wanneer twee sleutels met dezelfde naam zijn gedefinieerd, wordt de functie toets altijd gebruikt.

Elke functie-app heeft ook een speciale **hoofd sleutel**. Deze sleutel is een host-sleutel `_master`met de naam, die beheerders toegang biedt tot de runtime-api's. Deze sleutel kan niet worden ingetrokken. Wanneer u een autorisatie niveau van instelt `admin`, moeten aanvragen gebruikmaken van de hoofd sleutel. een andere sleutel resulteert in een autorisatie fout.

> [!CAUTION]  
> Als gevolg van de verhoogde machtigingen in uw functie-app die is verleend door de hoofd sleutel, moet u deze sleutel niet delen met derden of deze distribueren in native client toepassingen. Wees voorzichtig bij het kiezen van het autorisatie niveau beheerder.

### <a name="obtaining-keys"></a>Sleutels verkrijgen

Sleutels worden opgeslagen als onderdeel van de functie-app in Azure en worden op rest versleuteld. Als u de sleutels wilt weer geven, nieuwe wilt maken of sleutels naar nieuwe waarden wilt versleutelen, gaat u naar een van de functies die via HTTP worden geactiveerd in de [Azure Portal](https://portal.azure.com) en selecteert u **beheren**.

![Functie sleutels in de portal beheren.](./media/functions-bindings-http-webhook/manage-function-keys.png)

U kunt functie sleutels programmatisch verkrijgen met behulp van [Key Management API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API).

### <a name="api-key-authorization"></a>Verificatie van API-sleutels

Voor de meeste HTTP-trigger sjablonen is een API-sleutel in de aanvraag vereist. Uw HTTP-aanvraag lijkt daarom normaal te lijken op de volgende URL:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

De sleutel kan worden opgenomen in een query reeks variabele met `code`de naam, zoals hierboven is beschreven. Het kan ook worden opgenomen in een `x-functions-key` http-header. De waarde van de sleutel kan een functie sleutel zijn die is gedefinieerd voor de functie of een wille keurige host-sleutel.

U kunt anonieme aanvragen toestaan waarvoor geen sleutels zijn vereist. U kunt ook instellen dat de hoofd sleutel moet worden gebruikt. U wijzigt het standaard autorisatie niveau met behulp `authLevel` van de eigenschap in de JSON van de binding. Zie [trigger-configuratie](#trigger---configuration)voor meer informatie.

> [!NOTE]
> Wanneer de functie lokaal wordt uitgevoerd, wordt de autorisatie uitgeschakeld, ongeacht de opgegeven instelling voor het verificatie niveau. Na het publiceren naar Azure wordt `authLevel` de instelling in de trigger afgedwongen.



### <a name="secure-an-http-endpoint-in-production"></a>Een HTTP-eind punt in de productie omgeving beveiligen

Als u uw functie-eind punten in productie volledig wilt beveiligen, kunt u overwegen om een van de volgende functie-beveiligings opties op app-niveau te implementeren:

* Schakel App Service verificatie/autorisatie in voor uw functie-app. Het App Service-platform kan Azure Active Directory (AAD) en verschillende id-providers van derden gebruiken om clients te verifiëren. U kunt dit gebruiken om aangepaste autorisatie regels voor uw functies te implementeren en u kunt met gebruikers gegevens uit uw functie code werken. Zie [verificatie en autorisatie in azure app service](../app-service/overview-authentication-authorization.md) en [werken met client identiteiten](#working-with-client-identities)voor meer informatie.

* Gebruik Azure API Management (APIM) om aanvragen te verifiëren. APIM biedt diverse API-beveiligings opties voor inkomende aanvragen. Zie [API Management Authentication policies](../api-management/api-management-authentication-policies.md)(Engelstalig) voor meer informatie. Als APIM is geïmplementeerd, kunt u uw functie-app zodanig configureren dat aanvragen alleen worden geaccepteerd van het IP-adres van uw APIM-exemplaar. Zie [IP-adres beperkingen](ip-addresses.md#ip-address-restrictions)voor meer informatie.

* Implementeer uw functie-app in een Azure App Service Environment (ASE). ASE biedt een specifieke hosting omgeving waarin u uw functies kunt uitvoeren. Met ASE kunt u één front-end-gateway configureren die u kunt gebruiken om alle inkomende aanvragen te verifiëren. Zie [Configuring a Web Application firewall (WAF) (Engelstalig) voor app service Environment](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)voor meer informatie.

Wanneer u een van deze beveiligings methoden op app-niveau gebruikt, moet u het door HTTP geactiveerde functie verificatie niveau instellen `anonymous`op.

### <a name="webhooks"></a>Webhooks

> [!NOTE]
> De webhook-modus is alleen beschikbaar voor versie 1. x van de functions-runtime. Deze wijziging is doorgevoerd om de prestaties van HTTP-triggers in versie 2. x te verbeteren.

In versie 1. x, webhook-sjablonen bieden extra validatie voor webhook-payloads. In versie 2. x werkt de HTTP-basis trigger nog steeds en is de aanbevolen benadering voor webhooks. 

#### <a name="github-webhooks"></a>GitHub-webhooks

Als u wilt reageren op GitHub-webhooks, maakt u eerst uw functie met een HTTP- trigger en stelt `github`u de eigenschap webHookType in op. Kopieer vervolgens de URL en API-sleutel naar de pagina **webhook toevoegen** van uw github-opslag plaats. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Toegestane webhooks

De toegestane webhook genereert een token voor u in plaats van door u in te stellen. u moet dus een functie-specifieke sleutel met het token configureren van toegestane vertraging. Zie [autorisatie sleutels](#authorization-keys).

### <a name="webhooks-and-keys"></a>Webhooks en sleutels

Webhook-autorisatie wordt verwerkt door het onderdeel webhook-ontvanger, onderdeel van de HTTP-trigger en het mechanisme varieert op basis van het type webhook. Elk mechanisme is afhankelijk van een sleutel. Standaard wordt de functie sleutel met de naam ' default ' gebruikt. Als u een andere sleutel wilt gebruiken, configureert u de webhook-provider om de sleutel naam met de aanvraag te verzenden op een van de volgende manieren:

* **Query reeks**: De provider geeft de sleutel naam door in `clientid` de query teken reeks parameter, `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`zoals.
* **Aanvraag header**: De provider geeft de sleutel naam in de `x-functions-clientid` header door.

## <a name="trigger---limits"></a>Trigger-limieten

De lengte van de HTTP-aanvraag is beperkt tot 100 MB (104.857.600 bytes) en de URL-lengte is beperkt tot 4 KB (4.096 bytes). Deze limieten worden opgegeven door `httpRuntime` het element van het [Web. config-bestand](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config)van de runtime.

Als een functie die de HTTP-trigger gebruikt, niet binnen ongeveer 2,5 minuten wordt voltooid, treedt er een time-out op voor de gateway en wordt een HTTP 502-fout geretourneerd. De functie wordt nog steeds uitgevoerd, maar kan geen HTTP-antwoord retour neren. Voor langlopende functies wordt u aangeraden async-patronen te volgen en een locatie te retour neren waar u de status van de aanvraag kunt pingen. Zie voor meer informatie over hoe lang een functie kan worden uitgevoerd het [plan voor schalen en hosten](functions-scale.md#timeout).

## <a name="trigger---hostjson-properties"></a>Trigger - eigenschappen voor host.json

Het bestand [host. json](functions-host-json.md) bevat instellingen die het gedrag van http-triggers regelen.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Output

Gebruik de HTTP-uitvoer binding om te reageren op de afzender van de HTTP-aanvraag. Deze binding vereist een HTTP-trigger en Hiermee kunt u het antwoord aanpassen dat is gekoppeld aan de aanvraag van de trigger. Als er geen HTTP-uitvoer binding is opgegeven, retourneert een HTTP-trigger HTTP 200 OK met een lege hoofd tekst in functions 1. x of HTTP 204 geen inhoud met een lege hoofd tekst in de functies 2. x.

## <a name="output---configuration"></a>Uitvoer - configuratie

De volgende tabel bevat uitleg over de binding configuratie-eigenschappen die u in het bestand *Function. json* hebt ingesteld. Voor C# class-bibliotheken zijn er geen kenmerk eigenschappen die overeenkomen met deze *functie. json* -eigenschappen.

|Eigenschap  |Description  |
|---------|---------|
| **type** |Moet worden ingesteld op `http`. |
| **direction** | Moet worden ingesteld op `out`. |
|**name** | De naam van de variabele die wordt gebruikt in de functie code `$return` voor het antwoord of voor het gebruik van de retour waarde. |

## <a name="output---usage"></a>Uitvoer - gebruik

Als u een HTTP-antwoord wilt verzenden, gebruikt u de standaard-antwoord patronen voor de taal. Maak C# in C# of script het retour type `IActionResult` functie of. `Task<IActionResult>` In C#is geen retour waarde-kenmerk vereist.

Zie voor beeld van antwoorden het [voor beeld](#trigger---example)van de trigger.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over Azure functions-triggers en bindingen](functions-triggers-bindings.md)
