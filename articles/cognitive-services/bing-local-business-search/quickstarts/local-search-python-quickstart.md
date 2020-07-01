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
ms.openlocfilehash: c821df0e7cb00c73899a2694dd0b2eb6823b1d9e
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85611193"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Snelstartgids: een query naar de Bing Local Business Search-API in python verzenden

Gebruik deze Quick Start om te leren hoe u aanvragen verzendt naar de Bing lokale Business Search-API, een Azure-cognitieve service. Hoewel deze eenvoudige toepassing is geschreven in Python, is de API een betrouw bare webservice die compatibel is met elke programmeer taal die kan leiden tot het maken van HTTP-aanvragen en het parseren van JSON.

In deze voorbeeld toepassing worden lokale antwoord gegevens opgehaald uit de API voor een zoek query.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/cognitive-services/)
* [Python](https://www.python.org/) 2. x of 3. x.
* Wanneer u uw Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" maakt u een Bing Search resource Maak "  target="_blank"> een Bing Search resource <span class="docon docon-navigate-external x-hidden-focus"></span> </a> in de Azure Portal om uw sleutel en eind punt op te halen. Nadat de app is geïmplementeerd, klikt **u op Ga naar resource**.

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
- [Zoeken in lokale zakelijke Node.js Snelstartgids](local-search-node-quickstart.md)
