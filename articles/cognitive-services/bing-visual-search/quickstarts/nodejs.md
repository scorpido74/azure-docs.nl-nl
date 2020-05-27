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
ms.date: 05/22/2020
ms.author: scottwhi
ms.openlocfilehash: 7dfb3adb5d7bf5b005beb7e7b75fb339d456cd15
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872610"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Snelstartgids: Image Insights ophalen met behulp van de Bing Visual Search REST API en node. js

Gebruik deze Quick Start om uw eerste oproep naar de Bing Visual Search-API te maken. Met deze eenvoudige JavaScript-toepassing wordt er een afbeelding naar de API geüpload, waarna de geretourneerde gegevens van de afbeelding worden weergegeven. Hoewel deze toepassing wordt geschreven in Java script, is de API een REST-webservice die compatibel is met de meeste programmeer talen.

## <a name="prerequisites"></a>Vereisten

* [Node.js](https://nodejs.org/en/download/)
* De aanvraag module voor Java script. U kunt de-module installeren met behulp van `npm install request` de opdracht.
* De module form-data. U kunt de-module installeren met behulp van de `npm install form-data` opdracht. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>De toepassing initialiseren

1. Maak een Java script-bestand in uw favoriete IDE of editor en stel de volgende vereisten in:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Maak variabelen voor uw API-eindpunt, abonnementssleutel en het pad naar uw afbeelding. Voor de `baseUri` waarde kunt u het globale eind punt in de volgende code gebruiken of het [aangepaste subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) eindpunt gebruiken dat wordt weer gegeven in de Azure portal voor uw resource.

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Maak een functie `requestCallback()` met de naam om het antwoord van de API af te drukken.

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>De zoekaanvraag samenstellen en verzenden

1. Wanneer u een lokale installatie kopie uploadt, moeten de formulier gegevens de `Content-Disposition` koptekst bevatten. Stel de `name` para meter in op "image" en stel de `filename` para meter in op de bestands naam van de installatie kopie. De inhoud van het formulier bevat de binaire gegevens van de installatie kopie. De maximale afbeeldings grootte die u kunt uploaden, is 1 MB.

   ```
   --boundary_1234-abcd
   Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

   ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

   --boundary_1234-abcd--
   ```

2. Maak een nieuw `FormData` object met `FormData()` en voeg het pad naar de afbeelding toe met behulp van `fs.createReadStream()` .
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

3. Gebruik de bibliotheek voor aanvragen om de installatie kopie te uploaden en bel `requestCallback()` om het antwoord af te drukken. Voeg uw abonnements sleutel toe aan de aanvraag header.

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
