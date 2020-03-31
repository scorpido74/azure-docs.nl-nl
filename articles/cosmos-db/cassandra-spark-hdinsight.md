---
title: Toegang tot Azure Cosmos DB Cassandra API van Spark op YARN met HDInsight
description: Dit artikel gaat over hoe te werken met Azure Cosmos DB Cassandra API van Spark op YARN met HDInsight
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: bef4ee14cb4a7d64d80dc5776d8ecea0f831881a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887629"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Toegang tot Azure Cosmos DB Cassandra API van Spark op YARN met HDInsight

In dit artikel wordt uitgelegd hoe u toegang krijgt tot Azure Cosmos DB Cassandra API van Spark on YARN met HDInsight-Spark van spark-shell. HDInsight is Microsoft's Hortonworks Hadoop PaaS op Azure dat objectopslag voor HDFS gebruikt en in verschillende smaken komt, waaronder [Spark.](../hdinsight/spark/apache-spark-overview.md)  Hoewel de inhoud in dit document verwijst naar HDInsight-Spark, is deze van toepassing op alle Hadoop-distributies.  

## <a name="prerequisites"></a>Vereisten

* [Azure Cosmos DB Cassandra API inrichten](create-cassandra-dotnet.md#create-a-database-account)

* [Bekijk de basisprincipes van verbinding maken met Azure Cosmos DB Cassandra API](cassandra-spark-generic.md)

* [Een HDInsight-Spark-cluster inrichten](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Bekijk de codevoorbeelden voor het werken met Cassandra API](cassandra-spark-generic.md#next-steps)

* [Gebruik cqlsh voor validatie als u dat liever hebt](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Cassandra API-configuratie in Spark2** - De Spark-connector voor Cassandra vereist dat de Cassandra-verbindingsgegevens worden geïnitialiseerd als onderdeel van de Spark-context. Wanneer u een Jupyter-notebook start, zijn de vonksessie en context al geïnitialiseerd en is het niet raadzaam om de Spark-context te stoppen en opnieuw te initialiseren, tenzij deze is voltooid met elke configuratieset als onderdeel van de standaard start-up van de HDInsight-laptop. Een tijdelijke oplossing is om de Cassandra-instantiedetails rechtstreeks toe te voegen aan Ambari, Spark2-serviceconfiguratie. Dit is een eenmalige activiteit per cluster waarvoor een Spark2-service opnieuw moet worden opgestart.
 
  1. Ga naar Ambari, Spark2-service en selecteer configs

  2. Ga vervolgens naar aangepaste spark2-standaardinstellingen en voeg een nieuwe eigenschap toe met de volgende en start de Spark2-service opnieuw:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Toegang tot Azure Cosmos DB Cassandra API vanuit Spark shell

Spark shell wordt gebruikt voor het testen / exploratie doeleinden.

* Start spark-shell met de vereiste maven-afhankelijkheden die compatibel zijn met de Spark-versie van uw cluster.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Sommige DDL- en DML-bewerkingen uitvoeren

  ```scala
  import org.apache.spark.rdd.RDD
  import org.apache.spark.{SparkConf, SparkContext}

  import spark.implicits._
  import org.apache.spark.sql.functions._
  import org.apache.spark.sql.Column
  import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType,LongType,FloatType,DoubleType, TimestampType}
  import org.apache.spark.sql.cassandra._

  //Spark connector
  import com.datastax.spark.connector._
  import com.datastax.spark.connector.cql.CassandraConnector

  //CosmosDB library for multiple retry
  import com.microsoft.azure.cosmosdb.cassandra

  // Specify connection factory for Cassandra
  spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

  // Parallelism and throughput configs
  spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
  spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
  spark.conf.set("spark.cassandra.output.concurrent.writes", "100")
  spark.conf.set("spark.cassandra.concurrent.reads", "512")
  spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
  spark.conf.set("spark.cassandra.connection.keep_alive_ms", "60000000") //Increase this number as needed
  ```

* CRUD-bewerkingen uitvoeren

  ```scala
  //1) Create table if it does not exist
  val cdbConnector = CassandraConnector(sc)
  cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000;"))

  //2) Delete data from potential prior runs
  cdbConnector.withSessionDo(session => session.execute("DELETE FROM books_ks.books WHERE book_id IN ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999','b000009');"))

  //3) Generate a few rows
  val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
  ).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

  //4) Persist
  booksDF.write.mode("append").format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000")).save()

  //5) Read the data in the table
  spark.read.format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks")).load.show
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Toegang tot Azure Cosmos DB Cassandra API vanuit Jupyter-notitieblokken

HDInsight-Spark wordt geleverd met Zeppelin- en Jupyter-laptopservices. Het zijn beide webgebaseerde notebookomgevingen die Scala en Python ondersteunen. Notebooks zijn ideaal voor interactieve verkennende analyses en samenwerking, maar niet bedoeld voor operationele/ geproductiede processen.

De volgende Jupyter-notitieblokken kunnen worden geüpload naar uw HDInsight Spark-cluster en bieden kant-en-klare voorbeelden voor het werken met Azure Cosmos DB Cassandra API. Controleer het eerste notitieblok `1.0-ReadMe.ipynb` dat de Spark-serviceconfiguratie controleert voor verbinding met Azure Cosmos DB Cassandra API.

Download deze notitieblokken onder [azure-cosmos-db-cassandra-api-spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/master/scala/) naar uw machine.
  
### <a name="how-to-upload"></a>Uploaden:
Wanneer u Jupyter start, navigeert u naar Scala. Maak eerst een map en upload vervolgens de notitieblokken naar de map. De uploadknop bevindt zich aan de bovenkant, rechterzijde.  

### <a name="how-to-run"></a>Hoe te lopen:
Voer de notitieblokken en elke notebookcel achter elkaar uit.  Klik op de knop Uitvoeren boven aan elk notitieblok om alle cellen uit te voeren of shift+enter voor elke cel.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Toegang met Azure Cosmos DB Cassandra API vanuit uw Spark Scala-programma

Voor geautomatiseerde processen in productie worden Spark-programma's via [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html)aan het cluster voorgelegd.

## <a name="next-steps"></a>Volgende stappen

* [Hoe maak je een Spark Scala-programma in een IDE en stuur het naar het HDInsight Spark cluster via Livy voor uitvoering](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Verbinding maken met Azure Cosmos DB Cassandra API vanuit een Spark Scala-programma](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Volledige lijst met codevoorbeelden voor het werken met Cassandra API](cassandra-spark-generic.md)
