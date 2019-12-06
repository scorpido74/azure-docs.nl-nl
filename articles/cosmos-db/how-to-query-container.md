---
title: Query's uitvoeren op containers in Azure Cosmos DB
description: Meer informatie over het opvragen van containers in Azure Cosmos DB met behulp van in-Partition-, kruis partitie-en gedeeltelijke corss-partitie query's
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 88ebb8bb80ec3406c98b77db481994d415b04373
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872023"
---
# <a name="query-an-azure-cosmos-container"></a>Query’s uitvoeren op een Azure Cosmos-container

In dit artikel wordt uitgelegd hoe u query's kunt uitvoeren op containers (collecties, grafieken, tabellen) in Azure Cosmos DB.

## <a name="in-partition-query"></a>In-partitiequery

Wanneer een sleutel voor een partitiefilter is opgegeven voor de query en u gegevens opvraagt van containers, handelt Azure Cosmos DB de query automatisch af. Deze stuurt de query door naar de partities die overeenkomen met de partitiesleutelwaarden die zijn opgegeven in het filter. De volgende query wordt bijvoorbeeld doorgestuurd naar de partitie `DeviceId` die alle documenten bevat die overeenkomen met partitiesleutelwaarde `XMS-0001`.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Partitieoverkoepelende query

De volgende query heeft geen filter voor de partitiesleutel (`DeviceId`) en wordt verspreid over alle partities waarop deze wordt uitgevoerd op basis van de index van de partitie. Als u een query wilt uitvoeren op meerdere partities, stelt u `EnableCrossPartitionQuery` in op 'True' (of `x-ms-documentdb-query-enablecrosspartition`  in de REST-API).

De eigenschap EnableCrossPartitionQuery accepteert een Booleaanse waarde. Als deze is ingesteld op 'true' en als uw query geen partitiesleutel bevat, wordt de query door Azure Cosmos DB verspreid over alle partities. Dit wordt gedaan door afzonderlijke query's op alle partities uit te voeren. Als u de query resultaten wilt lezen, moeten de client toepassingen de resultaten van de FeedResponse gebruiken en de eigenschap ContinuationToken controleren. Om alle resultaten te lezen, moet dit worden herhaald totdat de eigenschap ContinuationToken null is. 

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Cosmos DB biedt ondersteuning voor de statistische functies COUNT, MIN, MAX en AVG voor containers door middel van SQL. De statistische functies voor containers beginnen vanaf SDK-versie 1.12.0. Query's moeten één samengestelde operator bevatten en één waarde in de projectie.

## <a name="parallel-cross-partition-query"></a>Parallelle partitieoverkoepelende query

De Cosmos DB-SDK's 1.9.0 en hoger bieden ondersteuning voor opties voor parallelle query-uitvoering. Met parallelle partitieoverkoepelende query's kunt u query's met lage latentie uitvoeren op meerdere partities. De volgende query is bijvoorbeeld geconfigureerd voor parallelle uitvoering in meerdere partities.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

U kunt parallelle queryuitvoering beheren door de volgende parameters af te stemmen:

- **MaxDegreeOfParallelism**: hiermee wordt het maximum aantal simultane netwerkverbindingen met de partities van de container ingesteld. Als u deze eigenschap instelt op -1, wordt de mate van parallelle uitvoering beheerd door de SDK. Als de  `MaxDegreeOfParallelism`  niet is opgegeven of is ingesteld op 0, wat de standaardwaarde is, is er één netwerkverbinding met de partities van de container.

- **MaxBufferedItemCount**: hiermee wordt de latentie van de query ingewisseld voor het geheugengebruik aan de clientzijde. Als deze optie wordt weggelaten of ingesteld op -1, wordt het aantal items dat in de buffer opgeslagen tijdens parallelle query-uitvoering, beheerd door de SDK.

Met dezelfde status van de verzameling, retourneert een parallelle query resultaten in dezelfde volgorde als bij seriële uitvoering. Wanneer u een partitieoverkoepelende query uitvoert met sorterings-operators (ORDER BY, TOP) uitvoert, geeft de Azure Cosmos DB-SDK de query's parallel uit over meerdere partities. Dit voegt gedeeltelijk gesorteerde resultaten aan de clientzijde samen voor globaal geordende resultaten.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over partitionering in Azure Cosmos DB:

- [Partitionering in Azure Cosmos DB](partitioning-overview.md)
- [Synthetic partition keys in Azure Cosmos DB](synthetic-partition-keys.md) (Synthetische partitiesleutels in Azure Cosmos DB)
