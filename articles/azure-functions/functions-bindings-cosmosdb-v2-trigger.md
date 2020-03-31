---
title: Azure Cosmos DB-trigger voor functies 2.x
description: Leer de Azure Cosmos DB-trigger gebruiken in Azure-functies.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: de8ad39ef731af3dc272d700eeee346acda64b53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277568"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x"></a>Azure Cosmos DB-trigger voor Azure-functies 2.x

De Azure Cosmos DB Trigger gebruikt de [Azure Cosmos DB Change Feed](../cosmos-db/change-feed.md) om te luisteren naar inserts en updates voor verschillende partities. De wijzigingsfeed publiceert inserts en updates, geen verwijderingen.

Zie het [overzicht](./functions-bindings-cosmosdb-v2.md)voor informatie over de installatie en configuratiedetails.

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die wordt aangeroepen wanneer er inserts of updates in de opgegeven database en verzameling zijn.

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld ziet u een Cosmos DB-triggerbinding in een *function.json-bestand* en een [C#-scriptfunctie](functions-reference-csharp.md) die de binding gebruikt. De functie schrijft logboekberichten wanneer Cosmos DB-records worden toegevoegd of gewijzigd.

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld ziet u een Cosmos DB-triggerbinding in een *function.json-bestand* en een [JavaScript-functie](functions-reference-node.md) die de binding gebruikt. De functie schrijft logboekberichten wanneer Cosmos DB-records worden toegevoegd of gewijzigd.

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

# <a name="python"></a>[Python](#tab/python)

In het volgende voorbeeld ziet u een Cosmos DB-triggerbinding in een *function.json-bestand* en een [Python-functie](functions-reference-python.md) die de binding gebruikt. De functie schrijft logboekberichten wanneer Cosmos DB-records worden gewijzigd.

Hier zijn de bindende gegevens in het *function.json-bestand:*

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

Hier is de Python-code:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

# <a name="java"></a>[Java](#tab/java)

Deze functie wordt aangeroepen wanneer er inserts of updates in de opgegeven database en verzameling zijn.

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


In de [Java-functies runtime bibliotheek](/java/api/overview/azure/functions/runtime), gebruik maken van de `@CosmosDBTrigger` annotatie op parameters waarvan de waarde zou komen van Cosmos DB.  Deze annotatie kan worden gebruikt met native Java-typen, `Optional<T>`POJOs of nullable waarden met behulp van .

---

## <a name="attributes-and-annotations"></a>Kenmerken en annotaties

# <a name="c"></a>[C #](#tab/csharp)

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)het kenmerk [CosmosDBTrigger.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs)

De constructor van het kenmerk neemt de naam en de verzamelnaam van de database. Zie [Trigger - configuratie](#configuration)voor informatie over de instellingen en andere eigenschappen die u configureren. Hier is `CosmosDBTrigger` een kenmerkvoorbeeld in een methodehandtekening:

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

Zie [Trigger voor](#example)een volledig voorbeeld.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

Gebruik vanuit de [runtime-bibliotheek van javafuncties](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)de `@CosmosDBInput` annotatie op parameters die gegevens van Cosmos DB lezen.

---

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `CosmosDBTrigger` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type** | N.v.t. | Moet ingesteld `cosmosDBTrigger`zijn op. |
|**direction** | N.v.t. | Moet ingesteld `in`zijn op. Deze parameter wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal. |
|**Naam** | N.v.t. | De variabele naam die wordt gebruikt in functiecode die de lijst met documenten met wijzigingen vertegenwoordigt. |
|**verbindingstekenreeksinstelling**|**Instelling voor verbindingstekenreeks** | De naam van een app-instelling die de verbindingstekenreeks bevat die wordt gebruikt om verbinding te maken met het Azure Cosmos DB-account dat wordt gecontroleerd. |
|**Databasenaam**|**DatabaseName**  | De naam van de Azure Cosmos DB-database waarbij de verzameling wordt gecontroleerd. |
|**collectionNaam** |**CollectionName** | De naam van de collectie wordt gecontroleerd. |
|**leaseConnectionStringInstelling** | **LeaseConnectionStringInstelling** | (Optioneel) De naam van een app-instelling die de verbindingstekenreeks bevat met het Azure Cosmos DB-account dat de leaseverzameling bevat. Wanneer deze niet `connectionStringSetting` is ingesteld, wordt de waarde gebruikt. Deze parameter wordt automatisch ingesteld wanneer de binding in de portal wordt gemaakt. De verbindingstekenreeks voor de leaseverzameling moet schrijfmachtigingen hebben.|
|**leaseDatabaseName** |**LeaseDatabaseNaam** | (Optioneel) De naam van de database met de verzameling die wordt gebruikt om leases op te slaan. Wanneer deze niet is `databaseName` ingesteld, wordt de waarde van de instelling gebruikt. Deze parameter wordt automatisch ingesteld wanneer de binding in de portal wordt gemaakt. |
|**leaseCollectionName** | **LeaseCollectionName** | (Optioneel) De naam van de collectie die wordt gebruikt om huurcontracten op te slaan. Wanneer deze niet `leases` is ingesteld, wordt de waarde gebruikt. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfBestaat niet** | (Optioneel) Wanneer ingesteld `true`op , wordt de lease-collectie automatisch gemaakt wanneer deze nog niet bestaat. De standaardwaarde is `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionDoorvoer**| (Optioneel) Hiermee definieert u het aantal aanvraageenheden dat moet worden toegewezen wanneer de leaseverzameling wordt gemaakt. Deze instelling wordt `createLeaseCollectionIfNotExists` alleen gebruikt `true`wanneer deze is ingesteld op . Deze parameter wordt automatisch ingesteld wanneer de binding wordt gemaakt met behulp van de portal.
|**leaseCollectionVoorfix**| **LeaseCollectionVoorvoegsel**| (Optioneel) Wanneer ingesteld, wordt de waarde toegevoegd als een voorvoegsel aan de leaseovereenkomsten die zijn gemaakt in de Lease-collectie voor deze functie. Met een voorvoegsel kunnen twee afzonderlijke Azure-functies dezelfde Lease-verzameling delen met behulp van verschillende voorvoegsels.
|**feedPollDelay feedPollDelay**| **FeedPollDelay FeedPollDelay FeedPollDelay FeedPoll**| (Optioneel) De tijd (in milliseconden) voor de vertraging tussen het stemmen van een partitie voor nieuwe wijzigingen in de feed, nadat alle huidige wijzigingen zijn uitgelekt. Standaard is 5.000 milliseconden of 5 seconden.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Optioneel) Wanneer ingesteld, definieert het in milliseconden het interval om een taak te starten om te berekenen of partities gelijkmatig worden verdeeld over bekende host-exemplaren. Standaard is 13000 (13 seconden).
|**leaseExpirsinterval**| **LeaseExpiratieinterval**| (Optioneel) Wanneer ingesteld, definieert het in milliseconden het interval waarvoor de lease wordt gemaakt op een lease die een partitie vertegenwoordigt. Als de lease niet binnen dit interval wordt verlengd, vervalt deze en wordt het eigendom van de partitie verplaatst naar een andere instantie. Standaard is 60000 (60 seconden).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Optioneel) Wanneer ingesteld, definieert het in milliseconden het vernieuwingsinterval voor alle leases voor partities die momenteel in het bezit zijn van een instantie. Standaard is 17000 (17 seconden).
|**controlepuntFrequentie**| **ControlepuntFrequentie**| (Optioneel) Wanneer ingesteld, definieert het in milliseconden het interval tussen leasecontrolepunten. Standaard is altijd na elke functieaanroep.
|**maxItemsPerInvocation**| **MaxItemsPerInvocatie**| (Optioneel) Wanneer deze eigenschap is ingesteld, stelt deze eigenschap het maximum aantal ontvangen objecten per functieaanroep in. Als bewerkingen in de bewaakte verzameling worden uitgevoerd via opgeslagen procedures, blijft [het transactiebereik](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) behouden bij het lezen van items uit de feed Wijzigen. Als gevolg hiervan kan het aantal ontvangen artikelen hoger zijn dan de opgegeven waarde, zodat de items die door dezelfde transactie worden gewijzigd, worden geretourneerd als onderdeel van één atomaire batch.
|**startVanafhet begin**| **Beginvanaf het begin**| (Optioneel) Met deze optie moet de trigger wijzigingen lezen vanaf het begin van de wijzigingsgeschiedenis van de verzameling in plaats van te beginnen op de huidige tijd. Lezen vanaf het begin werkt alleen de eerste keer dat de Trigger wordt gestart, zoals in de volgende runs, de checkpoints zijn al opgeslagen. Het instellen `true` van deze optie op wanneer er reeds gemaakte huurovereenkomsten zijn gemaakt, heeft geen effect. |
|**voorkeurLocaties**| **Voorkeurslocaties**| (Optioneel) Hiermee definieert u voorkeurslocaties (regio's) voor geogerepliceerde databaseaccounts in de Azure Cosmos DB-service. Waarden moeten door komma's worden gescheiden. Bijvoorbeeld "Oost-VS, Zuid-Centraal VS, Noord-Europa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

De trigger vereist een tweede verzameling die wordt gebruikt om _leases_ op te slaan over de partities. Zowel de collectie die wordt gecontroleerd als de verzameling die de leases bevat, moet beschikbaar zijn om de trigger te laten werken.

>[!IMPORTANT]
> Als meerdere functies zijn geconfigureerd om een Cosmos DB-trigger te gebruiken voor dezelfde verzameling, `LeaseCollectionPrefix` moet elk van de functies een speciale leaseverzameling gebruiken of een andere functie opgeven. Anders wordt slechts één van de functies geactiveerd. Zie de sectie Configuratie voor informatie over het [voorvoegsel](#configuration).

De trigger geeft niet aan of een document is bijgewerkt of ingevoegd, het biedt alleen het document zelf. Als u updates en inserts anders moet verwerken, u dat doen door tijdstempelvelden te implementeren voor het invoegen of bijwerken.

## <a name="next-steps"></a>Volgende stappen

- [Een Azure Cosmos DB-document lezen (invoerbinding)](./functions-bindings-cosmosdb-v2-input.md)
- [Wijzigingen opslaan in een Azure Cosmos DB-document (uitvoerbinding)](./functions-bindings-cosmosdb-v2-output.md)