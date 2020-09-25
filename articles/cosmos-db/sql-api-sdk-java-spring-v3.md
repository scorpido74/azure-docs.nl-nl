---
title: Lente data Azure Cosmos DB v3 voor opmerkingen bij de release van SQL API en bronnen
description: Meer informatie over de lente gegevens Azure Cosmos DB v3 voor SQL API, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: f0cb3d5f9184bacef42a0258add6dd2461a71dd7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326657"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Lente gegevens Azure Cosmos DB v3 voor Core-API (SQL): release opmerkingen en bronnen
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core-SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java-SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java-SDK v2](sql-api-sdk-java.md)
> * [Lente gegevens v2](sql-api-sdk-java-spring-v2.md)
> * [Lente gegevens v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resourceprovider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk-uitvoerder-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk-uitvoerder-java](sql-api-sdk-bulk-executor-java.md)

Met de lente gegevens Azure Cosmos DB versie 3 voor core (SQL) kunnen ontwikkel aars Azure Cosmos DB gebruiken in lente-toepassingen. Lente gegevens Azure Cosmos DB maakt de lente gegevens interface beschikbaar voor het bewerken van data bases en verzamelingen, het werken met documenten en het uitgeven van query's. Zowel synchronisatie-als async-Api's (reactieve) worden ondersteund in hetzelfde maven-artefact. 

Lente gegevens Azure Cosmos DB een afhankelijkheid hebben van het lente data Framework. In het Azure Cosmos DB SDK-team worden maven-artefacten vrijgegeven voor lente gegevens versies 2,2 en 2,3.

Het [lente-Framework](https://spring.io/projects/spring-framework) is een programmeer-en configuratie model dat het ontwikkelen van Java-toepassingen stroomlijnt. Lente stroomlijnt de ' sanitaire ' van toepassingen met behulp van afhankelijkheids injectie. Veel ontwikkel aars, zoals lente, maken het bouwen en testen van toepassingen eenvoudiger. [Spring boot](https://spring.io/projects/spring-boot) breidt deze verwerking van de sanitaire toepassing uit met een ogen ding voor de ontwikkeling van een web-app en micro Services. [Lente gegevens](https://spring.io/projects/spring-data) zijn een programmeer model en een framework voor toegang tot gegevens opslag zoals Azure Cosmos DB vanuit de context van een lente-of Spring boot-toepassing. 

U kunt lente gegevens Azure Cosmos DB gebruiken in uw [Azure lente-Cloud](https://azure.microsoft.com/services/spring-cloud/) toepassingen.

> [!IMPORTANT]  
> Deze release opmerkingen zijn voor versie 3 van Lente gegevens Azure Cosmos DB. U vindt [hier release opmerkingen voor versie 2](sql-api-sdk-java-spring-v2.md). 
>
> Lente gegevens Azure Cosmos DB ondersteunt alleen de SQL-API.
>
> Zie deze artikelen voor informatie over Lente gegevens op andere Azure Cosmos DB-Api's:
> * [Lente gegevens voor Apache Cassandra met Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Lente gegevens MongoDB met Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Lente gegevens Gremlin met Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Hier beginnen

# <a name="explore"></a>[Ontdek](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

#### <a name="these-tabs-contain-basic-spring-data-azure-cosmos-db-samples"></a>Deze tabbladen bevatten elementaire lente gegevens Azure Cosmos DB voor beelden.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Afhankelijkheden configureren

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

# <a name="connect"></a>[Verbinding maken](#tab/connect)

### <a name="connect"></a>Verbinding maken

Geef Azure Cosmos DB account en container Details op. Lente gegevens Azure Cosmos DB maakt de client automatisch en maakt verbinding met de container.

[Application. Properties](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-cosmos-java-getting-started/src/main/resources/application.properties):
```
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

# <a name="doc-ops"></a>[Doc OPS](#tab/docs)

### <a name="document-operations"></a>Document bewerkingen

---

## <a name="resources"></a>Resources

* **Bijdragen aan de SDK**: [lente data Azure Cosmos DB opslag plaats op github](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos)

* **Zelf studie**: [zelf studie over Lente data Azure Cosmos DB op github](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) 

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="faq"></a>Veelgestelde vragen

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Meer informatie over het [lente-Framework](https://spring.io/projects/spring-framework).

Meer informatie over [Spring boot](https://spring.io/projects/spring-boot).

Meer informatie over [lente gegevens](https://spring.io/projects/spring-data).