---
title: 'Snelstart: Python gebruiken om de Text Analytics-API aan te roepen'
titleSuffix: Azure Cognitive Services
description: Deze Quick Start laat zien hoe u informatie en code voorbeelden kunt ophalen om snel aan de slag te gaan met behulp van de Text Analytics-API in azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.custom: tracking-python
ms.openlocfilehash: 46fd431c14e6870058e415d619256f342d5f4ef4
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85610928"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>Quick Start: de python-REST API gebruiken om de Text Analytics cognitieve service aan te roepen 
<a name="HOLTop"></a>

Gebruik deze Quick Start om de taal te analyseren met de Text Analytics REST API en python. In dit artikel leest u hoe u [taal kunt detecteren](#Detect), sentiment kunt [analyseren](#SentimentAnalysis), [sleutel zinnen kunt extra heren](#KeyPhraseExtraction)en [gekoppelde entiteiten aanduidt](#Entities).

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Vereisten

* [Python 3. x](https://python.org)

* De bibliotheek met python-aanvragen
    
    U kunt de bibliotheek met de volgende opdracht installeren:

    ```console
    pip install --upgrade requests
    ```

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Maak een nieuwe python-toepassing in uw favoriete editor of IDE. Voeg de volgende import bewerkingen toe aan het bestand.

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
```

Maak variabelen voor het Azure-eind punt en de abonnements sleutel van uw resource.
    
```python
import os

subscription_key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```

In de volgende secties wordt beschreven hoe u de functies van de API aanroept.

<a name="Detect"></a>

## <a name="detect-languages"></a>Talen detecteren

Voeg `/text/analytics/v3.0/languages` toe aan het Text Analytics basis-eind punt om de URL voor de taal detectie op te maken. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`
    
```python
language_api_url = endpoint + "/text/analytics/v3.0/languages"
```

De payload van de API bestaat uit een lijst met `documents` -Tuples die een `id` en een-kenmerk bevatten `text` . Het `text` kenmerk slaat de tekst die moet worden geanalyseerd en de `id` kan een wille keurige waarde zijn. 

```python
documents = {"documents": [
    {"id": "1", "text": "This is a document written in English."},
    {"id": "2", "text": "Este es un document escrito en Español."},
    {"id": "3", "text": "这是一个用中文写的文件"}
]}
```

Gebruik de bibliotheek aanvragen om de documenten naar de API te verzenden. Voeg uw abonnements sleutel toe aan de `Ocp-Apim-Subscription-Key` header en verzend de aanvraag met `requests.post()` . 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>Uitvoer

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "English",
                "iso6391Name": "en",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "2",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "3",
            "detectedLanguage": {
                "name": "Chinese_Simplified",
                "iso6391Name": "zh_chs",
                "confidenceScore": 1.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Stemming analyseren

Als u de sentiment (tussen positieve of negatieve) van een set documenten wilt detecteren, voegt `/text/analytics/v3.0/sentiment` u toe aan het Text Analytics base-eind punt om de taal detectie-URL op te maken. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`
    
```python
sentiment_url = endpoint + "/text/analytics/v3.0/sentiment"
```

Net als bij het voor beeld van de taal detectie maakt u een woorden lijst met een `documents` sleutel die bestaat uit een lijst met documenten. Elk document is een tuple die bestaat uit de `id`, de te analyseren `text` en de `language` van de tekst. 

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."}
]}
```

Gebruik de bibliotheek aanvragen om de documenten naar de API te verzenden. Voeg uw abonnements sleutel toe aan de `Ocp-Apim-Subscription-Key` header en verzend de aanvraag met `requests.post()` . 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>Uitvoer

De sentiment-score voor een document ligt tussen 0,0 en 1,0, met een hogere score die een positieve sentiment aangeeft.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 102,
                    "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                }
            ],
            "warnings": []
        },
        {
            "id": "2",
            "sentiment": "negative",
            "confidenceScores": {
                "positive": 0.02,
                "neutral": 0.05,
                "negative": 0.93
            },
            "sentences": [
                {
                    "sentiment": "negative",
                    "confidenceScores": {
                        "positive": 0.02,
                        "neutral": 0.05,
                        "negative": 0.93
                    },
                    "offset": 0,
                    "length": 92,
                    "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Belangrijke woordgroepen herkennen
 
Als u de sleutel zinnen uit een reeks documenten wilt extra heren, voegt `/text/analytics/v3.0/keyPhrases` u toe aan het Text Analytics basis-eind punt om de URL voor de taal detectie op te maken. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`
    
```python
keyphrase_url = endpoint + "/text/analytics/v3.0/keyphrases"
```

Deze verzameling documenten is hetzelfde als die voor het analyse voorbeeld sentiment.

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
    {"id": "3", "language": "en",
        "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."}
]}
```

Gebruik de bibliotheek aanvragen om de documenten naar de API te verzenden. Voeg uw abonnements sleutel toe aan de `Ocp-Apim-Subscription-Key` header en verzend de aanvraag met `requests.post()` . 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>Uitvoer

```json
{
    "documents": [
        {
            "id": "1",
            "keyPhrases": [
                "HDR resolution",
                "new XBox",
                "clean look"
            ],
            "warnings": []
        },
        {
            "id": "2",
            "keyPhrases": [
                "Carlos",
                "notificacion",
                "algun problema",
                "telefono movil"
            ],
            "warnings": []
        },
        {
            "id": "3",
            "keyPhrases": [
                "new hotel",
                "Grand Hotel",
                "review",
                "center of Seattle",
                "classiest decor",
                "stars"
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Entiteiten identificeren

Als u bekende entiteiten (personen, plaatsen en dingen) in tekst documenten wilt identificeren, voegt `/text/analytics/v3.0/entities/recognition/general` u toe aan het Text Analytics base-eind punt om de URL voor de taal detectie te vormen. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`
    
```python
entities_url = endpoint + "/text/analytics/v3.0/entities/recognition/general/recognition/general"
```

Maak een verzameling documenten, zoals in de voor gaande voor beelden. 

```python
documents = {"documents": [
    {"id": "1", "text": "Microsoft is an It company."}
]}
```

Gebruik de bibliotheek aanvragen om de documenten naar de API te verzenden. Voeg uw abonnements sleutel toe aan de `Ocp-Apim-Subscription-Key` header en verzend de aanvraag met `requests.post()` .

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
pprint(entities)
```

### <a name="output"></a>Uitvoer

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "text": "Microsoft",
                    "category": "Organization",
                    "offset": 0,
                    "length": 9,
                    "confidenceScore": 0.86
                },
                {
                    "text": "IT",
                    "category": "Skill",
                    "offset": 16,
                    "length": 2,
                    "confidenceScore": 0.8
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Text Analytics met Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Zie ook 

 [Overzicht van Text Analytics](../overview.md)  
 [Veelgestelde vragen](../text-analytics-resource-faq.md)
