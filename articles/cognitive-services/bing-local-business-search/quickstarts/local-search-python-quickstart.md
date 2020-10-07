---
title: 'Quickstart: een query naar de API verzenden in Python - Bing Local Business Search'
titleSuffix: Azure Cognitive Services
description: Met deze quickstart leert u hoe u de Bing Local Business Search API gebruikt in Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 30e66260f0603139bf45f9a0f0b5f19539ae468e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87843552"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Quickstart: Een query verzenden naar de Bing Local Business Search API in Python

Gebruik deze quickstart om te leren hoe u aanvragen verzendt naar de Bing Local Business Search-API, een Azure Cognitive Service. Hoewel deze eenvoudige toepassing in Python is geschreven, is de API een RESTful-webservice die compatibel is met elke programmeertaal die HTTP-aanvragen kan doen en JSON kan parseren.

In deze voorbeeldtoepassing worden lokale antwoordgegevens opgehaald uit de API voor een zoekquery.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/cognitive-services/)
* [Python](https://www.python.org/) 2.x of 3.x.
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Een Bing Search-resource maken"  target="_blank">maakt u een Bing Search-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.

## <a name="run-the-complete-application"></a>De volledige toepassing uitvoeren

In het volgende voorbeeld worden gelokaliseerde resultaten opgehaald, die in de volgende stappen worden geïmplementeerd:
1. Declareer variabelen om het eindpunt op te geven met een host en pad.
2. Geef de queryparameter op. 
3. Definieer de zoekfunctie die de aanvraag maakt en de header `Ocp-Apim-Subscription-Key` toevoegt.
4. Stel de header `Ocp-Apim-Subscription-Key` in. 
5. Maak verbinding en verzend de aanvraag.
6. Geef de JSON-resultaten weer.

Dit is de volledige code voor deze demo:

```python
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>Volgende stappen
- [Quickstart over Bing Local Business Search met Java](local-search-java-quickstart.md)
- [Quickstart over zoeken naar lokale bedrijven met C#](local-quickstart.md)
- [Quickstart over zoeken naar lokale bedrijven met Node.js](local-search-node-quickstart.md)
