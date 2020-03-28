---
title: 'Snelstart: verkrijg afbeeldingsinzichten met behulp van de REST API en Node.js - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Leer hoe u een afbeelding uploadt naar de Bing Visual Search-API en inzichten in de afbeelding verkrijgt.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: 373d6fa5402ba703cbebe88ad562974ba97f3391
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379705"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Snelstart: krijg beeldinzichten met behulp van de Bing Visual Search REST API en Node.js

Gebruik deze snelstart om voor het eerst de Bing Visual Search-API aan te roepen en de zoekresultaten te bekijken. Met deze eenvoudige JavaScript-toepassing wordt er een afbeelding naar de API geüpload, waarna de geretourneerde gegevens van de afbeelding worden weergegeven. Hoewel deze toepassing in JavaScript is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

## <a name="prerequisites"></a>Vereisten

* [Node.js](https://nodejs.org/en/download/)
* De module Aanvragen voor JavaScript. U kunt `npm install request` de opdracht gebruiken om de module te installeren.
* De formuliergegevensmodule. U `npm install form-data` de opdracht gebruiken om de module te installeren. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>De toepassing initialiseren

1. Maak een JavaScript-bestand in uw favoriete IDE of editor en stel de volgende vereisten in:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Maak variabelen voor uw API-eindpunt, abonnementssleutel en het pad naar uw afbeelding. `baseUri`kan het algemene eindpunt hieronder zijn of het [aangepaste eindpunt voor subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) dat wordt weergegeven in de Azure-portal voor uw bron:

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Maak een `requestCallback()` functie met de naam om de respons vanuit de API af te drukken:

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>De zoekaanvraag samenstellen en verzenden

Bij het uploaden van een lokale `Content-Disposition` afbeelding moeten de formuliergegevens de koptekst bevatten. U moet `name` de parameter instellen op `filename` 'afbeelding' en de parameter kan op elke tekenreeks worden ingesteld. De inhoud van het formulier bevat de binaire gegevens van de afbeelding. De maximale afbeeldingsgrootte die u kunt uploaden is 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

1. Maak een nieuw **FormData-object** met `FormData()`en app uw `fs.createReadStream()`afbeeldingspad eraan toe met :
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. Gebruik de aanvraagbibliotheek om de `requestCallback()` afbeelding te uploaden en bel om het antwoord af te drukken. Zorg ervoor dat u uw abonnementssleutel toevoegt aan de aanmeldingskop:

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
> [Een web-app voor visueel zoeken met één pagina maken](../tutorial-bing-visual-search-single-page-app.md)
