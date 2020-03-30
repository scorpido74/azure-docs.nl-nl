---
title: Gegevens maken of invoegen in Azure Cosmos DB Cassandra API van Spark
description: In dit artikel wordt beschreven hoe u voorbeeldgegevens invoegt in API-tabellen voor Azure Cosmos DB Cassandra
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 3eb23a3d8b1098110bd8b75faa22cc483637d183
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442125"
---
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Gegevens maken/invoegen in Azure Cosmos DB Cassandra API van Spark
 
In dit artikel wordt beschreven hoe u voorbeeldgegevens invoegt in een tabel in Azure Cosmos DB Cassandra API van Spark.

## <a name="cassandra-api-configuration"></a>Cassandra API-configuratie

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="dataframe-api"></a>Api voor gegevensframe

### <a name="create-a-dataframe-with-sample-data"></a>Een gegevensframe maken met voorbeeldgegevens

```scala
// Generate a dataframe containing five records
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
).toDF("book_id", "book_author", "book_name", "book_pub_year")

//Review schema
booksDF.printSchema

//Print
booksDF.show
```

> [!NOTE]
> "Create if not exists" functionaliteit, op rijniveau, wordt nog niet ondersteund.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Persist naar Azure Cosmos DB Cassandra API

Wanneer u gegevens opslaat, u ook time-to-live- en consistentiebeleidsinstellingen instellen, zoals in het volgende voorbeeld wordt weergegeven:

```scala
//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

> [!NOTE]
> TTL op kolomniveau wordt nog niet ondersteund.

#### <a name="validate-in-cqlsh"></a>Valideren in cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>RDD-API (Resilient Distributed Database)

### <a name="create-a-rdd-with-sample-data"></a>Een RDD maken met voorbeeldgegevens
```scala
//Delete records created in the previous section 
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("delete from books_ks.books where book_id in ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999');"))

//Create RDD
val booksRDD = sc.parallelize(Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
))

//Review
booksRDD.take(2).foreach(println)
```

> [!NOTE]
> Als er geen functionaliteit bestaat, wordt de functionaliteit nog niet ondersteund.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Persist naar Azure Cosmos DB Cassandra API

Wanneer u gegevens opslaat in de Cassandra API, u ook time-to-live- en consistentiebeleidsinstellingen instellen, zoals in het volgende voorbeeld wordt weergegeven:

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>Valideren in cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>Volgende stappen

Nadat u gegevens hebt ingevoegd in de tabel Azure Cosmos DB Cassandra API, gaat u naar de volgende artikelen om andere bewerkingen uit te voeren op de gegevens die zijn opgeslagen in cosmos DB Cassandra API:
 
* [Bewerkingen lezen](cassandra-spark-read-ops.md)
* [Upsert-activiteiten](cassandra-spark-upsert-ops.md)
* [Bewerkingen verwijderen](cassandra-spark-delete-ops.md)
* [Aggregatiebewerkingen](cassandra-spark-aggregation-ops.md)
* [Bewerkingen voor tabelkopiëren](cassandra-spark-table-copy-ops.md)

