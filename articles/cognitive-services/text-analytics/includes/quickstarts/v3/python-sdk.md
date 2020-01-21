---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2019
ms.author: aahi
ms.openlocfilehash: 518e6d544547b808b278121bf6364dcd1590bd6f
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281140"
---
<a name="HOLTop"></a>

[Referentie documentatie](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b1/azure.ai.textanalytics.html) | - [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [pakket (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | -voor [beelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

> [!NOTE]
> * In deze Snelstartgids wordt gebruikgemaakt van versie `3.0-preview` van de Text Analytics-client bibliotheek, die een open bare Preview bevat voor verbeterde [sentimentanalyse](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) en [named entity Recognition (ner)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentie opslag om eenvoud. Voor productie scenario's wordt u aangeraden de batch-asynchrone methoden te gebruiken voor prestaties en schaal baarheid. Bijvoorbeeld: het importeren van de client uit de naam ruimte `azure.ai.textanalytics.aio` en het aanroepen van `analyze_sentiment()`in plaats van `analyze_sentiment()` van de naam ruimte `azure.ai.textanalytics`.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Instellen

### <a name="create-a-text-analytics-azure-resource"></a>Een Text Analytics Azure-resource maken

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Na de installatie van python kunt u de client bibliotheek installeren met:

```console
pip install azure-ai-textanalytics
```

### <a name="create-a-new-python-application"></a>Een nieuwe python-toepassing maken

Maak een nieuw python-bestand en maak variabelen voor het Azure-eind punt en de abonnements sleutel van uw resource.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Object model

De Text Analytics-client is een `TextAnalyticsClient`-object dat met behulp van uw sleutel wordt geverifieerd bij Azure. De-client biedt verschillende methoden voor het analyseren van tekst als een batch. In deze Quick Start wordt gebruikgemaakt van een verzameling functies waarmee u snel afzonderlijke documenten kunt verzenden.

Wanneer tekst met batch verwerking naar de API wordt verzonden als een lijst met `documents`, `dictionary` objecten die een combi natie van `id`, `text`en `language` kenmerken bevatten, afhankelijk van de gebruikte methode. Het kenmerk `text` slaat de tekst op die moet worden geanalyseerd in het oorspronkelijke `language`en de `id` kan een wille keurige waarde zijn. Bij het verwerken van één document is alleen een `text` invoer vereist, zoals in de onderstaande voor beelden kan worden weer gegeven.  

Het antwoord object is een lijst met de analyse-informatie voor elk document. 

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Text Analytics-client bibliotheek voor python:

* [Sentimentanalyse](#sentiment-analysis) (open bare preview)
* [Taal detectie](#language-detection)
* [Herkenning van benoemde entiteiten](#named-entity-recognition-public-preview) (open bare preview)
* [Herkenning van benoemde entiteiten-persoonlijke gegevens](#named-entity-recognition---personal-information-public-preview) (open bare preview)
* [Entiteit koppelen](#entity-linking)
* [Extractie van sleutel woorden](#key-phrase-extraction)

## <a name="sentiment-analysis"></a>Sentimentanalyse

> [!NOTE]
> De onderstaande code is voor sentiment Analysis v3, een open bare preview-versie.

Maak een nieuwe functie met de naam `sentiment_analysis_example()` die het eind punt en de sleutel als argumenten neemt, en vervolgens de `single_analyze_sentiment()`-functie aanroept. Het geretourneerde antwoord object bevat het sentiment label en de Score van het volledige invoer document, evenals een sentiment-analyse voor elke zin.


```python
from azure.ai.textanalytics import single_analyze_sentiment

def sentiment_analysis_example(endpoint, key):

    document = "I had the best day of my life. I wish you were there with me."

    response = single_analyze_sentiment(endpoint=endpoint, key=key, input_text=document)
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.3f}; neutral={1:.3f}; negative={2:.3f} \n".format(
        response.document_scores.positive,
        response.document_scores.neutral,
        response.document_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Offset: {}, Length: {}]".format(sentence.offset, sentence.length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.3f}\nNeutral={1:.3f}\nNegative={2:.3f}\n".format(
            sentence.sentence_scores.positive,
            sentence.sentence_scores.neutral,
            sentence.sentence_scores.negative,
        ))

            
sentiment_analysis_example(endpoint, key)
```

### <a name="output"></a>Uitvoer

```console
Document Sentiment: positive
Overall scores: positive=0.999; neutral=0.001; negative=0.000 

[Offset: 0, Length: 30]
Sentence 1 sentiment: positive
Sentence score:
positive=0.999
neutral=0.001
negative=0.000

[Offset: 31, Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
positive=0.212
neutral=0.771
negative=0.017
```

## <a name="language-detection"></a>Taaldetectie

Maak een nieuwe functie met de naam `language_detection_example()` die het eind punt en de sleutel als argumenten neemt, en vervolgens de `single_detect_languages()`-functie aanroept. Het geretourneerde antwoord object bevat de gedetecteerde taal in `detected_languages` als dit is gelukt en een `error` als dat niet het geval is.

> [!Tip]
> In sommige gevallen kan het lastig zijn om talen te dubbel zinnigheid op basis van de invoer. U kunt de para meter `country_hint` gebruiken om een land nummer van twee letters op te geven. Standaard gebruikt de API de standaard countryHint om dit gedrag te verwijderen, kunt u deze para meter opnieuw instellen door deze waarde in te stellen op een lege teken reeks `country_hint : ""`. 

```python
from azure.ai.textanalytics import single_detect_language

def language_detection_example(endpoint, key):
    try:
        document = "Ce document est rédigé en Français."
        response = single_detect_language(endpoint=endpoint, key=key, input_text= document)
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(endpoint, key)
```


### <a name="output"></a>Uitvoer

```console
Language:  French
```

## <a name="named-entity-recognition-public-preview"></a>Herkenning van benoemde entiteiten (open bare preview)

> [!NOTE]
> De onderstaande code is voor named entity Recognition v3, een open bare preview-versie.

Maak een nieuwe functie met de naam `entity_recognition_example` die het eind punt en de sleutel als argumenten neemt, vervolgens de `single_recognize_entities()`-functie aanroept en de resultaten doorloopt. Het geretourneerde antwoord object bevat de lijst met gedetecteerde entiteiten in `entity` als dit is gelukt en een `error` als dat niet het geval is. Voor elke gedetecteerde entiteit drukt u het type en subtype af, indien aanwezig.

```python
from azure.ai.textanalytics import single_recognize_entities

def entity_recognition_example(endpoint, key):

    try:
        document = "I had a wonderful trip to Seattle last week."
        result = single_recognize_entities(endpoint=endpoint, key=key, input_text= document)
        
        print("Named Entities:\n")
        for entity in result.entities:
                print("\tText: \t", entity.text, "\tType: \t", entity.type, "\tSubType: \t", entity.subtype,
                      "\n\tOffset: \t", entity.offset, "\tLength: \t", entity.offset, 
                      "\tConfidence Score: \t", round(entity.score, 3), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(endpoint, key)
```

### <a name="output"></a>Uitvoer

```console
Named Entities:

    Text:    Seattle    Type:    Location   SubType:     None 
    Offset:      26     Length:      26     Confidence Score:    0.806 

    Text:    last week  Type:    DateTime   SubType:     DateRange 
    Offset:      34     Length:      34     Confidence Score:    0.8 
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>Herkenning van benoemde entiteiten-persoonlijke gegevens (open bare preview)

> [!NOTE]
> De onderstaande code is voor het detecteren van persoonlijke gegevens met behulp van named entity Recognition v3, dat zich in de open bare preview bevindt.

Maak een nieuwe functie met de naam `entity_pii_example()` die het eind punt en de sleutel als argumenten neemt, vervolgens de `single_recognize_pii_entities()`-functie aanroept en het resultaat ophaalt. Herhaal vervolgens de resultaten en druk de entiteiten af.

```python
from azure.ai.textanalytics import single_recognize_pii_entities

def entity_pii_example(endpoint, key):

        document = "Insurance policy for SSN on file 123-12-1234 is here by approved."


        result = single_recognize_pii_entities(endpoint=endpoint, key=key, input_text= document)
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tType: ", entity.type,"\tSub-Type: ", entity.subtype)
            print("\t\tOffset: ", entity.offset, "\tLength: ", entity.length, "\tScore: {0:.3f}".format(entity.score), "\n")
        
entity_pii_example(endpoint, key)
```

### <a name="output"></a>Uitvoer

```console
Personally Identifiable Information Entities: 
    Text:  123-12-1234  Type:  U.S. Social Security Number (SSN)    Sub-Type:  
        Offset:  33     Length:  11     Score: 0.850 
```

## <a name="entity-linking"></a>Entiteiten koppelen

Maak een nieuwe functie met de naam `entity_linking_example()` die het eind punt en de sleutel als argumenten neemt, vervolgens de `single_recognize_linked_entities()`-functie aanroept en de resultaten doorloopt. Het geretourneerde antwoord object bevat de lijst met gedetecteerde entiteiten in `entities` als dit is gelukt en een `error` als dat niet het geval is. Aangezien gekoppelde entiteiten uniek worden geïdentificeerd, worden exemplaren van dezelfde entiteit gegroepeerd onder een `entity`-object als een lijst van `match`-objecten.

```python
from azure.ai.textanalytics import single_recognize_linked_entities

def entity_linking_example(endpoint, key):

    try:
        document = """Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""
        result = single_recognize_linked_entities(endpoint=endpoint, key=key, input_text= document)

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tScore: {0:.3f}".format(match.score), "\tOffset: ", match.offset, 
                      "\tLength: {}\n".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(endpoint, key)
```

### <a name="output"></a>Uitvoer

```console
Linked Entities:

    Name:  Altair 8800  Id:  Altair 8800    Url:  https://en.wikipedia.org/wiki/Altair_8800 
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Score: 0.777    Offset:  116    Length: 11

    Name:  Bill Gates   Id:  Bill Gates     Url:  https://en.wikipedia.org/wiki/Bill_Gates 
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.555    Offset:  25     Length: 10

        Text: Gates
        Score: 0.555    Offset:  161    Length: 5

    Name:  Paul Allen   Id:  Paul Allen     Url:  https://en.wikipedia.org/wiki/Paul_Allen 
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Score: 0.533    Offset:  40     Length: 10

    Name:  Microsoft    Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft 
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Score: 0.469    Offset:  0  Length: 9

        Text: Microsoft
        Score: 0.469    Offset:  150    Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4 
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.248    Offset:  54     Length: 7

    Name:  BASIC    Id:  BASIC  Url:  https://en.wikipedia.org/wiki/BASIC 
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.281    Offset:  89     Length: 5
```

## <a name="key-phrase-extraction"></a>Sleuteluitdrukkingen extraheren

Maak een nieuwe functie met de naam `key_phrase_extraction_example()` die het eind punt en de sleutel als argumenten neemt, en vervolgens de `single_extract_key_phrases()`-functie aanroept. Het resultaat bevat de lijst met gedetecteerde sleutel zinnen in `key_phrases` als deze is geslaagd en een `error` als dat niet het geval is. Alle gedetecteerde sleutel zinnen afdrukken.

```python
from azure.ai.textanalytics import single_extract_key_phrases

def key_phrase_extraction_example(endpoint, key):

    try:
        document = "My cat might need to see a veterinarian."

        response = single_extract_key_phrases(endpoint=endpoint, key=key, input_text= document)

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(endpoint, key)
```


### <a name="output"></a>Uitvoer

```console
    Key Phrases:
         cat
         veterinarian
```