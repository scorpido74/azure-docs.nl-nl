---
title: 'Snelstart: Ruby gebruiken om de Text Analytics-API aan te roepen'
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
ms.openlocfilehash: 0cda6005a014e0f269e468daf389c1abea9d4bcd
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85611171"
---
# <a name="quickstart-using-ruby-to-call-the-text-analytics-cognitive-service"></a>Snelstart: Ruby gebruiken om de Text Analytics Cognitive Service aan te roepen
<a name="HOLTop"></a>

In dit artikel ziet u hoe u de  [Text Analytics-API's](//go.microsoft.com/fwlink/?LinkID=759711)  met Ruby kunt gebruiken om [taal te detecteren](#Detect), [sentiment te analyseren](#SentimentAnalysis), [sleuteltermen op te halen](#KeyPhraseExtraction) en [gekoppelde entiteiten te identificeren](#Entities).

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="Detect"></a>

## <a name="detect-language"></a>Taal detecteren

Met de Language Detection-API wordt de taal van een tekstdocument gedetecteerd met behulp van de [methode Detect Language](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

1. Maak een nieuw Ruby-project in uw favoriete IDE.
1. Voeg de onderstaande code toe.
1. Kopieer de Text Analytics sleutel en het eind punt naar de code. 
1. Voer het programma uit.

```ruby
# encoding: UTF-8

require 'net/https'
require 'uri'
require 'json'

subscription_key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"

path = '/text/analytics/v3.0/languages'

uri = URI(endpoint + path)

documents = { 'documents': [
    { 'id' => '1', 'text' => 'This is a document written in English.' },
    { 'id' => '2', 'text' => 'Este es un document escrito en Español.' },
    { 'id' => '3', 'text' => '这是一个用中文写的文件' }
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = subscription_key
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Antwoord voor taaldetectie**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

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

Met de Sentiment Analysis-API wordt een set tekstrecords gedetecteerd met behulp van de [methode Sentiment](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9). In het volgende voorbeeld worden twee documenten beoordeeld, één in het Engels en één in het Spaans.

1. Maak een nieuw Ruby-project in uw favoriete IDE.
1. Voeg de onderstaande code toe.
1. Kopieer de Text Analytics sleutel en het eind punt naar de code. 
1. Voer het programma uit.

```ruby
# encoding: UTF-8

require 'net/https'
require 'uri'
require 'json'

subscription_key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"

path = '/text/analytics/v3.0/sentiment'

uri = URI(endpoint + path)

documents = { 'documents': [
    { 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
    { 'id' => '2', 'language' => 'es', 'text' => 'Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.' }
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = subscription_key
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Antwoord bij sentimentanalyse**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

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

Met de Key Phrase Extraction-API worden sleuteltermen opgehaald uit een tekstdocument met behulp van de [methode Key Phrases](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6). In het volgende voorbeeld worden sleuteltermen opgehaald voor zowel de Engelse als Spaanse documenten.

1. Maak een nieuw Ruby-project in uw favoriete IDE.
1. Voeg de onderstaande code toe.
1. Kopieer de Text Analytics sleutel en het eind punt naar de code.
1. Voer het programma uit.


```ruby
# encoding: UTF-8

require 'net/https'
require 'uri'
require 'json'

subscription_key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"

path = '/text/analytics/v3.0/keyPhrases'

uri = URI(endpoint + path)

documents = { 'documents': [
    { 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
    { 'id' => '2', 'language' => 'es', 'text' => 'Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.' },
    { 'id' => '3', 'language' => 'en', 'text' => 'The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I\'ve ever seen.' },
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = subscription_key
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Antwoord bij extraheren van sleuteltermen**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

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

## <a name="entity-recognition"></a>Herkenning van entiteiten

De Entities-API extraheert entiteiten uit een tekstdocument met behulp van de [methode Entities](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634). In het volgende voorbeeld worden entiteiten geïdentificeerd voor Engelse documenten.

1. Maak een nieuw Ruby-project in uw favoriete IDE.
1. Voeg de onderstaande code toe.
1. Kopieer de Text Analytics sleutel en het eind punt naar de code.
1. Voer het programma uit.

```ruby
# encoding: UTF-8

require 'net/https'
require 'uri'
require 'json'

subscription_key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"

path = '/text/analytics/v3.0/entities/recognition/general'

uri = URI(endpoint + path)

documents = { 'documents': [
    { 'id' => '1', 'language' => 'en', 'text' => 'Microsoft is an It company.' }
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = subscription_key
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Antwoord bij extraheren van entiteiten**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

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
