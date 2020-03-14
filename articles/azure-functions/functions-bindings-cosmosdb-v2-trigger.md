---
title: Azure Cosmos DB trigger voor functies 2. x
description: Meer informatie over het gebruik van de Azure Cosmos DB trigger in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: de8ad39ef731af3dc272d700eeee346acda64b53
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277568"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x"></a>Azure Cosmos DB trigger voor Azure Functions 2. x

De Azure Cosmos DB trigger gebruikt de [Azure Cosmos DB wijzigings feed](../cosmos-db/change-feed.md) om te Luis teren naar invoeg toepassingen en updates op meerdere partities. De wijzigingenfeed publiceert invoegingen en updates, niet verwijderen.

Zie het [overzicht](./functions-bindings-cosmosdb-v2.md)voor meer informatie over de installatie-en configuratie details.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

In het volgende voor beeld ziet u een binding van een Cosmos DB trigger in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie schrijft logboek berichten wanneer Cosmos DB records worden toegevoegd of gewijzigd.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u een Cosmos DB trigger binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie schrijft logboek berichten wanneer Cosmos DB records worden toegevoegd of gewijzigd.

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

Deze functie wordt aangeroepen wanneer er invoeg-of update bewerkingen zijn in de opgegeven Data Base en verzameling.

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


Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@CosmosDBTrigger` annotatie op para meters waarvan de waarde afkomstig is van Cosmos db.  Deze aantekening kan worden gebruikt met systeem eigen Java-typen, Pojo's of nullable-waarden met behulp van `Optional<T>`.

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)het kenmerk [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) .

De constructor van het kenmerk wordt de databasenaam en verzamelingsnaam. Zie [trigger-configuratie](#configuration)voor meer informatie over deze instellingen en andere eigenschappen die u kunt configureren. Hier volgt een voor beeld van een `CosmosDBTrigger` kenmerk in een methode handtekening:

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

Zie [trigger](#example)voor een volledig voor beeld.

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# het script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="java"></a>[Java](#tab/java)

Gebruik in de [runtime-bibliotheek van Java-functies](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)de `@CosmosDBInput` annotatie voor para meters die gegevens van Cosmos DB lezen.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `CosmosDBTrigger`.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op `cosmosDBTrigger`. |
|**direction** | N.v.t. | Moet worden ingesteld op `in`. Deze parameter wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt. |
|**naam** | N.v.t. | De naam van de variabele die wordt gebruikt in functiecode aan te geven dat de lijst met documenten met wijzigingen vertegenwoordigt. |
|**connectionStringSetting**|**ConnectionStringSetting** | De naam van een app-instelling met de verbindingsreeks waarmee verbinding met de Azure Cosmos DB-account dat wordt bewaakt. |
|**databaseName**|**DatabaseName**  | De naam van de Azure Cosmos DB-database met de verzameling die worden bewaakt. |
|**collectionName** |**CollectionName** | De naam van de verzameling die worden bewaakt. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Beschrijving De naam van een app-instelling die de connection string bevat naar het Azure Cosmos DB-account waarin de lease verzameling zich bevindt. Als deze niet wordt ingesteld, wordt de `connectionStringSetting` waarde gebruikt. Deze parameter is automatisch ingesteld wanneer de binding in de portal wordt gemaakt. De verbindingsreeks voor de verzameling leases moet schrijfmachtigingen hebben.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Optioneel) De naam van de database waarin de verzameling die wordt gebruikt voor het opslaan van de leases. Als deze optie niet is ingesteld, wordt de waarde van de instelling `databaseName` gebruikt. Deze parameter is automatisch ingesteld wanneer de binding in de portal wordt gemaakt. |
|**leaseCollectionName** | **LeaseCollectionName** | (Optioneel) De naam van de verzameling die wordt gebruikt voor het opslaan van de leases. Als deze niet wordt ingesteld, wordt de waarde `leases` gebruikt. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Beschrijving Als deze is ingesteld op `true`, wordt de verzameling leases automatisch gemaakt wanneer deze nog niet bestaat. De standaard waarde is `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Beschrijving Hiermee wordt het aantal aanvraag eenheden gedefinieerd dat moet worden toegewezen wanneer de leases-verzameling wordt gemaakt. Deze instelling wordt alleen gebruikt wanneer `createLeaseCollectionIfNotExists` is ingesteld op `true`. Deze parameter is automatisch ingesteld wanneer de binding wordt gemaakt met behulp van de portal.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Beschrijving Als deze optie is ingesteld, wordt de waarde als voor voegsel toegevoegd aan de leases die zijn gemaakt in de lease verzameling voor deze functie. Met een voor voegsel kunnen twee afzonderlijke Azure Functions dezelfde lease verzameling delen door gebruik te maken van verschillende voor voegsels.
|**feedPollDelay**| **FeedPollDelay**| Beschrijving De tijd (in milliseconden) voor de vertraging tussen het pollen van een partitie voor nieuwe wijzigingen in de feed, nadat alle huidige wijzigingen zijn vertraagd. De standaard waarde is 5.000 milliseconden of 5 seconden.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Optioneel) Als de waarde, deze wordt gedefinieerd, in milliseconden, het interval voor een vliegende start een taak voor het berekenen van als partities gelijkmatig zijn verdeeld over exemplaren bekende hosten. De standaardwaarde is 13000 (13 seconden).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Optioneel) Als de waarde, deze wordt gedefinieerd, in milliseconden, het interval waarvoor de lease op een lease voor een partitie wordt gemaakt. Als de lease niet binnen dit interval wordt vernieuwd, wordt het verlopen en eigendom van de partitie worden verplaatst naar een andere instantie. De standaardwaarde is 60000 (60 seconden).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Optioneel) Als de waarde, deze wordt gedefinieerd, in milliseconden, het vernieuwingsinterval voor alle leases voor partities die momenteel worden vastgehouden door een exemplaar. De standaardwaarde is 17000 (17 seconden).
|**checkpointFrequency**| **CheckpointFrequency**| (Optioneel) Als de waarde, deze wordt gedefinieerd, in milliseconden, het interval tussen de lease controlepunten. De standaard waarde is altijd na elke functie aanroep.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Beschrijving Als deze eigenschap is ingesteld, wordt het maximum aantal items ingesteld dat per functie aanroep wordt ontvangen. Als bewerkingen in de bewaakte verzameling worden uitgevoerd via opgeslagen procedures, blijft het [transactie bereik](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) behouden bij het lezen van items van de wijzigings feed. Als gevolg hiervan kan het aantal ontvangen items hoger zijn dan de opgegeven waarde, zodat de items die door dezelfde trans actie worden gewijzigd, worden geretourneerd als onderdeel van één atomische batch.
|**startFromBeginning**| **StartFromBeginning**| Beschrijving Met deze optie geeft u aan dat de trigger wijzigingen moet lezen aan het begin van de wijzigings geschiedenis van de verzameling, in plaats van vanaf de huidige tijd. Lezen vanaf het begin werkt alleen de eerste keer dat de trigger wordt gestart, zoals bij volgende uitvoeringen, de controle punten al zijn opgeslagen. Als u deze optie instelt op `true` wanneer er al leases zijn gemaakt, heeft dit geen effect. |
|**preferredLocations**| **PreferredLocations**| Beschrijving Hiermee worden voorkeurs locaties (regio's) gedefinieerd voor accounts met geo-gerepliceerde data bases in de Azure Cosmos DB-service. De waarden moeten worden gescheiden door komma's. Bijvoorbeeld ' vs-Oost, VS Zuid-Centraal, Europa-noord '. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

Voor de trigger is een tweede verzameling vereist die wordt gebruikt voor het opslaan van _leases_ via de partities. Zowel de verzameling die worden bewaakt en de verzameling waarin de leases moet beschikbaar zijn voor de trigger om te werken.

>[!IMPORTANT]
> Als er meerdere functies zijn geconfigureerd voor het gebruik van een Cosmos DB trigger voor dezelfde verzameling, moeten voor elk van de functies een toegewezen lease verzameling worden gebruikt, of moet voor elke functie een andere `LeaseCollectionPrefix` worden opgegeven. Anders wordt slechts één van de functies worden geactiveerd. Zie de [sectie configuratie](#configuration)voor meer informatie over het voor voegsel.

De trigger wordt niet aangegeven dat of een document is bijgewerkt of ingevoegd, biedt deze alleen het document zelf. Als u nodig hebt voor het afhandelen van updates en toevoegingen anders, kunt u dat doen door het implementeren van timestamp velden voor het invoegen of bijwerken.

## <a name="next-steps"></a>Volgende stappen

- [Een Azure Cosmos DB document lezen (invoer binding)](./functions-bindings-cosmosdb-v2-input.md)
- [Wijzigingen opslaan in een Azure Cosmos DB-document (uitvoer binding)](./functions-bindings-cosmosdb-v2-output.md)