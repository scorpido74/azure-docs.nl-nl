---
title: Apache Spark verbinden met Azure Cosmos DB
description: Meer informatie over de Azure Cosmos DB Spark-connector waarmee u Apache Spark verbinden met Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: bbc97489f0c7045040dd0189b97946c2bd8fb3c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481633"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Versnel big data-analyses met de Apache Spark naar Azure Cosmos DB-connector

U [Spark-taken](https://spark.apache.org/) uitvoeren met gegevens die zijn opgeslagen in Azure Cosmos DB met de Cosmos DB Spark-connector. Cosmos kan worden gebruikt voor batch- en streamverwerking en als een serverlaag voor toegang tot lage latentie.

U de connector gebruiken met [Azure Databricks](https://azure.microsoft.com/services/databricks) of [Azure HDInsight,](https://azure.microsoft.com/services/hdinsight/)die beheerde Spark-clusters op Azure bieden. In de volgende tabel worden ondersteunde Spark-versies weergegeven.

| Onderdeel | Versie |
|---------|-------|
| Apache Spark | 2,4.x, 2,3.x, 2,2.x en 2,1.x |
| Scala | 2.11 |
| Runtime-versie van Azure Databricks | > 3.4 |

> [!WARNING]
> Deze connector ondersteunt de core (SQL) API van Azure Cosmos DB.
> Voor Cosmos DB voor MongoDB API gebruikt u de [MongoDB Spark-connector](https://docs.mongodb.com/spark-connector/master/).
> Gebruik voor Cosmos DB Cassandra API de [Cassandra Spark-connector.](https://github.com/datastax/spark-cassandra-connector)
>

## <a name="quickstart"></a>Snelstartgids

* Volg de stappen bij [Aan de slag met de Java SDK](sql-api-async-java-get-started.md) om een Cosmos DB-account in te stellen en vul enkele gegevens in.
* Volg de stappen bij [Azure Databricks die aan de slag gaat](/azure/azure-databricks/quickstart-create-databricks-workspace-portal) met het instellen van een Azure Databricks-werkruimte en -cluster.
* U nu nieuwe notitieblokken maken en de Cosmos DB-connectorbibliotheek importeren. Ga naar [Werken met de Cosmos DB-connector](#bk_working_with_connector) voor meer informatie over het instellen van uw werkruimte.
* In de volgende sectie staan fragmenten over lezen en schrijven met behulp van de connector.

### <a name="batch-reads-from-cosmos-db"></a>Batch leest uit Cosmos DB

In het volgende fragment ziet u hoe u een Spark DataFrame maakt om te lezen vanuit Cosmos DB in PySpark.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

En hetzelfde codefragment in Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>Batch schrijft naar Cosmos DB

In het volgende fragment ziet u hoe u een gegevensframe naar Cosmos DB in PySpark schrijft.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

En hetzelfde codefragment in Scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" : "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>Streaming leest van Cosmos DB

In het volgende fragment ziet u hoe u verbinding maakt met en leest vanuit azure cosmos DB Change Feed.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
En hetzelfde codefragment in Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>Streaming schrijft naar Cosmos DB

In het volgende fragment ziet u hoe u een gegevensframe naar Cosmos DB in PySpark schrijft.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

En hetzelfde codefragment in Scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
Meer meer fragmenten en end-to-end monsters, zie [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a>Werken met de connector

Je de connector van bron in GitHub bouwen of de uber-potten van Maven downloaden in de onderstaande links.

| Spark | Scala | Nieuwste versie |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.4.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Databricks-notitieblokken gebruiken

Een bibliotheek maken met uw Databricks-werkruimte door de richtlijnen te volgen in de Azure Databricks-handleiding > [de Azure Cosmos DB Spark-connector gebruiken](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Opmerking, **de** pagina Azure Cosmos DB Spark Connector gebruiken is momenteel niet up-to-date. In plaats van het downloaden van de zes aparte potten in zes verschillende https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar) bibliotheken, u de uber pot downloaden van maven op en installeer deze een pot / bibliotheek.
> 

### <a name="using-spark-cli"></a>Met behulp van spark-cli

Om met de connector te werken met `spark-shell`behulp `pyspark` `spark-submit`van de spark-cli (dat wil zeggen,,, ) u de `--packages` parameter gebruiken met de [maven-coördinaten](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)van de connector.

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Jupyter-notitieblokken gebruiken

Als u Jupyter-notitieblokken gebruikt in HDInsight, `%%configure` kunt u de spark-magic-cel gebruiken om de maven-coördinaten van de connector op te geven.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Let op, de `spark.jars.excludes` opname van de is specifiek voor het verwijderen van potentiële conflicten tussen de connector, Apache Spark en Livy.

### <a name="build-the-connector"></a>De connector bouwen

Momenteel wordt dit `maven` connectorproject gebruikt om zonder afhankelijkheden te bouwen, u het als volgt uitvoeren:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Werken met onze monsters

De [Cosmos DB Spark GitHub-repository](https://github.com/Azure/azure-cosmosdb-spark) heeft de volgende voorbeeldnotitieblokken en -scripts die u uitproberen.

* **On-Time Flight Performance met Spark en Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html:](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html)Connect Spark to Cosmos DB met BEHULP van HDInsight Jupyter notebook service om Spark SQL, GraphFrames, showcase en het voorspellen van vlucht vertragingen met behulp van ML-pijplijnen.
* **Twitter Bron met Apache Spark en Azure Cosmos DB Change Feed:** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Apache Spark gebruiken om Cosmos DB-grafieken te bevragen:** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Azure Databricks koppelen aan](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** `azure-cosmosdb-spark`Azure Cosmos DB met behulp van .  Hier gekoppeld is ook een Azure Databricks-versie van het [on-time flight performance-notitieblok.](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)
* **[Lambda-architectuur met Azure Cosmos DB en HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: U de operationele overhead van het onderhouden van big data-pijplijnen verminderen met Cosmos DB en Spark.

## <a name="more-information"></a>Meer informatie

We hebben meer `azure-cosmosdb-spark` informatie in de [wiki,](https://github.com/Azure/azure-cosmosdb-spark/wiki) waaronder:

* [Gebruikershandleiding Azure Cosmos DB Spark Connector](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Voorbeelden van aggregaties](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Configuratie en installatie

* [Spark-connectorconfiguratie](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark to Cosmos DB-connectorsetup](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (in uitvoering)
* [Power BI Direct Query configureren naar Azure Cosmos DB via Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Problemen oplossen

* [Cosmos DB-aggregaten gebruiken](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Bekende problemen](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Prestaties

* [Prestatietips](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Querytestruns](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Testruns schrijven](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Feed wijzigen

* [Wijzigingen in streamverwerking met Azure Cosmos DB Change Feed en Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Feeddemo's wijzigen](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Gestructureerde streamdemo's](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Bewaking

* [Spark-taken bewaken met toepassingsinzichten](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan, downloadt u de Spark naar Azure Cosmos DB-connector vanuit de [GitHub-opslagplaats azure-cosmosdb-spark.](https://github.com/Azure/azure-cosmosdb-spark) Bekijk de volgende aanvullende bronnen in de repo:

* [Voorbeelden van aggregaties](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Voorbeeldscripts en -notitieblokken](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Mogelijk wilt u ook de [handleiding voor Apache Spark SQL, DataFrames en Datasets](https://spark.apache.org/docs/latest/sql-programming-guide.html)en het artikel Apache Spark op Azure [HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) bekijken.
