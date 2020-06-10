---
title: 'Snelstart: Een externe afbeelding analyseren - REST, Python'
titleSuffix: Azure Cognitive Services
description: In deze quickstart analyseert u een externe afbeelding met behulp van de Computer Vision-API met Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18, tracking-python
ms.openlocfilehash: 4ccdf5d2e68d249232600b7c9ff9b1bd29402cc5
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84604589"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-and-python"></a>Snelstartgids: een externe installatie kopie analyseren met behulp van de Computer Vision REST API en python

In deze Snelstartgids gaat u een op afstand opgeslagen afbeelding analyseren om visuele functies te extra heren met behulp van de Computer Vision REST API. Met de methode voor het [analyseren van afbeeldingen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) kunt u visuele functies extra heren op basis van de inhoud van de installatie kopie.

U kunt deze snelstart stapsgewijs uitvoeren met behulp van een Jupyter Notebook op [MyBinder](https://mybinder.org). Selecteer de volgende knop om Binder te starten:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/try/cognitive-services/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- [Python](https://www.python.org/downloads/) moet geïnstalleerd zijn als u het voorbeeld lokaal wilt uitvoeren.
- U moet beschikken over een abonnementssleutel voor Computer Vision. U kunt een gratis proef versie verkrijgen van [Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Of volg de instructies in [Create a cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om u te abonneren op computer vision en uw sleutel op te halen. Vervolgens kunt u [omgevings variabelen maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel-en service-eindpunt teken reeks, `COMPUTER_VISION_SUBSCRIPTION_KEY` respectievelijk met de naam en `COMPUTER_VISION_ENDPOINT` .
- U moet de volgende python-pakketten hebben geïnstalleerd. U kunt [PIP](https://packaging.python.org/tutorials/installing-packages/) gebruiken om Python-pakketten te installeren.
    - aanvragen
    - [matplotlib](https://matplotlib.org/)
    - [pillow](https://python-pillow.org/)

## <a name="create-and-run-the-sample"></a>Het voorbeeld maken en uitvoeren

U kunt het voorbeeld maken en uitvoeren aan de hand van de volgende stappen:

1. Kopieer de volgende code naar een teksteditor.
1. Vervang eventueel de waarde van `image_url` door de URL van een andere afbeelding die u wilt analyseren.
1. Sla de code op als een bestand met de extensie `.py`. Bijvoorbeeld `analyze-image.py`.
1. Open een opdrachtpromptvenster.
1. Typ bij de prompt de opdracht `python` om het voorbeeld uit te voeren. Bijvoorbeeld `python analyze-image.py`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
import json
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

analyze_url = endpoint + "vision/v3.0/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/" + \
    "Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'visualFeatures': 'Categories,Description,Color'}
data = {'url': image_url}
response = requests.post(analyze_url, headers=headers,
                         params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The most
# relevant caption for the image is obtained from the 'description' property.
analysis = response.json()
print(json.dumps(response.json()))
image_caption = analysis["description"]["captions"][0]["text"].capitalize()

# Display the image and overlay it with the caption.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(image_caption, size="x-large", y=-0.1)
plt.show()
```

## <a name="examine-the-response"></a>Het antwoord bekijken

Een geslaagd antwoord wordt geretourneerd in JSON-indeling. De voorbeeldwebpagina parseert en geeft een geslaagd antwoord weer in het opdrachtpromptvenster dat vergelijkbaar is met het volgende voorbeeld:

```json
{
  "categories": [
    {
      "name": "outdoor_",
      "score": 0.00390625,
      "detail": {
        "landmarks": []
      }
    },
    {
      "name": "outdoor_street",
      "score": 0.33984375,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "building",
      "outdoor",
      "street",
      "city",
      "people",
      "busy",
      "table",
      "walking",
      "traffic",
      "filled",
      "large",
      "many",
      "group",
      "night",
      "light",
      "crowded",
      "bunch",
      "standing",
      "man",
      "sign",
      "crowd",
      "umbrella",
      "riding",
      "tall",
      "woman",
      "bus"
    ],
    "captions": [
      {
        "text": "a group of people on a city street at night",
        "confidence": 0.9122243847383961
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Brown",
    "dominantColorBackground": "Brown",
    "dominantColors": [
      "Brown"
    ],
    "accentColor": "B54316",
    "isBwImg": false
  },
  "requestId": "c11894eb-de3e-451b-9257-7c8b168073d1",
  "metadata": {
    "height": 600,
    "width": 450,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>Volgende stappen

Verken vervolgens een python-toepassing die Computer Vision gebruikt voor het uitvoeren van optische teken herkenning (OCR). miniaturen met slimme bijgesneden maken; plus het detecteren, categoriseren, labelen en beschrijven van visuele functies in afbeeldingen.

> [!div class="nextstepaction"]
> [Zelfstudie voor de Computer Vision-API met Python](../Tutorials/PythonTutorial.md)

* Als u snel wilt experimenteren met de Computer Vision-API, gebruikt u de [Open API-testconsole](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).
