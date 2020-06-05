---
title: 'Azure Cosmos DB: voorbeelden van .NET (Microsoft.Azure.Cosmos) voor de SQL API'
description: Vind de C# .NET V3 SDK-voorbeelden op GitHub voor veelvoorkomende taken met de SQL-API in Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2019
ms.author: sngun
ms.openlocfilehash: d162639885a7736953a3e7388336c3105f2f0893
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683858"
---
# <a name="azure-cosmos-dbnet-v3-sdk-microsoftazurecosmos-examples-for-the-sql-api"></a>Azure Cosmos DB: voorbeelden van .NET V3 SDK (Microsoft.Azure.Cosmos) voor de SQL API

> [!div class="op_single_selector"]
> * [.NET V2 SDK-voorbeelden](sql-api-dotnet-samples.md)
> * [.NET V3 SDK-voorbeelden](sql-api-dotnet-v3sdk-samples.md)
> * [Java V4 SDK-voorbeelden](sql-api-java-sdk-samples.md)
> * [Node.js-voorbeelden](sql-api-nodejs-samples.md)
> * [Python-voorbeelden](sql-api-python-samples.md)
> * [Galerie met codevoorbeelden voor Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
>
>

De GitHub-opslagplaats [azure-cosmos-dotnet-v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage) bevat de nieuwste .NET-voorbeeldoplossingen voor het uitvoeren van CRUD-bewerkingen en andere veelvoorkomende bewerkingen in Azure Cosmos DB-resources. Als u bekend bent met de vorige versie van de .NET SDK, komen de termen 'verzameling' en 'document' u misschien bekend voor. Azure Cosmos DB biedt ondersteuning voor meerdere API-modellen. Daarom worden in versie 3.0 van de .NET SDK de generieke termen 'container' en 'item' gebruikt. Een container kan een verzameling, een graaf of een tabel zijn. Een item kan een document, rand/hoekpunt of rij zijn en is de inhoud binnen een container. Dit artikel bevat:

* Koppelingen naar de taken in elk van de C#-voorbeeldprojectbestanden.
* Koppelingen naar het bijbehorende API-referentiemateriaal.

## <a name="prerequisites"></a>Vereisten

Visual Studio 2019 met de Azure-ontwikkelworkflow geïnstalleerd

- U kunt de **gratis** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.

   Het pakket [Microsoft.Azure.cosmos NuGet](https://www.nuget.org/packages/Microsoft.Azure.cosmos/)

Een Azure-abonnement of gratis Cosmos DB-proefaccount

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- U kunt [voordelen als Visual Studio-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Via uw Visual Studio-abonnement ontvangt u elke maand een tegoed dat u voor betaalde Azure-services kunt gebruiken.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> De voorbeelden staan op zichzelf, en worden automatisch ingesteld en na gebruik opgeschoond. Voor elk exemplaar wordt uw abonnement gefactureerd voor één uur gebruik in de prestatielaag van uw container.
> 

## <a name="database-examples"></a>Voorbeelden voor databases

In de methode [RunDatabaseDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L65-L91) van het voorbeeldproject *DatabaseManagement* ziet u hoe de volgende taken worden uitgevoerd. Zie [Werken met databases, containers en items](databases-containers-items.md) voor meer informatie over de Azure Cosmos-databases voordat u de volgende voorbeelden uitvoert.

| Taak | API-verwijzing |
| --- | --- |
| [Een database maken](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L68) |[CosmosClient.CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) |
| [Een database lezen op id](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L80) |[Database.ReadAsync](/dotnet/api/microsoft.azure.cosmos.database.readasync?view=azure-dotnet) |
| [Alle databases voor een account lezen](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L96) |[CosmosClient.GetDatabaseQueryIterator](/dotnet/api/microsoft.azure.cosmos.cosmosclient.getdatabasequeryiterator?view=azure-dotnet) |
| [Een database verwijderen](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L106) |[Database.DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) |

## <a name="container-examples"></a>Voorbeelden van containers

In de methode [RunContainerDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L69-L89) van het voorbeeldproject *ContainerManagement* ziet u hoe de volgende taken worden uitgevoerd. Zie [Werken met databases, containers en items](databases-containers-items.md) voor meer informatie over Azure Cosmos-containers voordat u de volgende voorbeelden uitvoert.

| Taak | API-verwijzing |
| --- | --- |
| [Een container maken](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L97-L107) |[Database.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet) |
| [Een container maken met aangepast indexbeleid](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L111-L127) |[Database.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet) |
| [Geconfigureerde prestaties van een container wijzigen](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L149-L171) |[Container.ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync?view=azure-dotnet) |
| [Een container ophalen op basis van id](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L176-L185) |[Container.ReadContainerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readcontainerasync?view=azure-dotnet) |
| [Alle containers in een database lezen](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L193-L205) |[Database.GetContainerQueryIterator](/dotnet/api/microsoft.azure.cosmos.database.getcontainerqueryiterator?view=azure-dotnet) |
| [Container verwijderen](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L213-L2018) |[Container.DeleteContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.deletecontainerasync?view=azure-dotnet) |

## <a name="item-examples"></a>Voorbeelden van items

In de methode [RunItemsDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L119-L130) van het voorbeeldproject *ItemManagement* ziet u hoe de volgende taken worden uitgevoerd. Zie [Werken met databases, containers en items](databases-containers-items.md) voor meer informatie over Azure Cosmos-items voordat u de volgende voorbeelden uitvoert.

| Taak | API-verwijzing |
| --- | --- |
| [Een item maken](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L161-L200) |[Container.CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet) |
| [Een item op id lezen](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L203-L241) |[container.ReadItemAsync](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) |
| [Query uitvoeren op items](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L244-L320) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [Item vervangen](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L323-L363) |[container.ReplaceItemAsync](/dotnet/api/microsoft.azure.cosmos.container.replaceitemasync?view=azure-dotnet) |
| [Een item invoegen of bijwerken](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L366-L411) |[container.UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet) |
| [Item verwijderen](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L414-L424) |[container.DeleteItemAsync](/dotnet/api/microsoft.azure.cosmos.container.deleteitemasync?view=azure-dotnet) |
| [Een item vervangen met voorwaardelijke ETag-controle](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L585-L674) |[RequestOptions.IfMatchEtag](/dotnet/api/microsoft.azure.cosmos.requestoptions.ifmatchetag?view=azure-dotnet) |

## <a name="indexing-examples"></a>Voorbeelden van indexen

In de methode [RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L108-L122) van het voorbeeldproject *IndexManagement* ziet u hoe de volgende taken worden uitgevoerd. Zie [indexeringsbeleid](index-policy.md), [indexeringstypen](index-types.md) en [indexeringspaden](index-paths.md) voor meer informatie over indexeren in Azure Cosmos DB voordat u de volgende voorbeelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Een item uitsluiten van de index](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L130-L186) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.cosmos.indexingdirective?view=azure-dotnet) |
| [Luie indexering gebruiken](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L198-L220) |[IndexingPolicy.IndexingMode](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.indexingmode?view=azure-dotnet) |
| [Opgegeven itempaden uitsluiten van de index ](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L230-L297) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.excludedpaths?view=azure-dotnet) |

## <a name="query-examples"></a>Voorbeelden van query's

De [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L76-L96)-methode van het voorbeeldproject *Query's* laat zien hoe u de volgende taken uitvoert met behulp van de grammatica van de SQL-query, de LINQ-provider met de query en Lambda. Zie [SQL-queryvoorbeelden voor Azure Cosmos DB](how-to-sql-query.md) voor meer informatie over de SQL-queryreferentie in Azure Cosmos DB voordat u de volgende voorbeelden uitvoert.

| Taak | API-verwijzing |
| --- | --- |
| [Query's uitvoeren op items uit één partitie](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L154-L186) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [Query's uitvoeren op items uit meerdere partities](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L215-L275) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [Query's uitvoeren met behulp van een SQL-instructie](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L189-L212) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |

## <a name="change-feed-examples"></a>Voorbeelden van wijzigingenfeeds

In de methode [RunBasicChangeFeed](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) van het voorbeeldproject *ChangeFeed* ziet u hoe de volgende taken worden uitgevoerd. Als u meer wilt weten over de wijzigingenfeed in Azure Cosmos DB voordat u de volgende voor beelden uitvoert, raadpleegt u [Wijzigingenfeed in Azure Cosmos DB lezen](read-change-feed.md) en [Processor voor wijzigingenfeed in Azure Cosmos DB](change-feed-processor.md).

| Taak | API-verwijzing |
| --- | --- |
| [Basisfunctionaliteit van wijzigingenfeed](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |
| [Wijzigingenfeed van een specifieke tijd lezen](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L127-L162) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |
| [Wijzigingenfeed vanaf het begin lezen](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L170-L198) |[ChangeFeedProcessorBuilder.WithStartTime(DateTime)](/dotnet/api/microsoft.azure.cosmos.changefeedprocessorbuilder.withstarttime?view=azure-dotnet) |
| [Migreren van wijzigingenfeedprocessor naar wijzigingenfeed in V3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L256-L333) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |

## <a name="server-side-programming-examples"></a>Voorbeelden van programmering op de server

In de methode [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L72-L102) van het voorbeeldproject *ServerSideScripts* ziet u hoe de volgende taken worden uitgevoerd. Zie [Opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies](stored-procedures-triggers-udfs.md)voor meer informatie over het programmeren op de server in Azure Cosmos DB voordat u de volgende voorbeelden uitvoert.

| Taak | API-verwijzing |
| --- | --- |
| [Een opgeslagen procedure maken](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L116) |[Scripts.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.createstoredprocedureasync?view=azure-dotnet) |
| [Een opgeslagen procedure uitvoeren](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L135) |[Scripts.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.executestoredprocedureasync?view=azure-dotnet) |
| [Een opgeslagen procedure verwijderen](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L351) |[Scripts.DeleteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.deletestoredprocedureasync?view=azure-dotnet) |
