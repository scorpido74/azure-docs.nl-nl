---
title: 'Snelstartgids: Computer Vision 2,1 en 3,0-pak gedrukte en handgeschreven tekst-REST, python'
titleSuffix: Azure Cognitive Services
description: In deze Snelstartgids extraheert u gedrukte en handgeschreven tekst uit een afbeelding met behulp van de Computer Vision-API met python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: pafarley
ms.custom: seodec18, tracking-python
ms.openlocfilehash: 9c76e77db424d357ffcff12124a86bd7df550f97
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84608890"
---
# <a name="quickstart-extract-printed-and-handwritten-text-using-the-computer-vision-rest-api-and-python"></a>Snelstartgids: gedrukte en handgeschreven tekst extra heren met behulp van de Computer Vision REST API en python

In deze Quick Start haalt u gedrukte en handgeschreven tekst uit een afbeelding op met behulp van de Computer Vision REST API. Met de [methoden lezen en](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) [Lees resultaat ophalen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) kunt u tekst in een afbeelding detecteren en herkende tekens uitpakken in een door een machine Lees bare teken stroom. 

> [!IMPORTANT]
> De methode [Read](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) wordt asynchroon uitgevoerd. Deze methode retourneert geen gegevens in de hoofdtekst van een geslaagd antwoord. In plaats daarvan retourneert de batch Read-methode een URI in de waarde van het `Operation-Location` veld Antwoord header. U kunt deze URI vervolgens aanroepen, waarmee de API [Get Lees resultaat](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) wordt aangegeven om de status te controleren en de resultaten van de aanroep van de methode Read te retour neren.

---

U kunt deze snelstart stapsgewijs uitvoeren met behulp van een Jupyter Notebook op [MyBinder](https://mybinder.org). Selecteer de volgende knop om Binder te starten:

[![De knop Binder starten](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) aan voordat u begint.

- [Python](https://www.python.org/downloads/) moet geïnstalleerd zijn als u het voorbeeld lokaal wilt uitvoeren.
- U moet beschikken over een abonnementssleutel voor Computer Vision. U kunt een gratis proef versie verkrijgen van [Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Of volg de instructies in [Create a cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om u te abonneren op computer vision en uw sleutel op te halen. 
- [Maak omgevings variabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel-en service-eind punt teken reeks, `COMPUTER_VISION_SUBSCRIPTION_KEY` respectievelijk met de naam en `COMPUTER_VISION_ENDPOINT` .

## <a name="create-and-run-the-sample"></a>Het voorbeeld maken en uitvoeren


U kunt het voorbeeld maken en uitvoeren aan de hand van de volgende stappen:

1. Kopieer de volgende code naar een teksteditor.
1. U kunt de waarde van vervangen door `image_url` de URL van een andere afbeelding waaruit u tekst wilt extra heren.
1. Sla de code op als een bestand met de extensie `.py`. Bijvoorbeeld `get-text.py`.
1. Open een opdrachtpromptvenster.
1. Typ bij de prompt de opdracht `python` om het voorbeeld uit te voeren. Bijvoorbeeld `python get-text.py`.

```python
import json
import os
import sys
import requests
import time
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Polygon
from PIL import Image
from io import BytesIO

missing_env = False
# Add your Computer Vision subscription key and endpoint to your environment variables.
if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']
else:
    print("From Azure Cogntivie Service, retrieve your endpoint and subscription key.")
    print("\nSet the COMPUTER_VISION_ENDPOINT environment variable, such as \"https://westus2.api.cognitive.microsoft.com\".\n")
    missing_env = True

if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("From Azure Cogntivie Service, retrieve your endpoint and subscription key.")
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable, such as \"1234567890abcdef1234567890abcdef\".\n")
    missing_env = True

if missing_env:
    print("**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

text_recognition_url = endpoint + "/vision/v3.0/read/analyze"

# Set image_url to the URL of an image that you want to recognize.
image_url = "https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
data = {'url': image_url}
response = requests.post(
    text_recognition_url, headers=headers, json=data)
response.raise_for_status()

# Extracting text requires two API calls: One call to submit the
# image for processing, the other to retrieve the text found in the image.

# Holds the URI used to retrieve the recognized text.
operation_url = response.headers["Operation-Location"]

# The recognized text isn't immediately available, so poll to wait for completion.
analysis = {}
poll = True
while (poll):
    response_final = requests.get(
        response.headers["Operation-Location"], headers=headers)
    analysis = response_final.json()
    
    print(json.dumps(analysis, indent=4))

    time.sleep(1)
    if ("analyzeResult" in analysis):
        poll = False
    if ("status" in analysis and analysis['status'] == 'failed'):
        poll = False

polygons = []
if ("analyzeResult" in analysis):
    # Extract the recognized text, with bounding boxes.
    polygons = [(line["boundingBox"], line["text"])
                for line in analysis["analyzeResult"]["readResults"][0]["lines"]]

# Display the image and overlay it with the extracted text.
image = Image.open(BytesIO(requests.get(image_url).content))
ax = plt.imshow(image)
for polygon in polygons:
    vertices = [(polygon[0][i], polygon[0][i+1])
                for i in range(0, len(polygon[0]), 2)]
    text = polygon[1]
    patch = Polygon(vertices, closed=True, fill=False, linewidth=2, color='y')
    ax.axes.add_patch(patch)
    plt.text(vertices[0][0], vertices[0][1], text, fontsize=20, va="top")
plt.show()
```

## <a name="examine-the-response"></a>Het antwoord bekijken

Een geslaagd antwoord wordt geretourneerd in JSON-indeling. De voorbeeldwebpagina parseert en geeft een geslaagd antwoord weer in het opdrachtpromptvenster dat vergelijkbaar is met het volgende voorbeeld:


```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              },
              {
                "boundingBox": [
                  540,
                  665,
                  926,
                  679,
                  926,
                  825,
                  541,
                  823
                ],
                "text": "quick",
                "confidence": 0.57
              },
              {
                "boundingBox": [
                  1125,
                  686,
                  1569,
                  700,
                  1569,
                  838,
                  1125,
                  828
                ],
                "text": "brown",
                "confidence": 0.799
              },
              {
                "boundingBox": [
                  1674,
                  703,
                  1966,
                  711,
                  1966,
                  851,
                  1674,
                  841
                ],
                "text": "fox",
                "confidence": 0.442
              },
              {
                "boundingBox": [
                  2083,
                  714,
                  2580,
                  725,
                  2579,
                  876,
                  2083,
                  855
                ],
                "text": "jumps",
                "confidence": 0.878
              }
            ]
          },
          {
            "boundingBox": [
              187,
              1062,
              485,
              1056,
              486,
              1120,
              189,
              1126
            ],
            "text": "over",
            "words": [
              {
                "boundingBox": [
                  190,
                  1064,
                  439,
                  1059,
                  441,
                  1122,
                  192,
                  1126
                ],
                "text": "over",
                "confidence": 0.37
              }
            ]
          },
          {
            "boundingBox": [
              664,
              1008,
              1973,
              1023,
              1969,
              1178,
              664,
              1154
            ],
            "text": "the lazy dog!",
            "words": [
              {
                "boundingBox": [
                  668,
                  1008,
                  923,
                  1015,
                  923,
                  1146,
                  669,
                  1117
                ],
                "text": "the",
                "confidence": 0.909
              },
              {
                "boundingBox": [
                  1107,
                  1018,
                  1447,
                  1023,
                  1445,
                  1178,
                  1107,
                  1162
                ],
                "text": "lazy",
                "confidence": 0.853
              },
              {
                "boundingBox": [
                  1639,
                  1024,
                  1974,
                  1023,
                  1971,
                  1170,
                  1636,
                  1178
                ],
                "text": "dog!",
                "confidence": 0.41
              }
            ]
          }
        ]
      }
    ]
  }
}
```


## <a name="next-steps"></a>Volgende stappen

Verken vervolgens een python-toepassing die Computer Vision gebruikt voor het uitvoeren van optische teken herkenning (OCR). miniaturen met slimme bijgesneden maken; en het detecteren, categoriseren, labelen en beschrijven van visuele functies in afbeeldingen.

> [!div class="nextstepaction"]
> [Zelfstudie voor de Computer Vision-API met Python](../Tutorials/PythonTutorial.md)

* Als u snel wilt experimenteren met de Computer Vision-API, gebruikt u de [Open API-testconsole](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).
