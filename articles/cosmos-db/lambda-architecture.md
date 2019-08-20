---
title: Lambda-architectuur met Azure Cosmos DB en HDInsight (Apache Spark)
description: In dit artikel wordt beschreven hoe u voor het implementeren van een lambda-architectuur met behulp van Azure Cosmos DB, HDInsight en Spark
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 56f293600d876a5bc52b618ce8eed044e93f424d
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616877"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: Een lambda-architectuur implementeren op het Azure-platform 

Lambda-architectuur inschakelen efficiënte verwerking van grote gegevenssets. Lambda-architectuur gebruik batch-verwerking, streamverwerking en een leveringslaag om de latentie die betrokken zijn bij het uitvoeren van query's big data te minimaliseren. 

Voor het implementeren van een lambda-architectuur op Azure, kunt u de volgende technologieën voor het versnellen van realtime big data-analyses te combineren:
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), de bedrijfstak van de eerste wereldwijd gedistribueerde, multi-model database-service. 
* [Apache Spark voor Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), een framework voor gegevensverwerking waarop grootschalige toepassingen voor gegevensanalyse
* Azure Cosmos DB [wijzigingenfeed](change-feed.md), waarmee nieuwe gegevens met de batchlaag voor HDInsight voor het verwerken van streams
* De [Spark op Azure Cosmos DB-Connector](spark-connector.md)

Dit artikel beschrijft de grondbeginselen van een lambda-architectuur op basis van het oorspronkelijke ontwerp met meerdere lagen en de voordelen van een 'rearchitected' lambda-architectuur die bewerkingen vereenvoudigt.  

## <a name="what-is-a-lambda-architecture"></a>Wat is een lambda-architectuur?
Een lambda-architectuur is een generiek, schaalbare en fouttolerante architectuur naar adres voor gegevensverwerking batch- en snelheid latentie scenario's zoals is beschreven in [Nathan Marz](https://twitter.com/nathanmarz).

![Diagram van een lambda-architectuur](./media/lambda-architecture/lambda-architecture-intro.png)

Bron: http://lambda-architecture.net/

De grondbeginselen van een lambda-architectuur worden beschreven in het voorgaande diagram als per [ http://lambda-architecture.net ](http://lambda-architecture.net/).

 1. Alle **gegevens** wordt gepusht naar *beide* de *batchlaag* en *snelheidslaag*.
 2. De **batchlaag** heeft een master gegevensset (onveranderbare, alleen toevoegen set van onbewerkte gegevens) en de batch-weergaven vooraf worden berekend.
 3. De **leveringslaag** batch weergaven voor snelle query's heeft. 
 4. De **snelheidslaag** gecompenseerd voor de van verwerkingstijd (voor de leveringslaag) en behandelt alleen recente gegevens.
 5. Alle query's kunnen worden beantwoord door samenvoeging van exemplaarvoorwaarden resultaten van batch-weergaven en realtime weergaven of ze afzonderlijk te pingen.

Bij Lees hier meer over, zijn we kunnen voor het implementeren van deze architectuur met alleen de volgende:

* Azure Cosmos-container (s)
* HDInsight (Apache Spark 2.1)-cluster
* Spark-Connector [1.0](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark_2.1.0_2.11-1.0.0)

## <a name="speed-layer"></a>Snelheidslaag

Twee gegevensstromen onderhouden terwijl ervoor te zorgen dat de juiste status van de gegevens worden een complexe taak vanuit een perspectief bewerkingen. Als u wilt vereenvoudigen, gebruikmaken van de [ondersteuning voor Azure Cosmos DB-wijzigingenfeed](change-feed.md) te houden van de status van de *batchlaag* tijdens het onthullen van de Azure Cosmos DB-wijzigingenlogboek via de *wijzigen Feed API* voor uw *snelheidslaag*.  
![Diagram markeren de nieuwe gegevens, de snelheidslaag en master gegevensset deel van de lambda-architectuur](./media/lambda-architecture/lambda-architecture-change-feed.png)

Wat is het belangrijk is in deze lagen:

 1. Alle **gegevens** wordt gepusht *alleen* in Azure Cosmos DB, dus u kunt voorkomen dat meerdere casten problemen.
 2. De **batchlaag** heeft een master gegevensset (onveranderbare, alleen toevoegen set van onbewerkte gegevens) en de batch-weergaven vooraf worden berekend.
 3. De **leveringslaag** in de volgende sectie wordt besproken.
 4. De **snelheidslaag** maakt gebruik van HDInsight (Apache Spark) om te lezen van de Azure Cosmos DB-wijzigingenfeed. Hiermee kunt u het behoud van uw gegevens ook query's uitvoeren en tegelijkertijd te verwerken.
 5. Alle query's kunnen worden beantwoord door samenvoeging van exemplaarvoorwaarden resultaten van batch-weergaven en realtime weergaven of ze afzonderlijk te pingen.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Code voorbeeld: Met Spark Structured streamen naar een Azure Cosmos DB wijzigings feed
Om uit te voeren van een snelle prototype van de Azure Cosmos DB-wijzigingenfeed als onderdeel van de **snelheidslaag**, kunt testen uit Twitter-gegevens met als onderdeel van de [Stream verwerken van wijzigingen met behulp van Azure Cosmos DB wijzigen Feed en Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)voorbeeld. Als u wilt snel uw Twitter-uitvoer, Zie het codevoorbeeld in [Stream van Twitter-feed naar Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed). Met het vorige voorbeeld, u het laden van Twitter-gegevens in Azure Cosmos DB en u vervolgens het cluster voor HDInsight (Apache Spark) verbinding maken met de wijzigingenfeed kunt instellen. Zie voor meer informatie over het instellen van deze configuratie [Apache Spark op Azure Cosmos DB-Connector Setup](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

Het volgende codefragment laat zien hoe het configureren van `spark-shell` om uit te voeren een structured streaming-taak verbinding maken met een Azure Cosmos DB-wijzigingenfeed, die de realtime Twitter-gegevensstroom, om uit te voeren van een actieve interval aantal beoordeelt.

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

Zie voor voorbeelden van de volledige code, [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), waaronder:
* [Cosmos DB wijzigen Feed.scala streaming-Query](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Query voor streaming Tags van Cosmos DB wijzigen Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

De uitvoer van dit is een `spark-shell` console, die continu wordt uitgevoerd een structured streaming-taak die wordt uitgevoerd een interval tellen mee voor de Twitter-gegevens van de Azure Cosmos DB-wijzigingenfeed. De volgende afbeelding ziet de uitvoer van de stream-taak en het interval wordt geteld.

![Uitvoer van het interval tellen mee voor de Twitter-gegevens van de Azure Cosmos DB-wijzigingenfeed streaming](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Voor meer informatie over Azure Cosmos DB-wijzigingenfeed, Zie:

* [Werken met de change feed support in Azure Cosmos DB](change-feed.md)
* [Maak kennis met de Azure cosmos DB-wijziging Feed Processor-bibliotheek](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Wijzigingen in de stroom verwerking: Azure CosmosDB Change feed + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Batch ten behoeve van lagen
Omdat de nieuwe gegevens zijn geladen in Azure Cosmos DB (waar de wijzigingenfeed wordt gebruikt voor de snelheidslaag), dit is de locatie waar de **master gegevensset** (een onveranderbare, alleen toevoegen set van onbewerkte gegevens) zich bevindt. Vanaf dit punt en hoger, gebruikt u HDInsight (Apache Spark) om uit te voeren van de vooraf compute-functies van de **batchlaag** naar **leveringslaag**, zoals wordt weergegeven in de volgende afbeelding:

![Diagram van de batchlaag markeren ten behoeve van de laag van de lambda-architectuur](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Wat is het belangrijk is in deze lagen:

 1. Alle **gegevens** (multicast om problemen te vermijden) alleen in Azure Cosmos DB wordt gepusht.
 2. De **batchlaag** heeft een master gegevensset (onveranderbare, alleen toevoegen set van onbewerkte gegevens) die zijn opgeslagen in Azure Cosmos DB. U kunt vooraf een uw aggregaties worden opgeslagen in uw weergaven berekende batch berekenen met behulp van HDI Spark.
 3. De leverende **laag** is een Azure Cosmos-data base met verzamelingen voor de hoofd-gegevensset en de berekende batch weergave.
 4. De **snelheidslaag** verderop in dit artikel wordt besproken.
 5. Alle query's kunnen worden beantwoord door de resultaten van de batch en realtime weergaven samenvoegen of ze afzonderlijk te pingen.

### <a name="code-example-pre-computing-batch-views"></a>Voorbeeld van code: Batch weergaven vóór gebruik
Om te laten zien hoe u voor het uitvoeren van vooraf berekende weergaven op basis van uw **master gegevensset** van Apache Spark op Azure Cosmos DB, gebruik de volgende codefragmenten uit de notebooks [Lambda-architectuur Rearchitected - Batchlaag ](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) en [Lambda-architectuur Rearchitected - Batch om te voldoen aan laag](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). In dit scenario gebruikt u de Twitter-gegevens die zijn opgeslagen in Azure Cosmos DB.

Laten we beginnen met het maken van de van configuratieverbinding met de Twitter-gegevens in Azure Cosmos DB met behulp van de PySpark-code hieronder.

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

Hierna voeren we de volgende Spark SQL-instructie om te bepalen van de hashtags top 10 van de set van tweets. Voor deze Spark SQL-query voert we deze in een Jupyter-notebook zonder de uitvoer-staafdiagram meteen volgt dit codefragment.

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

Nu dat u uw query hebt, slaan we deze terug naar een verzameling met behulp van de Spark-Connector op te slaan van de uitvoergegevens in een andere verzameling.  In dit voorbeeld gebruiken Scala aan de verbinding presenteren. Net als in het vorige voor beeld maakt u de configuratie verbinding om de Apache Spark data frame op te slaan in een andere Azure Cosmos-container.

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

Nadat u hebt opgegeven de `SaveMode` (waarmee wordt aangegeven of moet worden `Overwrite` of `Append` documenten), maakt een `tweets_bytags` DataFrame die vergelijkbaar is met de Spark SQL-query in het vorige voorbeeld.  Met de `tweets_bytags` DataFrame gemaakt, kunt u opslaan met behulp van de `write` methode met behulp van de eerder opgegeven `writeConfig`.

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

Zie voor voorbeelden van de volledige code, [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) met inbegrip van:
* Lambda-architectuur Rearchitected - Batchlaag [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda-architectuur Rearchitected - Batch op voor de laag [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Snelheidslaag
Zoals eerder vermeld, met de Azure Cosmos DB Change Feed clientbibliotheek Hiermee kunt u de bewerkingen tussen de lagen van de batch- en snelheid te vereenvoudigen. In deze architectuur kunt u Apache Spark (via HDInsight) gebruiken om uit te voeren de *gestructureerd streamen* query's op de gegevens. U kunt ook tijdelijk de resultaten van uw structured streaming query's blijven zodat andere systemen toegang krijgen deze gegevens tot kunnen.

![Diagram markeren de snelheidslaag van de lambda-architectuur](./media/lambda-architecture/lambda-architecture-speed.png)

U doet dit door een afzonderlijke Azure Cosmos-container te maken om de resultaten van uw Structured streaming-query's op te slaan.  Hiermee kunt u andere systemen toegang heeft tot deze informatie niet alleen Apache Spark. U kunt ook uw documenten automatisch moeten worden verwijderd na een bepaalde duur configureren met de functie Cosmos DB Time-to-Live (TTL).  Zie voor meer informatie over de functie van de Azure Cosmos DB TTL [gegevens laten verlopen in azure Cosmos-containers automatisch met time to Live](time-to-live.md)

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

## <a name="lambda-architecture-rearchitected"></a>Lambda-architectuur: Opnieuw gearchitect
Zoals vermeld in de vorige secties, kunt u de oorspronkelijke lambda-architectuur vereenvoudigen met behulp van de volgende onderdelen:
* Azure Cosmos DB
* De Azure Cosmos DB Change Feed clientbibliotheek om te voorkomen dat de noodzaak om multicast te uw gegevens tussen de lagen van de batch- en snelheid
* Apache Spark in HDInsight
* De Spark-Connector voor Azure Cosmos DB

![Diagram van de rearchitecture van de lambda-architectuur met behulp van Azure Cosmos DB-, Spark- en de Azure Cosmos DB Change Feed API](./media/lambda-architecture/lambda-architecture-re-architected.png)

Met dit ontwerp hoeft u slechts twee beheerde services, Azure Cosmos DB en HDInsight. Ze adres samen de batch, servers en snelheid van lagen van de lambda-architectuur. Dit vereenvoudigt niet alleen de bewerkingen, maar ook de gegevensstroom. 
 1. Alle gegevens voor verwerking naar de Azure Cosmos DB gepusht
 2. De batchlaag heeft een master gegevensset (onveranderbare, alleen toevoegen set van onbewerkte gegevens) en vooraf berekent de batch-weergaven
 3. De leveringslaag heeft batch weergaven van gegevens voor snelle query's.
 4. De snelheidslaag gecompenseerd voor de van verwerkingstijd (voor de leveringslaag) en behandelt alleen recente gegevens.
 5. Alle query's kunnen worden beantwoord door resultaten van de batch-weergaven en realtime samen te voegen.

### <a name="resources"></a>Resources

* **Nieuwe gegevens**: De [Stream-feed van Twitter naar CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), het mechanisme voor het pushen van nieuwe gegevens naar Azure Cosmos db.
* **Batch-laag:** De batch-laag bestaat uit de *hoofd-gegevensset* (een onveranderlijke set van onbewerkte gegevens) en de mogelijkheid om batch weergaven vooraf te berekenen van de gegevens die worden gepusht naar de **Server**die het leveren.
   * De **Lambda-architectuur Rearchitected - Batchlaag** notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) query's de *master gegevensset* het aantal weergaven van de batch.
* **Leverende laag:** De leverende **laag** bestaat uit vooraf berekende gegevens die resulteren in batch weergaven (bijvoorbeeld aggregaties, specifieke Slicers enz.) voor snelle query's.
  * De **Lambda-architectuur Rearchitected - Batch op voor de laag** notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) verstuurd van de batchgegevens naar de leveringslaag; dat wil zeggen, Spark query's van een batch-verzameling van tweets, verwerkt en opgeslagen in een andere verzameling (een berekende-batch).
    * **Speed Layer:** De **snelheid slaag** bestaat uit Spark waarbij de Azure Cosmos DB wijzigings feed direct wordt gebruikt om te lezen en actie te ondernemen. De gegevens kan ook worden opgeslagen op *berekend RT* zodat andere systemen kunnen opvragen de verwerkte realtime gegevens in plaats van uitvoeren van een realtime query zelf.
  * De [Streaming Query van het Cosmos DB wijzigen Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) scala-script wordt een streaming-query uitgevoerd vanuit de Azure Cosmos DB-wijzigingenfeed voor het berekenen van een interval aantal van de spark-shell.
  * De [Streaming Tags Query van het Cosmos DB wijzigen Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) scala-script wordt een streaming-query uitgevoerd vanuit de Azure Cosmos DB-wijzigingenfeed voor het berekenen van een interval aantal tags van de spark-shell.
  
## <a name="next-steps"></a>Volgende stappen
Als u niet hebt gedaan, downloadt u de Spark op Azure Cosmos DB-connector van de [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub-opslagplaats en de extra resources in de opslagplaats verkennen:
* [Lambda-architectuur](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Gedistribueerde aggregaties voorbeelden](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Voorbeeldscripts en -laptops](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Gestructureerd streamen demo 's](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Demo's feed wijzigen](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Verwerken van wijzigingen met behulp van Azure Cosmos DB-Wijzigingenfeed en Apache Spark Stream](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

U kunt ook om te controleren de [Apache Spark SQL en DataFrames gegevenssets handleiding](https://spark.apache.org/docs/latest/sql-programming-guide.html) en de [Apache Spark in Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) artikel.
