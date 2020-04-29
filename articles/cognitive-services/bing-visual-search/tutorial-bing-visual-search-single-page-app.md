---
title: " Een web-app met één pagina maken-Bing Visual Search"
titleSuffix: Azure Cognitive Services
description: Meer informatie over het integreren van de Bing Visual Search-API in een webtoepassing met één pagina.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: aahi
ms.openlocfilehash: 83cdaecfb819fb1f4677b051f87e23e0e03daef2
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80370101"
---
# <a name="tutorial-create-a-visual-search-single-page-web-app"></a>Zelf studie: een Visual Search Web-app met één pagina maken

Het Bing Visual Search-API retourneert inzichten voor een afbeelding. U kunt een installatie kopie uploaden of een URL naar een afbeelding opgeven. Inzichten zijn visueel vergelijkbaar met afbeeldingen, Shop ping, webpagina's die de afbeelding bevatten, en meer. Inzichten die door de Bing Visual Search-API zijn geretourneerd, zijn vergelijkbaar met die van Bing.com/images.

In deze zelf studie wordt uitgelegd hoe u een web-app met één pagina kunt uitbreiden voor de Bing Afbeeldingen zoeken-API. Zie [zelf studie: een app met één pagina maken voor de Bing afbeeldingen zoeken-API](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)om deze zelf studie te bekijken of de gebruikte bron code op te halen.

De volledige bron code voor deze toepassing (na uitbrei ding voor het gebruik van de Bing Visual Search-API) is beschikbaar op [github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html).

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>De Bing Visual Search-API aanroepen en het antwoord verwerken

Bewerk de Bing Image Search zelf studie en voeg de volgende code toe aan het einde `<script>` van het element (en vóór `</script>` de afsluitende tag). Met de volgende code wordt een visueel Zoek resultaat van de API verwerkt, worden de resultaten door lopen en worden ze weer gegeven:

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

Met de volgende code wordt een zoek opdracht naar de API verzonden met behulp van een gebeurtenislistener `handleVisualSearchResponse()`voor het aanroepen van:

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

Voeg de volgende code toe aan `searchItemsRenderer` het object. Deze code voegt de koppeling **find similar** toe (vergelijkbare zoeken) die de `bingVisualSearch`-functie aanroept wanneer erop wordt geklikt. De functie ontvangt de `imageInsightsToken` als argument.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Vergelijkbare afbeeldingen weergeven

Voeg de volgende HTML-code toe op regel 601. Deze markerings code voegt een element toe om de resultaten van de Bing Visual Search-API-aanroep weer te geven:

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Met alle nieuwe JavaScript-code en HTML-elementen op hun plaats, worden zoekresultaten weergegeven met een koppeling **find similar** (vergelijkbare zoeken). Klik op koppeling om de sectie **Similar** (vergelijkbaar) te vullen met afbeeldingen die lijken op de afbeelding die u hebt gekozen. Mogelijk moet u de sectie **Similar** uitvouwen om de afbeeldingen weer te geven.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: een afbeelding bijsnijden met de Bing Visual Search SDK voor C #](tutorial-visual-search-crop-area-results.md)
