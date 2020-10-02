---
title: Cognitive Services for big data - Scala-voorbeelden
description: Cognitive Services voor Azure Databricks gebruiken om uw MMLSpark-pijplijn uit te voeren voor big data.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 4546ef03c82f19d188a71a86f6964ca87c0f834e
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90524960"
---
# <a name="quick-examples"></a>Snelle voorbeelden

De volgende codefragmenten zijn klaar om te worden uitgevoerd. U kunt ze gebruiken om aan de slag te gaan met Cognitive Services op Spark. De voorbeelden hieronder zijn in Scala.

De voorbeelden maken gebruik van de volgende services van Cognitive Services:

- Text Analytics: hiermee haalt u het gevoel op van een verzameling zinnen.
- Computer Vision: hiermee haalt u de tags (beschrijvingen van één woord) op die aan een verzameling afbeeldingen zijn gekoppeld.
- Bing Image Search: hiermee doorzoekt u het web op afbeeldingen die betrekking hebben op een query in natuurlijke taal.
- Spraak-naar-tekst: hiermee worden audiobestanden getranscribeerd om teksttranscripten te extraheren.
- Anomaly Detector: hiermee worden afwijkingen binnen een tijdreeks gedetecteerd.

## <a name="prerequisites"></a>Vereisten

1. Volg de stappen in [Aan de slag](getting-started.md) om uw omgeving voor Azure Databricks en Cognitive Services in te stellen. In deze zelfstudie kunt u onder andere zien hoe u MMLSpark installeert en hoe u uw Spark-cluster in Databricks kunt maken.
1. Nadat u een nieuw notebook in Azure Databricks hebt gemaakt, kopieert u de onderstaande **gedeelde code** en plakt u deze in een nieuwe cel in uw notebook.
1. Kies hieronder een voorbeeld van een service, kopieer deze en plak deze in een tweede nieuwe cel in uw notebook.
1. Vervang een tijdelijke aanduiding met de serviceabonnementssleutel door uw eigen sleutel.
1. Kies de knop Uitvoeren (pictogram met driehoek) in de rechterbovenhoek van de cel en selecteer **Cel uitvoeren**.
1. Bekijk de resultaten in een tabel onder de cel.

## <a name="shared-code"></a>Gedeelde code
Voeg de volgende code toe aan uw project om aan de slag te gaan:

```scala
import com.microsoft.ml.spark.cognitive._
import spark.implicits._ 

val serviceKey = "ADD-YOUR-SUBSCRIPTION-KEY"
val location = "eastus"
```

## <a name="text-analytics"></a>Tekstanalyse

De service [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) biedt verschillende algoritmen voor het extraheren van intelligente inzichten uit tekst. We kunnen bijvoorbeeld het gevoel van een bepaald stuk ingevoerde tekst vinden. De service retourneert een score tussen `0.0` en `1.0`, waarbij een lage score een negatief gevoel aangeeft en een hoge score een positief gevoel.  In het voorbeeld hieronder worden drie eenvoudige zinnen gebruikt en wordt het gevoel van elk ervan geretourneerd.

```scala
import org.apache.spark.sql.functions.col

val df = Seq(
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US")
).toDF("text", "language")

val sentiment = new TextSentiment()
    .setTextCol("text")
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language")

display(sentiment.transform(df).select(col("text"), col("sentiment")(0).getItem("score").alias("sentiment")))
```

### <a name="expected-result"></a>Verwacht resultaat

| tekst                                      | gevoel                                             |
|:------------------------------------------|:------------------------------------------------------|
| I am so happy today, its sunny! (Ik ben zo blij vandaag, de zon schijnt!)           | 0,9789592027664185                                    |
| I am frustrated by this rush hour traffic (Ik ben geïrriteerd vanwege het verkeer in de spits) | 0,023795604705810547                                  |
| The cognitive services on spark aint bad (De cognitieve services in Spark zijn niet slecht)  | 0,8888956308364868                                    |

## <a name="computer-vision"></a>Computer Vision

[Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) analyseert afbeeldingen om structuur (zoals gezichten), objecten en beschrijvingen in natuurlijke taal te herkennen.
In dit voorbeeld wordt een lijst met afbeeldingen van een tag voorzien. Tags zijn omschrijvingen van één woord van dingen in de afbeelding, zoals herkenbare voorwerpen, personen, taferelen en acties.

```scala
// Create a dataframe with the image URLs
val df = Seq(
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg")
).toDF("image")

// Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
val analysis = new AnalyzeImage()
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setVisualFeatures(Seq("Categories","Color","Description","Faces","Objects","Tags"))
    .setOutputCol("results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

// Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select(col("image"), col("results").getItem("tags").getItem("name")).alias("results")))

// Uncomment for full results with all visual feature requests
//display(analysis.transform(df).select(col("image"), col("results")))
``` 

### <a name="expected-result"></a>Verwacht resultaat

| image | tags |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | ['skating' 'person' 'man' 'outdoor' 'riding' 'sport' 'skateboard' 'young' 'board' 'shirt' 'air' 'black' 'park' 'boy' 'side' 'jumping' 'trick' 'ramp' 'doing' 'flying']
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | ['dog' 'outdoor' 'fence' 'wooden' 'small' 'brown' 'building' 'sitting' 'front' 'bench' 'standing' 'table' 'walking' 'board' 'beach' 'white' 'holding' 'bridge' 'track']                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | ['outdoor' 'grass' 'house' 'building' 'old' 'home' 'front' 'small' 'church' 'stone' 'large' 'grazing' 'yard' 'green' 'sitting' 'leading' 'sheep' 'brick' 'bench' 'street' 'white' 'country' 'clock' 'sign' 'parked' 'field' 'standing' 'garden' 'water' 'red' 'horse' 'man' 'tall' 'fire' 'group'] 

## <a name="bing-image-search"></a>Bing Afbeeldingen zoeken

[Bing Image Search](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview) doorzoekt het web om afbeeldingen op te halen die zijn gerelateerd aan de query in natuurlijke taal van een gebruiker. In dit voorbeeld gebruiken we een tekstquery die naar afbeeldingen met citaten zoekt. Er wordt een lijst met afbeeldings-URL's geretourneerd die foto's bevatten waarop de query betrekking heeft.


```scala
import org.apache.spark.ml.Pipeline

// Number of images Bing will return per query
val imgsPerBatch = 10

// A list of offsets, used to page into the search results
val df = (0 until 100).map(o => Tuple1(o*imgsPerBatch)).toSeq.toDF("offset")

// Run the Bing Image Search service with our text query
val bingSearch = new BingImageSearch()
    .setSubscriptionKey(bingSearchKey)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images")

// Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
val getUrls = BingImageSearch.getUrlTransformer("images", "url")

// This displays the full results returned, uncomment to use
// display(bingSearch.transform(bingParameters))

// Since we have two services, they are put into a pipeline
val pipeline = new Pipeline().setStages(Array(bingSearch, getUrls))

// Show the results of your search: image URLs
display(pipeline.fit(df).transform(df))
```

### <a name="expected-result"></a>Verwacht resultaat

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |

## <a name="speech-to-text"></a>Spraak naar tekst

Met de service [Spraak-naar-tekst](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-speech-to-text) worden streams of bestanden met gesproken tekst naar leesbare tekst geconverteerd. In dit voorbeeld worden twee audiobestanden getranscribeerd. Het eerste bestand is eenvoudig te begrijpen; het tweede is moeilijker.

```scala
import org.apache.spark.sql.functions.col

// Create a dataframe with audio URLs, tied to the column called "url"
val df = Seq(("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav"),
             ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3")).toDF("url")

// Run the Speech-to-text service to translate the audio into text
val speechToText = new SpeechToTextSDK()
    .setSubscriptionKey(serviceKey)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked")

// Show the results of the translation
display(speechToText.transform(df).select(col("url"), col("text").getItem("DisplayText")))
```

### <a name="expected-result"></a>Verwacht resultaat

| url | DisplayText |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | Aangepaste spraak biedt hulpmiddelen waarmee u de herkenningskwaliteit van een model visueel kunt controleren door audiogegevens te vergelijken met het bijbehorende herkenningsresultaat vanuit de portal met aangepaste spraak. U kunt geüploade audio afspelen en bepalen of het geboden herkenningsresultaat juist is. Met dit hulpprogramma kunt u snel de kwaliteit controleren van het spraak-naar-tekstmodel op basisniveau van Microsoft, of een aangepast, getraind model zonder dat u audiogegevens hoeft te transcriberen. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Add a gentleman Sir thinking visual check.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | I hear me. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | I like the reassurance for radio that I can hear it as well. |

## <a name="anomaly-detector"></a>Anomaly Detector

[Anomaly Detector](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/) is geschikt voor het detecteren van onregelmatigheden in uw tijdreeksen. In dit voorbeeld gebruiken we de service om afwijkingen in de gehele tijdreeks te zoeken.

```scala
import org.apache.spark.sql.functions.{col, lit}

val anomalyKey = "84a2c303cc7e49f6a44d692c27fb9967"

val df = Seq(
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0),
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0)
  ).toDF("timestamp", "value").withColumn("group", lit("series1"))

// Run the Anomaly Detector service to look for irregular data
val anamolyDetector = new SimpleDetectAnomalies()
    .setSubscriptionKey(anomalyKey)
    .setLocation("eastus")
    .setTimestampCol("timestamp")
    .setValueCol("value")
    .setOutputCol("anomalies")
    .setGroupbyCol("group")
    .setGranularity("monthly")

// Show the full results of the analysis with the anomalies marked as "True"
display(anamolyDetector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
```

### <a name="expected-result"></a>Verwacht resultaat

| tijdstempel            |   waarde | isAnomaly   |
|:---------------------|--------:|:------------|
| 1972-01-01T00:00:00Z |     826 | False       |
| 1972-02-01T00:00:00Z |     799 | False       |
| 1972-03-01T00:00:00Z |     890 | False       |
| 1972-04-01T00:00:00Z |     900 | False       |
| 1972-05-01T00:00:00Z |     766 | False       |
| 1972-06-01T00:00:00Z |     805 | False       |
| 1972-07-01T00:00:00Z |     821 | False       |
| 1972-08-01T00:00:00Z |   20.000 | True        |
| 1972-09-01T00:00:00Z |     883 | False       |
| 1972-10-01T00:00:00Z |     898 | False       |
| 1972-11-01T00:00:00Z |     957 | False       |
| 1972-12-01T00:00:00Z |     924 | False       |
| 1973-01-01T00:00:00Z |     881 | False       |
| 1973-02-01T00:00:00Z |     837 | False       |
| 1973-03-01T00:00:00Z |    9000 | True        |
