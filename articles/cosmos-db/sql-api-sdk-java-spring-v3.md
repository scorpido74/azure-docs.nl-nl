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
ms.openlocfilehash: e67e6911eeac29036dee2b68c19395b34e1d11da
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228032"
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

Met de lente gegevens Azure Cosmos DB v3 voor core (SQL) kunnen ontwikkel aars Azure Cosmos DB gebruiken in veer toepassingen. Lente gegevens Azure Cosmos DB maakt de lente gegevens interface beschikbaar voor het bewerken van data bases en verzamelingen, het werken met documenten en het uitgeven van query's. Zowel synchronisatie-als async-Api's (reactieve) worden ondersteund in hetzelfde maven-artefact. 

Lente gegevens Azure Cosmos DB een afhankelijkheid van het lente data Framework. Azure Cosmos DB SDK-team releases Maven artefacten voor lente gegevens v 2.2 en v 2.3.

Het [lente-Framework](https://spring.io/projects/spring-framework) is een programmeer-en configuratie model dat het ontwikkelen van Java-toepassingen stroomlijnt. Als u de website van de organisatie wilt citeren, stroomlijnt lente de ' sanitaire ' van toepassingen die gebruikmaken van afhankelijkheids injectie. Veel ontwikkel aars zoals lente, want het maken en testen van toepassingen wordt gecompliceerder. [Spring boot](https://spring.io/projects/spring-boot) breidt dit idee uit van het afhandelen van de sanitair met een ogen ding van webtoepassingen en de ontwikkeling van micro Services. [Lente gegevens](https://spring.io/projects/spring-data) zijn een programmeer model en een framework voor toegang tot gegevens opslag, zoals Azure Cosmos DB vanuit de context van een lente-of Spring boot-toepassing. 

U kunt lente gegevens Azure Cosmos DB gebruiken in uw [Azure lente-Cloud](https://azure.microsoft.com/services/spring-cloud/) toepassingen.

> [!IMPORTANT]  
> Deze release opmerkingen zijn voor v3 gegevens Azure Cosmos DB. [Hier](sql-api-sdk-java-spring-v2.md)vindt u de release opmerkingen voor v2. 
>
> Lente gegevens Azure Cosmos DB bieden alleen ondersteuning voor SQL API.
>
> De volgende hand leidingen ondersteunen lente gegevens op andere Azure Cosmos DB-Api's:
> * [Lente gegevens voor Apache Cassandra met Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Lente gegevens MongoDB met Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Lente gegevens Gremlin met Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Hier beginnen

# <a name="explore"></a>[Verkennen](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

### <a name="navigate-the-tabs-above-for-basic-spring-data-azure-cosmos-db-samples"></a>Ga naar de bovenstaande tabbladen voor voor beelden van Basic lente-gegevens Azure Cosmos DB.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Afhankelijkheden configureren

Er zijn twee lente gegevens Azure Cosmos DB v3-maven artefacten beschikbaar.

Artefacten die afhankelijk zijn van Lente data Framework v 2.2:
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-2-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

Artefacten die afhankelijk zijn van Lente data Framework v 2.3:
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-3-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

# <a name="connect"></a>[Verbinding maken](#tab/connect)

### <a name="connect"></a>Verbinden

Geef Azure Cosmos DB account en container Details op. Lente gegevens Azure Cosmos DB maakt de client automatisch en maakt verbinding met de container.

[Application. Properties](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-2-cosmos-java-getting-started/src/main/resources/application.properties):
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

[Maken](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]

[Verwijderen](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

# <a name="query"></a>[Query](#tab/queries)

### <a name="query"></a>Query’s uitvoeren

[Query](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

---

## <a name="helpful-content"></a>Nuttige inhoud

| Inhoud | Lente data Framework v 2.2 | Lente data Framework v 2.3 |
|---|---|
| **SDK downloaden** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-2-cosmos) | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-3-cosmos) |
|**Bijdragen aan SDK** | [Lente gegevens Azure Cosmos DB opslag plaats op GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-2-cosmos) | [Lente gegevens Azure Cosmos DB opslag plaats op GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-3-cosmos) | 
|**Zelfstudie**| [Zelf studie over Azure Cosmos DB van Lente gegevens op GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-2-cosmos-java-getting-started) | [Zelf studie over Azure Cosmos DB van Lente gegevens op GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-3-cosmos-java-getting-started) |

## <a name="release-history"></a>Release geschiedenis

### <a name="300-beta2-unreleased"></a>3.0.0-bèta versie 2 (niet-vrijgegeven)

### <a name="300-beta1-2020-08-17"></a>3.0.0-Beta. 1 (2020-08-17)
#### <a name="new-features"></a>Nieuwe functies
* De groeps-ID is bijgewerkt naar `com.azure` .
* De artefact-ID is bijgewerkt naar `azure-spring-data-2-3-cosmos` .
* De Azure-Cosmos SDK-afhankelijkheid is bijgewerkt naar `4.3.2-beta.2` .
* Ondersteuning voor het controleren van entiteiten-automatisch beheer van velden van het soort createdBy, createdDate, lastModifiedBy en lastModifiedDate aantekening.
* `@GeneratedValue` ondersteuning voor aantekeningen voor het automatisch genereren van id's voor ID-velden van het `String` type.
* Ondersteuning voor configuratie van meerdere data bases voor één Cosmos-account met meerdere data bases en meerdere Cosmos-accounts met meerdere data bases.
* Ondersteuning voor `@Version` aantekening voor een wille keurige teken reeks veld.
* Bijgewerkte synchronisatie-Api's retour neren typen naar `Iterable` typen in plaats van `List` .
* Wordt weer gegeven `CosmosClientBuilder` van de Cosmos SDK als lente bonen tot `@Configuration` klasse.
* Bijgewerkt `CosmosConfig` met query-metrische gegevens en de diagnostische gegevens van de reactie op de processor.
* Ondersteuning voor het retour neren van `Optional` gegevens typen voor query's met één resultaat.
#### <a name="renames"></a>Naam
* `CosmosDbFactory` tot `CosmosFactory` .
* `CosmosDBConfig` tot `CosmosConfig` .
* `CosmosDBAccessException` tot `CosmosAccessException` .
* `Document` aantekening naar `Container` aantekening.
* `DocumentIndexingPolicy` aantekening naar `CosmosIndexingPolicy` aantekening.
* `DocumentQuery` tot `CosmosQuery` .
* Application. Properties-markering `populateQueryMetrics` naar `queryMetricsEnabled` .
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Planning van de logboek registratie van diagnostische gegevens naar `Parallel` threads om te voor komen dat Netty-I/O-threads worden geblokkeerd.
* Vast optimistische vergren deling bij Verwijder bewerking.
* Probleem opgelost met escape-query's voor `IN` component.
* Probleem opgelost door het `long` gegevens type voor op toe te staan `@Id` .
* Probleem opgelost door `boolean` , `long` ,, `int` `double` als gegevens typen voor `@PartitionKey` aantekening toe te staan.
* Vaste `IgnoreCase`  &  `AllIgnoreCase` sleutel woorden voor het negeren van case query's.
* De standaard waarde van 4000 voor aanvraag eenheden is verwijderd bij het automatisch maken van containers.

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Volgende stappen
Zie [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-pagina voor meer informatie over Cosmos db.

Ga naar de [Start pagina](https://spring.io/projects/spring-framework)van het project voor meer informatie over het lente-Framework.

Ga naar de [Start pagina](https://spring.io/projects/spring-boot)van het project voor meer informatie over de lente boot.

Ga naar de [Start pagina](https://spring.io/projects/spring-data)van het project voor meer informatie over Lente gegevens.