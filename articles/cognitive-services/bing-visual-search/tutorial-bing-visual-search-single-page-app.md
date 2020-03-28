---
title: " Een web-app met één pagina maken - Bing Visual Search"
titleSuffix: Azure Cognitive Services
description: Meer informatie over het integreren van de Bing Visual Search API in een webtoepassing met één pagina.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: aahi
ms.openlocfilehash: 83cdaecfb819fb1f4677b051f87e23e0e03daef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370101"
---
# <a name="tutorial-create-a-visual-search-single-page-web-app"></a>Zelfstudie: Een web-app voor visueel zoeken met één pagina maken

De Bing Visual Search API retourneert inzichten voor een afbeelding. U een afbeelding uploaden of een URL aan een afbeelding opgeven. Inzichten zijn visueel vergelijkbare afbeeldingen, winkelbronnen, webpagina's met de afbeelding en meer. Inzichten die worden geretourneerd door de Bing Visual Search API zijn vergelijkbaar met die op Bing.com/images.

In deze zelfstudie wordt uitgelegd hoe u een web-app met één pagina uitbreidt voor de Bing Image Search API. Zie [Zelfstudie: Een app voor één pagina maken voor de Bing Image Search API](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)om die zelfstudie te bekijken of de broncode hier te laten gebruiken.

De volledige broncode voor deze toepassing (na uitbreiding naar de Bing Visual Search API) is beschikbaar op [GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Bel de Bing Visual Search API en behandel het antwoord

Bewerk de zelfstudie voor zoeken in Bing-afbeeldingen `<script>` en voeg de `</script>` volgende code toe aan het einde van het element (en vóór de afsluitende tag). De volgende code verwerkt een visuele zoekreactie vanuit de API, doorloopt de resultaten en geeft deze weer:

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}
```

De volgende code stuurt een zoekverzoek naar de `handleVisualSearchResponse()`API, met behulp van een gebeurtenislistener om aan te roepen:

```javascript
function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;

    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>Inzichttoken vastleggen

Voeg de volgende `searchItemsRenderer` code toe aan het object. Deze code voegt de koppeling **find similar** toe (vergelijkbare zoeken) die de `bingVisualSearch`-functie aanroept wanneer erop wordt geklikt. De functie `imageInsightsToken` ontvangt het als argument.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Vergelijkbare afbeeldingen weergeven

Voeg de volgende HTML-code toe op regel 601. Met deze markeringscode wordt een element toegevoegd om de resultaten van de Bing Visual Search API-aanroep weer te geven:

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Met alle nieuwe JavaScript-code en HTML-elementen op hun plaats, worden zoekresultaten weergegeven met een koppeling **find similar** (vergelijkbare zoeken). Klik op koppeling om de sectie **Similar** (vergelijkbaar) te vullen met afbeeldingen die lijken op de afbeelding die u hebt gekozen. Mogelijk moet u de sectie **Similar** uitvouwen om de afbeeldingen weer te geven.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Een afbeelding bijsnijden met de Bing Visual Search SDK voor C #](tutorial-visual-search-crop-area-results.md)
