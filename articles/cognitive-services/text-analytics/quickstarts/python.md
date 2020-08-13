---
title: 'Quickstart: Python gebruiken om de Text Analytics-API aan te roepen'
titleSuffix: Azure Cognitive Services
description: In deze quickstart leert u hoe u informatie en codevoorbeelden ophaalt om snel aan de slag te gaan met behulp van de Text Analytics-API in Microsoft Cognitive Services in Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 34d1b62ed97b966c000ff81e8f7676c30338b6a1
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876764"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>Quickstart: De Python REST API gebruiken om de Text Analytics Cognitive Service aan te roepen 
<a name="HOLTop"></a>

Gebruik deze quickstart om taal te analyseren met de Text Analytics REST API en Python. In dit artikel ziet u hoe u [taal detecteert](#Detect), [gevoel analyseert](#SentimentAnalysis), [sleuteltermen ophaalt](#KeyPhraseExtraction) en [gekoppelde identiteiten identificeert](#Entities).

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Vereisten

* [Python 3.x](https://python.org)

* De aanvragenbibliotheek voor Python
    
    U kunt de bibliotheek installeren met deze opdracht:

    ```console
    pip install --upgrade requests
    ```

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Maak een nieuwe Python-toepassing in uw favoriete editor of IDE. Voeg de volgende imports toe aan uw bestand.

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
```

Maak variabelen voor het Azure-eindpunt en de abonnementssleutel voor uw resource.
    
```python
import os

subscription_key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```

In de volgende secties wordt beschreven hoe u de functies van de API aanroept.

<a name="Detect"></a>

## <a name="detect-languages"></a>Talen detecteren

Voeg `/text/analytics/v3.0/languages` toe aan het Text Analytics-basiseindpunt om de URL voor taaldetectie op te maken. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`
    
```python
language_api_url = endpoint + "/text/analytics/v3.0/languages"
```

De nettolading voor de API bestaat uit een lijst met `documents`. Dit zijn tuples met een `id` en een `text`-kenmerk. Met het kenmerk `text` wordt de tekst opgeslagen die moet worden geanalyseerd. De `id` kan elke waarde zijn. 

```python
documents = {"documents": [
    {"id": "1", "text": "This is a document written in English."},
    {"id": "2", "text": "Este es un document escrito en Español."},
    {"id": "3", "text": "这是一个用中文写的文件"}
]}
```

Gebruik de aanvragenbibliotheek om de documenten naar de API te verzenden. Voeg uw abonnementssleutel toe aan de `Ocp-Apim-Subscription-Key`-header en verstuur de aanvraag met `requests.post()`. 

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

Als u het gevoel (ergens tussen positief en negatief) van een set documenten wilt detecteren, voegt u `/text/analytics/v3.0/sentiment` toe aan het Text Analytics-basiseindpunt om de URL voor de taaldetectie op te maken. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`
    
```python
sentiment_url = endpoint + "/text/analytics/v3.0/sentiment"
```

Net zoals in het voorbeeld van taaldetectie maakt u een woordenlijst met een `documents`-sleutel die uit een lijst met documenten bestaat. Elk document is een tuple die bestaat uit de `id`, de te analyseren `text` en de `language` van de tekst. 

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."}
]}
```

Gebruik de aanvragenbibliotheek om de documenten naar de API te verzenden. Voeg uw abonnementssleutel toe aan de `Ocp-Apim-Subscription-Key`-header en verstuur de aanvraag met `requests.post()`. 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>Uitvoer

De gevoelsscore voor een document is tussen de 0,0 en 1,0, waarbij een hogere score op een positiever gevoel wijst.

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
 
Als u de sleuteltermen uit een reeks documenten wilt extraheren, voegt u `/text/analytics/v3.0/keyPhrases` toe aan het Text Analytics-basiseindpunt om de URL voor taaldetectie op te maken. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`
    
```python
keyphrase_url = endpoint + "/text/analytics/v3.0/keyphrases"
```

De verzameling documenten is dezelfde als die in het voorbeeld voor gevoelsanalyse werd gebruikt.

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

Gebruik de aanvragenbibliotheek om de documenten naar de API te verzenden. Voeg uw abonnementssleutel toe aan de `Ocp-Apim-Subscription-Key`-header en verstuur de aanvraag met `requests.post()`. 

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

Als u bekende entiteiten (personen, plaatsen en dingen) in tekstdocumenten wilt identificeren, voegt u `/text/analytics/v3.0/entities/recognition/general` toe aan het Text Analytics-basiseindpunt om de URL voor taaldetectie op te maken. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`
    
```python
entities_url = endpoint + "/text/analytics/v3.0/entities/recognition/general"
```

Maak een verzameling documenten, zoals in de voorgaande voorbeelden. 

```python
documents = {"documents": [
    {"id": "1", "text": "Microsoft is an It company."}
]}
```

Gebruik de aanvragenbibliotheek om de documenten naar de API te verzenden. Voeg uw abonnementssleutel toe aan de `Ocp-Apim-Subscription-Key`-header en verstuur de aanvraag met `requests.post()`.

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
