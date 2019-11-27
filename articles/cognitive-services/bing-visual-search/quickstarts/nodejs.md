---
title: 'Snelstartgids: Image Insights ophalen met behulp van de REST API en node. js-Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Leer hoe u een afbeelding uploadt naar de Bing Visual Search-API en inzichten in de afbeelding verkrijgt.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: ecfe341fa050e693f919f35c29c8120c687c88f8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383194"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Snelstartgids: Image Insights ophalen met behulp van de Bing Visual Search REST API en node. js

Gebruik deze quickstart om voor het eerst de Bing Visual Search-API aan te roepen en de zoekresultaten te bekijken. Met deze eenvoudige JavaScript-toepassing wordt er een afbeelding naar de API geüpload, waarna de geretourneerde gegevens van de afbeelding worden weergegeven. Hoewel deze toepassing in JavaScript is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

Bij het uploaden van een lokale installatie kopie moeten de formulier gegevens de `Content-Disposition`-header bevatten. U moet de para meter `name` instellen op "afbeelding" en de `filename`-para meter kan worden ingesteld op een wille keurige teken reeks. De inhoud van het formulier bevat de binaire gegevens van de installatie kopie. De maximale afbeeldingsgrootte die u kunt uploaden is 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Vereisten

* [Node.js](https://nodejs.org/en/download/)
* De aanvraag module voor Java script. U kunt `npm install request` opdracht gebruiken om de module te installeren.
* De module form-data. U kunt de-opdracht `npm install form-data` gebruiken om de module te installeren. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>De toepassing initialiseren

1. Maak een Java script-bestand in uw favoriete IDE of editor en stel de volgende vereisten in:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Maak variabelen voor uw API-eind punt, abonnements sleutel en het pad naar uw installatie kopie:

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Maak een functie met de naam `requestCallback()` om het antwoord vanuit de API af te drukken:

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>De zoekaanvraag samenstellen en verzenden

1. Maak een nieuw **FormData** -object met `FormData()`en voeg het pad naar de afbeelding toe met behulp van `fs.createReadStream()`:
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. Gebruik de bibliotheek voor aanvragen om de installatie kopie te uploaden en roep `requestCallback()` aan om het antwoord af te drukken. Zorg ervoor dat u uw abonnements sleutel toevoegt aan de aanvraag header:

    ```javascript
    form.getLength(function(err, length){
      if (err) {
        return requestCallback(err);
      }
      var r = request.post(baseUri, requestCallback);
      r._form = form; 
      r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
    });
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Visual Search Web-app met één pagina bouwen](../tutorial-bing-visual-search-single-page-app.md)
