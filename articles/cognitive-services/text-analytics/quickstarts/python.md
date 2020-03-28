---
title: 'Snelstart: Python gebruiken om de Text Analytics-API aan te roepen'
titleSuffix: Azure Cognitive Services
description: In deze quickstart u informatie en codevoorbeelden opvragen om u snel aan de slag te helpen met de Text Analytics API in Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 7f2a4ff98345aa43dd6a99eafd60ff2d05ee1bee
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378548"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>Snelstart: de Python REST API gebruiken om de Text Analytics Cognitive Service aan te roepen 
<a name="HOLTop"></a>

Gebruik deze snelstart om taal te analyseren met de Text Analytics REST API en Python. In dit artikel ziet u hoe u [taal detecteren,](#Detect) [sentiment analyseren,](#SentimentAnalysis) [sleutelzinnen extraheren](#KeyPhraseExtraction)en [gekoppelde entiteiten identificeren](#Entities).

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Vereisten

* [Python 3.x](https://python.org)

* De Python vraagt bibliotheek aan
    
    U de bibliotheek met deze opdracht installeren:

    ```console
    pip install --upgrade requests
    ```

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Maak een nieuwe Python-toepassing in uw favoriete editor of IDE. Voeg de volgende importtoe aan uw bestand.

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
```

Maak variabelen voor het Azure-eindpunt en de abonnementssleutel van uw bron.
    
```python
import os

subscription_key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```

In de volgende secties wordt beschreven hoe u elk van de functies van de API aanroept.

<a name="Detect"></a>

## <a name="detect-languages"></a>Talen detecteren

Voeg `/text/analytics/v2.1/languages` toe aan het basiseindpunt Text Analytics om de URL voor taaldetectie te vormen. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/languages`
    
```python
language_api_url = endpoint + "/text/analytics/v2.1/languages"
```

De payload van de API `documents`bestaat uit een lijst van `id` , `text` die tuples met een en een attribuut. Het `text` kenmerk slaat de te analyseren `id` tekst op en deze kan elke waarde zijn. 

```python
documents = {"documents": [
    {"id": "1", "text": "This is a document written in English."},
    {"id": "2", "text": "Este es un document escrito en Español."},
    {"id": "3", "text": "这是一个用中文写的文件"}
]}
```

Gebruik de bibliotheek Aanvragen om de documenten naar de API te verzenden. Voeg uw abonnementssleutel `Ocp-Apim-Subscription-Key` toe aan de `requests.post()`koptekst en verzend de aanvraag met . 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>Uitvoer

```json
{
"documents":[
    {
        "detectedLanguages":[
        {
            "iso6391Name":"en",
            "name":"English",
            "score":1.0
        }
        ],
        "id":"1"
    },
    {
        "detectedLanguages":[
        {
            "iso6391Name":"es",
            "name":"Spanish",
            "score":1.0
        }
        ],
        "id":"2"
    },
    {
        "detectedLanguages":[
        {
            "iso6391Name":"zh_chs",
            "name":"Chinese_Simplified",
            "score":1.0
        }
        ],
        "id":"3"
    }
],
"errors":[]
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Stemming analyseren

Als u het gevoel (dat varieert tussen positief of negatief) van een set documenten wilt detecteren, wordt het `/text/analytics/v2.1/sentiment` basiseindpunt text Analytics toegevoegd aan de URL voor taaldetectie. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`
    
```python
sentiment_url = endpoint + "/text/analytics/v2.1/sentiment"
```

Net als bij het voorbeeld van `documents` taaldetectie maakt u een woordenboek met een sleutel die bestaat uit een lijst met documenten. Elk document is een tuple die bestaat uit de `id`, de te analyseren `text` en de `language` van de tekst. 

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
    {"id": "2", "language": "en",
        "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},
    {"id": "3", "language": "es",
        "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},
    {"id": "4", "language": "es",
     "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
]}
```

Gebruik de bibliotheek Aanvragen om de documenten naar de API te verzenden. Voeg uw abonnementssleutel `Ocp-Apim-Subscription-Key` toe aan de `requests.post()`koptekst en verzend de aanvraag met . 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>Uitvoer

De sentimentscore voor een document ligt tussen 0,0 en 1,0, met een hogere score die wijst op een positiever sentiment.

```json
{
  "documents":[
    {
      "id":"1",
      "score":0.9708490371704102
    },
    {
      "id":"2",
      "score":0.0019068121910095215
    },
    {
      "id":"3",
      "score":0.7456425428390503
    },
    {
      "id":"4",
      "score":0.334433376789093
    }
  ],
  "errors":[]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Belangrijke woordgroepen herkennen
 
Als u de belangrijkste zinnen uit een `/text/analytics/v2.1/keyPhrases` reeks documenten wilt extraheren, wordt u lid van het basiseindpunt Text Analytics om de URL voor taaldetectie te vormen. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/keyPhrases`
    
```python
keyphrase_url = endpoint + "/text/analytics/v2.1/keyphrases"
```

Deze verzameling documenten is hetzelfde gebruikt voor het sentiment analyse voorbeeld.

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
    {"id": "2", "language": "en",
        "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},
    {"id": "3", "language": "es",
        "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},
    {"id": "4", "language": "es",
     "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
]}
```

Gebruik de bibliotheek Aanvragen om de documenten naar de API te verzenden. Voeg uw abonnementssleutel `Ocp-Apim-Subscription-Key` toe aan de `requests.post()`koptekst en verzend de aanvraag met . 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>Uitvoer

```json
{
  "documents":[
    {
      "keyPhrases":[
        "wonderful experience",
        "staff",
        "rooms"
      ],
      "id":"1"
    },
    {
      "keyPhrases":[
        "food",
        "terrible time",
        "hotel",
        "staff"
      ],
      "id":"2"
    },
    {
      "keyPhrases":[
        "Monte Rainier",
        "caminos"
      ],
      "id":"3"
    },
    {
      "keyPhrases":[
        "carretera",
        "tráfico",
        "día"
      ],
      "id":"4"
    }
  ],
  "errors":[]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Entiteiten identificeren

Als u bekende entiteiten (personen, plaatsen en dingen) wilt `/text/analytics/v2.1/entities` identificeren in tekstdocumenten, wordt het basiseindpunt voor Text Analytics toegevoegd aan de URL voor taaldetectie. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`
    
```python
entities_url = endpoint + "/text/analytics/v2.1/entities"
```

Maak een verzameling documenten, zoals in de vorige voorbeelden. 

```python
documents = {"documents": [
    {"id": "1", "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."}
]}
```

Gebruik de bibliotheek Aanvragen om de documenten naar de API te verzenden. Voeg uw abonnementssleutel `Ocp-Apim-Subscription-Key` toe aan de `requests.post()`koptekst en verzend de aanvraag met .

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
pprint(entities)
```

### <a name="output"></a>Uitvoer

```json
{
   "documents" : [
      {
         "id" : "1",
         "entities" : [
            {
               "name" : "Microsoft",
               "matches" : [
                  {
                     "wikipediaScore" : 0.49897989655674446,
                     "entityTypeScore" : 1.0,
                     "text" : "Microsoft",
                     "offset" : 0,
                     "length" : 9
                  }
               ],
               "wikipediaLanguage" : "en",
               "wikipediaId" : "Microsoft",
               "wikipediaUrl" : "https://en.wikipedia.org/wiki/Microsoft",
               "bingId" : "a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type" : "Organization"
            },
            {
               "name" : "Bill Gates",
               "matches" : [
                  {
                     "wikipediaScore" : 0.58357497243368983,
                     "entityTypeScore" : 0.999847412109375,
                     "text" : "Bill Gates",
                     "offset" : 25,
                     "length" : 10
                  }
               ],
               "wikipediaLanguage" : "en",
               "wikipediaId" : "Bill Gates",
               "wikipediaUrl" : "https://en.wikipedia.org/wiki/Bill_Gates",
               "bingId" : "0d47c987-0042-5576-15e8-97af601614fa",
               "type" : "Person"
            },
            {
               "name" : "Paul Allen",
               "matches" : [
                  {
                     "wikipediaScore" : 0.52977533244176866,
                     "entityTypeScore" : 0.99884098768234253,
                     "text" : "Paul Allen",
                     "offset" : 40,
                     "length" : 10
                  }
               ],
               "wikipediaLanguage" : "en",
               "wikipediaId" : "Paul Allen",
               "wikipediaUrl" : "https://en.wikipedia.org/wiki/Paul_Allen",
               "bingId" : "df2c4376-9923-6a54-893f-2ee5a5badbc7",
               "type" : "Person"
            },
            {
               "name" : "April 4",
               "matches" : [
                  {
                     "wikipediaScore" : 0.37220990924571939,
                     "entityTypeScore" : 0.8,
                     "text" : "April 4",
                     "offset" : 54,
                     "length" : 7
                  }
               ],
               "wikipediaLanguage" : "en",
               "wikipediaId" : "April 4",
               "wikipediaUrl" : "https://en.wikipedia.org/wiki/April_4",
               "bingId" : "52535f87-235e-b513-54fe-c03e4233ac6e",
               "type" : "Other"
            },
            {
               "name" : "April 4, 1975",
               "matches" : [
                  {
                     "entityTypeScore" : 0.8,
                     "text" : "April 4, 1975",
                     "offset" : 54,
                     "length" : 13
                  }
               ],
               "type" : "DateTime",
               "subType" : "Date"
            },
            {
               "name" : "BASIC",
               "matches" : [
                  {
                     "wikipediaScore" : 0.35686239324548041,
                     "entityTypeScore" : 0.8,
                     "text" : "BASIC",
                     "offset" : 89,
                     "length" : 5
                  }
               ],
               "wikipediaLanguage" : "en",
               "wikipediaId" : "BASIC",
               "wikipediaUrl" : "https://en.wikipedia.org/wiki/BASIC",
               "bingId" : "5b16443d-501c-58f3-352e-611bbe75aa6e",
               "type" : "Other"
            },
            {
               "name" : "Altair 8800",
               "matches" : [
                  {
                     "wikipediaScore" : 0.868324676465041,
                     "entityTypeScore" : 0.8,
                     "text" : "Altair 8800",
                     "offset" : 116,
                     "length" : 11
                  }
               ],
               "wikipediaLanguage" : "en",
               "wikipediaId" : "Altair 8800",
               "wikipediaUrl" : "https://en.wikipedia.org/wiki/Altair_8800",
               "bingId" : "7216c654-3779-68a2-c7b7-12ff3dad5606",
               "type" : "Other"
            },
            {
               "name" : "Altair",
               "matches" : [
                  {
                     "entityTypeScore" : 0.52505272626876831,
                     "text" : "Altair",
                     "offset" : 116,
                     "length" : 6
                  }
               ],
               "type" : "Organization"
            },
            {
               "name" : "8800",
               "matches" : [
                  {
                     "entityTypeScore" : 0.8,
                     "text" : "8800",
                     "offset" : 123,
                     "length" : 4
                  }
               ],
               "type" : "Quantity",
               "subType" : "Number"
            }
         ]
      }
   ],
   "errors" : []
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Tekstanalyse met Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Zie ook 

 [Overzicht van Text Analytics](../overview.md)  
 [Veelgestelde vragen (FAQ)](../text-analytics-resource-faq.md)
