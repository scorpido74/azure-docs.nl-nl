---
title: 'Zelf studie: gegevens uit Azure-Event Hubs verwerken met Apache Spark in HDInsight'
description: 'Zelf studie: Apache Spark in azure HDInsight verbinden met Azure Event Hubs en de streaminggegevens verwerken.'
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/24/2019
ms.openlocfilehash: be21b809272a132ee6e63582036c36ad5dcdf4ad
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "71266195"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>Zelf studie: tweets verwerken met Azure Event Hubs en Apache Spark in HDInsight

In deze zelf studie leert u hoe u een [Apache Spark](https://spark.apache.org/) streaming-toepassing kunt maken om tweets te verzenden naar een Azure Event hub en een andere toepassing te maken om de tweets van de Event hub te lezen. Zie [overzicht van Apache Spark streaming](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview)voor een gedetailleerde uitleg van Spark-streaming. HDInsight brengt dezelfde streaming-functies naar een Spark-cluster in Azure.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Berichten verzenden naar Azure Event hub
> * Berichten lezen van Azure Event hub

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Apache Spark-cluster in HDInsight. Zie [een Apache Spark-cluster maken](./apache-spark-jupyter-spark-sql-use-portal.md).

* Weten hoe u Jupyter Notebook gebruikt met Spark on HDInsight. Zie [gegevens laden en query's uitvoeren met Apache Spark op HDInsight](./apache-spark-load-data-run-query.md)voor meer informatie.

* Een [Twitter-account](https://twitter.com/i/flow/signup).

## <a name="create-a-twitter-application"></a>Een Twitter-toepassing maken

Als u een stream van tweets wilt ontvangen, maakt u een toepassing in Twitter. Volg de instructies om een Twitter-toepassing te maken en noteer de waarden die u nodig hebt om deze zelf studie te volt ooien.

1. Blader naar [Twitter-toepassings beheer](https://apps.twitter.com/).

1. Selecteer **nieuwe app maken**.

1. Geef de volgende waarden op:

    |Eigenschap |Waarde |
    |---|---|
    |Naam|Geef de naam van de toepassing op. De waarde die voor deze zelf studie wordt gebruikt, is **HDISparkStreamApp0423**. Deze naam moet een unieke naam zijn.|
    |Beschrijving|Geef een korte beschrijving van de toepassing op. De waarde die voor deze zelf studie wordt gebruikt, is **een eenvoudige HDInsight Spark-streaming-toepassing**.|
    |Website|Geef de website van de toepassing op. Het hoeft geen geldige website te zijn.  De waarde die voor deze zelf studie wordt gebruikt, is `http://www.contoso.com`.|
    |URL voor terugbellen|U kunt het leeg laten.|

1. Selecteer **Ja, ik heb de Twitter-ontwikkelaars overeenkomst gelezen en ga hiermee akkoord**en selecteer vervolgens **uw Twitter-toepassing maken**.

1. Selecteer het tabblad **sleutels en toegangs tokens** .

1. Selecteer **mijn toegangs token maken** aan het einde van de pagina.

1. Noteer de volgende waarden op de pagina.  U hebt deze waarden later in de zelf studie nodig:

    - **Gebruikers sleutel (API-sleutel)**    
    - **Consument geheim (API-geheim)**  
    - **Toegangs token**
    - **Toegangs token geheim**   

## <a name="create-an-azure-event-hubs-namespace"></a>Een Azure Event Hubs-naamruimte maken

U gebruikt deze Event Hub om tweets op te slaan.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 

2. Selecteer in het menu links **alle services**.  

3. Selecteer **Event hubs**onder **Internet der dingen**. 

    ![Voor beeld van het maken van Event Hub voor Spark-streaming](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Voor beeld van het maken van Event Hub voor Spark-streaming")

4. Selecteer **+ Toevoegen**.

5. Voer de volgende waarden in voor de nieuwe Event Hubs naam ruimte:

    |Eigenschap |Waarde |
    |---|---|
    |Naam|Voer een naam in voor de Event Hub.  De waarde die voor deze zelf studie wordt gebruikt, is **myeventhubns20180403**.|
    |Prijscategorie|Selecteer **standaard**.|
    |Abonnement|Selecteer het juiste abonnement.|
    |Resourcegroep|Selecteer een bestaande resource groep in de vervolg keuzelijst of selecteer **nieuwe maken** om een nieuwe resource groep te maken.|
    |Locatie|Selecteer dezelfde **locatie** als uw Apache Spark-cluster in HDInsight om de latentie en kosten te verminderen.|
    |Automatisch verg Roten inschakelen (optioneel) |Automatisch verg Roten: Hiermee wordt het aantal doorvoer eenheden dat aan uw Event Hubs naam ruimte is toegewezen, geschaald wanneer uw verkeer de capaciteit van de toegewezen doorvoer eenheden overschrijdt.  |
    |Maximum aantal doorvoer eenheden automatisch verg Roten (optioneel)|Deze schuif regelaar wordt alleen weer gegeven als u **automatisch verg Roten inschakelen inschakelt**.  |

    ![Een Event Hub naam opgeven voor het voor beeld van Spark-streaming](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Een Event Hub naam opgeven voor het voor beeld van Spark-streaming")

6. Selecteer **maken** om de naam ruimte te maken.  De implementatie wordt binnen enkele minuten voltooid.

## <a name="create-an-azure-event-hub"></a>Een Azure-Event Hub maken
Maak een Event Hub nadat de naam ruimte Event Hubs is geïmplementeerd.  Vanuit de portal:

1. Selecteer in het menu links **alle services**.  

1. Selecteer **Event hubs**onder **Internet der dingen**.  

1. Selecteer uw Event Hubs naam ruimte in de lijst.  

1. Selecteer op de pagina **Event hubs naam ruimte** de optie **+ Event hub**.  
1. Voer de volgende waarden in op de pagina **Event hub maken** :

    - **Naam**: Geef een naam op voor de Event hub. 
 
    - **Aantal partities**: 10.  

    - **Bewaar periode**van het bericht: 1.   
   
      ![Event Hub Details opgeven voor het voor beeld van Spark-streaming](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Event Hub Details opgeven voor het voor beeld van Spark-streaming")

1. Selecteer **Maken**.  De implementatie wordt binnen een paar seconden voltooid en u keert terug naar de pagina met de Event Hubs naam ruimte.

1. Selecteer onder **instellingen** **beleid voor gedeelde toegang**.

1. Selecteer **RootManageSharedAccessKey**.
    
     ![Event hub-beleids regels instellen voor het Spark-streaming-voor beeld](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Event hub-beleids regels instellen voor het Spark-streaming-voor beeld")

1. Sla de waarden van de **primaire sleutel** en de **verbindings reeks-primaire sleutel** op om later in de zelf studie te gebruiken.

     ![Event hub-beleids sleutels weer geven voor het voor beeld van Spark-streaming](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Event hub-beleids sleutels weer geven voor het voor beeld van Spark-streaming")


## <a name="send-tweets-to-the-event-hub"></a>Tweets naar de Event Hub verzenden

Maak een Jupyter-notebook en geef deze de naam **SendTweetsToEventHub**. 

1. Voer de volgende code uit om de externe Apache Maven-bibliotheken toe te voegen:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Bewerk de onderstaande code door `<Event hub name>`, `<Event hub namespace connection string>`, `<CONSUMER KEY>`, `<CONSUMER SECRET>`, `<ACCESS TOKEN>` en `<TOKEN SECRET>` te vervangen door de juiste waarden. Voer de bewerkte code uit om tweets naar uw Event Hub te verzenden:

    ```scala
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._
    
    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)
    
    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }
    
    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours   
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"
    
    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)
    
    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
    
    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }
    
    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

3. Open de Event Hub in het Azure Portal.  In het **overzicht**ziet u enkele grafieken met de berichten die naar de Event hub zijn verzonden.

## <a name="read-tweets-from-the-event-hub"></a>Tweets van de Event Hub lezen

Maak een andere Jupyter-notebook en noem deze **ReadTweetsFromEventHub**. 

1. Voer de volgende code uit om een externe Apache Maven-bibliotheek toe te voegen:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13"}}
    ```

2. Bewerk de onderstaande code door `<Event hub name>` en `<Event hub namespace connection string>` te vervangen door de juiste waarden. Voer de bewerkte code uit om tweets te lezen uit uw Event Hub:

    ```scala
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build
    
    val customEventhubParameters = EventHubsConf(connStr).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema    
    
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")
    
    messages.printSchema
    
    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Met HDInsight worden uw gegevens opgeslagen in Azure Storage of Azure Data Lake Storage, zodat u een cluster veilig kunt verwijderen wanneer het niet in gebruik is. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Als u direct aan de slag wilt gaan met de volgende zelf studie, wilt u mogelijk het cluster behouden, anders gaat u verder met het cluster.

Open het cluster in Azure Portal en selecteer **Verwijderen**.

![HDInsight Azure Portal cluster verwijderen](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "HDInsight-cluster verwijderen")

U kunt ook de naam van de resourcegroep selecteren om de pagina van de resourcegroep te openen en vervolgens **Resourcegroep verwijderen** selecteren. Als u de resourcegroep verwijdert, verwijdert u zowel het HDInsight Spark-cluster als het standaardopslagaccount.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u een Apache Spark streaming-toepassing kunt maken om tweets te verzenden naar een Azure Event Hub en een andere toepassing hebt gemaakt om de tweets van de Event Hub te lezen.  Ga naar het volgende artikel om te zien dat u een machine learning-toepassing kunt maken.

> [!div class="nextstepaction"]
> [Een machine learning-toepassing maken](./apache-spark-ipython-notebook-machine-learning.md)
