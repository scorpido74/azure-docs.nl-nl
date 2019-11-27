---
title: Azure Cosmos DB bindingen voor de functies 2. x
description: Over het gebruik van Azure Cosmos DB-triggers en bindingen in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 0ad569977194441b026c2c987ecad544ce40cfa1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227364"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x"></a>Azure Cosmos DB bindingen voor Azure Functions 2. x

> [!div class="op_single_selector" title1="Selecteer de versie van de Azure Functions runtime die u gebruikt: "]
> * [Versie 1:](functions-bindings-cosmosdb.md)
> * [Versie 2](functions-bindings-cosmosdb-v2.md)

In dit artikel wordt uitgelegd hoe u [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) bindingen kunt gebruiken in azure functions 2. x. Azure Functions ondersteunt activeren, invoeren en uitvoerbindingen voor Azure Cosmos DB.

> [!NOTE]
> Dit artikel is voor [Azure functions versie 2. x](functions-versions.md).  Zie [Azure Cosmos DB bindingen voor Azure functions 1. x](functions-bindings-cosmosdb.md)voor informatie over het gebruik van deze bindingen in functions 1. x.
>
> Deze binding heette oorspronkelijk DocumentDB. In functies versie 2. x zijn de trigger, bindingen en pakket alle benoemde Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-apis"></a>Ondersteunde Api's

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="packages---functions-2x"></a>Pakketten - functies 2.x

De Azure Cosmos DB bindingen voor functies versie 2. x zijn opgenomen in het pakket [micro soft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) NuGet, versie 3. x. De bron code voor de bindingen bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK-extensies](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

De Azure Cosmos DB trigger gebruikt de [Azure Cosmos DB wijzigings feed](../cosmos-db/change-feed.md) om te Luis teren naar invoeg toepassingen en updates op meerdere partities. De wijzigingenfeed publiceert invoegingen en updates, niet verwijderen.

## <a name="trigger---example"></a>Trigger - voorbeeld

Zie het voorbeeld taalspecifieke:

* [C#](#trigger---c-example)
* [C#script (. CSX)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

Trigger voorbeelden overs Laan

### <a name="trigger---c-example"></a>Trigger - voorbeeld met C#

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die wordt aangeroepen wanneer er invoeg acties of updates in de opgegeven Data Base en verzameling zijn.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
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
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

Trigger voorbeelden overs Laan

### <a name="trigger---c-script-example"></a>Trigger - voorbeeld van C#-script

In het volgende voor beeld ziet u een binding van een Cosmos DB trigger in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie schrijft logboekberichten wanneer Cosmos DB-records zijn gewijzigd.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

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

Dit is de C#-scriptcode:

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

Trigger voorbeelden overs Laan

### <a name="trigger---javascript-example"></a>Trigger - JavaScript-voorbeeld

In het volgende voor beeld ziet u een Cosmos DB trigger binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie schrijft logboekberichten wanneer Cosmos DB-records zijn gewijzigd.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

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

Dit is de JavaScript-code:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

### <a name="trigger---java-example"></a>Trigger - Java-voorbeeld

In het volgende voor beeld ziet u een binding van het Cosmos DB trigger in het bestand *Function. json* en een [Java-functie](functions-reference-java.md) die gebruikmaakt van de binding. De functie is betrokken bij het invoegen of bijwerken van de opgegeven Data Base en verzameling.

```json
{
    "type": "cosmosDBTrigger",
    "name": "items",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "AzureCosmosDBConnection",
    "databaseName": "ToDoList",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": false
}
```

Dit is de Java-code:

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@CosmosDBTrigger` annotatie op para meters waarvan de waarde afkomstig is van Cosmos db.  Deze aantekening kan worden gebruikt met systeem eigen Java-typen, Pojo's of nullable-waarden met behulp van optionele\<T >.


Trigger voorbeelden overs Laan

### <a name="trigger---python-example"></a>Voor beeld van een trigger-python

In het volgende voor beeld ziet u een binding van Cosmos DB trigger in een *Function. json* -bestand en een [python-functie](functions-reference-python.md) die gebruikmaakt van de binding. De functie schrijft logboekberichten wanneer Cosmos DB-records zijn gewijzigd.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Dit is de python-code:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

## <a name="trigger---c-attributes"></a>Trigger - C# attributes

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)het kenmerk [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) .

De constructor van het kenmerk wordt de databasenaam en verzamelingsnaam. Zie [trigger-configuratie](#trigger---configuration)voor meer informatie over deze instellingen en andere eigenschappen die u kunt configureren. Hier volgt een voor beeld van een `CosmosDBTrigger` kenmerk in een methode handtekening:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

Zie voor een volledig voor beeld [trigger- C# voor beeld](#trigger---c-example).


## <a name="trigger---configuration"></a>Trigger - configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `CosmosDBTrigger`.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** || Moet worden ingesteld op `cosmosDBTrigger`. |
|**direction** || Moet worden ingesteld op `in`. Deze parameter wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt. |
|**naam** || De naam van de variabele die wordt gebruikt in functiecode aan te geven dat de lijst met documenten met wijzigingen vertegenwoordigt. |
|**connectionStringSetting**|**ConnectionStringSetting** | De naam van een app-instelling met de verbindingsreeks waarmee verbinding met de Azure Cosmos DB-account dat wordt bewaakt. |
|**databaseName**|**DatabaseName**  | De naam van de Azure Cosmos DB-database met de verzameling die worden bewaakt. |
|**collectionName** |**CollectionName** | De naam van de verzameling die worden bewaakt. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Optioneel) De naam van een app-instelling met de verbindingsreeks voor de service die de leaseverzameling bevat. Als deze niet wordt ingesteld, wordt de `connectionStringSetting` waarde gebruikt. Deze parameter is automatisch ingesteld wanneer de binding in de portal wordt gemaakt. De verbindingsreeks voor de verzameling leases moet schrijfmachtigingen hebben.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Optioneel) De naam van de database waarin de verzameling die wordt gebruikt voor het opslaan van de leases. Als deze optie niet is ingesteld, wordt de waarde van de instelling `databaseName` gebruikt. Deze parameter is automatisch ingesteld wanneer de binding in de portal wordt gemaakt. |
|**leaseCollectionName** | **LeaseCollectionName** | (Optioneel) De naam van de verzameling die wordt gebruikt voor het opslaan van de leases. Als deze niet wordt ingesteld, wordt de waarde `leases` gebruikt. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Beschrijving Als deze is ingesteld op `true`, wordt de verzameling leases automatisch gemaakt wanneer deze nog niet bestaat. De standaard waarde is `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (Optioneel) Hiermee definieert u de hoeveelheid Aanvraageenheden om toe te wijzen wanneer de verzameling leases wordt gemaakt. Deze instelling wordt alleen gebruikt wanneer `createLeaseCollectionIfNotExists` is ingesteld op `true`. Deze parameter is automatisch ingesteld wanneer de binding wordt gemaakt met behulp van de portal.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| (Optioneel) Als de waarde, wordt een voorvoegsel toegevoegd aan de leases gemaakt in de verzameling van de Lease voor deze functie is effectief zodat twee afzonderlijke Azure-functies voor het delen van dezelfde leaseverzameling met behulp van verschillende voorvoegsels.
|**feedPollDelay**| **FeedPollDelay**| (Optioneel) Wanneer is ingesteld, deze wordt gedefinieerd, in milliseconden, de vertraging tussen het opvragen van configuratiegegevens bij een partitie voor de nieuwe wijzigingen in de feed worden nadat alle huidige wijzigingen geleegd. De standaardwaarde is 5000 (5 seconden).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Optioneel) Als de waarde, deze wordt gedefinieerd, in milliseconden, het interval voor een vliegende start een taak voor het berekenen van als partities gelijkmatig zijn verdeeld over exemplaren bekende hosten. De standaardwaarde is 13000 (13 seconden).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Optioneel) Als de waarde, deze wordt gedefinieerd, in milliseconden, het interval waarvoor de lease op een lease voor een partitie wordt gemaakt. Als de lease niet binnen dit interval wordt vernieuwd, wordt het verlopen en eigendom van de partitie worden verplaatst naar een andere instantie. De standaardwaarde is 60000 (60 seconden).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Optioneel) Als de waarde, deze wordt gedefinieerd, in milliseconden, het vernieuwingsinterval voor alle leases voor partities die momenteel worden vastgehouden door een exemplaar. De standaardwaarde is 17000 (17 seconden).
|**checkpointFrequency**| **CheckpointFrequency**| (Optioneel) Als de waarde, deze wordt gedefinieerd, in milliseconden, het interval tussen de lease controlepunten. De standaard waarde is altijd na elke functie aanroep.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Beschrijving Als deze eigenschap is ingesteld, wordt de maximum hoeveelheid ontvangen items per functie aanroep ingesteld. Als bewerkingen in de bewaakte verzameling worden uitgevoerd via opgeslagen procedures, blijft het [transactie bereik](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) behouden bij het lezen van items van de wijzigings feed. Als gevolg hiervan is het mogelijk dat de hoeveelheid ontvangen items hoger is dan de opgegeven waarde zodat de items die door dezelfde trans actie worden gewijzigd, worden geretourneerd als onderdeel van één atomische batch.
|**startFromBeginning**| **StartFromBeginning**| Beschrijving Wanneer dit is ingesteld, wordt aan de trigger aangegeven dat de wijzigingen moeten worden gelezen vanaf het begin van de geschiedenis van de verzameling in plaats van de huidige tijd. Dit werkt alleen de eerste keer dat de trigger wordt gestart, zoals bij volgende uitvoeringen, de controle punten al zijn opgeslagen. Als u dit instelt op `true` wanneer er al leases zijn gemaakt, heeft dit geen effect.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger - gebruik

Voor de trigger is een tweede verzameling vereist die wordt gebruikt voor het opslaan van _leases_ via de partities. Zowel de verzameling die worden bewaakt en de verzameling waarin de leases moet beschikbaar zijn voor de trigger om te werken.

>[!IMPORTANT]
> Als er meerdere functies zijn geconfigureerd voor het gebruik van een Cosmos DB trigger voor dezelfde verzameling, moeten voor elk van de functies een toegewezen lease verzameling worden gebruikt, of moet voor elke functie een andere `LeaseCollectionPrefix` worden opgegeven. Anders wordt slechts één van de functies worden geactiveerd. Zie de [sectie configuratie](#trigger---configuration)voor meer informatie over het voor voegsel.

De trigger wordt niet aangegeven dat of een document is bijgewerkt of ingevoegd, biedt deze alleen het document zelf. Als u nodig hebt voor het afhandelen van updates en toevoegingen anders, kunt u dat doen door het implementeren van timestamp velden voor het invoegen of bijwerken.

## <a name="input"></a>Invoer

De Azure Cosmos DB-Invoerbinding maakt gebruik van de SQL-API om een of meer Azure Cosmos DB-documenten te halen en geeft deze door aan de invoerparameter van de functie. De document-ID of query parameters kunnen worden bepaald op basis van de trigger die de functie activeert.

> [!NOTE]
> Als de verzameling is [gepartitioneerd](../cosmos-db/partition-data.md#logical-partitions), moeten opzoek bewerkingen ook de partitie sleutel waarde opgeven.
>

## <a name="input---examples"></a>Invoer - voorbeelden

Zie de voorbeelden taalspecifieke die één document worden gelezen door een id-waarde op te geven:

* [C#](#input---c-examples)
* [C#script (. CSX)](#input---c-script-examples)
* [F#](#input---f-examples)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-examples)
* [Python](#input---python-examples)

[Invoer voorbeelden overs Laan](#input---attributes)

### <a name="input---c-examples"></a>Invoer - C#-voorbeelden

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

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Wachtrijtrigger, zoekt u de ID van JSON (C#)

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die één document ophaalt. De functie wordt geactiveerd door een wachtrijbericht met een JSON-object. De wachtrij trigger parseert de JSON naar een object met de naam `ToDoItemLookup`, dat de ID en de partitie sleutel waarde bevat die moet worden gezocht. Deze ID en partitie sleutel waarde worden gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

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

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>HTTP-trigger, uiterlijk-id van de query-tekenreeks (C#)

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

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>HTTP-trigger, ID opzoeken van routegegevens (C#)

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

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>HTTP-trigger, ID opzoeken van routegegevens, met behulp van SqlQuery (C#)

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

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>HTTP activeren, krijgen meerdere documenten, met behulp van SqlQuery (C#)

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

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP activeren, krijgen meerdere documenten, met behulp van DocumentClient (C#)

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

[Invoer voorbeelden overs Laan](#input---attributes)

### <a name="input---c-script-examples"></a>Invoer - voorbeelden van C#-script

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

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-string-c-script"></a>Wachtrijtrigger, zoekt u ID van een tekenreeks (C#-script)

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
In de [configuratie](#input---configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C#-scriptcode:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Trigger in de wachtrij, het ophalen van meerdere documenten, met behulp van SqlQuery (C#-script)

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

In de [configuratie](#input---configuration) sectie worden deze eigenschappen uitgelegd.

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

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c-script"></a>HTTP-trigger, uiterlijk-id van de query-tekenreeks (C#-script)

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

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c-script"></a>HTTP-trigger, ID opzoeken van gegevens routeren (C#-script)

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

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>HTTP activeren, krijgen meerdere documenten, met behulp van SqlQuery (C#-script)

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

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>HTTP activeren, krijgen meerdere documenten, met behulp van DocumentClient (C#-script)

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

[Invoer voorbeelden overs Laan](#input---attributes)

### <a name="input---javascript-examples"></a>Invoer - JavaScript-voorbeelden

Deze sectie bevat de volgende voor beelden van het lezen van één document door een ID-waarde op te geven uit verschillende bronnen:

* [Wachtrij trigger, ID opzoeken vanuit JSON](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP-trigger, ID opzoeken vanuit query reeks](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP-trigger, ID van route gegevens opzoeken](#http-trigger-look-up-id-from-route-data-javascript)
* [Wachtrij trigger, meerdere documenten ophalen met behulp van SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-javascript"></a>Wachtrijtrigger, zoekt u de ID van JSON (JavaScript)

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
In de [configuratie](#input---configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de JavaScript-code:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-javascript"></a>HTTP-trigger, uiterlijk-id van de query-tekenreeks (JavaScript)

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

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-javascript"></a>HTTP-trigger, uiterlijk-id van het routeren van gegevens (JavaScript)

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

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>Trigger in de wachtrij, het ophalen van meerdere documenten, met behulp van SqlQuery (JavaScript)

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

In de [configuratie](#input---configuration) sectie worden deze eigenschappen uitgelegd.

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

[Invoer voorbeelden overs Laan](#input---attributes)

### <a name="input---python-examples"></a>Invoer-python-voor beelden

Deze sectie bevat de volgende voor beelden van het lezen van één document door een ID-waarde op te geven uit verschillende bronnen:

* [Wachtrij trigger, ID opzoeken vanuit JSON](#queue-trigger-look-up-id-from-json-python)
* [HTTP-trigger, ID opzoeken vanuit query reeks](#http-trigger-look-up-id-from-query-string-python)
* [HTTP-trigger, ID van route gegevens opzoeken](#http-trigger-look-up-id-from-route-data-python)
* [Wachtrij trigger, meerdere documenten ophalen met behulp van SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-python"></a>Wachtrij trigger, ID opzoeken vanuit JSON (python)

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

In de [configuratie](#input---configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de python-code:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-python"></a>HTTP-trigger, ID opzoeken vanuit query reeks (python)

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

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-python"></a>HTTP-trigger, ID opzoeken op basis van route gegevens (python)

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

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>Wachtrij trigger, meerdere documenten ophalen met behulp van SqlQuery (python)

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

In de [configuratie](#input---configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de python-code:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```


[Invoer voorbeelden overs Laan](#input---attributes)

<a name="infsharp"></a>

### <a name="input---f-examples"></a>Invoer - F# voorbeelden

In het volgende voor beeld ziet u een Cosmos DB invoer binding in een *Function. json* -bestand en een [ F# functie](functions-reference-fsharp.md) die gebruikmaakt van de binding. De functie leest één document en updates van de tekstwaarde van het document.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

In de [configuratie](#input---configuration) sectie worden deze eigenschappen uitgelegd.

Hier volgt de F# code:

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

In dit voor beeld is een `project.json`-bestand vereist waarin de `FSharp.Interop.Dynamic`-en `Dynamitey` NuGet-afhankelijkheden worden opgegeven:

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

Zie [ F# Package Management](functions-reference-fsharp.md#package)om een `project.json` bestand toe te voegen.

### <a name="input---java-examples"></a>Invoer-java-voor beelden

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

#### <a name="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>HTTP-trigger, ID opzoeken vanuit query reeks-teken reeks parameter (Java)

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

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@CosmosDBInput` annotatie voor functie parameters waarvan de waarde afkomstig is van Cosmos db.  Deze aantekening kan worden gebruikt met systeem eigen Java-typen, Pojo's of nullable-waarden met behulp van optionele\<T >.

#### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>HTTP-trigger, ID opzoeken vanuit query teken reeks-POJO para meter (Java)

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

#### <a name="http-trigger-look-up-id-from-route-data-java"></a>HTTP-trigger, ID opzoeken op basis van route gegevens (Java)

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

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>HTTP-trigger, ID opzoeken op basis van route gegevens, met behulp van SqlQuery (Java)

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

#### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>HTTP-trigger, meerdere documenten ophalen van route gegevens, met behulp van SqlQuery (Java)

In het volgende voor beeld ziet u een Java-functie die meerdere documenten bevat. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van een route parameter ```desc``` om de teken reeks op te geven waarnaar u wilt zoeken in het veld ```description```. De zoek term wordt gebruikt om een verzameling documenten op te halen uit de opgegeven Data Base en verzameling, waarbij de resultatenset wordt geconverteerd naar een ```ToDoItem[]``` en wordt door gegeven als argument voor de functie.

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

## <a name="input---attributes"></a>Invoer - kenmerken

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)het kenmerk [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) .

De constructor van het kenmerk wordt de databasenaam en verzamelingsnaam. Zie [de volgende configuratie sectie](#input---configuration)voor informatie over de instellingen en andere eigenschappen die u kunt configureren.

## <a name="input---configuration"></a>Invoer - configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `CosmosDB`.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type**     || Moet worden ingesteld op `cosmosDB`.        |
|**direction**     || Moet worden ingesteld op `in`.         |
|**naam**     || De naam van de bindingsparameter die het document in de functie vertegenwoordigt.  |
|**databaseName** |**DatabaseName** |De database met het document.        |
|**collectionName** |**CollectionName** | De naam van de verzameling waarin het document. |
|**id**    | **Id** | De ID van het document om op te halen. Deze eigenschap ondersteunt [bindings expressies](./functions-bindings-expressions-patterns.md). Stel de eigenschappen **id** en **sqlQuery** niet in. Als u niet uit, één instelt, wordt de volledige verzameling worden opgehaald. |
|**sqlQuery**  |**SqlQuery**  | Een Azure Cosmos DB SQL-query die wordt gebruikt voor het ophalen van meerdere documenten. De eigenschap ondersteunt runtime bindingen, zoals in dit voor beeld: `SELECT * FROM c where c.departmentId = {departmentId}`. Stel de eigenschappen **id** en **sqlQuery** niet in. Als u niet uit, één instelt, wordt de volledige verzameling worden opgehaald.|
|**connectionStringSetting**     |**ConnectionStringSetting**|De naam van de app-instelling met daarin uw Azure Cosmos DB-verbindingsreeks.        |
|**partitionKey**|**PartitionKey**|Hiermee geeft u de waarde voor de partitiesleutel voor de zoekopdracht. Kan omvatten bindende parameters. Dit is vereist voor zoek acties in [gepartitioneerde](../cosmos-db/partition-data.md#logical-partitions) verzamelingen.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Invoer - gebruik

In C# en F# functioneert, wanneer de functie wordt afgesloten, alle wijzigingen aan het ingevoerde document via invoer van de benoemde parameters worden automatisch doorgevoerd.

In JavaScript-functies worden niet automatisch updates aangebracht bij afsluiten van de functie. Gebruik in plaats daarvan `context.bindings.<documentName>In` en `context.bindings.<documentName>Out` om updates te maken. Zie het Java script-voor beeld.

## <a name="output"></a>Uitvoer

De Azure Cosmos DB-uitvoer binding kunt schrijven u een nieuw document naar een Azure Cosmos DB-database met behulp van de SQL-API.

## <a name="output---examples"></a>Uitvoer - voorbeelden

Zie de voorbeelden taalspecifieke:

* [C#](#output---c-examples)
* [C#script (. CSX)](#output---c-script-examples)
* [F#](#output---f-examples)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-examples)
* [Python](#output---python-examples)

Zie ook het [invoer voorbeeld](#input---c-examples) dat gebruikmaakt van `DocumentClient`.

[Uitvoer voorbeelden overs Laan](#output---attributes)

### <a name="output---c-examples"></a>Uitvoer - C# voorbeelden

Deze sectie bevat de volgende voorbeelden:

* Wachtrijtrigger, één document schrijven
* Wachtrijtrigger, met behulp van IAsyncCollector schrijven-docs

De voor beelden verwijzen naar een eenvoudig `ToDoItem` type:

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

[Uitvoer voorbeelden overs Laan](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>Wachtrijtrigger, schrijven één doc-bestand (C#)

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) waarmee een document wordt toegevoegd aan een Data Base met behulp van gegevens die zijn opgenomen in het bericht uit de wachtrij opslag.

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

[Uitvoer voorbeelden overs Laan](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Wachtrijtrigger, schrijven docs IAsyncCollector (C#) gebruiken

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) waarmee een verzameling documenten wordt toegevoegd aan een Data Base, met behulp van gegevens die zijn opgenomen in een JSON van een wachtrij bericht.

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

[Uitvoer voorbeelden overs Laan](#output---attributes)

### <a name="output---c-script-examples"></a>Uitvoer - voorbeelden van C#-script

Deze sectie bevat de volgende voorbeelden:

* Wachtrijtrigger, één document schrijven
* Wachtrijtrigger, met behulp van IAsyncCollector schrijven-docs

[Uitvoer voorbeelden overs Laan](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c-script"></a>Wachtrijtrigger, schrijven één document (C#-script)

In het volgende voor beeld ziet u een Azure Cosmos DB uitvoer binding in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie maakt gebruik van een wachtrij-Invoerbinding voor een wachtrij die JSON in de volgende indeling ontvangt:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

De functie wordt gemaakt van Azure Cosmos DB-documenten in de volgende indeling voor elke record:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Hier vindt u de bindings gegevens in het bestand *Function. json* :

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

In de [configuratie](#output---configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C#-scriptcode:

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

#### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wachtrijtrigger, met behulp van IAsyncCollector schrijven-docs

Als u meerdere documenten wilt maken, kunt u binden aan `ICollector<T>` of `IAsyncCollector<T>` waarbij `T` een van de ondersteunde typen is.

In dit voor beeld wordt naar een eenvoudig `ToDoItem` type verwezen:

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

Dit is het bestand function.json:

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

Dit is de C#-scriptcode:

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

[Uitvoer voorbeelden overs Laan](#output---attributes)

### <a name="output---javascript-examples"></a>Uitvoer - JavaScript-voorbeelden

In het volgende voor beeld ziet u een Azure Cosmos DB uitvoer binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie maakt gebruik van een wachtrij-Invoerbinding voor een wachtrij die JSON in de volgende indeling ontvangt:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

De functie wordt gemaakt van Azure Cosmos DB-documenten in de volgende indeling voor elke record:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Hier vindt u de bindings gegevens in het bestand *Function. json* :

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

In de [configuratie](#output---configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de JavaScript-code:

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

[Uitvoer voorbeelden overs Laan](#output---attributes)

### <a name="output---f-examples"></a>Uitvoer - F# voorbeelden

In het volgende voor beeld ziet u een Azure Cosmos DB uitvoer binding in een *Function. json* -bestand en een [ F# functie](functions-reference-fsharp.md) die gebruikmaakt van de binding. De functie maakt gebruik van een wachtrij-Invoerbinding voor een wachtrij die JSON in de volgende indeling ontvangt:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

De functie wordt gemaakt van Azure Cosmos DB-documenten in de volgende indeling voor elke record:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Hier vindt u de bindings gegevens in het bestand *Function. json* :

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
In de [configuratie](#output---configuration) sectie worden deze eigenschappen uitgelegd.

Hier volgt de F# code:

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json
    open Microsoft.Extensions.Logging

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: ILogger) =
      log.LogInformation(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

In dit voor beeld is een `project.json`-bestand vereist waarin de `FSharp.Interop.Dynamic`-en `Dynamitey` NuGet-afhankelijkheden worden opgegeven:

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

Zie [ F# Package Management](functions-reference-fsharp.md#package)om een `project.json` bestand toe te voegen.

### <a name="output---java-examples"></a>Uitvoer-Java-voor beelden

* [Wachtrij trigger, bericht opslaan in data base via retour waarde](#queue-trigger-save-message-to-database-via-return-value-java)
* [HTTP-trigger, sla het ene document op in de data base via de retour waarde](#http-trigger-save-one-document-to-database-via-return-value-java)
* [HTTP-trigger, sla één document op in de data base via OutputBinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [HTTP-trigger, meerdere documenten opslaan in de data base via OutputBinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


#### <a name="queue-trigger-save-message-to-database-via-return-value-java"></a>Wachtrij trigger, bericht opslaan in data base via retour waarde (Java)

In het volgende voor beeld ziet u een Java-functie waarmee een document wordt toegevoegd aan een Data Base met gegevens uit een bericht in de wachtrij opslag.

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

#### <a name="http-trigger-save-one-document-to-database-via-return-value-java"></a>HTTP-trigger, een document opslaan in de data base via de retour waarde (Java)

In het volgende voor beeld ziet u een Java-functie waarvan de hand tekening is voorzien van ```@CosmosDBOutput``` en de retour waarde van het type ```String```heeft. Het JSON-document dat door de functie wordt geretourneerd, wordt automatisch naar de bijbehorende CosmosDB-verzameling geschreven.

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

#### <a name="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>HTTP-trigger, een document opslaan in de data base via OutputBinding (Java)

In het volgende voor beeld ziet u een Java-functie die een document schrijft naar CosmosDB via een ```OutputBinding<T>``` output-para meter. Houd er rekening mee dat in deze installatie de para meter ```outputItem``` is die moet worden gekoppeld aan ```@CosmosDBOutput```, niet de functie handtekening. Met behulp van ```OutputBinding<T>``` kan uw functie profiteren van de binding om het document naar CosmosDB te schrijven terwijl er ook een andere waarde kan worden geretourneerd aan de functie aanroeper, zoals een JSON-of XML-document.

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

#### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>HTTP-trigger, meerdere documenten opslaan in de data base via OutputBinding (Java)

In het volgende voor beeld ziet u een Java-functie die meerdere documenten naar CosmosDB schrijft via een ```OutputBinding<T>``` uitvoer parameter. Houd er rekening mee dat in deze installatie de para meter ```outputItem``` is die moet worden gekoppeld aan ```@CosmosDBOutput```, niet de functie handtekening. De para meter output bevat ```outputItem``` een lijst met ```ToDoItem```-objecten als het parameter type van de sjabloon. Met behulp van ```OutputBinding<T>``` kan uw functie profiteren van de binding om de documenten te schrijven naar CosmosDB, terwijl ook een andere waarde kan worden geretourneerd aan de functie aanroeper, zoals een JSON-of XML-document.

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

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@CosmosDBOutput` annotatie voor para meters die worden geschreven naar Cosmos db.  Het parameter type aantekening moet ```OutputBinding<T>```zijn, waarbij T een systeem eigen Java-type of een POJO is.

### <a name="output---python-examples"></a>Uitvoer-python-voor beelden

In het volgende voor beeld ziet u hoe u een document naar een Azure CosmosDB-data base schrijft als uitvoer van een functie.

De bindings definitie is gedefinieerd in *Function. json* waarbij *type* is ingesteld op `cosmosDB`.

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

Als u naar de Data Base wilt schrijven, geeft u een document object door aan de methode `set` van de para meter data base.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

## <a name="output---attributes"></a>Uitvoer - kenmerken

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)het kenmerk [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) .

De constructor van het kenmerk wordt de databasenaam en verzamelingsnaam. Zie [uitvoer-configuratie](#output---configuration)voor meer informatie over deze instellingen en andere eigenschappen die u kunt configureren. Hier volgt een voor beeld van een `CosmosDB` kenmerk in een methode handtekening:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Zie voor een volledig voor beeld uitvoer- C# voor beeld.

## <a name="output---configuration"></a>Uitvoer - configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `CosmosDB`.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type**     || Moet worden ingesteld op `cosmosDB`.        |
|**direction**     || Moet worden ingesteld op `out`.         |
|**naam**     || De naam van de bindingsparameter die het document in de functie vertegenwoordigt.  |
|**databaseName** | **DatabaseName**|De database met de verzameling waarin het document wordt gemaakt.     |
|**collectionName** |**CollectionName**  | De naam van de verzameling waarin het document wordt gemaakt. |
|**createIfNotExists**  |**CreateIfNotExists**    | Een Booleaanse waarde om aan te geven of de verzameling is gemaakt als deze nog niet bestaat. De standaard waarde is *False* omdat nieuwe verzamelingen worden gemaakt met gereserveerde door Voer, wat gevolgen heeft voor de kosten. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) voor meer informatie.  |
|**partitionKey**|**PartitionKey** |Als `CreateIfNotExists` waar is, definieert het pad van de partitie sleutel voor de gemaakte verzameling.|
|**collectionThroughput**|**CollectionThroughput**| Als `CreateIfNotExists` waar is, definieert de [door Voer](../cosmos-db/set-throughput.md) van de gemaakte verzameling.|
|**connectionStringSetting**    |**ConnectionStringSetting** |De naam van de app-instelling met daarin uw Azure Cosmos DB-verbindingsreeks.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uitvoer - gebruik

Wanneer u naar de output-parameter in de functie schrijft wordt een document standaard gemaakt in uw database. Dit document bevat een automatisch gegenereerde GUID als de document-ID. U kunt de document-ID van het uitvoer document opgeven door de eigenschap `id` op te geven in het JSON-object dat wordt door gegeven aan de uitvoer parameter.

> [!Note]
> Wanneer u de ID van een bestaand document opgeeft, wordt deze overschreven door het nieuwe uitvoerdocument.

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding | Naslaginformatie |
|---|---|
| CosmosDB | [CosmosDB-fout codes](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>instellingen voor host.JSON

In deze sectie beschrijft de globale configuratie-instellingen beschikbaar voor deze binding in versie 2.x. Zie voor meer informatie over globale configuratie-instellingen in versie 2. x [host. json Reference voor Azure functions versie 2. x](functions-host-json.md).

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
|GatewayMode|Gateway|De verbindings modus die wordt gebruikt door de functie bij het maken van verbinding met de Azure Cosmos DB-service. Opties zijn `Direct` en `Gateway`|
|Protocol|Https|Het verbindings protocol dat door de functie wordt gebruikt bij het verbinden met de Azure Cosmos DB-service.  Lees [hier voor een uitleg van beide modi](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|N.v.t.|Het lease voorvoegsel dat moet worden gebruikt voor alle functies in een app.|

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over database computing zonder server met Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
