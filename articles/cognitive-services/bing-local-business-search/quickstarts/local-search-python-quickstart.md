---
title: 'Quick Start: een query naar de Bing Local Business Search-API in python verzenden'
titleSuffix: Azure Cognitive Services
description: Gebruik dit artikel om te beginnen met het gebruik van de Bing Local Business Search-API in python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 100d731614e2193c5f858249315f748836323b4c
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906257"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Quickstart: Een query naar de Bing Local Business Search-API in python verzenden

Gebruik deze Quick Start om aanvragen te verzenden naar de Bing lokale Business Search-API, een Azure-cognitieve service. Hoewel deze eenvoudige toepassing is geschreven in Python, is de API een betrouw bare webservice die compatibel is met elke programmeer taal die kan leiden tot het maken van HTTP-aanvragen en het parseren van JSON.

In deze voorbeeld toepassing worden lokale antwoord gegevens opgehaald uit de API voor de `hotel in Bellevue`Zoek query.

## <a name="prerequisites"></a>Vereisten

* [Python](https://www.python.org/) 2.x of 3.x
 
U moet een [Cognitive Services-API-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) hebben met Bing-api's. De [gratis proefversie](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) is voldoende voor deze snelstartgids. Gebruik de toegangs sleutel die wordt gebruikt door de gratis proef versie.  Zie ook [Prijsinformatie Cognitive Services - Bing Zoeken-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>De volledige toepassing uitvoeren

De volgende code haalt gelokaliseerde resultaten op. De code wordt geïmplementeerd in de volgende stappen:
1. Declareer variabelen om het eindpunt op te geven met een host en pad.
2. Geef de query parameter op. 
3. Definieer de zoek functie die de aanvraag maakt en voegt de header van de APIM-abonnements sleutel toe.
4. Stel de APIM-abonnee sleutel header in. 
5. Maak de verbinding en verzend de aanvraag.
6. Geef de JSON-resultaten weer.

Dit is de volledige code voor deze demo:

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com/bing'
path = '/v7.0/localbusinesses/search'

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
- [Lokale zakelijke zoek opdracht voor zoeken in Java Quick Start](local-search-java-quickstart.md)
- [Snelstartgids voor lokale C# zakelijke Zoek opdrachten](local-quickstart.md)
- [Quick start voor lokaal bedrijfs Zoek knooppunt](local-search-node-quickstart.md)
