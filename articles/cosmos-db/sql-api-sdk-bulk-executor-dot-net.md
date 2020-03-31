---
title: 'Azure Cosmos DB: Bulk executor .NET API, SDK & resources'
description: Lees alles over de bulkexecutor .NET API en SDK, inclusief releasedatums, pensioendatums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB-bulkexecutor .NET SDK.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/16/2020
ms.author: ramkris
ms.openlocfilehash: 1a8040fc397b526b540ce9343baa985cab49e2b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169403"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET bulk executor library: Informatie downloaden 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-wijzigingsfeed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [Rest](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resourceprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulkexecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk executor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Beschrijving**| Met de .Net-bulkexecutorbibliotheek kunnen clienttoepassingen bulkbewerkingen uitvoeren op Azure Cosmos DB-accounts. Deze bibliotheek biedt naamruimten Voor BulkImport, BulkUpdate en BulkDelete. De BulkImport-module kan documenten zodanig innemen dat de doorvoer die voor een verzameling is ingericht, maximaal wordt verbruikt. De BulkUpdate-module kan bestaande gegevens in Azure Cosmos-containers bulkbijwerken als patches. De BulkDelete-module kan documenten op een geoptimaliseerde manier verwijderen, zodat de doorvoer die voor een verzameling is ingericht, maximaal wordt verbruikt.|
|**SDK downloaden**| [NuGet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Bulk executor bibliotheek in GitHub**| [GitHub GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API-documentatie**|[.NET API-referentiedocumentatie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Slag**|[Aan de slag met de bulkexecutorbibliotheek .NET SDK](bulk-executor-dot-net.md)|
| **Huidig ondersteund kader**| Microsoft .NET Framework 4.5.2, 4.6.1 en .NET Standard 2.0 |

> [!NOTE]
> Als u bulkexecutor gebruikt, raadpleegt u de nieuwste versie 3.x van de [.NET SDK](tutorial-sql-api-dotnet-bulk-import.md), waarin bulkexecutor is ingebouwd in de SDK. 

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="241-preview"></a><a name="2.4.1-preview"/>2.4.1-preview

* Fixed TotalElapsedTime in the response of BulkDelete to correct measure the total time including any retries.

### <a name="240-preview"></a><a name="2.4.0-preview"/>2.4.0-preview

* Sdk-afhankelijkheid gewijzigd in >= 2,5,1

### <a name="230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-preview2

* Ondersteuning toegevoegd voor de bulkuitvoerder van grafiekom ttl op vertices en randen te accepteren

### <a name="220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-preview2

* Fixed an issue, which caused exceptions during elastic scaling of Azure Cosmos DB when running in Gateway mode. Deze fix maakt het functioneel gelijk aan 1.4.1 release.

### <a name="210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-preview2

* Added BulkDelete support for SQL API accounts to accept partition key, document id tuples to delete. Deze wijziging maakt het functioneel gelijk aan 1.4.0 release.

### <a name="200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Inclusief MongoBulkExecutor ter ondersteuning van .NET Standard 2.0. Deze functie maakt het functioneel gelijk aan 1.3.0 release, met de toevoeging van ondersteuning .NET Standard 2.0 als het doelkader.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* Toegevoegd .NET Standard 2.0 als een van de ondersteunde doelkaders om de bulk executor bibliotheek te laten werken met .NET Core toepassingen.

### <a name="188"></a><a name="1.8.8"/>1.8.8

* Fixed an issue on MongoBulkExecutor that was increasing the document size unexpectedly by adding padding and in some cases, going over the maximum document size limit.

### <a name="187"></a><a name="1.8.7"/>1.8.7

* Fixed an issue with BulkDeleteAsync when the Collection has nested partition key paths.

### <a name="186"></a><a name="1.8.6"/>1.8.6

* MongoBulkExecutor implementeert nu IDisposable en het zal naar verwachting worden verwijderd na gebruik.

### <a name="185"></a><a name="1.8.5"/>1.8.5

* Verwijderd slot op SDK-versie. Pakket is nu afhankelijk van SDK >= 2.5.1.

### <a name="184"></a><a name="1.8.4"/>1.8.4

* Vaste verwerking van id's bij het aanroepen van BulkImport met een lijst met POCO-objecten met numerieke waarden.

### <a name="183"></a><a name="1.8.3"/>1.8.3

* Fixed TotalElapsedTime in the response of BulkDelete to correct measure the total time including any retries.

### <a name="182"></a><a name="1.8.2"/>1.8.2

* Fixed high CPU verbruik op bepaalde scenario's.
* Tracing maakt nu gebruik van TraceSource. Gebruikers kunnen listeners `BulkExecutorTrace` definiëren voor de bron.
* Fixed a rare scenario that could cause a lock when sending documents near 2Mb of size.

### <a name="160"></a><a name="1.6.0"/>1.6.0

* De bulkuitvoerder bijgewerkt om nu de nieuwste versie van de Azure Cosmos DB .NET SDK (2.4.0) te gebruiken

### <a name="150"></a><a name="1.5.0"/>1.5.0

* Ondersteuning toegevoegd voor de bulkuitvoerder van grafiekom ttl op vertices en randen te accepteren

### <a name="141"></a><a name="1.4.1"/>1.4.1

* Fixed an issue, which caused exceptions during elastic scaling of Azure Cosmos DB when running in Gateway mode.

### <a name="140"></a><a name="1.4.0"/>1.4.0

* Added BulkDelete support for SQL API accounts to accept partition key, document id tuples to delete.

### <a name="130"></a><a name="1.3.0"/>1.3.0

* Een probleem opgelost, waardoor een opmaakprobleem is ontstaan in de gebruikersagent die wordt gebruikt door bulkuitvoerder.

### <a name="120"></a><a name="1.2.0"/>1.2.0

* Verbeteringen aangebracht in het importeren en bijwerken van API's voor bulkuitvoerders om zich transparant aan te passen aan elastische schaling van Cosmos-container wanneer de opslag de huidige capaciteit overschrijdt zonder uitzonderingen te gooien.

### <a name="112"></a><a name="1.1.2"/>1.1.2

* Steeg de DocumentDB .NET SDK afhankelijkheid naar versie 2.1.3.

### <a name="111"></a><a name="1.1.1"/>1.1.1

* Fixed an issue, which caused bulk executor to throw JSRT error while importing to fixed collections.

### <a name="110"></a><a name="1.1.0"/>1.1.0

* Added support for BulkDelete operation for Azure Cosmos DB SQL API accounts.
* Ondersteuning toegevoegd voor bulkimport-bewerking voor accounts met de API van Azure Cosmos DB voor MongoDB.
* Steeg de DocumentDB .NET SDK afhankelijkheid naar versie 2.0.0. 

### <a name="102"></a><a name="1.0.2"/>1.0.2

* Ondersteuning toegevoegd voor bulkimportbewerking voor Azure Cosmos DB Gremlin API-accounts.

### <a name="101"></a><a name="1.0.1"/>1.0.1

* Kleine bugfix voor de BulkImport-bewerking voor Azure Cosmos DB SQL API-accounts.

### <a name="100"></a><a name="1.0.0"/>1.0.0

* Added support for BulkImport and BulkUpdate operations for Azure Cosmos DB SQL API accounts.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor meer informatie over de Java-bibliotheek voor bulkuitvoeraars:

[Java bulk executor library SDK en release informatie](sql-api-sdk-bulk-executor-java.md)
