---
title: Azure Cosmos DB Apache Spark-connector voor opmerkingen bij de release van SQL API en bronnen
description: Meer informatie over de Azure Cosmos DB Apache Spark-connector voor de SQL-API, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/05/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 72b3b190492be5cec9986729875c5b09e2559ae5
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854247"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark connector voor Core-API (SQL): release opmerkingen en bronnen
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core-SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java-SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java-SDK v2](sql-api-sdk-java.md)
> * [Spring Data](sql-api-sdk-java-spring.md)
> * [Spark-connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resourceprovider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk-uitvoerder-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk-uitvoerder-java](sql-api-sdk-bulk-executor-java.md)

Versnel big data Analytics met behulp van de Azure Cosmos DB Apache Spark connector voor core (SQL). Met de Spark-connector kunt u [Spark](https://spark.apache.org/) -taken uitvoeren op gegevens die zijn opgeslagen in azure Cosmos db. Batch-en stroom verwerking worden ondersteund.

U kunt de connector gebruiken met [Azure Databricks](https://azure.microsoft.com/services/databricks) of [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), die beheerde Spark-clusters in Azure biedt. In de volgende tabel worden de ondersteunde Spark-versies weer gegeven.

| Onderdeel | Versie |
|---------|-------|
| Apache Spark | 2.4. x, 2.3. x, 2.2. x en 2.1. x |
| Scala | 2,11 |
| Runtime versie van Azure Databricks | > 3,4 |

> [!WARNING]
> Deze connector ondersteunt de core-(SQL) API van Azure Cosmos DB.
> Gebruik de [MongoDb Spark-connector](https://docs.mongodb.com/spark-connector/master/)voor Cosmos DB voor MONGODB-API.
> Voor Cosmos DB Cassandra-API gebruikt u de [Cassandra Spark-connector](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="helpful-content"></a>Nuttige inhoud

| Inhoud | Koppeling |
|---|---|
| **SDK downloaden** | [Downloaden van Apache Spark](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) |
|**API-documentatie** | [Naslag informatie voor Spark-connector]() |
|**Bijdragen aan SDK** | [Azure Cosmos DB connector voor Apache Spark op GitHub](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Aan de slag** | [big data Analytics versnellen met behulp van de Apache Spark om de connector te Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/spark-connector#bk_working_with_connector) <br> [Apache Spark Structured streaming gebruiken met Apache Kafka en Azure Cosmos DB](https://docs.microsoft.com/azure/hdinsight/apache-kafka-spark-structured-streaming-cosmosdb?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Release geschiedenis

### <a name="311"></a>3.1.1
#### <a name="new-features"></a>Nieuwe functies
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Corrigeert een streaming-case van een controle punt waar in de id ' | ' staat, waarbij de configuratie ' ChangeFeedMaxPagesPerBatch ' is toegepast

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Nieuwe functies
* Voegt ondersteuning toe voor bulk updates bij het gebruik van geneste partitie sleutels
* Voegt ondersteuning toe voor de gegevens typen decimaal en float tijdens het schrijven naar Cosmos DB.
* Voegt ondersteuning toe voor tijds tempel typen wanneer ze Long (UNIX-epoche) gebruiken als waarde
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten

### <a name="308"></a>3.0.8
#### <a name="new-features"></a>Nieuwe functies
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Hiermee wordt het type cast-uitzonde ring opgelost bij het gebruik van ' WriteThroughputBudget-configuratie.

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Nieuwe functies
* Voegt fout gegevens voor bulk fouten toe aan uitzonde ring en logboek.
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten

### <a name="306"></a>3.0.6
#### <a name="new-features"></a>Nieuwe functies
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Hiermee worden problemen met streaming-controle punten opgelost.

### <a name="305"></a>3.0.5
#### <a name="new-features"></a>Nieuwe functies
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Hiermee wordt het logboek niveau van een bericht verholpen dat per ongeluk is opgetreden met niveau fout om ruis te verminderen

### <a name="304"></a>3.0.4
#### <a name="new-features"></a>Nieuwe functies
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Hiermee wordt een bug in structured streaming opgelost tijdens het splitsen van partities, mogelijk als gevolg van het ontbreken van een aantal wijzigings records of het bekijken van Null-uitzonde ringen voor schrijf bewerkingen

### <a name="303"></a>3.0.3
#### <a name="new-features"></a>Nieuwe functies
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Hiermee wordt een bug opgelost waarbij een aangepast schema voor readStream wordt genegeerd

### <a name="302"></a>3.0.2
#### <a name="new-features"></a>Nieuwe functies
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Herstelt regressie (niet-gearceerde JAR bevat alle gearceerde afhankelijkheden) die de build-tijd met 50% hebben verhoogd

### <a name="301"></a>3.0.1
#### <a name="new-features"></a>Nieuwe functies
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Hiermee wordt een afhankelijkheids probleem opgelost waardoor direct Trans Port via TCP mislukt met RequestTimeoutException

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Nieuwe functies
* Verbetert het verbindings beheer en de Groepsgewijze verbinding om het aantal meta gegevens oproepen te beperken
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Volgende stappen

Zie [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-pagina voor meer informatie over Cosmos db.

Ga voor meer informatie over Apache Spark naar [de start pagina](https://spark.apache.org/).