---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: aahi
ms.openlocfilehash: d58f294195efc393c07ecc3886c29e33dba02e6d
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81422094"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

[v3 Referentiedocumentatie](https://aka.ms/azsdk-python-textanalytics-ref-docs) | [v3 Bibliotheek broncode](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [v3 Package (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3 Samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

[v2 Referentiedocumentatie](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [v2 Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [v2-pakket (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [v2-voorbeelden](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* Zodra u uw Azure-abonnement <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="hebt,"  target="_blank">maakt u <span class="docon docon-navigate-external x-hidden-focus"></span> </a> een Text Analytics-bron en maakt u een Text Analytics-bron in de Azure-portal om uw sleutel en eindpunt op te halen. Nadat deze is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt van de resource die u maakt nodig om uw toepassing te verbinden met de Text Analytics API. U plakt uw sleutel en eindpunt later in de quickstart in de onderstaande code.
    * U de gratis`F0`prijscategorie () gebruiken om de service uit te proberen en later te upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Na het installeren van Python u de clientbibliotheek installeren met:

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

```console
pip install azure-ai-textanalytics
```

> [!TIP]
> Wilt u het hele quickstart-codebestand in één keer bekijken? U het [vinden op GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py), die de code voorbeelden in deze quickstart bevat. 

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

> [!TIP]
> Wilt u het hele quickstart-codebestand in één keer bekijken? U het [vinden op GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py), die de code voorbeelden in deze quickstart bevat. 

---

### <a name="create-a-new-python-application"></a>Een nieuwe python-toepassing maken

Maak een nieuw Python-bestand en maak variabelen voor de Azure-eindpunt- en abonnementssleutel van uw bron.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Objectmodel

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

De Text Analytics-client is een `TextAnalyticsClient` object dat met uw sleutel naar Azure wordt geverifieerd. De client biedt verschillende methoden voor het analyseren van tekst als een batch. 

Wanneer batchverwerkingtekst naar de API wordt `documents`verzonden `dictionary` als een lijst `id` `text`van `language` objecten die een combinatie van , en kenmerken bevatten, afhankelijk van de gebruikte methode. Het `text` kenmerk slaat de tekst op `language`die moet `id` worden geanalyseerd in de oorsprong en deze kan elke waarde zijn. 

Het antwoordobject is een lijst met de analysegegevens voor elk document. 

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

De Text Analytics-client is een [TextAnalyticsClient-object](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) dat met uw sleutel naar Azure wordt geverifieerd. De client biedt verschillende methoden voor het analyseren van tekst, als één tekenreeks of als batch. 

Tekst wordt naar de API `documents`verzonden `dictionary` als een lijst `id`van `text`, `language` die objecten zijn die een combinatie van , en kenmerken bevatten, afhankelijk van de gebruikte methode. Het `text` kenmerk slaat de tekst op `language`die moet `id` worden geanalyseerd in de oorsprong en deze kan elke waarde zijn. 

---

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken uitvoeren met de Text Analytics-clientbibliotheek voor Python:

* [De client verifiëren](#authenticate-the-client)
* [Sentimentanalyse](#sentiment-analysis)
* [Taaldetectie](#language-detection)
* [Benoemde entiteitsherkenning](#named-entity-recognition-ner) 
* [Koppeling van entiteiten](#entity-linking)
* [Trefwoordextractie](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>De client verifiëren

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

Maak een functie om het `TextAnalyticsClient` object `key` te `endpoint` instantiëren met uw en hierboven gemaakt. Maak vervolgens een nieuwe klant. 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

[!code-python[imports statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

Maak een functie om het `TextAnalyticsClient` object `key` te `endpoint` instantiëren met uw en hierboven gemaakt. Maak vervolgens een nieuwe klant. 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>Sentimentanalyse

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

Maak een nieuwe `sentiment_analysis_example()` functie genaamd die de client `analyze_sentiment()` als argument neemt en vervolgens de functie aanroept. Het geretourneerde antwoordobject bevat het sentimentslabel en de score van het volledige invoerdocument, evenals een sentimentanalyse voor elke zin.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents = documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Length: {}]".format(sentence.grapheme_length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Uitvoer

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

[Length: 30]
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

[Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Verifieer een clientobject en roep de functie [sentiment()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) aan. Doordeins de resultaten en druk de id van elk document en de sentimentscore af. Een score dichter bij 0 duidt op een negatief sentiment, terwijl een score dichter bij 1 wijst op een positief sentiment.

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>Uitvoer

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

---

## <a name="language-detection"></a>Taaldetectie

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

Maak een nieuwe `language_detection_example()` functie genaamd die de client `detect_language()` als argument neemt en vervolgens de functie aanroept. Het geretourneerde antwoordobject bevat de `primary_language` gedetecteerde taal `error` in indien dit is geslaagd en een zo niet.

> [!Tip]
> In sommige gevallen kan het moeilijk zijn om talen te disambiguate op basis van de input. U `country_hint` de parameter gebruiken om een landcode van 2 letters op te geven. Standaard gebruikt de API de 'US' als standaardlandHint, om dit gedrag te verwijderen kunt `country_hint : ""`u deze parameter opnieuw instellen door deze waarde in te stellen op lege tekenreeks. 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Uitvoer

```console
Language:  French
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Met behulp van de client die eerder is gemaakt, bel [detect_language()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) en krijg het resultaat. Vervolgens herhalen door de resultaten, en druk elk document ID, en de eerste geretourneerde taal.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Uitvoer

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Benoemde entiteitsherkenning (NER)

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

> [!NOTE]
> In `3.0-preview`versie : 
> * Entiteitkoppelen is een afzonderlijke aanvraag dan NER.

Maak een nieuwe `entity_recognition_example` functie genaamd die de client `recognize_entities()` als argument neemt, vervolgens de functie aanroept en door de resultaten heen wordt getint. Het geretourneerde antwoordobject bevat de lijst `entity` met gedetecteerde `error` entiteiten in indien succesvol, en een zo niet. Print voor elke gedetecteerde entiteit de categorie en subcategorie indien deze bestaat.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tLength: \t", entity.grapheme_length, "\tConfidence Score: \t", round(entity.confidence_score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Uitvoer

```console
Named Entities:

    Text:    Seattle        Category:        Location       SubCategory:     GPE
    Length:          7      Confidence Score:        0.92

    Text:    last week      Category:        DateTime       SubCategory:     DateRange
    Length:          9      Confidence Score:        0.8
```

## <a name="entity-linking"></a>Entiteiten koppelen

Maak een nieuwe `entity_linking_example()` functie genaamd die de client `recognize_linked_entities()` als argument neemt, vervolgens de functie aanroept en door de resultaten heen wordt getint. Het geretourneerde antwoordobject bevat de lijst `entities` met gedetecteerde `error` entiteiten in indien succesvol, en een zo niet. Aangezien gekoppelde entiteiten uniek zijn geïdentificeerd, worden gebeurtenissen van `entity` dezelfde entiteit gegroepeerd `match` onder een object als een lijst met objecten.

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score), "\tLength: {}\n".format(match.grapheme_length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Uitvoer

```console
Linked Entities:

    Name:  Altair 8800     Id:  Altair 8800     Url:  https://en.wikipedia.org/wiki/Altair_8800 
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Confidence Score: 0.00     Length: 11

    Name:  Bill Gates     Id:  Bill Gates     Url:  https://en.wikipedia.org/wiki/Bill_Gates 
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Confidence Score: 0.00     Length: 10

        Text: Gates
        Confidence Score: 0.00     Length: 5

    Name:  Paul Allen     Id:  Paul Allen     Url:  https://en.wikipedia.org/wiki/Paul_Allen 
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Confidence Score: 0.00     Length: 10

    Name:  Microsoft     Id:  Microsoft     Url:  https://en.wikipedia.org/wiki/Microsoft 
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Confidence Score: 0.00     Length: 9

        Text: Microsoft
        Confidence Score: 0.00     Length: 9

    Name:  April 4     Id:  April 4     Url:  https://en.wikipedia.org/wiki/April_4 
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Confidence Score: 0.00     Length: 7

    Name:  BASIC     Id:  BASIC     Url:  https://en.wikipedia.org/wiki/BASIC 
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Confidence Score: 0.00     Length: 5
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

> [!NOTE]
> In versie 2.1 is entiteitskoppeling opgenomen in het NER-antwoord.

Als u de client gebruikt die eerder is gemaakt, belt u de functie [entiteiten()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) aan en krijgt u het resultaat. Vervolgens herhalen door de resultaten, en druk elk document ID, en de entiteiten in het.

[!code-python[Entity recognition](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=entityRecognition)]

### <a name="output"></a>Uitvoer

```console
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>Sleuteltermextractie


#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

Maak een nieuwe `key_phrase_extraction_example()` functie genaamd die de client `extract_key_phrases()` als argument neemt en vervolgens de functie aanroept. Het resultaat bevat de lijst met gedetecteerde sleutelzinnen `key_phrases` `error` in indien succesvol, en een zo niet. Alle gedetecteerde sleutelzinnen afdrukken.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Uitvoer

```console
    Key Phrases:
         cat
         veterinarian
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Als u de client gebruikt die eerder is gemaakt, belt u de [functie key_phrases()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) en krijgt u het resultaat. Vervolgens herhalen door de resultaten, en druk elk document ID, en de belangrijkste zinnen in het.

[!code-python[key phrase extraction](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=keyPhrases)]


### <a name="output"></a>Uitvoer

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

---
