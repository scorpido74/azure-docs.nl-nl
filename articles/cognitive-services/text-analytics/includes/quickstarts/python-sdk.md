---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2019
ms.author: aahi
ms.openlocfilehash: 30c65abcbf469dd4f6e8987aa1e2ee8a36fef17a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987883"
---
<a name="HOLTop"></a>

#### <a name="version-30-previewtabversion-3"></a>[Versie 3,0-Preview](#tab/version-3)

[v3-referentie documentatie](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b1/azure.ai.textanalytics.html) | [v3-bibliotheek bron code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [v3-pakket (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | v3-voor [beelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21tabversion-2"></a>[Versie 2,1](#tab/version-2)

[v2-referentie documentatie](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [v2-bibliotheek bron code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [v2-pakket (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | v2-voor [beelden](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Instellen

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Na de installatie van python kunt u de client bibliotheek installeren met:

#### <a name="version-30-previewtabversion-3"></a>[Versie 3,0-Preview](#tab/version-3)

```console
pip install azure-ai-textanalytics
```

#### <a name="version-21tabversion-2"></a>[Versie 2,1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

---

### <a name="create-a-new-python-application"></a>Een nieuwe python-toepassing maken

Maak een nieuw python-bestand en maak variabelen voor het Azure-eind punt en de abonnements sleutel van uw resource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Object model

#### <a name="version-30-previewtabversion-3"></a>[Versie 3,0-Preview](#tab/version-3)

De Text Analytics-client is een `TextAnalyticsClient`-object dat met behulp van uw sleutel wordt geverifieerd bij Azure. De-client biedt verschillende methoden voor het analyseren van tekst als een batch. In deze Quick Start wordt gebruikgemaakt van een verzameling functies waarmee u snel afzonderlijke documenten kunt verzenden.

Wanneer tekst met batch verwerking naar de API wordt verzonden als een lijst met `documents`, `dictionary` objecten die een combi natie van `id`, `text`en `language` kenmerken bevatten, afhankelijk van de gebruikte methode. Het kenmerk `text` slaat de tekst op die moet worden geanalyseerd in het oorspronkelijke `language`en de `id` kan een wille keurige waarde zijn. Bij het verwerken van één document is alleen een `text` invoer vereist, zoals in de onderstaande voor beelden kan worden weer gegeven.  

Het antwoord object is een lijst met de analyse-informatie voor elk document. 

#### <a name="version-21tabversion-2"></a>[Versie 2,1](#tab/version-2)

De Text Analytics-client is een [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) -object dat met behulp van uw sleutel wordt geverifieerd bij Azure. De-client biedt verschillende methoden voor het analyseren van tekst, als één teken reeks of een batch. 

Tekst wordt naar de API verzonden als een lijst met `documents`. Dit zijn `dictionary` objecten die een combi natie van `id`, `text`en `language` kenmerken bevatten, afhankelijk van de gebruikte methode. Het kenmerk `text` slaat de tekst op die moet worden geanalyseerd in het oorspronkelijke `language`en de `id` kan een wille keurige waarde zijn. 

---

## <a name="code-examples"></a>Code voorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Text Analytics-client bibliotheek voor python:

* [Sentimentanalyse](#sentiment-analysis)
* [Taal detectie](#language-detection)
* [Herkenning van benoemde entiteiten](#named-entity-recognition-ner) 
* [Entiteit koppelen](#entity-linking)
* [Extractie van sleutel woorden](#key-phrase-extraction)

## <a name="sentiment-analysis"></a>Sentimentanalyse

#### <a name="version-30-previewtabversion-3"></a>[Versie 3,0-Preview](#tab/version-3)

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

#### <a name="version-21tabversion-2"></a>[Versie 2,1](#tab/version-2)

Verifieer een client object en roep de functie [sentiment ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) aan. Herhaal de resultaten en druk de document-ID en sentiment Score af. Een score dichter bij 0 geeft aan dat er een negatieve sentiment is, terwijl een score dichter bij 1 wijst op een positieve sentiment.

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

#### <a name="version-30-previewtabversion-3"></a>[Versie 3,0-Preview](#tab/version-3)

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

#### <a name="version-21tabversion-2"></a>[Versie 2,1](#tab/version-2)

Roep [detect_language ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) aan met behulp van de client die u eerder hebt gemaakt en ontvang het resultaat. Herhaal vervolgens de resultaten en druk de ID van elk document af en de eerste geretourneerde taal.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Uitvoer

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Herkenning van benoemde entiteiten (NER)

#### <a name="version-30-previewtabversion-3"></a>[Versie 3,0-Preview](#tab/version-3)

> [!NOTE]
> In versie `3.0-preview`:
> * NER bevat afzonderlijke methoden voor het detecteren van persoonlijke gegevens. 
> * Entiteits koppeling is een afzonderlijke aanvraag dan NER.

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

## <a name="using-ner-to-detect-personal-information"></a>NER gebruiken om persoonlijke gegevens te detecteren

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

#### <a name="version-21tabversion-2"></a>[Versie 2,1](#tab/version-2)

> [!NOTE]
> In versie 2,1 wordt koppeling van entiteit opgenomen in het NER-antwoord.

Als u de client eerder hebt gemaakt, moet u de functie [entities ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) aanroepen en het resultaat ophalen. Herhaal vervolgens de resultaten en druk de document-ID en de entiteiten daarin af.

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

## <a name="key-phrase-extraction"></a>Sleuteluitdrukkingen extraheren


#### <a name="version-30-previewtabversion-3"></a>[Versie 3,0-Preview](#tab/version-3)

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

#### <a name="version-21tabversion-2"></a>[Versie 2,1](#tab/version-2)

Roep de [key_phrases ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) -functie aan en haal het resultaat op met behulp van de client die u eerder hebt gemaakt. Vervolgens doorloopt u de resultaten en drukt u de ID van elk document af en de sleutel zinnen erin.

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