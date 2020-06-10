---
title: 'Quick Start: een query naar de API in python verzenden-zoeken in lokale bedrijfs-Bing'
titleSuffix: Azure Cognitive Services
description: Gebruik deze Quick Start om de Bing lokale zakelijke zoek-API in python te gebruiken.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: tracking-python
ms.openlocfilehash: fcd3ab3cce74aa0ef021427904077c364de1f493
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84606272"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Snelstartgids: een query naar de Bing Local Business Search-API in python verzenden

Gebruik deze Quick Start om te leren hoe u aanvragen verzendt naar de Bing lokale Business Search-API, een Azure-cognitieve service. Hoewel deze eenvoudige toepassing is geschreven in Python, is de API een betrouw bare webservice die compatibel is met elke programmeer taal die kan leiden tot het maken van HTTP-aanvragen en het parseren van JSON.

In deze voorbeeld toepassing worden lokale antwoord gegevens opgehaald uit de API voor een zoek query.

## <a name="prerequisites"></a>Vereisten

* [Python](https://www.python.org/) 2. x of 3. x.
* Een [Cognitive Services-API-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met Bing zoeken-API's. Voor deze Quick start is de [gratis proef versie](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) voldoende. Sla de API-sleutel op die wordt weer gegeven wanneer u de gratis proef versie activeert. Zie [Cognitive Services prijzen-Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)voor meer informatie.

## <a name="run-the-complete-application"></a>De volledige toepassing uitvoeren

In het volgende voor beeld worden gelokaliseerde resultaten opgehaald, die in de volgende stappen worden geïmplementeerd:
1. Declareer variabelen om het eindpunt op te geven met een host en pad.
2. Geef de query parameter op. 
3. Definieer de zoek functie die de aanvraag maakt en voegt de `Ocp-Apim-Subscription-Key` header toe.
4. Stel de `Ocp-Apim-Subscription-Key` koptekst in. 
5. Maak de verbinding en verzend de aanvraag.
6. Geef de JSON-resultaten weer.

De volledige code voor deze demo is als volgt:

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
- [Lokale zakelijke zoek opdracht voor zoeken in Java Quick Start](local-search-java-quickstart.md)
- [Lokale zakelijke zoek acties C# Quick Start](local-quickstart.md)
- [Snelstartgids voor lokaal zoeken in node. js](local-search-node-quickstart.md)
