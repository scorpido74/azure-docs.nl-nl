---
title: Azure Cosmos DB bindingen voor de functies 2. x
description: Meer informatie over het gebruik van Azure Cosmos DB triggers en bindingen in Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, functies, gebeurtenis verwerking, dynamische compute, serverloze architectuur
ms.service: azure-functions
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: d8aee88f6ef3f6a73beadfdf242d79d9b361de0a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469387"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x"></a>Azure Cosmos DB bindingen voor Azure Functions 2. x

> [!div class="op_single_selector" title1="Selecteer de versie van de Azure Functions runtime die u gebruikt: "]
> * [Versie 1](functions-bindings-cosmosdb.md)
> * [Versie 2](functions-bindings-cosmosdb-v2.md)

In dit artikel wordt uitgelegd hoe u [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) bindingen kunt gebruiken in azure functions 2. x. Azure Functions ondersteunt trigger-, invoer-en uitvoer bindingen voor Azure Cosmos DB.

> [!NOTE]
> Dit artikel is voor [Azure functions versie 2. x](functions-versions.md).  Zie [Azure Cosmos DB bindingen voor Azure functions 1. x](functions-bindings-cosmosdb.md)voor informatie over het gebruik van deze bindingen in functions 1. x.
>
> Deze binding heeft oorspronkelijk de naam DocumentDB. In functies versie 2. x zijn de trigger, bindingen en pakket alle benoemde Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-apis"></a>Ondersteunde Api's

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="packages---functions-2x"></a>Pakketten-functions 2. x

De Azure Cosmos DB bindingen voor functies versie 2. x zijn opgenomen in het pakket [micro soft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) NuGet, versie 3. x. De bron code voor de bindingen bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK-extensies](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

De Azure Cosmos DB trigger gebruikt de [Azure Cosmos DB wijzigings feed](../cosmos-db/change-feed.md) om te Luis teren naar invoeg toepassingen en updates op meerdere partities. De wijzigings feed publiceert invoegingen en updates, en kan niet worden verwijderd.

## <a name="trigger---example"></a>Trigger-voor beeld

Zie het taalspecifieke voor beeld:

* [C#](#trigger---c-example)
* [C#script (. CSX)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

Trigger voorbeelden overs Laan

### <a name="trigger---c-example"></a>Trigger- C# voor beeld

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

### <a name="trigger---c-script-example"></a>Voor beeld C# van trigger-script

In het volgende voor beeld ziet u een binding van een Cosmos DB trigger in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie schrijft logboek berichten wanneer Cosmos DB records worden gewijzigd.

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

Dit is de C# script code:

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

### <a name="trigger---javascript-example"></a>Trigger-java script-voor beeld

In het volgende voor beeld ziet u een Cosmos DB trigger binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie schrijft logboek berichten wanneer Cosmos DB records worden gewijzigd.

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

Dit is de Java script-code:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

### <a name="trigger---java-example"></a>Trigger-Java-voor beeld

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

In het volgende voor beeld ziet u een binding van Cosmos DB trigger in een *Function. json* -bestand en een [python-functie](functions-reference-python.md) die gebruikmaakt van de binding. De functie schrijft logboek berichten wanneer Cosmos DB records worden gewijzigd.

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

## <a name="trigger---c-attributes"></a>Trigger- C# kenmerken

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)het kenmerk [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) .

De constructor van het kenmerk neemt de naam van de data base en de verzameling. Zie [trigger-configuratie](#trigger---configuration)voor meer informatie over deze instellingen en andere eigenschappen die u kunt configureren. Hier volgt een voor beeld van een `CosmosDBTrigger` kenmerk in een methode handtekening:

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


## <a name="trigger---configuration"></a>Trigger-configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `CosmosDBTrigger`.

|function. json-eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** || Moet worden ingesteld op `cosmosDBTrigger`. |
|**direction** || Moet worden ingesteld op `in`. Deze para meter wordt automatisch ingesteld wanneer u de trigger maakt in de Azure Portal. |
|**naam** || De naam van de variabele die wordt gebruikt in de functie code die de lijst met documenten met wijzigingen vertegenwoordigt. |
|**connectionStringSetting**|**ConnectionStringSetting** | De naam van een app-instelling die de connection string bevat die wordt gebruikt om verbinding te maken met het Azure Cosmos DB account dat wordt bewaakt. |
|**databaseName**|**DatabaseName**  | De naam van de Azure Cosmos DB-Data Base met de verzameling die wordt bewaakt. |
|**collectionName** |**CollectionName** | De naam van de verzameling die wordt bewaakt. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Beschrijving De naam van een app-instelling die de connection string bevat voor de service waarin de lease verzameling zich bevindt. Als deze niet wordt ingesteld, wordt de `connectionStringSetting` waarde gebruikt. Deze para meter wordt automatisch ingesteld wanneer de binding wordt gemaakt in de portal. De connection string voor de leases-verzameling moet schrijf machtigingen hebben.|
|**leaseDatabaseName** |**LeaseDatabaseName** | Beschrijving De naam van de data base die de verzameling bevat die wordt gebruikt voor het opslaan van leases. Als deze optie niet is ingesteld, wordt de waarde van de instelling `databaseName` gebruikt. Deze para meter wordt automatisch ingesteld wanneer de binding wordt gemaakt in de portal. |
|**leaseCollectionName** | **LeaseCollectionName** | Beschrijving De naam van de verzameling die wordt gebruikt voor het opslaan van leases. Als deze niet wordt ingesteld, wordt de waarde `leases` gebruikt. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Beschrijving Als deze is ingesteld op `true`, wordt de verzameling leases automatisch gemaakt wanneer deze nog niet bestaat. De standaard waarde is `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Beschrijving Hiermee wordt het aantal aanvraag eenheden gedefinieerd dat moet worden toegewezen wanneer de leases-verzameling wordt gemaakt. Deze instelling wordt alleen gebruikt wanneer `createLeaseCollectionIfNotExists` is ingesteld op `true`. Deze para meter wordt automatisch ingesteld wanneer de binding wordt gemaakt met behulp van de portal.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Beschrijving Als deze optie is ingesteld, wordt er een voor voegsel toegevoegd aan de leases die zijn gemaakt in de lease verzameling voor deze functie, waardoor twee afzonderlijke Azure Functions dezelfde lease verzameling kunnen delen met behulp van verschillende voor voegsels.
|**feedPollDelay**| **FeedPollDelay**| Beschrijving Als deze instelling is ingesteld, wordt in milliseconden de vertraging tussen het navragen van een partitie voor nieuwe wijzigingen in de feed gedefinieerd nadat alle huidige wijzigingen zijn vertraagd. De standaard waarde is 5000 (5 seconden).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| Beschrijving Als deze instelling is ingesteld, wordt in milliseconden het interval voor het starten van een taak gedefinieerd om te berekenen of de partities gelijkmatig worden verdeeld over bekende exemplaren van hosts. De standaard waarde is 13000 (13 seconden).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| Beschrijving Wanneer dit is ingesteld, wordt in milliseconden het interval voor het nemen van de lease op een lease die een partitie vertegenwoordigt. Als de lease niet binnen dit interval wordt vernieuwd, wordt het verlopen en het eigendom van de partitie verplaatst naar een ander exemplaar. De standaard waarde is 60000 (60 seconden).
|**leaseRenewInterval**| **LeaseRenewInterval**| Beschrijving Wanneer dit is ingesteld, wordt in milliseconden het Vernieuwings interval voor alle leases gedefinieerd voor partities die momenteel door een instantie worden beheerd. De standaard waarde is 17000 (17 seconden).
|**checkpointFrequency**| **CheckpointFrequency**| Beschrijving Als deze instelling is ingesteld, wordt in milliseconden het interval tussen de controle punten van de lease gedefinieerd. De standaard waarde is altijd na elke functie aanroep.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Beschrijving Als deze eigenschap is ingesteld, wordt de maximum hoeveelheid ontvangen items per functie aanroep ingesteld. Als bewerkingen in de bewaakte verzameling worden uitgevoerd via opgeslagen procedures, blijft het [transactie bereik](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) behouden bij het lezen van items van de wijzigings feed. Als gevolg hiervan is het mogelijk dat de hoeveelheid ontvangen items hoger is dan de opgegeven waarde zodat de items die door dezelfde trans actie worden gewijzigd, worden geretourneerd als onderdeel van één atomische batch.
|**startFromBeginning**| **StartFromBeginning**| Beschrijving Wanneer dit is ingesteld, wordt aan de trigger aangegeven dat de wijzigingen moeten worden gelezen vanaf het begin van de geschiedenis van de verzameling in plaats van de huidige tijd. Dit werkt alleen de eerste keer dat de trigger wordt gestart, zoals bij volgende uitvoeringen, de controle punten al zijn opgeslagen. Als u dit instelt op `true` wanneer er al leases zijn gemaakt, heeft dit geen effect.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger-gebruik

Voor de trigger is een tweede verzameling vereist die wordt gebruikt voor het opslaan van _leases_ via de partities. Zowel de verzameling die wordt bewaakt als de verzameling die de leases bevat, moet beschikbaar zijn voor de werking van de trigger.

>[!IMPORTANT]
> Als er meerdere functies zijn geconfigureerd voor het gebruik van een Cosmos DB trigger voor dezelfde verzameling, moeten voor elk van de functies een toegewezen lease verzameling worden gebruikt, of moet voor elke functie een andere `LeaseCollectionPrefix` worden opgegeven. Anders wordt slechts een van de functies geactiveerd. Zie de [sectie configuratie](#trigger---configuration)voor meer informatie over het voor voegsel.

De trigger geeft niet aan of een document is bijgewerkt of ingevoegd, maar het document zelf wordt geleverd. Als u updates wilt afhandelen en anders wilt invoegen, kunt u dit doen door tijds tempel velden te implementeren voor invoegen of bijwerken.

## <a name="input"></a>Invoer

De Azure Cosmos DB invoer binding gebruikt de SQL API om een of meer Azure Cosmos DB documenten op te halen en geeft deze door aan de invoer parameter van de functie. De document-ID of query parameters kunnen worden bepaald op basis van de trigger die de functie aanroept.

## <a name="input---examples"></a>Invoer-voor beelden

Zie de taalspecifieke voor beelden van het lezen van één document door een ID-waarde op te geven:

* [C#](#input---c-examples)
* [C#script (. CSX)](#input---c-script-examples)
* [F#](#input---f-examples)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-examples)
* [Python](#input---python-examples)

[Invoer voorbeelden overs Laan](#input---attributes)

### <a name="input---c-examples"></a>Invoer- C# voor beelden

Deze sectie bevat de volgende voor beelden:

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
        public string Description { get; set; }
    }
}
```

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Wachtrij trigger, ID opzoeken vanuit JSON (C#)

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die één document ophaalt. De functie wordt geactiveerd door een wachtrij bericht dat een JSON-object bevat. De wachtrij trigger parseert de JSON naar een object met de naam `ToDoItemLookup`, dat de ID bevat die moet worden gezocht. Deze ID wordt gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

```cs
namespace CosmosDBSamplesV2
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
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

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

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>HTTP-trigger, ID opzoeken vanuit query teken reeksC#()

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van een query reeks om de ID op te geven die moet worden gezocht. Deze ID wordt gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

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
                Id = "{Query.id}")] ToDoItem toDoItem,
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

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>HTTP-trigger, ID opzoeken op basis van routeC#gegevens ()

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van route gegevens om de ID op te geven die moet worden gezocht. Deze ID wordt gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

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
                Route = "todoitems/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}")] ToDoItem toDoItem,
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

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>HTTP-trigger, ID opzoeken op basis van route gegevens, metC#behulp van SqlQuery ()

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van route gegevens om de ID op te geven die moet worden gezocht. Deze ID wordt gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

In het voor beeld ziet u hoe u een bindings expressie gebruikt in de para meter `SqlQuery`. U kunt route gegevens door geven aan de para meter `SqlQuery` zoals weer gegeven, maar [u mag geen query reeks waarden door geven](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).


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

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>HTTP-trigger, meerdere docs ophalen met behulpC#van SqlQuery ()

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

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP-trigger, meerdere docs ophalen met behulpC#van DocumentClient ()

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

### <a name="input---c-script-examples"></a>Voor beelden C# van invoer scripts

Deze sectie bevat de volgende voor beelden:

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

#### <a name="queue-trigger-look-up-id-from-string-c-script"></a>Wachtrij trigger, zoek-ID van teken reeksC# (script)

In het volgende voor beeld ziet u een Cosmos DB invoer binding in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. Met de functie wordt één document gelezen en wordt de tekst waarde van het document bijgewerkt.

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

Dit is de C# script code:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Wachtrij trigger, meerdere documenten ophalen met behulp vanC# SqlQuery (script)

In het volgende voor beeld ziet u een Azure Cosmos DB invoer binding in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie haalt meerdere documenten op die zijn opgegeven door een SQL-query, met behulp van een wachtrij trigger om de query parameters aan te passen.

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

Dit is de C# script code:

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

#### <a name="http-trigger-look-up-id-from-query-string-c-script"></a>HTTP-trigger, ID opzoeken vanuit query teken reeksC# (script)

In het volgende voor beeld ziet u een [ C# script functie](functions-reference-csharp.md) waarmee één document wordt opgehaald. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van een query reeks om de ID op te geven die moet worden gezocht. Deze ID wordt gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

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
      "Id": "{Query.id}"
    }
  ],
  "disabled": false
}
```

Dit is de C# script code:

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

#### <a name="http-trigger-look-up-id-from-route-data-c-script"></a>HTTP-trigger, ID opzoeken op basis van routeC# gegevens (script)

In het volgende voor beeld ziet u een [ C# script functie](functions-reference-csharp.md) waarmee één document wordt opgehaald. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van route gegevens om de ID op te geven die moet worden gezocht. Deze ID wordt gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

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
      "route":"todoitems/{id}"
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
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Dit is de C# script code:

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

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>HTTP-trigger, meerdere documenten ophalen met behulpC# van SqlQuery (script)

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

Dit is de C# script code:

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

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>HTTP-trigger, meerdere documenten ophalen met behulpC# van DocumentClient (script)

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

Dit is de C# script code:

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

### <a name="input---javascript-examples"></a>Invoer-java script-voor beelden

Deze sectie bevat de volgende voor beelden van het lezen van één document door een ID-waarde op te geven uit verschillende bronnen:

* [Wachtrij trigger, ID opzoeken vanuit JSON](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP-trigger, ID opzoeken vanuit query reeks](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP-trigger, ID van route gegevens opzoeken](#http-trigger-look-up-id-from-route-data-javascript)
* [Wachtrij trigger, meerdere documenten ophalen met behulp van SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-javascript"></a>Wachtrij trigger, ID opzoeken vanuit JSON (Java script)

In het volgende voor beeld ziet u een Cosmos DB invoer binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. Met de functie wordt één document gelezen en wordt de tekst waarde van het document bijgewerkt.

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

Dit is de Java script-code:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

[Invoer voorbeelden overs Laan](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-javascript"></a>HTTP-trigger, ID opzoeken vanuit de query reeks (Java script)

In het volgende voor beeld ziet u een [Java script-functie](functions-reference-node.md) die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van een query reeks om de ID op te geven die moet worden gezocht. Deze ID wordt gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

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
      "Id": "{Query.id}"
    }
  ],
  "disabled": false
}
```

Dit is de Java script-code:

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

#### <a name="http-trigger-look-up-id-from-route-data-javascript"></a>HTTP-trigger, ID opzoeken op basis van route gegevens (Java script)

In het volgende voor beeld ziet u een [Java script-functie](functions-reference-node.md) die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van een query reeks om de ID op te geven die moet worden gezocht. Deze ID wordt gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

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
      "route":"todoitems/{id}"
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
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Dit is de Java script-code:

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

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>Wachtrij trigger, meerdere documenten ophalen met behulp van SqlQuery (Java script)

In het volgende voor beeld ziet u een Azure Cosmos DB invoer binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie haalt meerdere documenten op die zijn opgegeven door een SQL-query, met behulp van een wachtrij trigger om de query parameters aan te passen.

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

Dit is de Java script-code:

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

In het volgende voor beeld ziet u een Cosmos DB invoer binding in een *Function. json* -bestand en een [python-functie](functions-reference-python.md) die gebruikmaakt van de binding. Met de functie wordt één document gelezen en wordt de tekst waarde van het document bijgewerkt.

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

In het volgende voor beeld ziet u een [python-functie](functions-reference-python.md) die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van een query reeks om de ID op te geven die moet worden gezocht. Deze ID wordt gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

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
      "Id": "{Query.id}"
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

In het volgende voor beeld ziet u een [python-functie](functions-reference-python.md) die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van een query reeks om de ID op te geven die moet worden gezocht. Deze ID wordt gebruikt om een `ToDoItem`-document op te halen uit de opgegeven Data Base en verzameling.

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
      "route":"todoitems/{id}"
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
      "Id": "{id}"
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

In het volgende voor beeld ziet u een Azure Cosmos DB invoer binding in een *Function. json* -bestand en een [python-functie](functions-reference-python.md) die gebruikmaakt van de binding. De functie haalt meerdere documenten op die zijn opgegeven door een SQL-query, met behulp van een wachtrij trigger om de query parameters aan te passen.

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

### <a name="input---f-examples"></a>Invoer- F# voor beelden

In het volgende voor beeld ziet u een Cosmos DB invoer binding in een *Function. json* -bestand en een [ F# functie](functions-reference-fsharp.md) die gebruikmaakt van de binding. Met de functie wordt één document gelezen en wordt de tekst waarde van het document bijgewerkt.

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

Deze sectie bevat de volgende voor beelden:

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

In het volgende voor beeld ziet u een Java-functie die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van een query reeks om de ID op te geven die moet worden gezocht. Deze ID wordt gebruikt om een document op te halen uit de opgegeven Data Base en verzameling in de vorm van een teken reeks.

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
              partitionKey = "{Query.id}",
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

In het volgende voor beeld ziet u een Java-functie die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van een query reeks om de ID op te geven die moet worden gezocht. Deze ID wordt gebruikt om een document op te halen uit de opgegeven Data Base en verzameling. Het document wordt vervolgens geconverteerd naar een exemplaar van de ```ToDoItem``` POJO eerder gemaakt en door gegeven als een argument voor de functie.

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
              partitionKey = "{Query.id}",
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

In het volgende voor beeld ziet u een Java-functie die één document ophaalt. De functie wordt geactiveerd door een HTTP-aanvraag die gebruikmaakt van een route parameter om de ID op te geven die moet worden gezocht. Deze ID wordt gebruikt om een document op te halen uit de opgegeven Data Base en verzameling, waarna het wordt geretourneerd als een ```Optional<String>```.

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{id}",
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

## <a name="input---attributes"></a>Invoer kenmerken

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)het kenmerk [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) .

De constructor van het kenmerk neemt de naam van de data base en de verzameling. Zie [de volgende configuratie sectie](#input---configuration)voor informatie over de instellingen en andere eigenschappen die u kunt configureren.

## <a name="input---configuration"></a>Invoer-configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `CosmosDB`.

|function. json-eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**type**     || Moet worden ingesteld op `cosmosDB`.        |
|**direction**     || Moet worden ingesteld op `in`.         |
|**naam**     || De naam van de bindings parameter die het document in de functie vertegenwoordigt.  |
|**databaseName** |**DatabaseName** |De data base met het document.        |
|**collectionName** |**CollectionName** | De naam van de verzameling die het document bevat. |
|**id**    | **Id** | De ID van het document dat moet worden opgehaald. Deze eigenschap ondersteunt [bindings expressies](./functions-bindings-expressions-patterns.md). Stel de eigenschappen **id** en **sqlQuery** niet in. Als u er niet een hebt ingesteld, wordt de volledige verzameling opgehaald. |
|**sqlQuery**  |**SqlQuery**  | Een Azure Cosmos DB SQL-query die wordt gebruikt om meerdere documenten op te halen. De eigenschap ondersteunt runtime bindingen, zoals in dit voor beeld: `SELECT * FROM c where c.departmentId = {departmentId}`. Stel de eigenschappen **id** en **sqlQuery** niet in. Als u er niet een hebt ingesteld, wordt de volledige verzameling opgehaald.|
|**connectionStringSetting**     |**ConnectionStringSetting**|De naam van de app-instelling met uw Azure Cosmos DB connection string.        |
|**partitionKey**|**PartitionKey**|Hiermee geeft u de partitie sleutel waarde voor de zoek opdracht op. Kan bindende para meters bevatten.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Invoer gebruik

In C# en F# functies worden wijzigingen die zijn aangebracht in het invoer document via benoemde invoer parameters automatisch behouden wanneer de functie wordt afgesloten.

In Java script-functies worden updates niet automatisch uitgevoerd bij het afsluiten van de functie. Gebruik in plaats daarvan `context.bindings.<documentName>In` en `context.bindings.<documentName>Out` om updates te maken. Zie het Java script-voor beeld.

## <a name="output"></a>Uitvoer

Met de Azure Cosmos DB uitvoer binding kunt u een nieuw document naar een Azure Cosmos DB-Data Base schrijven met behulp van de SQL-API.

## <a name="output---examples"></a>Uitvoer-voor beelden

Zie de taalspecifieke voor beelden:

* [C#](#output---c-examples)
* [C#script (. CSX)](#output---c-script-examples)
* [F#](#output---f-examples)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-examples)
* [Python](#output---python-examples)

Zie ook het [invoer voorbeeld](#input---c-examples) dat gebruikmaakt van `DocumentClient`.

[Uitvoer voorbeelden overs Laan](#output---attributes)

### <a name="output---c-examples"></a>Uitvoer- C# voor beelden

Deze sectie bevat de volgende voor beelden:

* Wachtrij trigger, een document schrijven
* Wachtrij trigger, documenten schrijven met IAsyncCollector

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

#### <a name="queue-trigger-write-one-doc-c"></a>Wachtrij trigger, een document schrijven (C#)

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

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Wachtrij trigger, documenten schrijven met behulpC#van IAsyncCollector ()

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

### <a name="output---c-script-examples"></a>Voor beelden C# van uitvoer scripts

Deze sectie bevat de volgende voor beelden:

* Wachtrij trigger, een document schrijven
* Wachtrij trigger, documenten schrijven met IAsyncCollector

[Uitvoer voorbeelden overs Laan](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c-script"></a>Wachtrij trigger, een doc schrijven (C# script)

In het volgende voor beeld ziet u een Azure Cosmos DB uitvoer binding in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie maakt gebruik van een wachtrij-invoer binding voor een wachtrij die JSON ontvangt in de volgende indeling:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

De functie maakt Azure Cosmos DB documenten in de volgende indeling voor elke record:

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

Dit is de C# script code:

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

#### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wachtrij trigger, documenten schrijven met IAsyncCollector

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

Hier is het bestand function. json:

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

Dit is de C# script code:

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

### <a name="output---javascript-examples"></a>Uitvoer-java script-voor beelden

In het volgende voor beeld ziet u een Azure Cosmos DB uitvoer binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie maakt gebruik van een wachtrij-invoer binding voor een wachtrij die JSON ontvangt in de volgende indeling:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

De functie maakt Azure Cosmos DB documenten in de volgende indeling voor elke record:

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

Dit is de Java script-code:

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

### <a name="output---f-examples"></a>Uitvoer- F# voor beelden

In het volgende voor beeld ziet u een Azure Cosmos DB uitvoer binding in een *Function. json* -bestand en een [ F# functie](functions-reference-fsharp.md) die gebruikmaakt van de binding. De functie maakt gebruik van een wachtrij-invoer binding voor een wachtrij die JSON ontvangt in de volgende indeling:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

De functie maakt Azure Cosmos DB documenten in de volgende indeling voor elke record:

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

## <a name="output---attributes"></a>Uitvoer kenmerken

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)het kenmerk [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) .

De constructor van het kenmerk neemt de naam van de data base en de verzameling. Zie [uitvoer-configuratie](#output---configuration)voor meer informatie over deze instellingen en andere eigenschappen die u kunt configureren. Hier volgt een voor beeld van een `CosmosDB` kenmerk in een methode handtekening:

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

## <a name="output---configuration"></a>Uitvoer-configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `CosmosDB`.

|function. json-eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**type**     || Moet worden ingesteld op `cosmosDB`.        |
|**direction**     || Moet worden ingesteld op `out`.         |
|**naam**     || De naam van de bindings parameter die het document in de functie vertegenwoordigt.  |
|**databaseName** | **DatabaseName**|De data base met de verzameling waarin het document is gemaakt.     |
|**collectionName** |**CollectionName**  | De naam van de verzameling waar het document wordt gemaakt. |
|**createIfNotExists**  |**CreateIfNotExists**    | Een Booleaanse waarde die aangeeft of de verzameling wordt gemaakt wanneer deze niet bestaat. De standaard waarde is *False* omdat nieuwe verzamelingen worden gemaakt met gereserveerde door Voer, wat gevolgen heeft voor de kosten. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) voor meer informatie.  |
|**partitionKey**|**PartitionKey** |Als `CreateIfNotExists` waar is, definieert het pad van de partitie sleutel voor de gemaakte verzameling.|
|**collectionThroughput**|**CollectionThroughput**| Als `CreateIfNotExists` waar is, definieert de [door Voer](../cosmos-db/set-throughput.md) van de gemaakte verzameling.|
|**connectionStringSetting**    |**ConnectionStringSetting** |De naam van de app-instelling met uw Azure Cosmos DB connection string.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uitvoer gebruik

Wanneer u in uw functie naar de uitvoer parameter schrijft, wordt er standaard een document gemaakt in uw data base. Dit document heeft een automatisch gegenereerde GUID als document-ID. U kunt de document-ID van het uitvoer document opgeven door de eigenschap `id` op te geven in het JSON-object dat wordt door gegeven aan de uitvoer parameter.

> [!Note]
> Wanneer u de ID van een bestaand document opgeeft, wordt dit overschreven door het nieuwe uitvoer document.

## <a name="exceptions-and-return-codes"></a>Uitzonde ringen en retour codes

| Dwingen | Naslaginformatie |
|---|---|
| CosmosDB | [CosmosDB-fout codes](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>instellingen voor de host. json

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versie 2. x. Zie voor meer informatie over globale configuratie-instellingen in versie 2. x [host. json Reference voor Azure functions versie 2. x](functions-host-json.md).

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
|Protocol|https|Het verbindings protocol dat door de functie wordt gebruikt bij het verbinden met de Azure Cosmos DB-service.  Lees [hier voor een uitleg van beide modi](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|N.v.t.|Het lease voorvoegsel dat moet worden gebruikt voor alle functies in een app.|

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over database computing zonder server met Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
