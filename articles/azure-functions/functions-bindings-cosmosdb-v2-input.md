---
title: Invoer binding Azure Cosmos DB voor functies 2. x
description: Meer informatie over het gebruik van de Azure Cosmos DB invoer binding in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: 4fe04d491525b8119ca21ff1118a2ea460cc0795
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77606605"
---
# <a name="azure-cosmos-db-input-binding-for-azure-functions-2x"></a>Invoer binding Azure Cosmos DB voor Azure Functions 2. x

De Azure Cosmos DB-Invoerbinding maakt gebruik van de SQL-API om een of meer Azure Cosmos DB-documenten te halen en geeft deze door aan de invoerparameter van de functie. De document-ID of query parameters kunnen worden bepaald op basis van de trigger die de functie activeert.

Zie het [overzicht](./functions-bindings-cosmosdb-v2.md)voor meer informatie over de installatie-en configuratie details.

> [!NOTE]
> Als de verzameling is [gepartitioneerd](../cosmos-db/partition-data.md#logical-partitions), moeten opzoek bewerkingen ook de partitie sleutel waarde opgeven.
>

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Deze sectie bevat de volgende voorbeelden:

* [Wachtrij trigger, ID opzoeken vanuit JSON](#queue-trigger-look-up-id-from-json-c)
* [HTTP-trigger, ID opzoeken vanuit query reeks](#http-trigger-look-up-id-from-query-string-c)
* [HTTP-trigger, ID van route gegevens opzoeken](#http-trigger-look-up-id-from-route-data-c)
* [HTTP-trigger, ID opzoeken op basis van route gegevens, met behulp van SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP-trigger, meerdere docs ophalen met behulp van SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP-trigger, meerdere docs ophalen met behulp van DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

De voor beelden verwijzen naar een eenvoudig `ToDoItem` type:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string PartitionKey { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Wachtrij trigger, ID opzoeken vanuit JSON 

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die één document ophaalt. De functie wordt geactiveerd door een wachtrijbericht met een JSON-object. De wachtrij trigger parseert de JSON naar een object van het type `ToDoItemLookup`, dat de ID en de partitie sleutel waarde bevat die moet worden gezocht. Deze ID en partitie sleutel waarde worden gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }

        public string ToDoItemPartitionKeyValue { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}",
                PartitionKey = "{ToDoItemPartitionKeyValue}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId} Key={toDoItemLookup?.ToDoItemPartitionKeyValue}");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, ID opzoeken vanuit query reeks

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van een query reeks om de ID en de partitie sleutel waarde op te geven die moet worden gezocht. Deze ID en partitie sleutel waarde worden gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

>[!NOTE]
>De para meter voor de HTTP-query reeks is hoofdletter gevoelig.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}",
                PartitionKey = "{Query.partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, ID van route gegevens opzoeken

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van route gegevens om de ID en de partitie sleutel waarde op te geven die moet worden gezocht. Deze ID en partitie sleutel waarde worden gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{partitionKey}/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}",
                PartitionKey = "{partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-trigger, ID opzoeken op basis van route gegevens, met behulp van SqlQuery

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag dat maakt gebruik van gegevens om op te geven van de ID opzoeken routeren. Deze ID wordt gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

In het voor beeld ziet u hoe u een bindings expressie gebruikt in de para meter `SqlQuery`. U kunt route gegevens door geven aan de para meter `SqlQuery` zoals weer gegeven, maar [u mag geen query reeks waarden door geven](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).

> [!NOTE]
> Als u alleen een query wilt uitvoeren op basis van de ID, is het raadzaam om een zoek opdracht te gebruiken, zoals de [voor gaande voor beelden](#http-trigger-look-up-id-from-query-string-c), omdat er minder [aanvraag eenheden](../cosmos-db/request-units.md)worden gebruikt. Lees bewerkingen op punten (GET) zijn [efficiënter](../cosmos-db/optimize-cost-queries.md) dan QUERY'S op id.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-trigger, meerdere docs ophalen met behulp van SqlQuery

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) waarmee een lijst met documenten wordt opgehaald. De functie wordt geactiveerd door een HTTP-aanvraag. De query is opgegeven in de eigenschap van het `SqlQuery` kenmerk.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP-trigger, meerdere docs ophalen met behulp van DocumentClient

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) waarmee een lijst met documenten wordt opgehaald. De functie wordt geactiveerd door een HTTP-aanvraag. De code gebruikt een `DocumentClient` exemplaar dat wordt aangeboden door de Azure Cosmos DB-binding om een lijst met documenten te lezen. Het `DocumentClient`-exemplaar kan ook worden gebruikt voor schrijf bewerkingen.

> [!NOTE]
> U kunt ook de [IDocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet) -interface gebruiken om het testen gemakkelijker te maken.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.LogInformation($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.LogInformation(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

Deze sectie bevat de volgende voorbeelden:

* [Wachtrij trigger, ID opzoeken op basis van teken reeks](#queue-trigger-look-up-id-from-string-c-script)
* [Wachtrij trigger, meerdere documenten ophalen met behulp van SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-trigger, ID opzoeken vanuit query reeks](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP-trigger, ID van route gegevens opzoeken](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP-trigger, meerdere docs ophalen met behulp van SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-trigger, meerdere docs ophalen met behulp van DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

De voor beelden van HTTP-triggers verwijzen naar een eenvoudig `ToDoItem` type:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>Wachtrij trigger, ID opzoeken op basis van teken reeks

In het volgende voor beeld ziet u een Cosmos DB invoer binding in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie leest één document en updates van de tekstwaarde van het document.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```
In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C#-scriptcode:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Wachtrij trigger, meerdere documenten ophalen met behulp van SqlQuery

In het volgende voor beeld ziet u een Azure Cosmos DB invoer binding in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie haalt meerdere documenten die zijn opgegeven door een SQL-query met behulp van een wachtrijtrigger voor het aanpassen van de queryparameters.

De trigger van de wachtrij bevat een para meter `departmentId`. Een wachtrij bericht van `{ "departmentId" : "Finance" }` zou alle records voor de afdeling Financiën retour neren.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C#-scriptcode:

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, ID opzoeken vanuit query reeks

In het volgende voor beeld ziet u een [ C# script functie](functions-reference-csharp.md) waarmee één document wordt opgehaald. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van een query reeks om de ID en de partitie sleutel waarde op te geven die moet worden gezocht. Deze ID en partitie sleutel waarde worden gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

Hier is het bestand *Function. json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
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
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey" : "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Dit is de C#-scriptcode:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, ID van route gegevens opzoeken

In het volgende voor beeld ziet u een [ C# script functie](functions-reference-csharp.md) waarmee één document wordt opgehaald. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van route gegevens om de ID en de partitie sleutel waarde op te geven die moet worden gezocht. Deze ID en partitie sleutel waarde worden gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

Hier is het bestand *Function. json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Dit is de C#-scriptcode:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-trigger, meerdere docs ophalen met behulp van SqlQuery

In het volgende voor beeld ziet u een [ C# script functie](functions-reference-csharp.md) waarmee een lijst met documenten wordt opgehaald. De functie wordt geactiveerd door een HTTP-aanvraag. De query is opgegeven in de eigenschap van het `SqlQuery` kenmerk.

Hier is het bestand *Function. json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
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
    },
    {
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

Dit is de C#-scriptcode:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.LogInformation(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP-trigger, meerdere docs ophalen met behulp van DocumentClient

In het volgende voor beeld ziet u een [ C# script functie](functions-reference-csharp.md) waarmee een lijst met documenten wordt opgehaald. De functie wordt geactiveerd door een HTTP-aanvraag. De code gebruikt een `DocumentClient` exemplaar dat wordt aangeboden door de Azure Cosmos DB-binding om een lijst met documenten te lezen. Het `DocumentClient`-exemplaar kan ook worden gebruikt voor schrijf bewerkingen.

Hier is het bestand *Function. json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
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
    },
    {
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

Dit is de C#-scriptcode:

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.LogInformation($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.LogInformation(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Deze sectie bevat de volgende voor beelden van het lezen van één document door een ID-waarde op te geven uit verschillende bronnen:

* [Wachtrij trigger, ID opzoeken vanuit JSON](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP-trigger, ID opzoeken vanuit query reeks](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP-trigger, ID van route gegevens opzoeken](#http-trigger-look-up-id-from-route-data-javascript)
* [Wachtrij trigger, meerdere documenten ophalen met behulp van SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Wachtrij trigger, ID opzoeken vanuit JSON

In het volgende voor beeld ziet u een Cosmos DB invoer binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie leest één document en updates van de tekstwaarde van het document.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de JavaScript-code:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, ID opzoeken vanuit query reeks

In het volgende voor beeld ziet u een [Java script-functie](functions-reference-node.md) die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van een query reeks om de ID en de partitie sleutel waarde op te geven die moet worden gezocht. Deze ID en partitie sleutel waarde worden gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

Hier is het bestand *Function. json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
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
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Dit is de JavaScript-code:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, ID van route gegevens opzoeken

In het volgende voor beeld ziet u een [Java script-functie](functions-reference-node.md) die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van route gegevens om de ID en de partitie sleutel waarde op te geven die moet worden gezocht. Deze ID en partitie sleutel waarde worden gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

Hier is het bestand *Function. json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Dit is de JavaScript-code:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Wachtrij trigger, meerdere documenten ophalen met behulp van SqlQuery

In het volgende voor beeld ziet u een Azure Cosmos DB invoer binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie haalt meerdere documenten die zijn opgegeven door een SQL-query met behulp van een wachtrijtrigger voor het aanpassen van de queryparameters.

De trigger van de wachtrij bevat een para meter `departmentId`. Een wachtrij bericht van `{ "departmentId" : "Finance" }` zou alle records voor de afdeling Financiën retour neren.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de JavaScript-code:

```javascript
    module.exports = function (context, input) {
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }
        context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

Deze sectie bevat de volgende voor beelden van het lezen van één document door een ID-waarde op te geven uit verschillende bronnen:

* [Wachtrij trigger, ID opzoeken vanuit JSON](#queue-trigger-look-up-id-from-json-python)
* [HTTP-trigger, ID opzoeken vanuit query reeks](#http-trigger-look-up-id-from-query-string-python)
* [HTTP-trigger, ID van route gegevens opzoeken](#http-trigger-look-up-id-from-route-data-python)
* [Wachtrij trigger, meerdere documenten ophalen met behulp van SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Wachtrij trigger, ID opzoeken vanuit JSON

In het volgende voor beeld ziet u een Cosmos DB invoer binding in een *Function. json* -bestand en een [python-functie](functions-reference-python.md) die gebruikmaakt van de binding. De functie leest één document en updates van de tekstwaarde van het document.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "$return",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de python-code:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, ID opzoeken vanuit query reeks

In het volgende voor beeld ziet u een [python-functie](functions-reference-python.md) die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van een query reeks om de ID en de partitie sleutel waarde op te geven die moet worden gezocht. Deze ID en partitie sleutel waarde worden gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

Hier is het bestand *Function. json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
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
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": true,
  "scriptFile": "__init__.py"
}
```

Dit is de python-code:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])

    return 'OK'
```

<a id="http-trigger-look-up-id-from-route-data-python"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, ID van route gegevens opzoeken

In het volgende voor beeld ziet u een [python-functie](functions-reference-python.md) die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van route gegevens om de ID en de partitie sleutel waarde op te geven die moet worden gezocht. Deze ID en partitie sleutel waarde worden gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

Hier is het bestand *Function. json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Dit is de python-code:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])
    return 'OK'
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Wachtrij trigger, meerdere documenten ophalen met behulp van SqlQuery

In het volgende voor beeld ziet u een Azure Cosmos DB invoer binding in een *Function. json* -bestand en een [python-functie](functions-reference-python.md) die gebruikmaakt van de binding. De functie haalt meerdere documenten die zijn opgegeven door een SQL-query met behulp van een wachtrijtrigger voor het aanpassen van de queryparameters.

De trigger van de wachtrij bevat een para meter `departmentId`. Een wachtrij bericht van `{ "departmentId" : "Finance" }` zou alle records voor de afdeling Financiën retour neren.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de python-code:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

# <a name="java"></a>[Java](#tab/java)

Deze sectie bevat de volgende voorbeelden:

* [HTTP-trigger, ID opzoeken op basis van query teken reeks para meter](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [HTTP-trigger, ID opzoeken vanuit query teken reeks-POJO para meter](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [HTTP-trigger, ID van route gegevens opzoeken](#http-trigger-look-up-id-from-route-data-java)
* [HTTP-trigger, ID opzoeken op basis van route gegevens, met behulp van SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [HTTP-trigger, meerdere documenten ophalen van route gegevens, met behulp van SqlQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

De voor beelden verwijzen naar een eenvoudig `ToDoItem` type:

```java
public class ToDoItem {

  private String id;
  private String description;

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

<a id="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>HTTP-trigger, ID opzoeken op basis van query teken reeks para meter

In het volgende voor beeld ziet u een Java-functie die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van een query reeks om de ID en de partitie sleutel waarde op te geven die moet worden gezocht. Deze ID en partitie sleutel waarde worden gebruikt om een document op te halen uit de opgegeven Data Base en verzameling, in de vorm van een teken reeks.

```java
public class DocByIdFromQueryString {

    @FunctionName("DocByIdFromQueryString")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@CosmosDBInput` annotatie voor functie parameters waarvan de waarde afkomstig is van Cosmos db.  Deze aantekening kan worden gebruikt met systeem eigen Java-typen, Pojo's of nullable-waarden met behulp van `Optional<T>`.

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>HTTP-trigger, ID opzoeken vanuit query teken reeks-POJO para meter

In het volgende voor beeld ziet u een Java-functie die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van een query reeks om de ID en de partitie sleutel waarde op te geven die moet worden gezocht. Deze ID en partitie sleutel waarde die wordt gebruikt om een document op te halen uit de opgegeven Data Base en verzameling. Het document wordt vervolgens geconverteerd naar een exemplaar van de ```ToDoItem``` POJO eerder gemaakt en door gegeven als een argument voor de functie.

```java
public class DocByIdFromQueryStringPojo {

    @FunctionName("DocByIdFromQueryStringPojo")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Item from the database is " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

<a id="http-trigger-look-up-id-from-route-data-java"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, ID van route gegevens opzoeken

In het volgende voor beeld ziet u een Java-functie die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van een route parameter om de ID en de waarde van de partitie sleutel op te geven die moet worden gezocht. Deze ID en partitie sleutel waarde worden gebruikt om een document op te halen uit de opgegeven Data Base en verzameling, en dit te retour neren als ```Optional<String>```.

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{partitionKeyValue}/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-trigger, ID opzoeken op basis van route gegevens, met behulp van SqlQuery

In het volgende voor beeld ziet u een Java-functie die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van een route parameter om de ID op te geven die moet worden gezocht. Deze ID wordt gebruikt om een document op te halen uit de opgegeven Data Base en verzameling, waarbij de resultatenset wordt geconverteerd naar een ```ToDoItem[]```, omdat er veel documenten kunnen worden geretourneerd, afhankelijk van de query criteria.

> [!NOTE]
> Als u alleen een query wilt uitvoeren op basis van de ID, is het raadzaam om een zoek opdracht te gebruiken, zoals de [voor gaande voor beelden](#http-trigger-look-up-id-from-query-string---pojo-parameter-java), omdat er minder [aanvraag eenheden](../cosmos-db/request-units.md)worden gebruikt. Lees bewerkingen op punten (GET) zijn [efficiënter](../cosmos-db/optimize-cost-queries.md) dan QUERY'S op id.
>

```java
public class DocByIdFromRouteSqlQuery {

    @FunctionName("DocByIdFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems2/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where r.id = {id}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Items from the database are " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>HTTP-trigger, meerdere documenten ophalen van route gegevens, met behulp van SqlQuery

In het volgende voor beeld ziet u een Java-functie waarmee meerdere documenten worden opgehaald. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van een route parameter ```desc``` om de teken reeks op te geven waarnaar u wilt zoeken in het veld ```description```. De zoek term wordt gebruikt om een verzameling documenten op te halen uit de opgegeven Data Base en verzameling, waarbij de resultatenset wordt geconverteerd naar een ```ToDoItem[]``` en wordt door gegeven als argument voor de functie.

```java
public class DocsFromRouteSqlQuery {

    @FunctionName("DocsFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems3/{desc}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where contains(r.description, {desc})",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] items,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Number of items from the database is " + (items == null ? 0 : items.length));

        // Convert and display
        if (items == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("No documents found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(items)
                          .build();
        }
    }
}
 ```

 ---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)het kenmerk [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) .

De constructor van het kenmerk wordt de databasenaam en verzamelingsnaam. Zie [de volgende configuratie sectie](#configuration)voor informatie over de instellingen en andere eigenschappen die u kunt configureren.

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# het script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="java"></a>[Java](#tab/java)

Gebruik in de [runtime-bibliotheek van Java-functies](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)de `@CosmosDBOutput` annotatie voor para meters die naar Cosmos DB schrijven. Het parameter type aantekening moet `OutputBinding<T>`zijn, waarbij `T` een systeem eigen Java-type of een POJO is.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `CosmosDB`.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type**     | N.v.t. | moet worden ingesteld op `cosmosDB`.        |
|**direction**     | N.v.t. | moet worden ingesteld op `in`.         |
|**naam**     | N.v.t. | De naam van de bindingsparameter die het document in de functie vertegenwoordigt.  |
|**databaseName** |**DatabaseName** |De database met het document.        |
|**collectionName** |**CollectionName** | De naam van de verzameling waarin het document. |
|**id**    | **Id** | De ID van het document om op te halen. Deze eigenschap ondersteunt [bindings expressies](./functions-bindings-expressions-patterns.md). Stel de eigenschappen `id` en **sqlQuery** niet in. Als u niet uit, één instelt, wordt de volledige verzameling worden opgehaald. |
|**sqlQuery**  |**SqlQuery**  | Een Azure Cosmos DB SQL-query die wordt gebruikt voor het ophalen van meerdere documenten. De eigenschap ondersteunt runtime bindingen, zoals in dit voor beeld: `SELECT * FROM c where c.departmentId = {departmentId}`. Stel de eigenschappen `id` en `sqlQuery` niet in. Als u niet uit, één instelt, wordt de volledige verzameling worden opgehaald.|
|**connectionStringSetting**     |**ConnectionStringSetting**|De naam van de app-instelling met daarin uw Azure Cosmos DB-verbindingsreeks.        |
|**partitionKey**|**PartitionKey**|Hiermee geeft u de waarde voor de partitiesleutel voor de zoekopdracht. Kan omvatten bindende parameters. Dit is vereist voor zoek acties in [gepartitioneerde](../cosmos-db/partition-data.md#logical-partitions) verzamelingen.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

Wanneer de functie is afgesloten, worden alle wijzigingen die zijn aangebracht in het invoer document via benoemde invoer parameters automatisch behouden.

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

Wanneer de functie is afgesloten, worden alle wijzigingen die zijn aangebracht in het invoer document via benoemde invoer parameters automatisch behouden.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Updates worden niet automatisch doorgevoerd bij het afsluiten van de functie. Gebruik in plaats daarvan `context.bindings.<documentName>In` en `context.bindings.<documentName>Out` om updates te maken. Zie het Java script-voor beeld.

# <a name="python"></a>[Python](#tab/python)

Gegevens worden beschikbaar gesteld voor de functie via een `DocumentList` para meter. Wijzigingen in het document worden niet automatisch doorgevoerd.

# <a name="java"></a>[Java](#tab/java)

Met de [@CosmosDBInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) aantekening in de [runtime bibliotheek van Java-functies](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)worden Cosmos DB gegevens weer gegeven aan de functie. Deze aantekening kan worden gebruikt met systeem eigen Java-typen, Pojo's of nullable-waarden met behulp van `Optional<T>`.

---

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer een Azure Cosmos DB-document wordt gemaakt of gewijzigd (trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Wijzigingen opslaan in een Azure Cosmos DB-document (uitvoer binding)](./functions-bindings-cosmosdb-v2-output.md)