---
title: Snelstart - Een query verzenden naar de API in Python - Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: Gebruik deze snelstart om de Bing Local Business Search API in Python te gebruiken.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: c7e7ef7f052fccfea18b246f41109d5fa7528b4b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379740"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Snelstart: een query verzenden naar de Bing Local Business Search API in Python

Gebruik deze snelstart om aanvragen te verzenden naar de Bing Local Business Search API, een Azure Cognitive Service. Hoewel deze eenvoudige toepassing is geschreven in Python, is de API een RESTful Web-service die compatibel is met elke programmeertaal die HTTP-verzoeken kan uitvoeren en JSON kan ontleden.

Met deze voorbeeldtoepassing worden lokale antwoordgegevens `hotel in Bellevue`uit de API voor de zoekopdracht opgehaald.

## <a name="prerequisites"></a>Vereisten

* [Python (Python)](https://www.python.org/) 2.x of 3.x
 
U moet een [API-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) voor Cognitive Services hebben met Bing-API's. De [gratis proefversie](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) is voldoende voor deze quickstart. Gebruik de toegangssleutel van de gratis proefperiode.  Zie ook [Prijsinformatie Cognitive Services - Bing Zoeken-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>De volledige toepassing uitvoeren

De volgende code krijgt gelokaliseerde resultaten. De code wordt in de volgende stappen geïmplementeerd:
1. Declareer variabelen om het eindpunt op te geven met een host en pad.
2. Geef de queryparameter op. 
3. Definieer de Search-functie die de aanvraag maakt en de header Ocp-Apim-Subscription-Key toevoegt.
4. Stel de header Ocp-Apim-Subscription-Key in. 
5. Maak de verbinding en stuur het verzoek.
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
- [Lokaal zakelijk zoeken Java Quickstart](local-search-java-quickstart.md)
- [Lokaal zakelijk zoeken C# Snelstart](local-quickstart.md)
- [Quickstart voor lokaal zakelijk zoeken](local-search-node-quickstart.md)
