---
title: Azure Cosmos DB-uitvoerbinding voor functies 2.x
description: Leer de Azure Cosmos DB-uitvoerbinding gebruiken in Azure-functies.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: 636903c20e07f11a2fd919654cfaa62037171f20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277763"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x"></a>Azure Cosmos DB-uitvoerbinding voor Azure-functies 2.x

Met de Azure Cosmos DB-uitvoerbinding u een nieuw document schrijven naar een Azure Cosmos DB-database met behulp van de SQL API.

Zie het [overzicht](./functions-bindings-cosmosdb-v2.md)voor informatie over de installatie en configuratiedetails.

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

Deze sectie bevat de volgende voorbeelden:

* [Wachtrijtrigger, één document schrijven](#queue-trigger-write-one-doc-c)
* [Wachtrijtrigger, documenten schrijven met IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c)

De voorbeelden verwijzen naar `ToDoItem` een eenvoudig type:

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

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>Wachtrijtrigger, één document schrijven

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die een document aan een database toevoegt, met behulp van gegevens die zijn verstrekt in het bericht van wachtrijopslag.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wachtrijtrigger, documenten schrijven met IAsyncCollector

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die een verzameling documenten toevoegt aan een database, met behulp van gegevens in een wachtrijbericht JSON.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Deze sectie bevat de volgende voorbeelden:

* [Wachtrijtrigger, één document schrijven](#queue-trigger-write-one-doc-c-script)
* [Wachtrijtrigger, documenten schrijven met IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>Wachtrijtrigger, één document schrijven

In het volgende voorbeeld wordt een Azure Cosmos DB-uitvoerbinding weergegeven in een *function.json-bestand* en een [C#-scriptfunctie](functions-reference-csharp.md) die de binding gebruikt. De functie gebruikt een wachtrijinvoerbinding voor een wachtrij die JSON in de volgende indeling ontvangt:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

De functie maakt Azure Cosmos DB-documenten in de volgende indeling voor elke record:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Hier zijn de bindende gegevens in het *function.json-bestand:*

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is de C# scriptcode:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wachtrijtrigger, documenten schrijven met IAsyncCollector

Als u meerdere documenten wilt `ICollector<T>` `IAsyncCollector<T>` maken, u binden aan of waar `T` een van de ondersteunde typen is.

Dit voorbeeld verwijst `ToDoItem` naar een eenvoudig type:

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

Hier is het function.json bestand:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Hier is de C# scriptcode:

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld wordt een Azure Cosmos DB-uitvoerbinding weergegeven in een *function.json-bestand* en een [JavaScript-functie](functions-reference-node.md) die de binding gebruikt. De functie gebruikt een wachtrijinvoerbinding voor een wachtrij die JSON in de volgende indeling ontvangt:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

De functie maakt Azure Cosmos DB-documenten in de volgende indeling voor elke record:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Hier zijn de bindende gegevens in het *function.json-bestand:*

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is de JavaScript-code:

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voorbeeld wordt uitgelegd hoe u een document naar een Azure CosmosDB-database schrijft als de uitvoer van een functie.

De bindingsdefinitie wordt gedefinieerd in *function.json* waar *het type* is ingesteld op `cosmosDB`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Als u naar de database wilt `set` schrijven, geeft u een documentobject door aan de methode van de databaseparameter.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

* [Wachtrijtrigger, bericht opslaan in database via retourwaarde](#queue-trigger-save-message-to-database-via-return-value-java)
* [HTTP-trigger, één document opslaan in database via retourwaarde](#http-trigger-save-one-document-to-database-via-return-value-java)
* [HTTP-trigger, één document opslaan in database via OutputBinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [HTTP-trigger, meerdere documenten opslaan in database via OutputBinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Wachtrijtrigger, bericht opslaan in database via retourwaarde

In het volgende voorbeeld wordt een Java-functie weergegeven die een document toevoegt aan een database met gegevens uit een bericht in wachtrijopslag.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>HTTP-trigger, één document opslaan in database via retourwaarde

In het volgende voorbeeld wordt een Java-functie ```@CosmosDBOutput``` weergegeven waarvan de ```String```handtekening is geannoteerd en de retourwaarde van het type heeft. Het JSON-document dat door de functie wordt geretourneerd, wordt automatisch naar de bijbehorende CosmosDB-verzameling geschreven.

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>HTTP-trigger, één document opslaan in database via OutputBinding

In het volgende voorbeeld wordt een Java-functie ```OutputBinding<T>``` weergegeven die een document aan CosmosDB schrijft via een uitvoerparameter. In dit voorbeeld ```outputItem``` moet de parameter worden ```@CosmosDBOutput```geannoteerd met , niet de functiehandtekening. Met ```OutputBinding<T>``` behulp van u uw functie gebruik maken van de binding om het document naar CosmosDB te schrijven, terwijl u ook een andere waarde retourneren naar de functiebeller, zoals een JSON- of XML-document.

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>HTTP-trigger, meerdere documenten opslaan in database via OutputBinding

In het volgende voorbeeld wordt een Java-functie ```OutputBinding<T>``` weergegeven die meerdere documenten naar CosmosDB schrijft via een uitvoerparameter. In dit voorbeeld ```outputItem``` wordt de parameter ```@CosmosDBOutput```geannoteerd met , niet de functiehandtekening. De parameter ```outputItem``` uitvoer heeft ```ToDoItem``` een lijst met objecten als sjabloonparametertype. Met ```OutputBinding<T>``` behulp van u uw functie gebruik maken van de binding om de documenten naar CosmosDB te schrijven, terwijl u ook een andere waarde retourneren naar de functiebeller, zoals een JSON- of XML-document.

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@CosmosDBOutput` annotatie op parameters die naar Cosmos DB worden geschreven.  De annotatie parameter ```OutputBinding<T>```type moet worden, waar T is ofwel een native Java-type of een POJO.

---

## <a name="attributes-and-annotations"></a>Kenmerken en annotaties

# <a name="c"></a>[C #](#tab/csharp)

Gebruik in [c#-klassebibliotheken](functions-dotnet-class-library.md)het kenmerk [CosmosDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs)

De constructor van het kenmerk neemt de naam en de verzamelnaam van de database. Zie [Uitvoer - configuratie](#configuration)voor informatie over de instellingen en andere eigenschappen die u configureren. Hier is `CosmosDB` een kenmerkvoorbeeld in een methodehandtekening:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

De `CosmosDBOutput` annotatie is beschikbaar om gegevens te schrijven naar Cosmos DB. U de annotatie toepassen op de functie of op een afzonderlijke functieparameter. Bij gebruik op de functiemethode is de retourwaarde van de functie wat is geschreven naar Cosmos DB. Als u de annotatie met een parameter gebruikt, moet `OutputBinding<T>` het `T` type van de parameter worden gedeclareerd als een plaats waar een native Java-type of een POJO.

---

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `CosmosDB` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type**     | N.v.t. | Moet ingesteld `cosmosDB`zijn op.        |
|**direction**     | N.v.t. | Moet ingesteld `out`zijn op.         |
|**Naam**     | N.v.t. | Naam van de bindingsparameter die het document in de functie vertegenwoordigt.  |
|**Databasenaam** | **DatabaseName**|De database met de verzameling waarin het document wordt gemaakt.     |
|**collectionNaam** |**CollectionName**  | De naam van de verzameling waar het document wordt gemaakt. |
|**makenIfNotExists**  |**Makenals bestaat niet**    | Een booleaanse waarde om aan te geven of de verzameling wordt gemaakt wanneer deze niet bestaat. De standaardinstelling is *false* omdat nieuwe verzamelingen worden gemaakt met gereserveerde doorvoer, wat gevolgen heeft voor de kosten. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) voor meer informatie.  |
|**partitionKey**|**PartitionKey** |Wanneer `CreateIfNotExists` dit waar is, definieert het hoofdpad van de partitievoor de gemaakte verzameling.|
|**CollectieDoorvoer**|**Doorvoer van verzamelingen**| Wanneer `CreateIfNotExists` dit waar is, wordt de [doorvoer](../cosmos-db/set-throughput.md) van de gemaakte verzameling gedefinieerd.|
|**verbindingstekenreeksinstelling**    |**Instelling voor verbindingstekenreeks** |De naam van de app-instelling die uw Azure Cosmos DB-verbindingstekenreeks bevat.        |
|**voorkeurLocaties**| **Voorkeurslocaties**| (Optioneel) Hiermee definieert u voorkeurslocaties (regio's) voor geogerepliceerde databaseaccounts in de Azure Cosmos DB-service. Waarden moeten door komma's worden gescheiden. Bijvoorbeeld "Oost-VS, Zuid-Centraal VS, Noord-Europa". |
|**gebruikenMultipleWriteLocaties**| **GebruikMultipleWriteLocaties**| (Optioneel) Wanneer ingesteld `true` op `PreferredLocations`samen met , kan het gebruik maken van [multi-regio schrijft](../cosmos-db/how-to-manage-database-account.md#configure-multiple-write-regions) in de Azure Cosmos DB-service. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

Wanneer u naar de uitvoerparameter in uw functie schrijft, wordt er standaard een document gemaakt in uw database. Dit document heeft een automatisch gegenereerde GUID als document-id. U de document-id van het `id` uitvoerdocument opgeven door de eigenschap op te geven in het JSON-object dat is doorgegeven aan de uitvoerparameter.

> [!Note]
> Wanneer u de id van een bestaand document opgeeft, wordt deze overschreven door het nieuwe uitvoerdocument.

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding | Naslaginformatie |
|---|---|
| CosmosDB | [CosmosDB-foutcodes](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>host.json-instellingen

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versie 2.x. Zie [host.json-verwijzing naar Azure Functions-versie 2.x voor](functions-host-json.md)meer informatie over algemene configuratie-instellingen in versie 2.x .

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|GatewayMode|Gateway|De verbindingsmodus die door de functie wordt gebruikt bij het maken van verbinding met de Azure Cosmos DB-service. Opties `Direct` zijn en`Gateway`|
|Protocol|Https (https)|Het verbindingsprotocol dat door de functie wordt gebruikt bij de verbinding met de Azure Cosmos DB-service.  Lees [hier een uitleg van beide modi](../cosmos-db/performance-tips.md#networking)|
|leaseVoorvoegsel|N.v.t.|Lease-voorvoegsel voor alle functies in een app.|

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer een Azure Cosmos DB-document wordt gemaakt of gewijzigd (Trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Een Azure Cosmos DB-document lezen (invoerbinding)](./functions-bindings-cosmosdb-v2-input.md)