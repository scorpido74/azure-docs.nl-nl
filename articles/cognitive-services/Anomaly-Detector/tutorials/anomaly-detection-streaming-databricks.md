---
title: 'Zelf studie: anomalie detectie van streaminggegevens met behulp van Azure Databricks'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de Anomaliey detector API en Azure Databricks voor het bewaken van afwijkingen in uw gegevens.
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 93ee5df4327aa396573665cd0c2cbd8222015cce
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448903"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>Zelf studie: anomalie detectie van streaminggegevens met behulp van Azure Databricks

[Azure Databricks](https://azure.microsoft.com/services/databricks/) is een snelle, gemakkelijke en samen werkende, op Apache Spark gebaseerde analyse service. De anomalie detectie-API, onderdeel van Azure Cognitive Services, biedt een manier om uw time series-gegevens te bewaken. Gebruik deze zelf studie om anomalie detectie uit te voeren op een gegevens stroom in bijna realtime met behulp van Azure Databricks. U kunt Twitter-gegevens opnemen met Azure Event Hubs en deze importeren in Azure Databricks met behulp van de Spark Event Hubs-connector. Daarna gebruikt u de API om afwijkingen te detecteren op gestreamde gegevens. 

In de volgende afbeelding wordt de stroom van de toepassing weergegeven:

![Azure Databricks met Event Hubs en Cognitive Services](../media/tutorials/databricks-cognitive-services-tutorial.png "Azure Databricks met Event Hubs en Cognitive Services")

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een Azure Databricks-werkruimte maken
> * Een Apache Spark-cluster in Azure Databricks maken
> * Een Twitter-app voor toegang tot streaminggegevens maken
> * Notitieblokken maken in Azure Databricks
> * Bibliotheken koppelen voor Event Hubs en Twitter API
> * Een anomalie detector-resource maken en de toegangs sleutel ophalen
> * Tweets verzenden naar Event Hubs
> * Tweets lezen van Event Hubs
> * Anomalie detectie uitvoeren op tweets

> [!Note]
> In deze zelf studie wordt een aanpak geïntroduceerd voor het implementeren van de aanbevolen [oplossings architectuur](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/) voor de anomalie detectie-API.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

> [!Note]
> Deze zelf studie kan niet worden voltooid met een gratis proef versie voor de anomalie detectie-API. Als u een gratis account wilt gebruiken om het Azure Databricks-cluster te maken, gaat u voordat het cluster is gemaakt naar uw profiel en wijzigt u uw abonnement in **betalen per gebruik**. Zie [Gratis Azure-account](https://azure.microsoft.com/free/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- Een [Azure Event hubs-naam ruimte](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) en Event hub.

- De [Connection String](../../../event-hubs/event-hubs-get-connection-string.md) voor toegang tot de Event hubs naam ruimte. Het connection string moet een vergelijk bare indeling hebben:

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`. 

- De naam en beleids sleutel van het gedeelde toegangs beleid voor Event Hubs.

Raadpleeg de [Snelstartgids](../../../event-hubs/event-hubs-create.md) van Azure Event hubs voor meer informatie over het maken van een naam ruimte en Event hub.

## <a name="create-an-azure-databricks-workspace"></a>Een Azure Databricks-werkruimte maken

In deze sectie maakt u een Azure Databricks-werk ruimte met behulp van de [Azure Portal](https://portal.azure.com/).

1. Selecteer in Azure Portal **Een resource maken** > **Analyse** > **Azure Databricks**.

    ![Databricks op Azure Portal](../media/tutorials/azure-databricks-on-portal.png "Databricks op Azure Portal")

3. Geef bij **Azure Databricks Service** de volgende waarden op voor het maken van een Databricks-werkruimte:


    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |**Werkruimtenaam**     | Geef een naam op voor uw Databricks-werkruimte.        |
    |**Abonnement**     | Selecteer uw Azure-abonnement in de vervolgkeuzelijst.        |
    |**Resourcegroep**     | Geef aan of u een nieuwe resourcegroep wilt maken of een bestaande groep wilt gebruiken. Een resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. Zie [Overzicht van Azure Resource Manager](../../../azure-resource-manager/management/overview.md) voor meer informatie. |
    |**Locatie**     | Selecteer **VS Oost 2** of een van de andere beschik bare regio's. Zie [Azure-Services beschikbaar per regio](https://azure.microsoft.com/regions/services/) voor Beschik baarheid van regio's.        |
    |**Prijscategorie**     |  U kunt kiezen tussen **Standard** en **Premium**. Kies geen **proef versie**. Bekijk de pagina [Prijzen voor Databricks](https://azure.microsoft.com/pricing/details/databricks/) voor meer informatie over deze categorieën.       |

    Selecteer **Maken**.

4. Het maken van de werkruimte duurt enkele minuten. 

## <a name="create-a-spark-cluster-in-databricks"></a>Een Spark-cluster maken in Databricks

1. Ga in Azure Portal naar de Databricks-werkruimte die u hebt gemaakt en selecteer **Werkruimte starten**.

2. U wordt omgeleid naar de Azure Databricks-portal. Selecteer in de portal **Nieuw cluster**.

    ![Databricks op Azure](../media/tutorials/databricks-on-azure.png "Databricks op Azure")

3. Geef op de pagina **Nieuw cluster** de waarden op voor het maken van een cluster.

    ![Een Databricks Spark-cluster maken in azure](../media/tutorials/create-databricks-spark-cluster.png "Een Databricks Spark-cluster maken in azure")

    Accepteer alle andere standaardwaarden, anders dan de volgende:

   * Voer een naam in voor het cluster.
   * Voor dit artikel maakt u een cluster met **5,2** runtime. Selecteer niet **5,3** runtime.
   * Zorg ervoor dat het selectie vakje **beëindigen na \_\_ minuten van inactiviteit** is geselecteerd. Geef een duur (in minuten) op voor het beëindigen van het cluster als het cluster niet wordt gebruikt.

     Selecteer **Cluster maken**. 
4. Het maken van het cluster duurt enkele minuten. Zodra het cluster wordt uitgevoerd, kunt u notitieblokken koppelen aan het cluster en Spark-taken uitvoeren.

## <a name="create-a-twitter-application"></a>Een Twitter-toepassing maken

Als u een stream van tweets wilt ontvangen, moet u een toepassing in Twitter maken. Volg de stappen om een Twitter-toepassing te maken en de waarden vast te leggen die u nodig hebt om deze zelfstudie te voltooien.

1. Ga in een webbrowser naar [Twitter Application Management](https://apps.twitter.com/) en selecteer **Create New App**.

    ![Twitter-toepassing maken](../media/tutorials/databricks-create-twitter-app.png "Twitter-toepassing maken")

2. Voer op de pagina **Create an application** de gegevens voor de nieuwe app in en selecteer **Create your Twitter application**.

    ![Details van Twitter-toepassing](../media/tutorials/databricks-provide-twitter-app-details.png "Details van Twitter-toepassing")

3. Selecteer op de toepassingspagina het tabblad **Keys and Access Tokens** en kopieer de waarden voor **Consumer Key** en **Consumer Secret**. Selecteer ook **Create my access token** om de toegangstokens te genereren. Kopieer de waarden voor **Access Token** en **Access Token Secret**.

    ![Details van Twitter-toepassing](../media/tutorials/twitter-app-key-secret.png "Details van Twitter-toepassing")

Sla de waarden op die u hebt opgehaald voor de Twitter-toepassing. U hebt deze waarden later in de zelfstudie nodig.

## <a name="attach-libraries-to-spark-cluster"></a>Bibliotheken koppelen aan een Apache Spark-cluster

In deze zelfstudie gebruikt u de Twitter-API's om tweets te verzenden naar Event Hubs. U gebruikt ook de [Apache Spark Event Hubs-connector](https://github.com/Azure/azure-event-hubs-spark) om gegevens naar Azure Event Hubs te lezen en schrijven. Als u deze API's wilt gebruiken als onderdeel van uw cluster, kunt u ze als bibliotheken toevoegen aan Azure Databricks en ze vervolgens aan uw Apache Spark-cluster koppelen. De volgende instructies laten zien hoe u de bibliotheken kunt toevoegen aan de **gedeelde** map in uw werk ruimte.

1. Selecteer **Werkruimte** in de Azure Databricks-werkruimte en klik vervolgens met de rechtermuisknop op **Gedeeld**. Selecteer **Bibliotheek** > **maken** in het contextmenu.

   ![Het dialoog venster bibliotheek toevoegen](../media/tutorials/databricks-add-library-option.png "Het dialoog venster bibliotheek toevoegen")

2. Selecteer op de pagina nieuwe bibliotheek voor **bron** **maven**. Voer voor **coördinaten**de coördinaat in voor het pakket dat u wilt toevoegen. Dit zijn de Maven-coördinaten voor de bibliotheken die in deze zelfstudie worden gebruikt:

   * Apache Spark Event Hubs-connector - `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Twitter API - `org.twitter4j:twitter4j-core:4.0.7`

     ![Maven-coördinaten opgeven](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Maven-coördinaten opgeven")

3. Selecteer **Maken**.

4. Selecteer de map waaraan u de bibliotheek hebt toegevoegd en selecteer vervolgens de naam van de bibliotheek.

    ![Toe te voegen bibliotheek selecteren](../media/tutorials/select-library.png "Toe te voegen bibliotheek selecteren")

5. Als er zich geen cluster op de pagina bibliotheek bevindt, selecteert u **clusters** en voert u het cluster uit dat u hebt gemaakt. Wacht tot de status wordt uitgevoerd en ga vervolgens terug naar de pagina bibliotheek.
Selecteer op de pagina bibliotheek het cluster waar u de bibliotheek wilt gebruiken en selecteer vervolgens **installeren**. Zodra de tape wisselaar is gekoppeld aan het cluster, wordt de status direct gewijzigd in **geïnstalleerd**.

    ![Bibliotheek installeren in cluster](../media/tutorials/databricks-library-attached.png "Bibliotheek installeren in cluster")

6. Herhaal deze stappen voor het Twitter-pakket, `twitter4j-core:4.0.7`.

## <a name="get-a-cognitive-services-access-key"></a>Een Cognitive Services-toegangssleutel ophalen

In deze zelf studie maakt u gebruik van de [Azure Cognitive Services anomalie](../overview.md) detectie-api's om een afwijkende opsporing uit te voeren op een stroom van tweets in bijna realtime. Voordat u de Api's gebruikt, moet u een afwijkende detector bron op Azure maken en een toegangs sleutel voor het gebruik van de anomalie detectie-Api's ophalen.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

2. Selecteer **+ Een resource maken**.

3. Selecteer onder Azure Marketplace **AI + Machine Learning** > alle > **Cognitive Services-meer** > **anomalie detectie** **weer te geven** . Of u kunt [deze koppeling](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) gebruiken om rechtstreeks naar het dialoog venster **maken** te gaan.

    ![Afwijkende detector-resource maken](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "Afwijkende detector-resource maken")

4. Geef in het dialoogvenster **Maken** de volgende waarden op:

    |Waarde |Beschrijving  |
    |---------|---------|
    |Name     | Een naam voor de afwijkende detector-resource.        |
    |Abonnement     | Het Azure-abonnement waaraan de resource wordt gekoppeld.        |
    |Locatie     | Een Azure-locatie.        |
    |Prijscategorie     | Een prijs categorie voor de service. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/)voor meer informatie over de prijzen voor anomalie detectie.        |
    |Resourcegroep     | Geef aan of u een nieuwe resourcegroep wilt maken of een bestaande groep wilt gebruiken.        |


     Selecteer **Maken**.

5. Nadat de resource is gemaakt, kopieert u op het tabblad **overzicht** de **eind punt** -URL en slaat u deze op, zoals wordt weer gegeven in de scherm opname. Selecteer vervolgens **toegangs sleutels weer geven**.

    ![Toegangs sleutels weer geven](../media/tutorials/cognitive-services-get-access-keys.png "Toegangs sleutels weer geven")

6. Onder **sleutels**selecteert u het Kopieer pictogram voor de sleutel die u wilt gebruiken. Sla de toegangs sleutel op.

    ![Toegangs sleutels kopiëren](../media/tutorials/cognitive-services-copy-access-keys.png "Toegangs sleutels kopiëren")

## <a name="create-notebooks-in-databricks"></a>Notitieblokken maken in Azure Databricks

In deze sectie gaat u in de Databricks-werkruimte twee notitieblokken met de volgende namen maken

- **SendTweetsToEventHub** - een notitieblok voor producenten waarmee u tweets kunt ophalen uit Twitter en ze kunt streamen naar Event Hubs.
- **Notitie analyzetweetsfromeventhub** : een notebook van de consument die u gebruikt om de tweets van Event hubs te lezen en anomalie detectie uit te voeren.

1. Selecteer in de werk ruimte Azure Databricks **werk ruimte** in het linkerdeel venster. Selecteer **Maken** in de vervolgkeuzelijst **Werkruimte** en selecteer **Notitieblok**.

    ![Een notitie blok maken in Databricks](../media/tutorials/databricks-create-notebook.png "Een notitie blok maken in Databricks")

2. Voer in het dialoog venster **notitie blok maken** **SendTweetsToEventHub** als naam in, selecteer **scala** als taal en selecteer het Spark-cluster dat u eerder hebt gemaakt.

    ![Een notitie blok maken in Databricks](../media/tutorials/databricks-notebook-details.png "Een notitie blok maken in Databricks")

    Selecteer **Maken**.

3. Herhaal de stappen om het notitieblok **AnalyzeTweetsFromEventHub** te maken.

## <a name="send-tweets-to-event-hubs"></a>Tweets verzenden naar Event Hubs

Plak in het notitieblok **SendTweetsToEventHub** de volgende code en vervang de tijdelijke aanduiding door waarden voor uw Event Hubs-naamruimte en Twitter-toepassing die u eerder hebt gemaakt. In deze notebook worden de aanmaak tijd en het aantal "like" s opgehaald van tweets met het sleutel woord "Azure" en worden deze als gebeurtenissen gestreamd naar Event Hubs in realtime.

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

val cb = new ConfigurationBuilder()
cb.setDebugEnabled(true)
  .setOAuthConsumerKey(twitterConsumerKey)
  .setOAuthConsumerSecret(twitterConsumerSecret)
  .setOAuthAccessToken(twitterOauthAccessToken)
  .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

val twitterFactory = new TwitterFactory(cb.build())
val twitter = twitterFactory.getInstance()

// Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

val query = new Query(" #Azure ")
query.setCount(100)
query.lang("en")

var finished = false
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }
  
  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

Voor het uitvoeren van het notitieblok, drukt u op **SHIFT + ENTER**. De volgende uitvoer wordt weergegeven. Elke gebeurtenis in de uitvoer is een combi natie van tijds tempel en het aantal ' like ' s dat is opgenomen in de Event Hubs.

    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Tweets lezen van Event Hubs

Plak de volgende code in de **notitie analyzetweetsfromeventhub** -notebook en vervang de tijdelijke aanduiding door waarden voor de bron van de anomalie detector die u eerder hebt gemaakt. Dit notitieblok leest de tweets die u eerder hebt gestreamd naar Event Hubs met behulp van het **SendTweetsToEventHub**-notitieblok.

Schrijf eerst een client om anomalie detectie aan te roepen. 
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
    val connection = getConnection(path)
    val wr = new DataOutputStream(connection.getOutputStream())
    wr.write(encoded_text, 0, encoded_text.length)
    wr.flush()
    wr.close()

    val response = new StringBuilder()
    val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
    var line = in.readLine()
    while (line != null) {
      response.append(line)
      line = in.readLine()
    }
    in.close()
    return response.toString()
  }

  // Calls the Latest Point Detection API.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

Voor het uitvoeren van het notitieblok, drukt u op **SHIFT + ENTER**. De volgende uitvoer wordt weergegeven.

    import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
    import java.net.URL
    import java.sql.Timestamp
    import com.google.gson.{Gson, GsonBuilder, JsonParser}
    import javax.net.ssl.HttpsURLConnection
    defined class Point
    defined class Series
    defined class AnomalySingleResponse
    defined class AnomalyBatchResponse
    defined object AnomalyDetector

Bereid vervolgens een statistische functie voor op toekomstig gebruik.
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)
  
  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))
  
  override def dataType: DataType = BooleanType
  
  override def deterministic: Boolean = false
  
  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }
  
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }
  
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }
  
  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }
      
      
      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms.
      // Check Anomaly detector API reference (https://aka.ms/anomaly-detector-rest-api-ref)
      
      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }
    
    return None
  }
}

```

Voor het uitvoeren van het notitieblok, drukt u op **SHIFT + ENTER**. De volgende uitvoer wordt weergegeven.

    import org.apache.spark.sql.Row
    import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
    import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
    import scala.collection.immutable.ListMap
    defined class AnomalyDetectorAggregationFunction

Laad vervolgens gegevens uit Event Hub voor anomalie detectie. Vervang de tijdelijke aanduiding door de waarden voor uw Azure-Event Hubs die u eerder hebt gemaakt.

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

De uitvoer lijkt nu op de volgende afbeelding. Houd er rekening mee dat de datum in de tabel mogelijk anders is dan de datum in deze zelf studie, omdat de gegevens realtime zijn.
![Gegevens laden vanuit Event hub](../media/tutorials/load-data-from-eventhub.png "Gegevens laden vanuit Event hub")

U hebt nu in bijna realtime gegevens van Azure Event Hubs naar Azure Databricks gestreamd met behulp van de Event Hubs-connector voor Apache Spark. Raadpleeg voor meer informatie over het gebruik van de Event Hubs-connector voor Spark de [connector-documentatie](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs).



## <a name="run-anomaly-detection-on-tweets"></a>Anomalie detectie uitvoeren op tweets

In deze sectie voert u anomalie detectie uit op de tweets die zijn ontvangen met behulp van de afwijkings detector-API. Voor deze sectie voegt u de codefragmenten aan hetzelfde **AnalyzeTweetsFromEventHub**-notitieblok toe.

Als u afwijkings detectie wilt uitvoeren, moet u eerst het aantal metrieken per uur samen voegen.
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window. 
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
De uitvoer lijkt nu op de volgende fragmenten.
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

Haal vervolgens het cumulatieve uitvoer resultaat op Delta. Omdat voor afwijkings detectie een langer geschiedenis venster is vereist, gebruiken we Delta om de geschiedenis gegevens te blijven voor het punt dat u wilt detecteren. Vervang de [tijdelijke aanduiding: tabel naam] door de naam van een gekwalificeerde Delta tabel die moet worden gemaakt (bijvoorbeeld "tweets"). Vervang ' [placeholder: mapnaam voor controle punten] ' door een teken reeks waarde die uniek is telkens wanneer u deze code uitvoert (bijvoorbeeld ' ETL-from-eventhub-20190605 ').
Voor meer informatie over Delta Lake on Azure Databricks raadpleegt u de [hand leiding voor Delta Lake](https://docs.azuredatabricks.net/delta/index.html)


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

Vervang de [tijdelijke aanduiding: tabel naam] door de naam van de Delta tabel die u hierboven hebt geselecteerd.
```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
De uitvoer zoals hieronder: 
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

De geaggregeerde time series-gegevens worden nu voortdurend opgenomen in de Delta. Vervolgens kunt u een taak plannen om de afwijking van het laatste punt te detecteren. Vervang de [tijdelijke aanduiding: tabel naam] door de naam van de Delta tabel die u hierboven hebt geselecteerd.

```scala
//
// Anomaly Detection
//

import java.time.Instant
import java.time.format.DateTimeFormatter
import java.time.ZoneOffset
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// You could use Databricks to schedule an hourly job and always monitor the latest data point
// Or you could specify a const value here for testing purpose
// For example, val endTime = Instant.parse("2019-04-16T00:00:00Z")
val endTime = Instant.now()

// This is when your input of anomaly detection starts. It is hourly time series in this tutorial, so 72 means 72 hours ago from endTime.
val batchSize = 72
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val DATE_TIME_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss").withZone(ZoneOffset.UTC);

val series = detectData.filter($"groupTime" <= DATE_TIME_FORMATTER.format(endTime))
  .filter($"groupTime" > DATE_TIME_FORMATTER.format(startTime))
  .sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as datetime, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
Resultaat als hieronder: 

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+
```

Dat is alles. Met behulp van Azure Databricks hebt u gegevens gestreamd naar Azure Event Hubs, de streamgegevens gebruikt met de Event Hubs-connector en vervolgens afwijkings detectie voor streaming-gegevens in vrijwel realtime uitgevoerd.
In deze zelf studie is de granulariteit elk uur, maar u kunt de granulariteit altijd wijzigen om te voldoen aan uw behoeften. 

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de zelfstudie hebt voltooid, kunt u het cluster beëindigen. Hiertoe selecteert u in de werk ruimte Azure Databricks **clusters** in het linkerdeel venster. Voor het cluster dat u wilt beëindigen, plaatst u de cursor op het weglatings teken onder **acties** kolom, selecteert u het pictogram **beëindigen** en selecteert u vervolgens **bevestigen**.

![Een Databricks-cluster stoppen](../media/tutorials/terminate-databricks-cluster.png "Een Databricks-cluster stoppen")

Als u het cluster niet hand matig beëindigt, wordt het automatisch gestopt, op voor waarde dat u het selectie vakje **beëindigen na \_\_ minuten van inactiviteit** tijdens het maken van het cluster hebt geselecteerd. In dat geval stopt het cluster automatisch als het gedurende de opgegeven tijd inactief is geweest.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u Azure Databricks kunt gebruiken om gegevens naar Azure Event Hubs te streamen, waarna u de streaming-gegevens vanuit Event Hubs in realtime hebt gelezen. Ga naar de volgende zelf studie voor meer informatie over het aanroepen van de anomalie detectie-API en het visualiseren van afwijkingen met Power BI bureau blad. 

> [!div class="nextstepaction"]
>[Batch anomalie detectie met Power BI bureau blad](batch-anomaly-detection-powerbi.md)
