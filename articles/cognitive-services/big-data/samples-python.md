---
title: Cognitive Services for big data - Python-voorbeelden
description: Probeer Cognitive Services-voorbeelden in Python voor Azure Databricks om uw MMLSpark-pijplijn uit te voeren voor big data.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: caf492c2cd3940fd7f37e2a4462c8376a127f393
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86189303"
---
# <a name="python-samples-for-cognitive-services-for-big-data"></a>Python-voorbeelden voor Cognitive Services voor big data

De volgende codefragmenten zijn klaar om te worden uitgevoerd. U kunt ze gebruiken om met Python aan de slag te gaan met Cognitive Services op Spark.

De voorbeelden in dit artikel maken gebruik van de volgende services van Cognitive Services:

- Text Analytics: hiermee haalt u het gevoel op van een verzameling zinnen.
- Computer Vision: hiermee haalt u de tags (beschrijvingen van één woord) op die aan een verzameling afbeeldingen zijn gekoppeld.
- Bing Image Search: hiermee doorzoekt u het web op afbeeldingen die betrekking hebben op een query in natuurlijke taal.
- Spraak-naar-tekst: hiermee worden audiobestanden getranscribeerd om teksttranscripten te extraheren.
- Anomaly Detector: hiermee worden afwijkingen binnen een tijdreeks gedetecteerd.

## <a name="prerequisites"></a>Vereisten

1. Volg de stappen in [Aan de slag](getting-started.md) om uw omgeving voor Azure Databricks en Cognitive Services in te stellen. In deze zelfstudie kunt u zien hoe u MMLSpark installeert en hoe u uw Spark-cluster in Databricks kunt maken.
1. Nadat u een nieuw notebook in Azure Databricks hebt gemaakt, kopieert u de onderstaande **gedeelde code** en plakt u deze in een nieuwe cel in uw notebook.
1. Kies hieronder een voorbeeld van een service, kopieer deze en plak deze in een tweede nieuwe cel in uw notebook.
1. Vervang een tijdelijke aanduiding met de serviceabonnementssleutel door uw eigen sleutel.
1. Kies de knop Uitvoeren (pictogram met driehoek) in de rechterbovenhoek van de cel en selecteer **Cel uitvoeren**.
1. Bekijk de resultaten in een tabel onder de cel.

## <a name="shared-code"></a>Gedeelde code

Als u aan de slag wilt gaan, moet u deze code aan het project toevoegen:

```python
from mmlspark.cognitive import *

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key = "ADD_YOUR_SUBSCRIPION_KEY"
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY"
# An Anomaly Dectector subscription key
anomaly_key = "ADD_YOUR_SUBSCRIPION_KEY"

# Validate the key
assert service_key != "ADD_YOUR_SUBSCRIPION_KEY"
```    

## <a name="text-analytics-sample"></a>Voorbeeld in Text Analytics

De service [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) biedt verschillende algoritmen voor het extraheren van intelligente inzichten uit tekst. We kunnen bijvoorbeeld het gevoel van een bepaald stuk ingevoerde tekst vinden. De service retourneert een score tussen 0,0 en 1,0, waarbij een lage score een negatief gevoel aangeeft en een hoge score een positief gevoel.  In dit voorbeeld worden drie eenvoudige zinnen gebruikt en wordt het gevoel van elk ervan geretourneerd.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format
display(sentiment.transform(df).select("text", col("sentiment")[0].getItem("score").alias("sentiment")))
```

### <a name="expected-result"></a>Verwacht resultaat

| tekst                                      | gevoel                                             |
|:------------------------------------------|:------------------------------------------------------|
| I am so happy today, its sunny! (Ik ben zo blij vandaag, de zon schijnt!)           | 0,9789592027664185                                    |
| I am frustrated by this rush hour traffic (Ik ben geïrriteerd vanwege het verkeer in de spits) | 0,023795604705810547                                  |
| The cognitive services on spark aint bad (De cognitieve services in Spark zijn niet slecht)  | 0,8888956308364868                                    |

## <a name="computer-vision-sample"></a>Voorbeeld in Computer Vision

[Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) analyseert afbeeldingen om structuur (zoals gezichten), objecten en beschrijvingen in natuurlijke taal te herkennen. In dit voorbeeld wordt een lijst met afbeeldingen van een tag voorzien. Tags zijn omschrijvingen van één woord van dingen in de afbeelding, zoals herkenbare voorwerpen, personen, taferelen en acties.

```python

# Create a dataframe with the image URLs
df = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select("image", "analysis_results.description.tags"))
```

### <a name="expected-result"></a>Verwacht resultaat

| image | tags |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | ['skating' 'person' 'man' 'outdoor' 'riding' 'sport' 'skateboard' 'young' 'board' 'shirt' 'air' 'black' 'park' 'boy' 'side' 'jumping' 'trick' 'ramp' 'doing' 'flying']
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | ['dog' 'outdoor' 'fence' 'wooden' 'small' 'brown' 'building' 'sitting' 'front' 'bench' 'standing' 'table' 'walking' 'board' 'beach' 'white' 'holding' 'bridge' 'track']                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | ['outdoor' 'grass' 'house' 'building' 'old' 'home' 'front' 'small' 'church' 'stone' 'large' 'grazing' 'yard' 'green' 'sitting' 'leading' 'sheep' 'brick' 'bench' 'street' 'white' 'country' 'clock' 'sign' 'parked' 'field' 'standing' 'garden' 'water' 'red' 'horse' 'man' 'tall' 'fire' 'group']


## <a name="bing-image-search-sample"></a>Voorbeeld in Bing Image Search

[Bing Image Search](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview) doorzoekt het web om afbeeldingen op te halen die zijn gerelateerd aan de query in natuurlijke taal van een gebruiker. In dit voorbeeld gebruiken we een tekstquery die naar afbeeldingen met citaten zoekt. Er wordt een lijst met afbeeldings-URL's geretourneerd die foto's bevatten waarop de query betrekking heeft.

```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 10
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(100)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bing_search_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")

# This displays the full results returned, uncomment to use
# display(bingSearch.transform(bingParameters))

# Since we have two services, they are put into a pipeline
pipeline = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
display(pipeline.transform(bingParameters))
```

### <a name="expected-result"></a>Verwacht resultaat

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |


## <a name="speech-to-text-sample"></a>Spraak-naar-tekst-voorbeeld
Met de service [Spraak-naar-tekst](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-speech-to-text) worden streams of bestanden met gesproken tekst naar leesbare tekst geconverteerd. In dit voorbeeld worden twee audiobestanden getranscribeerd. Het eerste bestand is eenvoudig te begrijpen; het tweede is moeilijker.

```python

# Create a dataframe with our audio URLs, tied to the column called "url"
df = spark.createDataFrame([("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav",),
                           ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3",)
                           ], ["url"])

# Run the Speech-to-text service to translate the audio into text
speech_to_text = (SpeechToTextSDK()
    .setSubscriptionKey(service_key)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked"))

# Show the results of the translation
display(speech_to_text.transform(df).select("url", "text.DisplayText"))
```

### <a name="expected-result"></a>Verwacht resultaat

| url | DisplayText |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | Aangepaste spraak biedt hulpmiddelen waarmee u de herkenningskwaliteit van een model visueel kunt controleren door audiogegevens te vergelijken met het bijbehorende herkenningsresultaat vanuit de portal met aangepaste spraak. U kunt geüploade audio afspelen en bepalen of het geboden herkenningsresultaat juist is. Met dit hulpprogramma kunt u snel de kwaliteit controleren van het spraak-naar-tekstmodel op basisniveau van Microsoft, of een aangepast, getraind model zonder dat u audiogegevens hoeft te transcriberen. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Add a gentleman Sir thinking visual check.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | I hear me. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | I like the reassurance for radio that I can hear it as well. |


## <a name="anomaly-detector-sample"></a>Voorbeeld in Anomaly Detector

[Anomaly Detector](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/) is geschikt voor het detecteren van onregelmatigheden in uw tijdreeksen. In dit voorbeeld gebruiken we de service om afwijkingen in de gehele tijdreeks te zoeken.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df = spark.createDataFrame([
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
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomaly_key)
  .setLocation("eastus")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
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

## <a name="arbitrary-web-apis"></a>Willekeurige web-API's

Met HTTP in Spark kan elke webservice in uw pijplijn voor big data worden gebruikt. In dit voorbeeld wordt de [World Bank-API](http://api.worldbank.org/v2/country/) gebruikt om informatie over verschillende landen ter wereld op te halen.

```python
from requests import Request
from mmlspark.io.http import HTTPTransformer, http_udf
from pyspark.sql.functions import udf, col

# Use any requests from the python requests library
def world_bank_request(country):
  return Request("GET", "http://api.worldbank.org/v2/country/{}?format=json".format(country))

# Create a dataframe with spcificies which countries we want data on
df = (spark.createDataFrame([("br",),("usa",)], ["country"])
  .withColumn("request", http_udf(world_bank_request)(col("country"))))

# Much faster for big data because of the concurrency :)
client = (HTTPTransformer()
      .setConcurrency(3)
      .setInputCol("request")
      .setOutputCol("response"))

# Get the body of the response
def get_response_body(resp):
  return resp.entity.content.decode()

# Show the details of the country data returned
display(client.transform(df).select("country", udf(get_response_body)(col("response")).alias("response")))
```

### <a name="expected-result"></a>Verwacht resultaat

| country   | antwoord |
|:----------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| br | [{"page":1,"pages":1,"per_page":"50","total":1},[{"id":"BRA","iso2Code":"BR","name":"Brazil","region":{"id":"LCN","iso2code":"ZJ","value":"Latin America & Caribbean "},"adminregion":{"id":"LAC","iso2code":"XJ","value":"Latin America & Caribbean (excluding high income)"},"incomeLevel":{"id":"UMC","iso2code":"XT","value":"Upper middle income"},"lendingType":{"id":"IBD","iso2code":"XF","value":"IBRD"},"capitalCity":"Brasilia","longitude":"-47.9292","latitude":"-15.7801"}]] |
| usa  | [{"page":1,"pages":1,"per_page":"50","total":1},[{"id":"USA","iso2Code":"US","name":"United States","region":{"id":"NAC","iso2code":"XU","value":"North America"},"adminregion":{"id":"","iso2code":"","value":""},"incomeLevel":{"id":"HIC","iso2code":"XD","value":"High income"},"lendingType":{"id":"LNX","iso2code":"XX","value":"Not classified"},"capitalCity":"Washington D.C.","longitude":"-77.032","latitude":"38.8895"}]] |

## <a name="see-also"></a>Zie ook

* [Recept: Anomaly Detection](./recipes/anomaly-detection.md)
* [Recept: Art Explorer](./recipes/art-explorer.md)
