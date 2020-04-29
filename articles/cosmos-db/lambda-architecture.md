---
title: Lambda-architectuur met Azure Cosmos DB en Apache Spark
description: In dit artikel wordt beschreven hoe u een lambda-architectuur implementeert met Azure Cosmos DB, HDInsight en Spark
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 68ce06d8a2904bf99f58a53817444b2992b23501
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76719735"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: een lambda-architectuur op het Azure-platform implementeren 

Lambda-architecturen bieden efficiënte gegevens verwerking van enorme gegevens sets. Lambda-architecturen maken gebruik van batch verwerking, stroom verwerking en het leveren van een laag om de latentie te minimaliseren bij het uitvoeren van query's in big data. 

Als u een lambda-architectuur op Azure wilt implementeren, kunt u de volgende technologieën combi neren om real-time big data Analytics te versnellen:
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), de eerste wereld wijd gedistribueerde, multi-model database service van de branche. 
* [Apache Spark for Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), een verwerkings raamwerk dat grootschalige toepassingen voor gegevens analyse uitvoert
* Azure Cosmos DB [Change feed](change-feed.md), waarmee nieuwe gegevens worden gestreamd naar de batch-laag voor het verwerken van HDInsight
* De [Spark-naar-Azure Cosmos DB-connector](spark-connector.md)

In dit artikel worden de basis principes beschreven van een lambda-architectuur op basis van het oorspronkelijke ontwerp voor meerdere lagen en de voor delen van een ' opnieuw gevormde ' Lambda-architectuur die de bewerkingen vereenvoudigt.  

## <a name="what-is-a-lambda-architecture"></a>Wat is een lambda-architectuur?
Een lambda-architectuur is een generieke, schaal bare en fout tolerante architectuur voor gegevens verwerking om batch-en snelheids latentie scenario's te verhelpen zoals beschreven door [Nathan Marz](https://twitter.com/nathanmarz).

![Diagram van een lambda-architectuur](./media/lambda-architecture/lambda-architecture-intro.png)

Bron: http://lambda-architecture.net/

De basis principes van een lambda-architectuur worden beschreven in het voor gaande diagram [http://lambda-architecture.net](http://lambda-architecture.net/)als per.

 1. Alle **gegevens** *worden naar de* *batch-laag* en de *snelheids laag*gepusht.
 2. De **batch-laag** bevat een hoofd gegevensset (onveranderbaar, alleen toevoegen van onbewerkte gegevens) en de batch weergaven vooraf berekent.
 3. De op-leverende **laag** heeft batch weergaven voor snelle query's. 
 4. De **snelheids laag** compenseert de verwerkings tijd (naar de server voor het leveren van gegevens) en behandelt alleen recent gebruikte informatie.
 5. Alle query's kunnen worden beantwoord door de resultaten van batch weergaven en realtime weer gaven samen te voegen of ze afzonderlijk te pingen.

Na verdere Lees bewerkingen kunnen we deze architectuur implementeren met behulp van alleen de volgende:

* Azure Cosmos-container (s)
* HDInsight-cluster (Apache Spark 2,1)
* Spark-connector [1,0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.6/jar)

## <a name="speed-layer"></a>Speed Layer

Vanuit een bewerkings perspectief kunnen twee gegevens stromen worden onderhouden terwijl de juiste status van de gegevens wordt gegarandeerd. Gebruik de [Azure Cosmos DB Change feed-ondersteuning](change-feed.md) om de status van de *batch laag* te hand haven, terwijl u het Azure Cosmos DB wijzigingen logboek via de feed- *API* voor uw *snelheid slaag*onthult om de bewerkingen te vereenvoudigen.  
![Diagram van het markeren van de nieuwe gegevens, de snelheids laag en de hoofd gegevensset van de Lambda-architectuur](./media/lambda-architecture/lambda-architecture-change-feed.png)

Wat is er belang rijk in deze lagen:

 1. Alle **gegevens** worden *alleen* naar Azure Cosmos DB gepusht, zodat u problemen met meerdere Casting kunt voor komen.
 2. De **batch-laag** bevat een hoofd gegevensset (onveranderbaar, alleen toevoegen van onbewerkte gegevens) en de batch weergaven vooraf berekent.
 3. De leverende **laag** wordt beschreven in de volgende sectie.
 4. De **snelheid slaag** maakt gebruik van HDInsight (Apache Spark) om de Azure Cosmos DB wijzigings feed te lezen. Zo kunt u uw gegevens persistent maken en deze gelijktijdig opvragen en verwerken.
 5. Alle query's kunnen worden beantwoord door de resultaten van batch weergaven en realtime weer gaven samen te voegen of ze afzonderlijk te pingen.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Code voorbeeld: in Spark gestructureerd streamen naar een Azure Cosmos DB wijzigings feed
Als u een snel prototype wilt uitvoeren van de Azure Cosmos DB wijzigings feed als onderdeel van de **Speed laag**, kunt u deze testen met behulp van Twitter-gegevens als onderdeel van de wijzigingen in de [stroom verwerking met behulp van Azure Cosmos DB wijzigings invoer en Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) voor beeld. Als u uw Twitter-uitvoer snel wilt starten, raadpleegt u het code voorbeeld in [Stream-feed van Twitter naar Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed). In het voor gaande voor beeld laadt u Twitter-gegevens in Azure Cosmos DB en vervolgens kunt u uw HDInsight-cluster (Apache Spark) instellen om verbinding te maken met de wijzigings feed. Zie [Apache Spark Azure Cosmos DB connector Setup](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup)voor meer informatie over het instellen van deze configuratie.  

Het volgende code fragment laat zien hoe u `spark-shell` configureert om een Structured streaming-taak uit te voeren om verbinding te maken met een Azure Cosmos DB wijzigings feed, die de realtime Twitter-gegevens stroom controleert, om een lopend interval aantal uit te voeren.

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

Zie voor beelden van volledige code [Azure-cosmosdb-Spark/Lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), waaronder:
* [Streaming-query van Cosmos DB Change feed. scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Streaming-Tags query van Cosmos DB Change feed. scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

De uitvoer van dit is een `spark-shell` -console, die doorlopend een Structured streaming-taak uitvoert die een interval telling van de Twitter-gegevens uit de Azure Cosmos DB wijzigings feed uitvoert. In de volgende afbeelding ziet u de uitvoer van de stroom taak en de interval aantallen.

![Streaming-uitvoer met het interval aantal voor de Twitter-gegevens van de Azure Cosmos DB wijzigings feed](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Zie voor meer informatie over Azure Cosmos DB Change feed:

* [Werken met de ondersteuning voor wijzigings invoer in Azure Cosmos DB](change-feed.md)
* [Introductie van de Azure CosmosDB Change feed processor-bibliotheek](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Wijzigingen in de stroom verwerking: Azure CosmosDB Change feed + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Batch en voor het leveren van lagen
Omdat de nieuwe gegevens worden geladen in Azure Cosmos DB (waarbij de wijzigings feed wordt gebruikt voor de snelheid slaag), is dit de locatie van de **hoofd-gegevensset** (een onveranderlijke set van onbewerkte gegevens toevoegen). Gebruik op dit moment HDInsight (Apache Spark) voor het uitvoeren van de vooraf Compute-functies van de **batch-laag** naar de **laag**, zoals wordt weer gegeven in de volgende afbeelding:

![Diagram dat de batch-laag markeert en de laag van de Lambda-architectuur levert](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Wat is er belang rijk in deze lagen:

 1. Alle **gegevens** worden alleen naar Azure Cosmos DB gepusht (om multi cast-problemen te voor komen).
 2. De **batch-laag** bevat een hoofd gegevensset (onveranderbaar, alleen toevoegen van onbewerkte gegevens) die zijn opgeslagen in azure Cosmos db. Met behulp van HDI Spark kunt u uw aggregaties vooraf berekenen om te worden opgeslagen in uw berekende batch weergaven.
 3. De leverende **laag** is een Azure Cosmos-data base met verzamelingen voor de hoofd-gegevensset en de berekende batch weergave.
 4. De **laag snelheid** wordt verderop in dit artikel besproken.
 5. Alle query's kunnen worden beantwoord door de resultaten van de batch weergaven en realtime weer gaven samen te voegen of ze afzonderlijk te pingen.

### <a name="code-example-pre-computing-batch-views"></a>Code voorbeeld: batch weergaven vóór gebruik
Als u wilt laten zien hoe u vooraf berekende weer gaven kunt uitvoeren op uw **hoofd gegevensset** van Apache Spark naar Azure Cosmos DB, gebruikt u de volgende code fragmenten uit de notitie blokken [Lambda-architectuur Rearchitect-batch laag](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) en [Lambda-architectuur opnieuw ontworpen om laag te](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)leveren. In dit scenario gebruikt u de Twitter-gegevens die zijn opgeslagen in Azure Cosmos DB.

Laten we beginnen door de configuratie verbinding te maken met de Twitter-gegevens binnen Azure Cosmos DB met behulp van de onderstaande PySpark-code.

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

Vervolgens voert u de volgende Spark SQL-instructie uit om de Top 10 Hashtags van de set tweets te bepalen. Voor deze Spark SQL-query worden deze in een Jupyter-notebook uitgevoerd zonder het uitvoer staaf diagram direct na dit code fragment te volgen.

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

Nu u de query hebt, kunt u deze weer opslaan in een verzameling met behulp van de Spark-connector om de uitvoer gegevens op te slaan in een andere verzameling.  In dit voor beeld gebruikt u scala om de verbinding aan te tonen. Net als in het vorige voor beeld maakt u de configuratie verbinding om de Apache Spark data frame op te slaan in een andere Azure Cosmos-container.

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

Nadat u het `SaveMode` (aangeeft of `Overwrite` `Append` documenten) hebt opgegeven, maakt u `tweets_bytags` in het vorige voor beeld een data frame vergelijkbaar met de Spark SQL-query.  Als de `tweets_bytags` data frame is gemaakt, kunt u deze opslaan met `write` de-methode met behulp van de eerder opgegeven `writeConfig`.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Met deze laatste instructie is uw Spark-data frame opgeslagen in een nieuwe Azure Cosmos-container. vanuit het perspectief van een lambda-architectuur is dit de **batch weergave** in de laag die moet worden **gereserveerd**.
 
#### <a name="resources"></a>Resources

Zie voor beelden van volledige code [Azure-cosmosdb-Spark/Lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) , met inbegrip van:
* Lambda-architectuur opnieuw ontworpen-batch laag [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | -[ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda-architectuur opnieuw gearchitectd-batch voor het leveren van laag [-HTML-](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Speed Layer
Zoals eerder is vermeld, kunt u met behulp van de Azure Cosmos DB Change feed-bibliotheek de bewerkingen tussen de batch-en snelheids lagen vereenvoudigen. In deze architectuur gebruikt u Apache Spark (via HDInsight) om de *Structured streaming* -query's uit te voeren op de gegevens. Het is ook mogelijk dat u de resultaten van uw Structured streaming query's tijdelijk wilt behouden, zodat andere systemen toegang hebben tot deze gegevens.

![Diagram van het markeren van de snelheid slaag van de Lambda-architectuur](./media/lambda-architecture/lambda-architecture-speed.png)

U doet dit door een afzonderlijke Azure Cosmos-container te maken om de resultaten van uw Structured streaming-query's op te slaan.  Zo kunt u andere systemen toegang geven tot deze gegevens niet alleen Apache Spark. Daarnaast kunt u met de Cosmos DB TTL-functie (time-to-Live) uw documenten zo configureren dat deze automatisch worden verwijderd na een ingestelde duur.  Zie voor meer informatie over de functie van de Azure Cosmos DB TTL [gegevens laten verlopen in azure Cosmos-containers automatisch met time to Live](time-to-live.md)

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

## <a name="lambda-architecture-rearchitected"></a>Lambda-architectuur: opnieuw gearchitect
Zoals vermeld in de vorige secties, kunt u de oorspronkelijke Lambda-architectuur vereenvoudigen met behulp van de volgende onderdelen:
* Azure Cosmos DB
* De Azure Cosmos DB Change feed-bibliotheek om te voor komen dat u uw gegevens tussen de batch-en snelheids lagen hoeft te casten
* Apache Spark op HDInsight
* De Spark-connector voor Azure Cosmos DB

![Diagram van de herarchitectuur van de Lambda-architectuur met Azure Cosmos DB, Spark en de Azure Cosmos DB Change feed-API](./media/lambda-architecture/lambda-architecture-re-architected.png)

Met dit ontwerp hebt u slechts twee beheerde services, Azure Cosmos DB en HDInsight nodig. Samen hebben ze de batch, het leveren en de snelheids lagen van de Lambda-architectuur. Dit vereenvoudigt niet alleen de bewerkingen, maar ook de gegevens stroom. 
 1. Alle gegevens worden naar Azure Cosmos DB gepusht voor verwerking
 2. De batch-laag bevat een hoofd gegevensset (onveranderbaar, alleen toevoegen van onbewerkte gegevens) en de batch weergaven worden vooraf berekend
 3. De te leveren laag heeft batch weergaven van gegevens voor snelle query's.
 4. De snelheids laag compenseert de verwerkings tijd (naar de server voor het leveren van gegevens) en behandelt alleen recent gebruikte informatie.
 5. Alle query's kunnen worden beantwoord door de resultaten van batch weergaven en realtime-weer gaven samen te voegen.

### <a name="resources"></a>Resources

* **Nieuwe gegevens**: de [Stream-feed van Twitter naar CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), het mechanisme om nieuwe gegevens naar Azure Cosmos DB te pushen.
* **Batch-laag:** De batch-laag bestaat uit de *hoofd-gegevensset* (een onveranderlijke set van onbewerkte gegevens) en de mogelijkheid om batch weergaven vooraf te berekenen van de gegevens die worden gepusht naar de **Server**die het leveren.
   * Met de **Lambda-architectuur opnieuw gearchitectd-batch-laag** notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) wordt een query uitgevoerd op de *gegevensset* van batch weergaven.
* Leverende **laag:** De leverende **laag** bestaat uit vooraf berekende gegevens die resulteren in batch weergaven (bijvoorbeeld aggregaties, specifieke Slicers enz.) voor snelle query's.
  * De **Lambda-architectuur opnieuw gearchitectd-batch voor** het leveren van laag notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) duwt de batch gegevens naar de server voor het leveren van de laag. dat wil zeggen dat Spark een batch verzameling van tweets, verwerkt en opslaat in een andere verzameling (een berekende batch).
    * **Speed Layer:** De **snelheid slaag** bestaat uit Spark waarbij de Azure Cosmos DB wijzigings feed direct wordt gebruikt om te lezen en actie te ondernemen. De gegevens kunnen ook worden opgeslagen in de berekening van een *RT* zodat andere systemen de verwerkte realtime gegevens kunnen opvragen in plaats van een realtime-query zelf uit te voeren.
  * De [streaming-query uit Cosmos DB script scala Change feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) voert een streaming-query uit van de Azure Cosmos DB wijzigings feed om een interval telling te berekenen op basis van de Spark-shell.
  * De [streaming-Tags query uit Cosmos DB script scala Change feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) voert een streaming-query uit van de Azure Cosmos DB wijzigings feed om een interval telling van tags van de Spark-shell te berekenen.
  
## <a name="next-steps"></a>Volgende stappen
Als u dat nog niet hebt gedaan, downloadt u de Spark naar Azure Cosmos DB-connector van de [Azure-cosmosdb-Spark github-](https://github.com/Azure/azure-cosmosdb-spark) opslag plaats en bekijkt u de aanvullende bronnen in de opslag plaats:
* [Lambda-architectuur](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Voor beelden van gedistribueerde aggregaties](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Voorbeeld scripts en notitie blokken](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Demo's van Structured streaming](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Demonstraties van feeds wijzigen](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Wijzigingen in de stroom verwerking met Azure Cosmos DB wijzigings feed en Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Mogelijk wilt u ook de [Apache Spark SQL-, DataFrames-en gegevens sets gids](https://spark.apache.org/docs/latest/sql-programming-guide.html) en het [Apache Spark in azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) -artikel bekijken.
