---
title: Azure Cosmos DB-invoerbinding voor functies 2.x
description: Leer de Azure Cosmos DB-invoerbinding gebruiken in Azure-functies.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: eabcf40e28927919215979ccc46fa029d19adbfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943428"
---
# <a name="azure-cosmos-db-input-binding-for-azure-functions-2x"></a>Azure Cosmos DB-invoerbinding voor Azure-functies 2.x

Bij een Azure Cosmos DB-invoerbinding wordt de SQL-API gebruikt voor het ophalen van een of meer Azure Cosmos DB-documenten en het doorgeven daarvan aan de invoerparameter van de functie. De document-id of queryparameters kunnen worden bepaald op basis van de trigger waarmee de functie wordt geactiveerd.

Zie het [overzicht](./functions-bindings-cosmosdb-v2.md)voor informatie over de installatie en configuratiedetails.

> [!NOTE]
> Als de verzameling is [verdeeld,](../cosmos-db/partition-data.md#logical-partitions)moeten opzoekbewerkingen ook de waarde van de partitiesleutel opgeven.
>

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

Deze sectie bevat de volgende voorbeelden:

* [Wachtrijtrigger, opzoeken ID van JSON](#queue-trigger-look-up-id-from-json-c)
* [HTTP-trigger, opzoeken id van querytekenreeks](#http-trigger-look-up-id-from-query-string-c)
* [HTTP-trigger, zoek ID op uit routegegevens](#http-trigger-look-up-id-from-route-data-c)
* [HTTP trigger, look up ID from route data, using SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP trigger, get multiple docs, using SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP-trigger, meerdere documenten downloaden met DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

De voorbeelden verwijzen naar `ToDoItem` een eenvoudig type:

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

### <a name="queue-trigger-look-up-id-from-json"></a>Wachtrijtrigger, opzoeken ID van JSON 

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die één document ophaalt. De functie wordt geactiveerd door een wachtrijbericht met een JSON-object. De wachtrijtrigger ontleden de JSON `ToDoItemLookup`in een object van type , dat de waarde id en partitiesleutel bevat om op te zoeken. Die id- en partitiesleutelwaarde `ToDoItem` worden gebruikt om een document op te halen uit de opgegeven database en verzameling.

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

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, opzoeken id van querytekenreeks

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-verzoek dat een querytekenreeks gebruikt om de waarde van de id- en partitiesleutel op te geven om op te zoeken. Die id- en partitiesleutelwaarde `ToDoItem` worden gebruikt om een document op te halen uit de opgegeven database en verzameling.

>[!NOTE]
>De parameter HTTP-querytekenreeks is hoofdlettergevoelig.
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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, zoek ID op uit routegegevens

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-verzoek dat routegegevens gebruikt om de waarde van de id- en partitiesleutel op te geven om op te zoeken. Die id- en partitiesleutelwaarde `ToDoItem` worden gebruikt om een document op te halen uit de opgegeven database en verzameling.

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

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP trigger, look up ID from route data, using SqlQuery

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-verzoek dat routegegevens gebruikt om de id op te geven die moet worden weergegeven om op te zoeken. Die ID wordt gebruikt `ToDoItem` om een document op te halen uit de opgegeven database en verzameling.

In het voorbeeld ziet u hoe `SqlQuery` u een bindende expressie in de parameter gebruikt. U routegegevens `SqlQuery` doorgeven aan de parameter zoals weergegeven, maar op dit moment [kunt u de querytekenreekswaarden niet doorgeven.](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583)

> [!NOTE]
> Als u alleen de ID moet opvragen, wordt aanbevolen om een opzoekte te gebruiken, zoals de [vorige voorbeelden,](#http-trigger-look-up-id-from-query-string-c)omdat het minder [aanvraageenheden](../cosmos-db/request-units.md)verbruikt. Point read operations (GET) zijn [efficiënter](../cosmos-db/optimize-cost-queries.md) dan query's per ID.
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

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP trigger, get multiple docs, using SqlQuery

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die een lijst met documenten ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag. De query is `SqlQuery` opgegeven in de eigenschap attribute.

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

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP-trigger, meerdere documenten downloaden met DocumentClient

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die een lijst met documenten ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag. De code `DocumentClient` gebruikt een instantie die wordt geleverd door de Azure Cosmos DB-binding om een lijst met documenten te lezen. De `DocumentClient` instantie kan ook worden gebruikt voor schrijfbewerkingen.

> [!NOTE]
> U ook de [IDocumentClient-interface](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet) gebruiken om het testen eenvoudiger te maken.

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Deze sectie bevat de volgende voorbeelden:

* [Wachtrijtrigger, opzoeken ID van tekenreeks](#queue-trigger-look-up-id-from-string-c-script)
* [Wachtrijtrigger, meerdere documenten opvragen met SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-trigger, opzoeken id van querytekenreeks](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP-trigger, zoek ID op uit routegegevens](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP trigger, get multiple docs, using SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-trigger, meerdere documenten downloaden met DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

De voorbeelden van http-triggers verwijzen naar een eenvoudig `ToDoItem` type:

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

### <a name="queue-trigger-look-up-id-from-string"></a>Wachtrijtrigger, opzoeken ID van tekenreeks

In het volgende voorbeeld wordt een Cosmos DB-invoerbinding weergegeven in een *function.json-bestand* en een [C#-scriptfunctie](functions-reference-csharp.md) die de binding gebruikt. De functie leest één document en werkt de tekstwaarde van het document bij.

Hier zijn de bindende gegevens in het *function.json-bestand:*

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
In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is de C# scriptcode:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Wachtrijtrigger, meerdere documenten opvragen met SqlQuery

In het volgende voorbeeld wordt een Azure Cosmos DB-invoerbinding weergegeven in een *function.json-bestand* en een [C#-scriptfunctie](functions-reference-csharp.md) die de binding gebruikt. De functie haalt meerdere documenten op die zijn opgegeven door een SQL-query, met behulp van een wachtrijtrigger om de queryparameters aan te passen.

De wachtrijtrigger biedt `departmentId`een parameter . Een wachtrijbericht `{ "departmentId" : "Finance" }` van zou alle records voor de financiële afdeling retourneren.

Hier zijn de bindende gegevens in het *function.json-bestand:*

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

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is de C# scriptcode:

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

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, opzoeken id van querytekenreeks

In het volgende voorbeeld wordt een [C#-scriptfunctie](functions-reference-csharp.md) weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-verzoek dat een querytekenreeks gebruikt om de waarde van de id- en partitiesleutel op te geven om op te zoeken. Die id- en partitiesleutelwaarde `ToDoItem` worden gebruikt om een document op te halen uit de opgegeven database en verzameling.

Hier is het *function.json* bestand:

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

Hier is de C# scriptcode:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, zoek ID op uit routegegevens

In het volgende voorbeeld wordt een [C#-scriptfunctie](functions-reference-csharp.md) weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-verzoek dat routegegevens gebruikt om de waarde van de id- en partitiesleutel op te geven om op te zoeken. Die id- en partitiesleutelwaarde `ToDoItem` worden gebruikt om een document op te halen uit de opgegeven database en verzameling.

Hier is het *function.json* bestand:

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

Hier is de C# scriptcode:

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

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP trigger, get multiple docs, using SqlQuery

In het volgende voorbeeld wordt een [C#-scriptfunctie](functions-reference-csharp.md) weergegeven die een lijst met documenten ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag. De query is `SqlQuery` opgegeven in de eigenschap attribute.

Hier is het *function.json* bestand:

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

Hier is de C# scriptcode:

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

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP-trigger, meerdere documenten downloaden met DocumentClient

In het volgende voorbeeld wordt een [C#-scriptfunctie](functions-reference-csharp.md) weergegeven die een lijst met documenten ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag. De code `DocumentClient` gebruikt een instantie die wordt geleverd door de Azure Cosmos DB-binding om een lijst met documenten te lezen. De `DocumentClient` instantie kan ook worden gebruikt voor schrijfbewerkingen.

Hier is het *function.json* bestand:

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

Hier is de C# scriptcode:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

Deze sectie bevat de volgende voorbeelden die één document lezen door een ID-waarde uit verschillende bronnen op te geven:

* [Wachtrijtrigger, opzoeken ID van JSON](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP-trigger, opzoeken id van querytekenreeks](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP-trigger, zoek ID op uit routegegevens](#http-trigger-look-up-id-from-route-data-javascript)
* [Wachtrijtrigger, meerdere documenten opvragen met SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Wachtrijtrigger, opzoeken ID van JSON

In het volgende voorbeeld wordt een Cosmos DB-invoerbinding weergegeven in een *function.json-bestand* en een [JavaScript-functie](functions-reference-node.md) die de binding gebruikt. De functie leest één document en werkt de tekstwaarde van het document bij.

Hier zijn de bindende gegevens in het *function.json-bestand:*

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

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is de JavaScript-code:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, opzoeken id van querytekenreeks

In het volgende voorbeeld wordt een [JavaScript-functie](functions-reference-node.md) weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-verzoek dat een querytekenreeks gebruikt om de waarde van de id- en partitiesleutel op te geven om op te zoeken. Die id- en partitiesleutelwaarde `ToDoItem` worden gebruikt om een document op te halen uit de opgegeven database en verzameling.

Hier is het *function.json* bestand:

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

Hier is de JavaScript-code:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, zoek ID op uit routegegevens

In het volgende voorbeeld wordt een [JavaScript-functie](functions-reference-node.md) weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-verzoek dat routegegevens gebruikt om de waarde van de id- en partitiesleutel op te geven om op te zoeken. Die id- en partitiesleutelwaarde `ToDoItem` worden gebruikt om een document op te halen uit de opgegeven database en verzameling.

Hier is het *function.json* bestand:

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

Hier is de JavaScript-code:

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

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Wachtrijtrigger, meerdere documenten opvragen met SqlQuery

In het volgende voorbeeld wordt een Azure Cosmos DB-invoerbinding weergegeven in een *function.json-bestand* en een [JavaScript-functie](functions-reference-node.md) die de binding gebruikt. De functie haalt meerdere documenten op die zijn opgegeven door een SQL-query, met behulp van een wachtrijtrigger om de queryparameters aan te passen.

De wachtrijtrigger biedt `departmentId`een parameter . Een wachtrijbericht `{ "departmentId" : "Finance" }` van zou alle records voor de financiële afdeling retourneren.

Hier zijn de bindende gegevens in het *function.json-bestand:*

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

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is de JavaScript-code:

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

Deze sectie bevat de volgende voorbeelden die één document lezen door een ID-waarde uit verschillende bronnen op te geven:

* [Wachtrijtrigger, opzoeken ID van JSON](#queue-trigger-look-up-id-from-json-python)
* [HTTP-trigger, opzoeken id van querytekenreeks](#http-trigger-look-up-id-from-query-string-python)
* [HTTP-trigger, zoek ID op uit routegegevens](#http-trigger-look-up-id-from-route-data-python)
* [Wachtrijtrigger, meerdere documenten opvragen met SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Wachtrijtrigger, opzoeken ID van JSON

In het volgende voorbeeld ziet u een Cosmos DB-invoerbinding in een *function.json-bestand* en een [Python-functie](functions-reference-python.md) die de binding gebruikt. De functie leest één document en werkt de tekstwaarde van het document bij.

Hier zijn de bindende gegevens in het *function.json-bestand:*

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

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is de Python-code:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, opzoeken id van querytekenreeks

In het volgende voorbeeld wordt een [Python-functie](functions-reference-python.md) weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-verzoek dat een querytekenreeks gebruikt om de waarde van de id- en partitiesleutel op te geven om op te zoeken. Die id- en partitiesleutelwaarde `ToDoItem` worden gebruikt om een document op te halen uit de opgegeven database en verzameling.

Hier is het *function.json* bestand:

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

Hier is de Python-code:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, zoek ID op uit routegegevens

In het volgende voorbeeld wordt een [Python-functie](functions-reference-python.md) weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-verzoek dat routegegevens gebruikt om de waarde van de id- en partitiesleutel op te geven om op te zoeken. Die id- en partitiesleutelwaarde `ToDoItem` worden gebruikt om een document op te halen uit de opgegeven database en verzameling.

Hier is het *function.json* bestand:

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

Hier is de Python-code:

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

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Wachtrijtrigger, meerdere documenten opvragen met SqlQuery

In het volgende voorbeeld wordt een Azure Cosmos DB-invoerbinding weergegeven in een *function.json-bestand* en een [Python-functie](functions-reference-python.md) die de binding gebruikt. De functie haalt meerdere documenten op die zijn opgegeven door een SQL-query, met behulp van een wachtrijtrigger om de queryparameters aan te passen.

De wachtrijtrigger biedt `departmentId`een parameter . Een wachtrijbericht `{ "departmentId" : "Finance" }` van zou alle records voor de financiële afdeling retourneren.

Hier zijn de bindende gegevens in het *function.json-bestand:*

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

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is de Python-code:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

# <a name="java"></a>[Java](#tab/java)

Deze sectie bevat de volgende voorbeelden:

* [HTTP-trigger, opzoeken id van querytekenreeks - Tekenreeksparameter](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [HTTP-trigger, zoek id op van querytekenreeks - POJO-parameter](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [HTTP-trigger, zoek ID op uit routegegevens](#http-trigger-look-up-id-from-route-data-java)
* [HTTP trigger, look up ID from route data, using SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [HTTP-trigger, meerdere documenten ophalen uit routegegevens, met SqlQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

De voorbeelden verwijzen naar `ToDoItem` een eenvoudig type:

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

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>HTTP-trigger, opzoeken id van querytekenreeks - Tekenreeksparameter

In het volgende voorbeeld wordt een Java-functie weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-verzoek dat een querytekenreeks gebruikt om de waarde van de id- en partitiesleutel op te geven om op te zoeken. Die id- en partitiesleutelwaarde worden gebruikt om een document op te halen uit de opgegeven database en verzameling in tekenreeksvorm.

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

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@CosmosDBInput` annotatie op functieparameters waarvan de waarde afkomstig zou zijn van Cosmos DB.  Deze annotatie kan worden gebruikt met native Java-typen, `Optional<T>`POJOs of nullable waarden met behulp van .

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>HTTP-trigger, zoek id op van querytekenreeks - POJO-parameter

In het volgende voorbeeld wordt een Java-functie weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-verzoek dat een querytekenreeks gebruikt om de waarde van de id- en partitiesleutel op te geven om op te zoeken. Die id- en partitiesleutelwaarde die wordt gebruikt om een document op te halen uit de opgegeven database en verzameling. Het document wordt vervolgens geconverteerd naar een instantie van de ```ToDoItem``` POJO die eerder is gemaakt en wordt als argument doorgegeven aan de functie.

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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, zoek ID op uit routegegevens

In het volgende voorbeeld wordt een Java-functie weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-verzoek dat een routeparameter gebruikt om de waarde van de ID- en partitiesleutel op te geven om op te zoeken. Die id- en partitiesleutelwaarde worden gebruikt om een document op ```Optional<String>```te halen uit de opgegeven database en verzameling en het terug te sturen als een .

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

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP trigger, look up ID from route data, using SqlQuery

In het volgende voorbeeld wordt een Java-functie weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-verzoek dat een routeparameter gebruikt om de id op te geven die moet worden weergegeven om op te zoeken. Die id wordt gebruikt om een document op te halen uit ```ToDoItem[]```de opgegeven database en verzameling, waarbij het resultaat dat is ingesteld naar een , wordt geconverteerd, omdat veel documenten kunnen worden geretourneerd, afhankelijk van de querycriteria.

> [!NOTE]
> Als u alleen de ID moet opvragen, wordt aanbevolen om een opzoekte te gebruiken, zoals de [vorige voorbeelden,](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)omdat het minder [aanvraageenheden](../cosmos-db/request-units.md)verbruikt. Point read operations (GET) zijn [efficiënter](../cosmos-db/optimize-cost-queries.md) dan query's per ID.
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

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>HTTP-trigger, meerdere documenten ophalen uit routegegevens, met SqlQuery

In het volgende voorbeeld wordt een Java-functie weergegeven die meerdere documenten ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag waarmee een ```desc``` routeparameter ```description``` wordt gebruikt om de tekenreeks op te geven waarnaar moet worden gezocht in het veld. De zoekterm wordt gebruikt om een verzameling documenten op te halen uit ```ToDoItem[]``` de opgegeven database en verzameling, het resultaat dat is ingesteld op een te converteren en deze als argument door te geven aan de functie.

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

## <a name="attributes-and-annotations"></a>Kenmerken en annotaties

# <a name="c"></a>[C #](#tab/csharp)

Gebruik in [c#-klassebibliotheken](functions-dotnet-class-library.md)het kenmerk [CosmosDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs)

De constructor van het kenmerk neemt de naam en de verzamelnaam van de database. Zie [de volgende configuratiesectie](#configuration)voor informatie over de instellingen en andere eigenschappen die u configureren.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

Gebruik vanuit de [runtime-bibliotheek van javafuncties](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)de `@CosmosDBOutput` annotatie op parameters die naar Cosmos DB schrijven. De annotatie parameter `OutputBinding<T>`type `T` moet worden, waar is ofwel een native Java-type of een POJO.

---

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `CosmosDB` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type**     | N.v.t. | Moet ingesteld `cosmosDB`zijn op.        |
|**direction**     | N.v.t. | Moet ingesteld `in`zijn op.         |
|**Naam**     | N.v.t. | Naam van de bindingsparameter die het document in de functie vertegenwoordigt.  |
|**Databasenaam** |**DatabaseName** |De database met het document.        |
|**collectionNaam** |**CollectionName** | De naam van de verzameling die het document bevat. |
|**id**    | **Id** | De id van het document op te halen. Deze eigenschap ondersteunt [bindende expressies](./functions-bindings-expressions-patterns.md). Stel niet zowel `id` de eigenschappen van SQLQuery als **sqlQuery** in. Als u geen van beide instelt, wordt de hele verzameling opgehaald. |
|**sqlQuery sqlQuery**  |**SqlQuery SqlQuery**  | Een Azure Cosmos DB SQL-query die wordt gebruikt voor het ophalen van meerdere documenten. De eigenschap ondersteunt runtime bindingen, `SELECT * FROM c where c.departmentId = {departmentId}`zoals in dit voorbeeld: . Stel niet zowel `id` de `sqlQuery` eigenschappen en eigenschappen. Als u geen van beide instelt, wordt de hele verzameling opgehaald.|
|**verbindingstekenreeksinstelling**     |**Instelling voor verbindingstekenreeks**|De naam van de app-instelling die uw Azure Cosmos DB-verbindingstekenreeks bevat. |
|**partitionKey**|**PartitionKey**|Hiermee geeft u de waarde van de partitiesleutel voor de opzoeking op. Kan bindende parameters bevatten. Het is vereist voor [partitioned](../cosmos-db/partition-data.md#logical-partitions) lookups in partitieverzamelingen.|
|**voorkeurLocaties**| **Voorkeurslocaties**| (Optioneel) Hiermee definieert u voorkeurslocaties (regio's) voor geogerepliceerde databaseaccounts in de Azure Cosmos DB-service. Waarden moeten door komma's worden gescheiden. Bijvoorbeeld "Oost-VS, Zuid-Centraal VS, Noord-Europa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C #](#tab/csharp)

Wanneer de functie wordt afgesloten, blijven eventuele wijzigingen in het invoerdocument via benoemde invoerparameters automatisch bestaan.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Wanneer de functie wordt afgesloten, blijven eventuele wijzigingen in het invoerdocument via benoemde invoerparameters automatisch bestaan.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Updates worden niet automatisch uitgevoerd bij het verlaten van de functie. In plaats `context.bindings.<documentName>In` `context.bindings.<documentName>Out` daarvan gebruiken en om updates te maken. Zie het Voorbeeld van JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gegevens worden via een `DocumentList` parameter beschikbaar gesteld aan de functie. Wijzigingen in het document blijven niet automatisch bestaan.

# <a name="java"></a>[Java](#tab/java)

Van de [Java-functies runtime bibliotheek](https://docs.microsoft.com/java/api/overview/azure/functions/runtime), de [@CosmosDBInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) annotatie stelt Cosmos DB gegevens aan de functie. Deze annotatie kan worden gebruikt met native Java-typen, `Optional<T>`POJOs of nullable waarden met behulp van .

---

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer een Azure Cosmos DB-document wordt gemaakt of gewijzigd (Trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Wijzigingen opslaan in een Azure Cosmos DB-document (uitvoerbinding)](./functions-bindings-cosmosdb-v2-output.md)