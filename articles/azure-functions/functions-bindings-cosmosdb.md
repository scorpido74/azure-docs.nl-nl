---
title: Azure Cosmos DB-bindingen voor functies 1.x
description: Meer informatie over het gebruik van Azure Cosmos DB-triggers en bindingen in Azure-functies.
author: craigshoemaker
ms.author: cshoe
ms.topic: reference
ms.date: 11/21/2017
ms.custom: seodec18
ms.openlocfilehash: e30b256d9fa43402c3b2c444aa1a0e0dc16cfdcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277542"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Azure Cosmos DB-bindingen voor Azure-functies 1.x

> [!div class="op_single_selector" title1="Selecteer de versie van de runtime van Azure-functies die u gebruikt: "]
> * [Versie 1](functions-bindings-cosmosdb.md)
> * [Versie 2](functions-bindings-cosmosdb-v2.md)

In dit artikel wordt uitgelegd hoe u werken met [Azure Cosmos DB-bindingen](../cosmos-db/serverless-computing-database.md) in Azure-functies. Azure Functions ondersteunt trigger-, invoer- en uitvoerbindingen voor Azure Cosmos DB.

> [!NOTE]
> Dit artikel is voor Azure Functions 1.x. Zie [Azure Cosmos DB-bindingen voor Azure Functions 2.x](functions-bindings-cosmosdb-v2.md)voor informatie over het gebruik van deze bindingen in functies 2.x en hoger.
>
>Deze binding heette oorspronkelijk DocumentDB. In Functies versie 1.x werd alleen de trigger omgedoopt tot Cosmos DB; het invoerbindings-, uitvoerbinding- en NuGet-pakket behoudt de naam DocumentDB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Azure Cosmos DB-bindingen worden alleen ondersteund voor gebruik met de SQL-API. Voor alle andere Azure Cosmos DB API's moet u toegang krijgen tot de database vanuit uw functie met behulp van de statische client voor uw API, inclusief [de API van Azure Cosmos DB voor MongoDB,](../cosmos-db/mongodb-introduction.md)Cassandra [API,](../cosmos-db/cassandra-introduction.md) [Gremlin API](../cosmos-db/graph-introduction.md)en Table [API.](../cosmos-db/table-introduction.md)

## <a name="packages---functions-1x"></a>Pakketten - Functies 1.x

De Azure Cosmos DB-bindingen voor functies versie 1.x worden geleverd in het [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) NuGet-pakket, versie 1.x. Broncode voor de bindingen bevindt zich in de [GitHub-repository van Azure-Webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Trigger

De Azure Cosmos DB Trigger gebruikt de [Azure Cosmos DB Change Feed](../cosmos-db/change-feed.md) om te luisteren naar inserts en updates voor verschillende partities. De wijzigingsfeed publiceert inserts en updates, geen verwijderingen.

## <a name="trigger---example"></a>Trigger - voorbeeld

# <a name="c"></a>[C #](#tab/csharp)

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die wordt aangeroepen wanneer er inserts of updates in de opgegeven database en verzameling zijn.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV1
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            TraceWriter log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.Info($"Documents modified: {documents.Count}");
                log.Info($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld ziet u een Cosmos DB-triggerbinding in een *function.json-bestand* en een [C#-scriptfunctie](functions-reference-csharp.md) die de binding gebruikt. De functie schrijft logboekberichten wanneer Cosmos DB-records worden gewijzigd.

Hier zijn de bindende gegevens in het *function.json-bestand:*

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Hier is de C# scriptcode:

```cs
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
    }
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld ziet u een Cosmos DB-triggerbinding in een *function.json-bestand* en een [JavaScript-functie](functions-reference-node.md) die de binding gebruikt. De functie schrijft logboekberichten wanneer Cosmos DB-records worden gewijzigd.

Hier zijn de bindende gegevens in het *function.json-bestand:*

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Hier is de JavaScript-code:

```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

---

## <a name="trigger---attributes"></a>Trigger - kenmerken

# <a name="c"></a>[C #](#tab/csharp)

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)het kenmerk [CosmosDBTrigger.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs)

De constructor van het kenmerk neemt de naam en de verzamelnaam van de database. Zie [Trigger - configuratie](#trigger---configuration)voor informatie over de instellingen en andere eigenschappen die u configureren. Hier is `CosmosDBTrigger` een kenmerkvoorbeeld in een methodehandtekening:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

Zie Voorbeeld trigger [- C#](#trigger)voor een volledig voorbeeld.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

---

## <a name="trigger---configuration"></a>Trigger - configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `CosmosDBTrigger` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type** | N.v.t. | Moet ingesteld `cosmosDBTrigger`zijn op. |
|**direction** | N.v.t. | Moet ingesteld `in`zijn op. Deze parameter wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal. |
|**Naam** | N.v.t. | De variabele naam die wordt gebruikt in functiecode die de lijst met documenten met wijzigingen vertegenwoordigt. |
|**verbindingstekenreeksinstelling**|**Instelling voor verbindingstekenreeks** | De naam van een app-instelling die de verbindingstekenreeks bevat die wordt gebruikt om verbinding te maken met het Azure Cosmos DB-account dat wordt gecontroleerd. |
|**Databasenaam**|**DatabaseName**  | De naam van de Azure Cosmos DB-database waarbij de verzameling wordt gecontroleerd. |
|**collectionNaam** |**CollectionName** | De naam van de collectie wordt gecontroleerd. |
|**leaseConnectionStringInstelling** | **LeaseConnectionStringInstelling** | (Optioneel) De naam van een app-instelling die de verbindingstekenreeks bevat met de service die de leaseverzameling bevat. Wanneer deze niet `connectionStringSetting` is ingesteld, wordt de waarde gebruikt. Deze parameter wordt automatisch ingesteld wanneer de binding in de portal wordt gemaakt. De verbindingstekenreeks voor de leaseverzameling moet schrijfmachtigingen hebben.|
|**leaseDatabaseName** |**LeaseDatabaseNaam** | (Optioneel) De naam van de database met de verzameling die wordt gebruikt om leases op te slaan. Wanneer deze niet is `databaseName` ingesteld, wordt de waarde van de instelling gebruikt. Deze parameter wordt automatisch ingesteld wanneer de binding in de portal wordt gemaakt. |
|**leaseCollectionName** | **LeaseCollectionName** | (Optioneel) De naam van de collectie die wordt gebruikt om huurcontracten op te slaan. Wanneer deze niet `leases` is ingesteld, wordt de waarde gebruikt. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfBestaat niet** | (Optioneel) Wanneer ingesteld `true`op , wordt de lease-collectie automatisch gemaakt wanneer deze nog niet bestaat. De standaardwaarde is `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionDoorvoer**| (Optioneel) Hiermee definieert u het bedrag van de aanvraageenheden die moeten worden toegewezen wanneer de leaseverzameling wordt gemaakt. Deze instelling wordt `createLeaseCollectionIfNotExists` alleen gebruikt `true`wanneer is ingesteld op . Deze parameter wordt automatisch ingesteld wanneer de binding wordt gemaakt met behulp van de portal.
|**leaseCollectionVoorfix**| **LeaseCollectionVoorvoegsel**| (Optioneel) Wanneer deze is ingesteld, wordt een voorvoegsel toegevoegd aan de leaseovereenkomsten die zijn gemaakt in de Lease-verzameling voor deze functie, waardoor twee afzonderlijke Azure-functies dezelfde Lease-verzameling kunnen delen met behulp van verschillende voorvoegsels.
|**feedPollDelay feedPollDelay**| **FeedPollDelay FeedPollDelay FeedPollDelay FeedPoll**| (Optioneel) Wanneer ingesteld, definieert het, in milliseconden, de vertraging tussen het stemmen van een partitie voor nieuwe wijzigingen in de feed, nadat alle huidige wijzigingen zijn afgevoerd. Standaard is 5000 (5 seconden).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Optioneel) Wanneer ingesteld, definieert het in milliseconden het interval om een taak te starten om te berekenen of partities gelijkmatig worden verdeeld over bekende host-exemplaren. Standaard is 13000 (13 seconden).
|**leaseExpirsinterval**| **LeaseExpiratieinterval**| (Optioneel) Wanneer ingesteld, definieert het in milliseconden het interval waarvoor de lease wordt gemaakt op een lease die een partitie vertegenwoordigt. Als de lease niet binnen dit interval wordt verlengd, vervalt deze en wordt het eigendom van de partitie verplaatst naar een andere instantie. Standaard is 60000 (60 seconden).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Optioneel) Wanneer ingesteld, definieert het in milliseconden het vernieuwingsinterval voor alle leases voor partities die momenteel in het bezit zijn van een instantie. Standaard is 17000 (17 seconden).
|**controlepuntFrequentie**| **ControlepuntFrequentie**| (Optioneel) Wanneer ingesteld, definieert het in milliseconden het interval tussen leasecontrolepunten. Standaard is altijd na elke functieaanroep.
|**maxItemsPerInvocation**| **MaxItemsPerInvocatie**| (Optioneel) Wanneer ingesteld, wordt de maximale hoeveelheid ontvangen items per functieaanroep aangepast.
|**startVanafhet begin**| **Beginvanaf het begin**| (Optioneel) Wanneer deze is ingesteld, wordt de trigger gevraagd om wijzigingen te lezen vanaf het begin van de geschiedenis van de verzameling in plaats van de huidige tijd. Dit werkt alleen de eerste keer dat de Trigger wordt gestart, omdat in volgende runs de controlepunten al zijn opgeslagen. Het instellen `true` van dit op wanneer er reeds gemaakte huurcontracten zijn gemaakt, heeft geen effect.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger - gebruik

De trigger vereist een tweede verzameling die wordt gebruikt om _leases_ op te slaan over de partities. Zowel de collectie die wordt gecontroleerd als de verzameling die de leases bevat, moet beschikbaar zijn om de trigger te laten werken.

>[!IMPORTANT]
> Als meerdere functies zijn geconfigureerd om een Cosmos DB-trigger te gebruiken voor dezelfde verzameling, `LeaseCollectionPrefix` moet elk van de functies een speciale leaseverzameling gebruiken of een andere functie opgeven. Anders wordt slechts één van de functies geactiveerd. Zie de sectie Configuratie voor informatie over het [voorvoegsel](#trigger---configuration).

De trigger geeft niet aan of een document is bijgewerkt of ingevoegd, het biedt alleen het document zelf. Als u updates en inserts anders moet verwerken, u dat doen door tijdstempelvelden te implementeren voor het invoegen of bijwerken.

## <a name="input"></a>Invoer

Bij een Azure Cosmos DB-invoerbinding wordt de SQL-API gebruikt voor het ophalen van een of meer Azure Cosmos DB-documenten en het doorgeven daarvan aan de invoerparameter van de functie. De document-id of queryparameters kunnen worden bepaald op basis van de trigger waarmee de functie wordt geactiveerd.

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
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Wachtrijtrigger, opzoeken ID van JSON

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die één document ophaalt. De functie wordt geactiveerd door een wachtrijbericht met een JSON-object. De wachtrijtrigger ontleden de JSON `ToDoItemLookup`in een object met de naam , dat de ID bevat om omhoog te kijken. Die ID wordt gebruikt `ToDoItem` om een document op te halen uit de opgegeven database en verzameling.

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, opzoeken id van querytekenreeks

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag waarin een querytekenreeks wordt gebruikt om de id op te zoeken. Die ID wordt gebruikt `ToDoItem` om een document op te halen uit de opgegeven database en verzameling.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, zoek ID op uit routegegevens

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-verzoek dat routegegevens gebruikt om de id op te geven die moet worden weergegeven om op te zoeken. Die ID wordt gebruikt `ToDoItem` om een document op te halen uit de opgegeven database en verzameling.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static HttpResponseMessage Run(
            [HttpTrigger(
                AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Invoervoorbeelden overslaan](#input---attributes)

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP trigger, look up ID from route data, using SqlQuery

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-verzoek dat routegegevens gebruikt om de id op te geven die moet worden weergegeven om op te zoeken. Die ID wordt gebruikt `ToDoItem` om een document op te halen uit de opgegeven database en verzameling.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")] IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Invoervoorbeelden overslaan](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP trigger, get multiple docs, using SqlQuery

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die een lijst met documenten ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag. De query is `SqlQuery` opgegeven in de eigenschap attribute.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Invoervoorbeelden overslaan](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP-trigger, meerdere documenten downloaden met DocumentClient (C#)

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die een lijst met documenten ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag. De code `DocumentClient` gebruikt een instantie die wordt geleverd door de Azure Cosmos DB-binding om een lijst met documenten te lezen. De `DocumentClient` instantie kan ook worden gebruikt voor schrijfbewerkingen.

```cs
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System;
using System.Linq;
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
            string searchterm = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
                .Value;

            if (searchterm == null)
            {
                return req.CreateResponse(HttpStatusCode.NotFound);
            }

            log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.Info(result.Description);
                }
            }
            return req.CreateResponse(HttpStatusCode.OK);
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
namespace CosmosDBSamplesV1
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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

In de [configuratiesectie](#input---configuration) worden deze eigenschappen uitgelegd.

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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

In de [configuratiesectie](#input---configuration) worden deze eigenschappen uitgelegd.

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

In het volgende voorbeeld wordt een [C#-scriptfunctie](functions-reference-csharp.md) weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag waarin een querytekenreeks wordt gebruikt om de id op te zoeken. Die ID wordt gebruikt `ToDoItem` om een document op te halen uit de opgegeven database en verzameling.

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
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Hier is de C# scriptcode:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, zoek ID op uit routegegevens

In het volgende voorbeeld wordt een [C#-scriptfunctie](functions-reference-csharp.md) weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-verzoek dat routegegevens gebruikt om de id op te geven die moet worden weergegeven om op te zoeken. Die ID wordt gebruikt `ToDoItem` om een document op te halen uit de opgegeven database en verzameling.

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
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Hier is de C# scriptcode:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
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
      "type": "documentDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
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

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.Info(toDoItem.Description);
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
      "type": "documentDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
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

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.Info(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Deze sectie bevat de volgende voorbeelden:

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

In de [configuratiesectie](#input---configuration) worden deze eigenschappen uitgelegd.

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

In het volgende voorbeeld wordt een [JavaScript-functie](functions-reference-node.md) weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag waarin een querytekenreeks wordt gebruikt om de id op te zoeken. Die ID wordt gebruikt `ToDoItem` om een document op te halen uit de opgegeven database en verzameling.

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
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
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

In het volgende voorbeeld wordt een [JavaScript-functie](functions-reference-node.md) weergegeven die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag waarin een querytekenreeks wordt gebruikt om de id op te zoeken. Die ID wordt gebruikt `ToDoItem` om een document op te halen uit de opgegeven database en verzameling.

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
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

In de [configuratiesectie](#input---configuration) worden deze eigenschappen uitgelegd.

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

---

## <a name="input---attributes"></a>Invoer - kenmerken

# <a name="c"></a>[C #](#tab/csharp)

Gebruik [in klassebibliotheken van C#](functions-dotnet-class-library.md)het kenmerk [DocumentDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs)

De constructor van het kenmerk neemt de naam en de verzamelnaam van de database. Zie [de volgende configuratiesectie](#input---configuration)voor informatie over de instellingen en andere eigenschappen die u configureren.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

---

## <a name="input---configuration"></a>Invoer - configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `DocumentDB` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type**     | N.v.t. | Moet ingesteld `documentdb`zijn op.        |
|**direction**     | N.v.t. | Moet ingesteld `in`zijn op.         |
|**Naam**     | N.v.t. | Naam van de bindingsparameter die het document in de functie vertegenwoordigt.  |
|**Databasenaam** |**DatabaseName** |De database met het document.        |
|**collectionNaam** |**CollectionName** | De naam van de verzameling die het document bevat. |
|**id**    | **Id** | De id van het document op te halen. Deze eigenschap ondersteunt [bindende expressies](./functions-bindings-expressions-patterns.md). Stel niet zowel de **id-** als **sqlQuery-eigenschappen** in. Als u geen van beide instelt, wordt de hele verzameling opgehaald. |
|**sqlQuery sqlQuery**  |**SqlQuery SqlQuery**  | Een Azure Cosmos DB SQL-query die wordt gebruikt voor het ophalen van meerdere documenten. De eigenschap ondersteunt runtime bindingen, `SELECT * FROM c where c.departmentId = {departmentId}`zoals in dit voorbeeld: . Stel niet zowel de **id-** als **sqlQuery-eigenschappen** in. Als u geen van beide instelt, wordt de hele verzameling opgehaald.|
|**verbinding**     |**Instelling voor verbindingstekenreeks**|De naam van de app-instelling die uw Azure Cosmos DB-verbindingstekenreeks bevat.        |
|**partitionKey**|**PartitionKey**|Hiermee geeft u de waarde van de partitiesleutel voor de opzoeking op. Kan bindende parameters bevatten.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Invoer - gebruik

# <a name="c"></a>[C #](#tab/csharp)

Wanneer de functie wordt afgesloten, blijven eventuele wijzigingen in het invoerdocument via benoemde invoerparameters automatisch bestaan.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Wanneer de functie wordt afgesloten, blijven eventuele wijzigingen in het invoerdocument via benoemde invoerparameters automatisch bestaan.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Updates worden niet automatisch uitgevoerd bij het verlaten van de functie. In plaats `context.bindings.<documentName>In` `context.bindings.<documentName>Out` daarvan gebruiken en om updates te maken. Zie het [invoervoorbeeld](#input).

---

## <a name="output"></a>Uitvoer

Met de Azure Cosmos DB-uitvoerbinding u een nieuw document schrijven naar een Azure Cosmos DB-database met behulp van de SQL API.

# <a name="c"></a>[C #](#tab/csharp)

Deze sectie bevat de volgende voorbeelden:

* Wachtrijtrigger, één document schrijven
* Wachtrijtrigger, documenten schrijven met`IAsyncCollector`

De voorbeelden verwijzen naar `ToDoItem` een eenvoudig type:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

### <a name="queue-trigger-write-one-doc"></a>Wachtrijtrigger, één document schrijven

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die een document aan een database toevoegt, met behulp van gegevens die zijn verstrekt in het bericht van wachtrijopslag.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System;

namespace CosmosDBSamplesV1
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            TraceWriter log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.Info($"C# Queue trigger function inserted one row");
            log.Info($"Description={queueMessage}");
        }
    }
}
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wachtrijtrigger, documenten schrijven met IAsyncCollector

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die een verzameling documenten toevoegt aan een database, met behulp van gegevens in een wachtrijbericht JSON.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.Info($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Deze sectie bevat de volgende voorbeelden:

* Wachtrijtrigger, één document schrijven
* Wachtrijtrigger, documenten schrijven met`IAsyncCollector`

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

In de [configuratiesectie](#output---configuration) worden deze eigenschappen uitgelegd.

Hier is de C# scriptcode:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        dynamic employee = JObject.Parse(myQueueItem);

        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wachtrijtrigger, documenten schrijven met IAsyncCollector

Als u meerdere documenten wilt `ICollector<T>` `IAsyncCollector<T>` maken, u binden aan of waar `T` een van de ondersteunde typen is.

Dit voorbeeld verwijst `ToDoItem` naar een eenvoudig type:

```cs
namespace CosmosDBSamplesV1
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
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "documentDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Hier is de C# scriptcode:

```cs
using System;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.Info($"Description={toDoItem.Description}");
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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

In de [configuratiesectie](#output---configuration) worden deze eigenschappen uitgelegd.

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

---

## <a name="output---attributes"></a>Uitvoer - kenmerken

# <a name="c"></a>[C #](#tab/csharp)

Gebruik [in klassebibliotheken van C#](functions-dotnet-class-library.md)het kenmerk [DocumentDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs)

De constructor van het kenmerk neemt de naam en de verzamelnaam van de database. Zie [Uitvoer - configuratie](#output---configuration)voor informatie over de instellingen en andere eigenschappen die u configureren. Hier is `DocumentDB` een kenmerkvoorbeeld in een methodehandtekening:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Zie [Uitvoer voor](#output)een volledig voorbeeld .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

---

## <a name="output---configuration"></a>Uitvoer - configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `DocumentDB` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type**     | N.v.t. | Moet ingesteld `documentdb`zijn op.        |
|**direction**     | N.v.t. | Moet ingesteld `out`zijn op.         |
|**Naam**     | N.v.t. | Naam van de bindingsparameter die het document in de functie vertegenwoordigt.  |
|**Databasenaam** | **DatabaseName**|De database met de verzameling waarin het document wordt gemaakt.     |
|**collectionNaam** |**CollectionName**  | De naam van de verzameling waar het document wordt gemaakt. |
|**makenIfNotExists**  |**Makenals bestaat niet**    | Een booleaanse waarde om aan te geven of de verzameling wordt gemaakt wanneer deze niet bestaat. De standaardinstelling is *false* omdat nieuwe verzamelingen worden gemaakt met gereserveerde doorvoer, wat gevolgen heeft voor de kosten. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/documentdb/) voor meer informatie.  |
|**partitionKey**|**PartitionKey** |Wanneer `CreateIfNotExists` is waar, definieert u het hoofdpad van de partitievoor de gemaakte verzameling.|
|**CollectieDoorvoer**|**Doorvoer van verzamelingen**| Wanneer `CreateIfNotExists` is waar, definieert de [doorvoer](../cosmos-db/set-throughput.md) van de gemaakte verzameling.|
|**verbinding**    |**Instelling voor verbindingstekenreeks** |De naam van de app-instelling die uw Azure Cosmos DB-verbindingstekenreeks bevat.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uitvoer - gebruik

Wanneer u naar de uitvoerparameter in uw functie schrijft, wordt er standaard een document gemaakt in uw database. Dit document heeft een automatisch gegenereerde GUID als document-id. U de document-id van het `id` uitvoerdocument opgeven door de eigenschap op te geven in het JSON-object dat is doorgegeven aan de uitvoerparameter.

> [!Note]
> Wanneer u de id van een bestaand document opgeeft, wordt deze overschreven door het nieuwe uitvoerdocument.

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding | Naslaginformatie |
|---|---|
| CosmosDB | [CosmosDB-foutcodes](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over serverless database computing met Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Meer informatie over triggers en bindingen voor Azure-functies](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
