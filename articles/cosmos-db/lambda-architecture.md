---
title: Lambda-architectuur met Azure Cosmos DB en Apache Spark
description: In dit artikel wordt beschreven hoe u een lambda-architectuur implementeert met Azure Cosmos DB, HDInsight en Spark
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 68ce06d8a2904bf99f58a53817444b2992b23501
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76719735"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: Een lambda-architectuur implementeren op het Azure-platform 

Lambda architecturen maken een efficiënte gegevensverwerking van enorme datasets mogelijk. Lambda-architecturen gebruiken batchverwerking, streamverwerking en een serverlaag om de latentie die betrokken is bij het opvragen van big data te minimaliseren. 

Als u een lambda-architectuur op Azure wilt implementeren, u de volgende technologieën combineren om realtime big data-analyses te versnellen:
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), de eerste wereldwijd gedistribueerde databaseservice met meerdere modellen. 
* [Apache Spark voor Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), een verwerkingsframework dat grootschalige toepassingen voor gegevensanalyse uitvoert
* Azure Cosmos DB [change feed](change-feed.md), die nieuwe gegevens streamt naar de batchlaag voor HDInsight om te verwerken
* De [Spark naar Azure Cosmos DB-connector](spark-connector.md)

Dit artikel beschrijft de fundamenten van een lambda architectuur gebaseerd op de oorspronkelijke multi-layer design en de voordelen van een "rearchitected" lambda architectuur die operaties vereenvoudigt.  

## <a name="what-is-a-lambda-architecture"></a>Wat is een lambda architectuur?
Een lambda-architectuur is een generieke, schaalbare en fouttolerante gegevensverwerkingsarchitectuur om batch- en snelheidslatentiescenario's aan te pakken zoals beschreven door [Nathan Marz.](https://twitter.com/nathanmarz)

![Diagram dat een lambdaarchitectuur toont](./media/lambda-architecture/lambda-architecture-intro.png)

Bron: http://lambda-architecture.net/

De basisprincipes van een lambdaarchitectuur worden in het [http://lambda-architecture.net](http://lambda-architecture.net/)voorgaande diagram per .

 1. Alle **gegevens** worden in zowel de *batchlaag* *als* *de snelheidslaag*gepusht.
 2. De **batchlaag** heeft een hoofdgegevensset (onveranderlijke, alleen toevoegenset met ruwe gegevens) en berekent de batchweergaven vooraf.
 3. De **serverlaag** heeft batchweergaven voor snelle query's. 
 4. De **snelheidslaag** compenseert de verwerkingstijd (naar de serveerlaag) en behandelt alleen recente gegevens.
 5. Alle query's kunnen worden beantwoord door resultaten van batchweergaven en realtime weergaven samen te voegen of ze afzonderlijk te pingen.

Bij nader inzien kunnen we deze architectuur implementeren met alleen de volgende:

* Azure Cosmos-container(en)
* CLUSTER HDInsight (Apache Spark 2.1)
* Spark-connector [1.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.6/jar)

## <a name="speed-layer"></a>Snelheidslaag

Vanuit een operations perspectief, het handhaven van twee stromen van gegevens, terwijl het waarborgen van de juiste toestand van de gegevens kan een ingewikkelde inspanning. Als u de bewerkingen wilt vereenvoudigen, gebruikt u de [azure cosmos DB-feedondersteuning](change-feed.md) om de status voor de *batchlaag* te behouden en tegelijkertijd het Azure Cosmos DB-wijzigingslogboek te onthullen via de *Change Feed API* voor uw *snelheidslaag.*  
![Diagram met het nieuwe gegevens-, snelheidslaag- en hoofdgegevenssetgedeelte van de lambda-architectuur](./media/lambda-architecture/lambda-architecture-change-feed.png)

Wat is belangrijk in deze lagen:

 1. Alle **gegevens** worden *alleen* in Azure Cosmos DB gepusht, zodat u problemen met meerdere castingkunt voorkomen.
 2. De **batchlaag** heeft een hoofdgegevensset (onveranderlijke, alleen toevoegenset met ruwe gegevens) en berekent de batchweergaven vooraf.
 3. De **serveerlaag** wordt in de volgende sectie besproken.
 4. De **snelheidslaag** maakt gebruik van HDInsight (Apache Spark) om de Azure Cosmos DB-wijzigingsfeed te lezen. Dit stelt u in staat om uw gegevens te blijven bestaan en om deze gelijktijdig op te vragen en te verwerken.
 5. Alle query's kunnen worden beantwoord door resultaten van batchweergaven en realtime weergaven samen te voegen of ze afzonderlijk te pingen.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Codevoorbeeld: Gestructureerde streaming naar een Azure Cosmos DB-wijzigingsfeed stimuleren
Als u een snel prototype van de Azure Cosmos DB-wijzigingsfeed wilt uitvoeren als onderdeel van de **snelheidslaag,** u deze testen met Behulp van Twitter-gegevens als onderdeel van de [stroomverwerkingswijzigingen met behulp](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) van het voorbeeld Azure Cosmos DB Change Feed en Apache Spark. Als u uw Twitter-uitvoer wilt starten, raadpleegt u het codevoorbeeld in [streamfeed van Twitter naar Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed). Met het vorige voorbeeld laadt u Twitter-gegevens in Azure Cosmos DB en u vervolgens uw HDInsight-cluster (Apache Spark) instellen om verbinding te maken met de wijzigingsfeed. Zie Apache Spark naar Azure Cosmos [DB Connector Setup](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup)voor meer informatie over het instellen van deze configuratie.  

In het volgende codefragment `spark-shell` ziet u hoe u configureren om een gestructureerde streamingtaak uit te voeren om verbinding te maken met een Azure Cosmos DB-wijzigingsfeed, waarin de realtime Twitter-gegevensstroom wordt beoordeeld, om een aantal lopende intervalgegevens uit te voeren.

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceConfigMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[MASTER KEY]",
"Database" -> "[DATABASE]",
"Collection" -> "[COLLECTION]",
"ConnectionMode" -> "Gateway",
"ChangeFeedCheckpointLocation" -> "checkpointlocation",
"changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Interval Count")

// Start reading change feed as a stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()

// Start streaming query to console sink
val query = streamData.withColumn("countcol", streamData.col("id").substr(0, 0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
```

Zie [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)voor volledige codevoorbeelden, waaronder:
* [Streamingquery van Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Streaming tags Query van Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

De uitvoer hiervan `spark-shell` is een console, die continu een gestructureerde streamingtaak uitvoert die een intervaltelling uitvoert ten opzichte van de Twitter-gegevens uit de Azure Cosmos DB-wijzigingsfeed. In de volgende afbeelding ziet u de uitvoer van de streamtaak en telt de intervaltellingen.

![Streaming-uitvoer met het intervalaantal voor de Twitter-gegevens uit de Azure Cosmos DB-wijzigingsfeed](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Zie voor meer informatie over de feed wijzigen van Azure Cosmos DB:

* [Werken met de ondersteuning voor wijzigingsfeed in Azure Cosmos DB](change-feed.md)
* [Introductie van de Azure CosmosDB Change Feed Processor Library](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Wijzigingen in streamverwerking: Azure CosmosDB wijzigt feed + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Batch- en serveerlagen
Aangezien de nieuwe gegevens worden geladen in Azure Cosmos DB (waar de wijzigingsfeed wordt gebruikt voor de snelheidslaag), bevindt dit de **hoofdgegevensset** (een onveranderlijke, alleen-toetemakenset van ruwe gegevens) zich. Gebruik vanaf dit punt HDInsight (Apache Spark) om de pre-compute-functies uit te voeren van de **batchlaag** tot **de serveerlaag,** zoals in de volgende afbeelding wordt weergegeven:

![Diagram met de batchlaag en de serveerlaag van de lambda-architectuur](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Wat is belangrijk in deze lagen:

 1. Alle **gegevens** worden alleen in Azure Cosmos DB gepusht (om problemen met meerdere cast's te voorkomen).
 2. De **batchlaag** heeft een hoofdgegevensset (onveranderlijke, alleen toetemakenset met ruwe gegevens) die is opgeslagen in Azure Cosmos DB. Met HDI Spark u uw aggregaties vooraf berekenen die moeten worden opgeslagen in uw berekende batchweergaven.
 3. De **serverlaag** is een Azure Cosmos-database met verzamelingen voor de hoofdgegevensset en de gegevenstabelweergave.
 4. De **snelheidslaag** wordt later in dit artikel besproken.
 5. Alle query's kunnen worden beantwoord door resultaten van de batchweergaven en realtimeweergaven samen te voegen of ze afzonderlijk te pingen.

### <a name="code-example-pre-computing-batch-views"></a>Codevoorbeeld: Batchweergaven vooraf berekenen
Als u wilt laten zien hoe u vooraf berekende weergaven uitvoert ten opzichte van uw **hoofdgegevensset** van Apache Spark tot Azure Cosmos DB, gebruikt u de volgende codefragmenten van de notitieblokken [Lambda Architecture Rearchitected - Batch Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) en [Lambda Architecture Rearchitected - Batch to Serving Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). Gebruik in dit scenario de Twitter-gegevens die zijn opgeslagen in Azure Cosmos DB.

Laten we beginnen met het maken van de configuratieverbinding met de Twitter-gegevens binnen Azure Cosmos DB met behulp van de PySpark-code hieronder.

```
# Configuration to connect to Azure Cosmos DB
tweetsConfig = {
  "Endpoint" : "[Endpoint URL]",
  "Masterkey" : "[Master Key]",
  "Database" : "[Database]",
  "Collection" : "[Collection]", 
  "preferredRegions" : "[Preferred Regions]",
  "SamplingRatio" : "1.0",
  "schema_samplesize" : "200000",
  "query_custom" : "[Cosmos DB SQL Query]"
}

# Create DataFrame
tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()

# Create Temp View (to run Spark SQL statements)
tweets.createOrReplaceTempView("tweets")
```

Laten we vervolgens de volgende Spark SQL-instructie uitvoeren om de top 10-hashtags van de set tweets te bepalen. Voor deze Spark SQL-query voeren we deze uit in een Jupyter-notitieblok zonder dat de uitvoerbalkdiagram dit codefragment rechtstreeks volgt.

```
%%sql
select hashtags.text, count(distinct id) as tweets
from (
  select 
    explode(hashtags) as hashtags,
    id
  from tweets
) a
group by hashtags.text
order by tweets desc
limit 10
```

![Grafiek met het aantal tweets per hashtag](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Nu u uw query hebt, slaan we deze op in een verzameling met behulp van de Spark Connector om de uitvoergegevens op te slaan in een andere verzameling.  Gebruik in dit voorbeeld Scala om de verbinding te demonstreren. Net als in het vorige voorbeeld maakt u de configuratieverbinding om het Apache Spark DataFrame op te slaan in een andere Azure Cosmos-container.

```
val writeConfigMap = Map(
    "Endpoint" -> "[Endpoint URL]",
    "Masterkey" -> "[Master Key]",
    "Database" -> "[Database]",
    "Collection" -> "[New Collection]", 
    "preferredRegions" -> "[Preferred Regions]",
    "SamplingRatio" -> "1.0",
    "schema_samplesize" -> "200000"
)

// Configuration to write
val writeConfig = Config(writeConfigMap)

```

Nadat u `SaveMode` de (aangeven `Overwrite` of `Append` aan- `tweets_bytags` of documenten) hebt opgegeven, maakt u een DataFrame dat vergelijkbaar is met de Spark SQL-query in het vorige voorbeeld.  Met `tweets_bytags` het DataFrame u deze `write` opslaan met `writeConfig`behulp van de methode met behulp van de eerder opgegeven.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Deze laatste instructie heeft nu uw Spark DataFrame opgeslagen in een nieuwe Azure Cosmos-container; vanuit een lambda architectuur perspectief, dit is uw **batch weergave** binnen de **serveerlaag.**
 
#### <a name="resources"></a>Resources

Zie [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) voor volledige codevoorbeelden, waaronder:
* Lambda Architecture Rearchitected - Batch Layer [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda Architecture Rearchitected - Batch to Serving Layer [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Snelheidslaag
Zoals eerder opgemerkt, u met behulp van de Azure Cosmos DB Change Feed Library de bewerkingen tussen de batch- en snelheidslagen vereenvoudigen. Gebruik in deze architectuur Apache Spark (via HDInsight) om de *gestructureerde streamingquery's* uit te voeren tegen de gegevens. U ook tijdelijk de resultaten van uw gestructureerde streamingquery's blijven voortbestaan, zodat andere systemen toegang hebben tot deze gegevens.

![Diagram dat de snelheidslaag van de lambdaarchitectuur benadrukt](./media/lambda-architecture/lambda-architecture-speed.png)

Maak hiervoor een aparte Azure Cosmos-container om de resultaten van uw gestructureerde streamingquery's op te slaan.  Hierdoor u andere systemen toegang tot deze informatie niet alleen Apache Spark. Ook met de Cosmos DB Time-to-Live (TTL)-functie u configureren dat uw documenten automatisch worden verwijderd na een ingestelde duur.  Zie [Gegevens vervallen in Azure Cosmos-containers automatisch met de tijd om te leven](time-to-live.md) voor meer informatie over de azure cosmos DB TTL-functie.

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceCollectionName = "[SOURCE COLLECTION NAME]"
val sinkCollectionName = "[SINK COLLECTION NAME]"

val configMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[COSMOSDB MASTER KEY]",
"Database" -> "[DATABASE NAME]",
"Collection" -> sourceCollectionName,
"ChangeFeedCheckpointLocation" -> "changefeedcheckpointlocation")

val sourceConfigMap = configMap.+(("changefeedqueryname", "Structured Stream replication streaming test"))

// Start to read the stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
val sinkConfigMap = configMap.-("collection").+(("collection", sinkCollectionName))

// Start the stream writer to new collection
val streamingQueryWriter = streamData.writeStream.format(classOf[CosmosDBSinkProvider].getName).outputMode("append").options(sinkConfigMap).option("checkpointLocation", "streamingcheckpointlocation")
var streamingQuery = streamingQueryWriter.start()

```

## <a name="lambda-architecture-rearchitected"></a>Lambda architectuur: Rearchitected
Zoals in de vorige secties is opgemerkt, u de originele lambda-architectuur vereenvoudigen met behulp van de volgende componenten:
* Azure Cosmos DB
* De Azure Cosmos DB Change Feed Library om te voorkomen dat u uw gegevens moet multicasten tussen de batch- en snelheidslagen
* Apache Spark op HDInsight
* De Spark-connector voor Azure Cosmos DB

![Diagram met de rearchitectuur van de lambda-architectuur met Azure Cosmos DB, Spark en de Azure Cosmos DB Change Feed API](./media/lambda-architecture/lambda-architecture-re-architected.png)

Met dit ontwerp hebt u slechts twee beheerde services nodig, Azure Cosmos DB en HDInsight. Samen richten ze zich op de batch-, portie- en snelheidslagen van de lambda-architectuur. Dit vereenvoudigt niet alleen de bewerkingen, maar ook de gegevensstroom. 
 1. Alle gegevens worden in Azure Cosmos DB gepusht voor verwerking
 2. De batchlaag heeft een hoofdgegevensset (onveranderlijke, alleen toevoegenset met ruwe gegevens) en berekent de batchweergaven vooraf
 3. De serverlaag heeft batchweergaven van gegevens voor snelle query's.
 4. De snelheidslaag compenseert de verwerkingstijd (naar de serveerlaag) en behandelt alleen recente gegevens.
 5. Alle query's kunnen worden beantwoord door resultaten van batchweergaven en realtime weergaven samen te voegen.

### <a name="resources"></a>Resources

* **Nieuwe gegevens**: De [streamfeed van Twitter naar CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), het mechanisme om nieuwe gegevens in Azure Cosmos DB te pushen.
* **Batchlaag:** De batchlaag bestaat uit de *hoofdgegevensset* (een onveranderlijke, alleen toetemakenset met ruwe gegevens) en de mogelijkheid om batchweergaven vooraf te berekenen van de gegevens die in de **serverlaag**worden geduwd.
   * De **Lambda Architecture Rearchitected - Batch Layer** notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) query's de master *dataset* set van batch views.
* **Serveerlaag:** De **serverlaag** bestaat uit vooraf berekende gegevens die resulteren in batchweergaven (bijvoorbeeld aggregaties, specifieke slicers, enz.) voor snelle query's.
  * De **Lambda Architecture Rearchitected - Batch to Serving Layer** notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) duwt de batchgegevens naar de serveerlaag; Dat wil zeggen, Spark queries een batch verzameling tweets, verwerkt het, en slaat het op in een andere verzameling (een berekende batch).
    * **Snelheidslaag:** De **snelheidslaag** bestaat uit Spark die gebruik maakt van de Azure Cosmos DB-wijzigingsfeed om onmiddellijk te lezen en te reageren. De gegevens kunnen ook worden opgeslagen op *computerrt,* zodat andere systemen de verwerkte realtime gegevens kunnen opvragen in plaats van zelf een real-time query uit te voeren.
  * Met [de streamingquery van cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) voert het scalascript een streamingquery uit van de Azure Cosmos DB-wijzigingsfeed om een intervaltelling van de spark-shell te berekenen.
  * Met [de query Streaming Tags van Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) voert het scalascript een streamingquery uit van de Azure Cosmos DB-wijzigingsfeed om een intervalaantal tags uit de spark-shell te berekenen.
  
## <a name="next-steps"></a>Volgende stappen
Als u dit nog niet hebt gedaan, downloadt u de Spark-naar Azure Cosmos DB-connector vanuit de [GitHub-opslagplaats azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) en verkent u de extra bronnen in de repo:
* [Lambda-architectuur](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Voorbeelden van gedistribueerde aggregaties](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Voorbeeldscripts en -notitieblokken](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Gestructureerde streaming demo's](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Feeddemo's wijzigen](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Wijzigingen in streamverwerking met Azure Cosmos DB Change Feed en Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Mogelijk wilt u ook de [handleiding voor Apache Spark SQL, DataFrames en Datasets](https://spark.apache.org/docs/latest/sql-programming-guide.html) en het artikel Apache Spark op Azure [HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) bekijken.
