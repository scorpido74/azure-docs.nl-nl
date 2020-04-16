---
title: 'Snelstart: Een externe afbeelding analyseren - REST, cURL'
titleSuffix: Azure Cognitive Services
description: In deze snelstart analyseert u een externe afbeelding met behulp van de Computer Vision-API met cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: bcde9778ec030d040704ae9fcbf8f733e45c153a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405087"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-and-curl"></a>Snelstart: een externe afbeelding analyseren met behulp van de Computer Vision REST API en cURL

In deze quickstart analyseert u een op afstand opgeslagen afbeelding om visuele functies te extraheren met behulp van de Computer Vision REST API. Met de methode [Afbeelding analyseren](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) u visuele functies extraheren op basis van afbeeldingsinhoud.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) voordat u begint.

## <a name="prerequisites"></a>Vereisten

- U moet [cURL](https://curl.haxx.se/windows) hebben.
- U moet beschikken over een abonnementssleutel voor Computer Vision. U een gratis testsleutel krijgen van [Try Cognitive Services.](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) Of volg de instructies in [Een Cognitive Services-account maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om u te abonneren op Computer Vision en uw sleutel te krijgen. 

## <a name="create-and-run-the-sample-command"></a>Een voorbeeldopdracht maken en uitvoeren

U kunt het voorbeeld maken en uitvoeren aan de hand van de volgende stappen:

1. Kopieer de volgende opdracht naar een teksteditor.
1. Breng waar nodig de volgende wijzigingen in de opdracht aan:
    1. Vervang de waarde van `<subscriptionKey>` door uw abonnementssleutel.
    1. Vervang het eerste deel van`westcentralus`de aanvraag-URL ( ) door de tekst in uw eigen eindpunt-URL.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Wijzig eventueel de taalparameter van de aanvraag-URL (`language=en`) om een andere ondersteunde taal te gebruiken.
    1. Wijzig eventueel de afbeeldings-URL in de aanvraagtekst (`http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\`) naar de URL van een andere afbeelding die u wilt analyseren.
1. Open een opdrachtpromptvenster.
1. Plak de opdracht van de teksteditor in het opdrachtpromptvenster en voer de opdracht uit.

```bash
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

## <a name="examine-the-response"></a>Het antwoord bekijken

Een geslaagd antwoord wordt geretourneerd in JSON-indeling. De voorbeeldtoepassing parseert en geeft een geslaagd antwoord weer in het opdrachtpromptvenster dat vergelijkbaar is met het volgende voorbeeld:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>Volgende stappen

Bekijk de Computer Vision-API die wordt gebruikt om een afbeelding te analyseren, beroemdheden en oriëntatiepunten te detecteren, een miniatuur te maken en gedrukte en handgeschreven tekst te verkrijgen. Als u snel wilt experimenteren met de Computer Vision-API, gebruikt u de [Open API-testconsole](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [De Computer Vision-API verkennen](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
